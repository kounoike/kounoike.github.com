<!-- 
.. title: GitbookでPDF出力を試してみる
.. slug: 2016-06-16-gitbook-pdf
.. date: 2016-06-16 23:18:17 UTC+09:00
.. tags: gitbook pdf
.. category: 
.. link: 
.. description: 
.. type: text
-->

ちょっと最近 Markdown/asciidoc などからの PDF 作成を調べて回っている。
asciidoctor も調べたんだけど出力の微調整でうまくいかなくて行き詰っている。
というわけで、Ver.3.0.0以降でPDF出力のテーマ設定が可能になったみたいな、
[GitbookIO/gitboo](https://github.com/GitbookIO/gitbook) を試してみる。

## インストール

適当に Node.js/npm をインストールして、`sudo npm install -g gitbook-cli` する。
それから`gitbook fetch 3.1.1`とかやると、~/.gitbook 以下にそのバージョンが入るっぽい。

あと PDF 生成に calibre とゆーのがいるみたいなんで、適当に `apt-get install` する。Ubuntu 14.04 LTS では結構古いバージョンだけど多分大丈夫。（手動で新しいバージョンに入れ替えちゃった）

それから PDF を作成するときになぜか X が必要なので、ssh してたりとか、将来的に Jenkins 使ったりするときには、xvfb というパッケージを入れておく。

## book を書く

```
$ gitbook init
```

で雛形を作って、ごりごり書く。

## PDF を作る

```
$ gitbook pdf ./ ./output.pdf
```

とやると、ssh しているときとかだとなんかエラーが表示される。

```
$ xvfb-run gitbook pdf ./ ./output.pdf
```

としてやると良い。


## PDF 出力の微調整

### ページ番号を入れる

```book.json
{
  "pdf": {
    "pageNumbers": false
  }
}
```

なぜか、`pdf.pageNumbers` を **false** にしてやるとページ番号が出るみたい。
バグ？

### 余白

book.json に `pdf.margin.top` とかで設定する。

### 本文を字下げする

`_layouts/ebook/page.html` というファイルを作る。これはテーマの同じ名前のファイルを置き換えることになるらしい。だからこのファイルの中で元のテーマを呼び出して拡張していく形にする。

```page.html
{% extends template.self %}
{% block style %}
{{ super() }}
<style type="text/css">
.page .section > p {
    text-indent: 1em;
}
.pdf-footer {
    border-top: 1px solid #666;
    color:#333;
}
.pdf-header {
    border-bottom: 1px solid #666;
    color:#333;
}
</style>
{% endblock %}

{% block body %}
<div class="page">
    {% block page %}
        <h1 class="book-chapter book-chapter-{{ page.depth }}">{{ page.title }}</h1>
        <div class="section">
            {{ page.content|safe }}
        </div>
        <hr />
        <div>ソース</div>
        <hr />
        <div class="section">
            <pre>{{ page.content | e }}</pre>
        </div>
    {% endblock %}
</div>
{% endblock %}
```

最初の block style でスタイルシート定義部分を上書きする。`.page .section > p` というセレクタを使っているので、本文だけがひっかかる・・・はず？

pdf-footer/pdf-header はヘッダ・フッタの色がデフォルトだと薄いので上書きしている。

block body の中ではページ（HTMLでいうページなので、PDFだと複数ページになり得る）をレンダリングする部分を上書きしている。
普通はこれを定義しない（テーマのままにする）か、`super()` するかで良いと思うが、
ここではデバッグ用にちょっと書き換えている。普通にレンダリングした後で HTML タグをエスケープして出力することで、Markdown/asciidoc がどういう HTML タグに置き換えられたかをデバッグできるようにした。
同時に、どんなタグ構造になってるかを把握することで、CSS のセレクタをちゃんと×ように・・・
