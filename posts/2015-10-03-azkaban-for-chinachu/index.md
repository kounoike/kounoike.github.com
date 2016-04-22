<!--
.. date: 2015-10-03T20:14:58+09:00
.. draft: false
.. socialsharing: true
.. tags: azkaban, chinachu, ubuntu
.. title: Chinachuの録画後エンコード・CMカットにAzkabanを入れてジョブ実行管理してみる(Azkabanインストール編)
-->

[Azkaban](http://azkaban.github.io/)というジョブ管理ツールがあります。
今回はこれを使ってChinachuの録画後にエンコード・CM検出といったジョブを投げて、ジョブ実行管理させてみます。
recordedCommandで直接エンコードした場合には同時実行されてしまうという問題があるので、
Azkabanに並列実行数の制御をやらせてみることが狙いです。なんせ、J2900は30分の番組のエンコードに3時間もかかってしまうような
非力なCPUですので、同時実行の制御は非常に重要です。
<!--more-->

# Azkaban のインストール

[Azkaban ダウンロードページ](http://azkaban.github.io/downloads.html) には 2.5.0 までしか無いですが、
[GitHub](https://github.com/azkaban/azkaban) の Releases には 2.7.0 までがあります。
したがって、自前でビルドすることにします。しかし、2.7.0はうまく動かないので、2.6.4をビルドします。

環境はいつもの Ubuntu Server LTS 14.04.3 です。

## 事前準備

Java がいるのでOracle JDKを入れます。Ubuntu への入れ方は適当にぐぐって調べてください。

* 2.6.4のtar.gzをダウンロード
* 展開＆cd

までやったとします。

## Azkaban Solo Server のビルド

今回は MySQLとかたっていないので、Javaの内蔵DBである H2 DBを使ってみます。
更にWebUI のサーバと実行用サーバの2つが一緒になっている Solo Server というものでやってみます。

ビルドシステムも Ant から Gradle に変わっています。良く分からないのですが、以下の手順でいけるようです。

### ビルド

soloserver の配布用 tar をビルドします。
なんかpythonジョブのテストに失敗するので、テストをスキップします。

```
$ ./gradlew soloserverDistTar -x test
```

なんか注意が出てますけどとりあえずビルドは出来ました。build/distributionsにazkaban-solo-server-2.6.4.tar.gzが出来ています。

## 展開＆テスト実行

```
$ tar zxvf build/distributions/azkaban-solo-server-2.6.4.tar.gz && sudo cp -R azkaban-solo-server-2.6.4 /opt/azkaban-solo-server
```

念のため設定系は一般ユーザから読めないようにしておきましょう。

```
$ chmod og-r /opt/azkaban-solo-server/conf/*
```

テスト実行してみます。

```
$ cd /opt/azkaban-solo-server
$ sudo ./bin/azkaban-solo-start.sh
```

バックグラウンドで色々微妙なログが流れますが、何か起動したっぽいです。

## ufw（ファイアウォール）の設定

```
$ ufw allow 8081/tcp
```

ufwでファイアウォールを管理している場合は8081/tcpを空けます。

ポート8081にブラウザで接続して、何か見えるようでしたら、動作確認できたということで、もうちょっと設定をしていきます。
azkaban-solo-shutdown.shで停止します。

# 設定

## conf/azkaban-users.xml

てきとーにユーザ・パスワードを設定します。エンコード用にユーザchinachuを追加。

## conf/azkaban.properties

サーバの名前とタイムゾーンくらいは設定しておきましょう。重要なこととして、並列実行されるジョブを1つだけに制限しておきます。
（executor.flow.threadsとflow.num.job.threadsのどっちが利くかわかってない。後者だけだと同時実行されちゃったので多分前者かな）

```
# Azkaban Personalization Settings
azkaban.name=pt3rec
azkaban.label=My PT3REC Azkaban
azkaban.color=#FF3601
default.timezone.id=Asia/Tokyo

executor.flow.threads=1

flow.num.job.threads=1
```

## ユーザ

なんか一般ユーザで実行させるっぽいので適当に作る。

```
$ sudo useradd -s /bin/false -d /opt/azkaban-solo-server azkaban
$ sudo chown -R azkaban:azkaban /opt/azkaban-solo-server
```

## /etc/init/azkaban-solo.conf

```/etc/init/azkaban-solo.conf
description "Azkaban Solo Server"
author "KOUNOIKE Yuusuke <kounoike.yuusuke@gmail.com>"

start on runlevel [2345]
stop on runlevel [016]

setuid azkaban
setgid azkaban

chdir /opt/azkaban-solo-server

script
echo $$ > /var/run/azkaban-solo/azkaban-solo.pid

azkaban_dir=/opt/azkaban-solo-server
tmpdir=/tmp

for file in $azkaban_dir/lib/*.jar;
do
  CLASSPATH=$CLASSPATH:$file
done

for file in $azkaban_dir/extlib/*.jar;
do
  CLASSPATH=$CLASSPATH:$file
done

for file in $azkaban_dir/plugins/*/*.jar;
do
  CLASSPATH=$CLASSPATH:$file
done

if [ "$HADOOP_HOME" != "" ]; then
        echo "Using Hadoop from $HADOOP_HOME"
        CLASSPATH=$CLASSPATH:$HADOOP_HOME/conf:$HADOOP_HOME/*
        JAVA_LIB_PATH="-Djava.library.path=$HADOOP_HOME/lib/native/Linux-amd64-64"
fi

if [ "$HIVE_HOME" != "" ]; then
        echo "Using Hive from $HIVE_HOME"
        CLASSPATH=$CLASSPATH:$HIVE_HOME/conf:$HIVE_HOME/lib/*
fi

echo $azkaban_dir;
echo $CLASSPATH;

executorport=`cat $azkaban_dir/conf/azkaban.properties | grep executor.port | cut -d = -f 2`
serverpath=/opt/azkaban-solo-server

if [ -z $AZKABAN_OPTS ]; then
  AZKABAN_OPTS=-Xmx3G
fi
AZKABAN_OPTS="$AZKABAN_OPTS -server -Dcom.sun.management.jmxremote -Djava.io.tmpdir=$tmpdir -Dexecutorport=$executorport -Dserverpath=$serverpath"

java $AZKABAN_OPTS -cp $CLASSPATH azkaban.soloserver.AzkabanSingleServer -conf $azkaban_dir/conf >> /var/log/azkaban-solo/azkaban-solo.log 2>&1
end script
```

```
$ sudo mkdir /var/run/azkaban-solo /var/log/azkaban-solo
$ sudo chown azkaban:azkaban /var/run/azkaban-solo /var/log/azkaban-solo
$ sudo initctl reload-configuration
$ sudo start azkaban-solo
```

Upstartよくわかんない。

ジョブ作成編に続く。

