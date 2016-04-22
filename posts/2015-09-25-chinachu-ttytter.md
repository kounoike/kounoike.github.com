<!--
.. date: 2015-09-25T14:27:40+09:00
.. draft: false
.. tags: chinachu, twitter, ttytter
.. title: chinachu と ttytter で Twitter に録画投稿
.. socialsharing: true
-->

# やること

recordedCommand で Twitter に録画終了を投稿する。

<!--more-->

# 前提

* [ttytterのセットアップ](http://kounoike.github.io/posts/2015-09-23-ttytter/) が出来ていること
* chinachu がセットアップできていること
* [jq](https://stedolan.github.io/jq/) を落としてきてあること

# chinachu の設定

```json
  "recordedCommand": "/home/chinachu/chinachu-scripts/recorded.sh",
```

を一行書きましょう。もちろん、recorded.shは`chmod +x`しておきます。

# recorded.sh

```bash
#!/bin/bash

RECORDED=$1
PROGRAM_JSON=$2
JQ=$(dirname $0)/jq-linux64

MSG=$(echo "$PROGRAM_JSON" | $JQ -r '"ID:" + .id + " " + .channel.name + "で「" + .title + "」の録画を終了しました"')

ttytter -ssl -status="$MSG"
```

はい、簡単ですね。ドキュメントの無いoperTweeterを設定するよりも簡単かと。
コマンドラインのメール送信ツールとして、heirloom-mailxとかを入れていればメール送信することも簡単です。

しかし、録画準備・録画開始はTweeterフックがあって、終了だけスクリプト実行フックがあるという、今の実装は何か意図があってのものなのでしょうかね。
いっそのこと、全部スクリプトフックでいいんじゃないかということで、[#214](https://github.com/kanreisa/Chinachu/pull/214)を出してます。
"under review" になっているので、そのうちマージされるのではないでしょうか。

ちなみにうちではこのパッチを当てたchinachuを動かしているので、準備・開始・終了でフックが走るようになっています。
