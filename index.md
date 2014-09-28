---
layout: page
title: Kounoike's GitHub Pages
tagline: GitHub Pages
---
{% include JB/setup %}

## 投稿

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## 作ったもの

### Chef

#### chef-apt-select
[chef-apt-select](https://github.com/kounoike/chef-apt-select)は[apt-select](https://github.com/jblakeman/apt-select)を呼ぶ
cookbookです。ubuntuのミラーのうち近いものを選んでsources.listを更新します。その性質上、冪等性が無いですが、まあ仕方ないということで。

#### chef-python-bs4
chef-apt-selectの中に入れても良かったんですが、まあ練習ということで、pythonのbeautifulsoup4をインストールします。
apt-selectがbs4に依存しているので必要です。
python3にも対応しているんだけど、python3しか入っていないOSイメージって、今作れるんだっけ・・・？



### TAS

<table>
<tr><td>
 <a href="TAS-Airfli/misuzu.html">えあふり 観鈴ノーマルモード難易度ハード</a>
 <iframe width="312" height="176" src="http://ext.nicovideo.jp/thumb/sm23280135" scrolling="no" style="border:solid 1px #CCC;" frameborder="0"><a href="http://www.nicovideo.jp/watch/sm23280135">【ニコニコ動画】【TAS】えあふり　観鈴ちん危機一髪　ノーマルモードハード観鈴 in 20:41.60</a></iframe>
</td>
<td>
<a href="TAS-Airfli/kano.html">えあふり 佳乃ノーマルモード難易度ハード</a>
<iframe width="312" height="176" src="http://ext.nicovideo.jp/thumb/sm24571384" scrolling="no" style="border:solid 1px #CCC;" frameborder="0"><a href="http://www.nicovideo.jp/watch/sm24571384">【ニコニコ動画】【TAS】えあふり　観鈴ちん危機一髪　ノーマルモードハード佳乃 in 20:45.00</a></iframe></td></tr>
<tr>
<td>
 <a href="TAS-MamonoHunterMai/pages/mai/">魔物ハンター舞 舞（2周目まで）</a>
 <iframe width="312" height="176" src="http://ext.nicovideo.jp/thumb/sm16498187" scrolling="no" style="border:solid 1px #CCC;" frameborder="0"><a href="http://www.nicovideo.jp/watch/sm16498187">【ニコニコ動画】[TAS] 魔物ハンター舞 22:53.65</a></iframe>
</td>

<td>
 <a href="TAS-MamonoHunterMai/pages/sayuri/">魔物ハンター舞 佐祐理</a>
 <iframe width="312" height="176" src="http://ext.nicovideo.jp/thumb/sm22990748" scrolling="no" style="border:solid 1px #CCC;" frameborder="0"><a href="http://www.nicovideo.jp/watch/sm22990748">【ニコニコ動画】【TAS】魔物ハンター舞　佐祐理モード in 11:08.07</a></iframe>
</td></tr>
</table>

### 最適化問題

* GLPK 魔方陣ソルバー
[GLPK 魔方陣ソルバー](https://github.com/kounoike/glpk-mahoujin)


