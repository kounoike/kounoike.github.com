<!-- 
.. title: GitBucket と Jenkins を Pipeline で強力に連携させる
.. slug: 2016-07-23-gitbucket-jenkins
.. date: 2016-07-23
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text
-->

Jenkins の 2.7.1 が LTS にやってきたことを機に Jenkins 2 について色々と調べてみた。
目玉はやはり Pipeline Plugin の導入であり、Pipeline ジョブの場合、GitHub との連携は
GitHub Organization Folder Plugin で行うのがとても便利らしい。
ところがこの GitHub Organization Folder Plugin はそのままでは GitBucket とは
連携できないらしい。そこで、どうして動かないのかを調べて、解決するべく PR 送るので、
補足を兼ねてメモしておく。

<!-- more -->

## Pipeline って何？

[Jenkins Pipelines と Android アプリ開発 第7回大阪Jenkins勉強会資料 P.6](http://nobuoka.github.io/presentations/2016/0618-osaka-jenkins.html#/6) より、

> **PIPELINE AS A CODE**
> 
> * Jenkins 2 の目玉の一つ
> * ジョブの流れをコードで表現
>     * Groovy による DSL

もっと砕けた言い方をすると、
「Travis CI とかが YAML でジョブ書くような奴の Jenkins 版を Groovy で書く奴」
というところだろうか。今まで GUI でジョブ設定をしていたところを、
Groovy スクリプトで書くようなもの。

## GitHub Organization Folder って何？

GitHub Organization **Folder** と、名前に Folder が入ってることがあらわしているように、
複数のジョブをまとめて設定するための機能を実現している。それぞれのジョブは GitHub の
一つ一つのリポジトリに対応している。

Travis CI とかがジョブ設定の YAML ファイルを GitHub のリポジトリに置くように、
Jenkins の Groovy スクリプトファイルをリポジトリに置く方法の一つで、一番オススメの
方法。設定したユーザ／ Organization のリポジトリの PR やブランチをチェックして、
`Jenkinsfile` という名前のファイルを見つけると、処理対象として Jenkins ジョブを
生成し、処理してくれる。

また、GitHub の WebHook にも対応していて Push や PR に即座に反応させることもできるし、
Jenkins の実行結果を ブランチや PR にステータスとして送信することもできてとっても便利。

この機能を設定しておくと、GitHub で新しくリポジトリを作ったときに Jenkins と連携させようと
思ったときに行うことは、

* Jenkinsfile にジョブの詳細を Groovy で記述し、リポジトリに入れる
* （ポーリングを待つのが嫌なら）GitHub 側で WebHook を設定する
* （もし、Jenkins からアクセスするユーザが異なるなら）GitHub 側で Collaborator に追加する

これだけの手順で、しかも **Jenkins 側の設定を何もいじらず**に実現できる。便利！

## だけど・・・

そもそも GitHub においてあるのなら、他の CI でもいいんじゃない？　とか思ったり。
さらに、**GitHub に置けないから GitBucket 立ててるようなところにこそ向いてるんじゃないの？**
とか思ったりした。実際に、このプラグインでは GitHub Enterprise にも対応させてあるようで、
設定で GitHub Enterprise のサーバを追加することができるようになっている。
そこに GitBucket のアドレスを書けばそれで連携できる・・・と思いきや、問題発生。


## 対応状況

とりあえず、

