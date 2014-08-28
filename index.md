---
layout: page
title: Kounoike's GitHub Pages
tagline: GitHub Pages
---
{% include JB/setup %}

## 作ったもの

### TAS

ゲーム|ページ|ニコ動
------|------|------
えあふり|[観鈴ノーマルモード難易度ハード](TAS-Airfli/misuzu.html)|<iframe width="312" height="176" src="http://ext.nicovideo.jp/thumb/sm23280135" scrolling="no" style="border:solid 1px #CCC;" frameborder="0"><a href="http://www.nicovideo.jp/watch/sm23280135">【ニコニコ動画】【TAS】えあふり　観鈴ちん危機一髪　ノーマルモードハード観鈴 in 20:41.60</a></iframe>
魔物ハンター舞|[舞（2周目まで）](TAS-MamonoHunterMai/pages/mai/)|<iframe width="312" height="176" src="http://ext.nicovideo.jp/thumb/sm16498187" scrolling="no" style="border:solid 1px #CCC;" frameborder="0"><a href="http://www.nicovideo.jp/watch/sm16498187">【ニコニコ動画】[TAS] 魔物ハンター舞 22:53.65</a></iframe>
魔物ハンター舞|[佐祐理](TAS-MamonoHunterMai/pages/sayuri/)|<iframe width="312" height="176" src="http://ext.nicovideo.jp/thumb/sm22990748" scrolling="no" style="border:solid 1px #CCC;" frameborder="0"><a href="http://www.nicovideo.jp/watch/sm22990748">【ニコニコ動画】【TAS】魔物ハンター舞　佐祐理モード in 11:08.07</a></iframe>

### 最適化問題

* GLPK 魔方陣ソルバー
[GLPK 魔方陣ソルバー](https://github.com/kounoike/glpk-mahoujin)


## 投稿

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
