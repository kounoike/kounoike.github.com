<!--
.. date: 2015-10-04T22:06:26+09:00
.. draft: false
.. socialsharing: true
.. tags: ubuntu, azkaban
.. title: Azkabanでエラーメール
-->

Azkabanでエラーがあったときにメールするようにしよう、と思ったらGmailの587ポート接続に対応していない。パッチを当てて再ビルドしよう。
<!--more-->
[PR #395のパッチ](https://github.com/azkaban/azkaban/pull/395.patch)を当ててもう一度ビルド、出来上がったjarをazakabanのlib/に置いて再起動すれば
mail.portの設定が使えるようになるので、Gmailでも使えるようになる。

しかし、これが本体に取り込まれる気配がないという。
