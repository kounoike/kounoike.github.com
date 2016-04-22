<!--
.. date: 2015-10-04T00:36:13+09:00
.. draft: false
.. socialsharing: true
.. tags: azkaban, chinachu, ubuntu
.. title: Chinachuの録画後エンコード・CMカットにAzkabanを入れてジョブ実行管理してみる(ジョブ作成編)
-->

Azkabanが入ったとして、ジョブを作成して投入・実行指令を出すシェルスクリプトを作ってみます。
まずはrecordedCommandに登録する前にテスト実行できるレベルで、CM検出無しのエンコードのみで。
エンコードオプションは、[ffmpeg で TS をできるだけ高画質な mp4 へ変換してみた](http://d.hatena.ne.jp/munepi/20091227/1261941397)を参考に設定。
<!--more-->

# 準備

ジョブフローのアップロードにzipファイルの作成が必要なので、インストールしておく。

```shell-session
$ sudo apt-get install zip
```

ffmpegのプリセットを、/home/chinachu/chinachu/usr/share/ffmpeg/にlibx264-hq-ts.ffpresetの名前で置いておく。

```text
level=41
crf=25
coder=1
flags=+loop
cmp=+chroma
partitions=+parti8x8+parti4x4+partp8x8+partb8x8
me_method=umh
subq=7
me_range=16
g=250
keyint_min=25
sc_threshold=40
i_qfactor=0.71
b_strategy=1
qmin=10
rc_eq='blurCplx^(1-qComp)'
bf=16
bidir_refine=1
refs=6
```

deblockalpha, deblockbetaは設定しているとエラーになったので外した。


# 作成するジョブのイメージ

## shared.properties, gr.properties

shared.propertiesには共有する変数などを代入しておく。以下は地上波用の設定値が入っている

```text
ffmpeg=/home/chinachu/chinachu/usr/bin/ffmpeg
threads=2
```

gr.propertiesとして、地上波用エンコード設定値を入れておく。

```text
preset=libx264-hq-ts
size=640x480
rate=30000/1001
aspect=16:9
```

## encode.job

エンコードを行うジョブ。テンポラリディレクトリでエンコードを行い、完了後にmvする。

```bash
type=command
command=${ffmpeg} -y -i "${m2ts}" -v 0 -f mp4 -vcodec libx264 -vpre ${preset} -r ${rate} -aspect ${aspect} -s ${size} -bufsize 20000k -maxrate 25000k -acodec libfdk_aac -ac 2 -ar 48000 -ab 128k -threads ${threads} "${encodingmp4}"
command.1=mv -f ${encodingmp4} ${encodedmp4}
```

## files.properties

ファイル名を入れておく。シェルスクリプトでこのファイルだけを番組ごとに変える。

```text
m2ts=/path/to/recoreded.m2ts
encodingmp4=/tmp/path/to/encoding.mp4
encodedmp4=/another/path/to/encoded.mp4
```

## create_job.sh

ジョブのzipファイルを作成し、アップロード・実行開始を指示するシェルスクリプト。
recordedCommandから呼び出す前提で作っておくので、引数としてm2tsのファイル名・番組ID・チャンネルIDを渡すことにする。

TODO: チャンネルIDから放送波を見てエンコード設定を切り替える。

```bash
#!/bin/bash

JQ=/home/chinachu/chinachu-scripts/jq-linux64

tmpldir=$(dirname $0)/job_tmpl
jobsdir=$(dirname $0)/jobs

m2ts=$1
mp4=$(basename "${1%.*}").mp4

program_id=$2
ch_id=$3

encodingmp4=/video/encoding/$mp4
encodedmp4=/video/mp4/$mp4

tmpdir=/tmp/$program_id

mkdir $tmpdir
cd $tmpldir
cp gr.properties shared.properties encode.job $tmpdir/
cd $tmpdir

cat << EOS | native2ascii > files.properties
# files.properties
m2ts=${m2ts}
encodingmp4=${encodingmp4}
encodedmp4=${encodedmp4}
EOS

zip ${program_id}.zip *.properties *.job

# login
curl -o login.json -k -X POST --data "action=login&username=azkaban&password=azkaban" http://localhost:8081

session_id=$($JQ -r '.["session.id"]' login.json)
if [ -z "$session_id" ] ; then
  echo $($JQ -r '.["error"]' login.json)
  exit 1
fi

# Create project
curl -k -o project.json -X POST --data "session.id=${session_id}&name=${program_id}&description=encode+${mp4}" http://localhost:8081/manager?action=create

# Upload Project Zip
curl -k -o upload.json -i -H "Content-Type: multipart/mixed" -X POST --form "session.id=${session_id}" --form 'ajax=upload' --form "file=@${program_id}.zip;type=application/zip" --form "project=${program_id}" http://localhost:8081/manager

# Fetch Flows of a Project
curl -k -o flow.json --get --data "session.id=${session_id}&project=${program_id}&ajax=fetchprojectflows" http://localhost:8081/manager

# Execute Flows
for flowId in $($JQ -r '.["flows"][0]["flowId"]' flow.json); do
  echo "Execute ${flowId}"
  curl -k -o ${flowId}.json --get --data "session.id=${session_id}&ajax=executeFlow&project=${program_id}&flow=${flowId}" http://localhost:8081/executor
  $JQ -r '.execid' ${flowId}.json > ${jobsdir}/${flowId}
done

cd /
rm -rf $tmpdir

# お片づけ
for f in ${jobsdir}/*; do
  execid=$(cat $f)
  status=$(curl -k --data "session.id=${session_id}&ajax=fetchexecflow&execid=${execid}" http://localhost:8081/executor | $JQ -r '.status')
  if [ "$status" -eq "SUCCEEDED" ]; then
    proj=$(curl -k --data "session.id=${session_id}&ajax=fetchexecflow&execid=${execid}" http://localhost:8081/executor | $JQ -r '.project')
    curl -k --data "session.id=${session_id}&delete=true&project=${proj}" http://localhost:8081/manager
    rm -f $f
  fi
done
```

