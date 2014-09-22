---
layout: post
title: "WAQWAQProject"
description: "WAQWAQプロジェクト"
category: WAQWAQ
tags: ["WAQWAQ", "Python"]
---
{% include JB/setup %}

next49さんが[WAQWAQプロジェクト2014](http://d.hatena.ne.jp/next49/20140831/p1)で進行状況チェックに
困ってるらしいことを見つけた。

ちょっとPythonのWebスクレイピングの実験も兼ねて書いてみた。

{% gist 4ee86c32058235ed2e68 %}

[waqwaq.xlsx](/waqwaq.xlsx) こんなファイルが出来上がる。

TODO:

* コメント書く
* MS-Excelで表示内容を確認する（Libre Officeでしか見てない）
* matplotlibを使ったpng/svgの出力を試してみる
* 期間を限定する
* スコアの重み付けの見直しをする
* バイト数や、新規ページ数などでもグラフを作る

といった微修正が残っているが、まあ、まずはプロトタイプとしてこんな感じでどうだろうか。


