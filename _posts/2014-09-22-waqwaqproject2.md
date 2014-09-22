---
layout: post
title: "waqwaqproject2"
description: "WAQWAQプロジェクト進捗スクリプト改"
category: WAQWAQ
tags: ["WAQWAQ", "Python"]
---
{% include JB/setup %}

[next49さんのリクエスト](http://d.hatena.ne.jp/next49/20140916/p1)に応じてスクリプトを改造してみた。ちょっと一つのCSVにまとめたりencodingの指定したりとかで依存ライブラリが増えてしまったけれど。

確か、MS-Excelは普通にCSVを開くとShiftJISとして読み込んでしまうので、UTF-8を読み込ませるにはBOMを付ける必要があったはず。UTF-8が必要な執筆者がいなければ、ShiftJIS指定でもOKかと。

ポイントは過去データの取得制限で、ちょこっとHTMLのフォームを読んだ限りではWikiPedia（MediaWiki？）には「過去いついつから取得」という機能が無い様子。そこで、件数（get_limit）を指定してまとめて取得することにした。値は調整しながら運用で回避ということで。

あと、プロトタイプのExcel出力の狙いは主催者側よりも、執筆者側にあって、執筆者側が右肩上がりのグラフを見ることでモチベーションの向上につながるかなーと思って作ったものだったりする。なので、Excel出力の機能はあえて残している。

{% gist d73b798384195bd893d5 %}