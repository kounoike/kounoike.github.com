<!--
.. date: 2015-10-24T14:43:17+09:00
.. draft: false
.. socialsharing: true
.. tags: ubuntu, azkaban, airflow, luigi, rundeck
.. title: スケジューラ色々
-->

Chinachuのエンコードジョブのバッチ管理に[Azkaban](https://azkaban.github.io/)を使っていたのだが、若干不満もあるので他に良いツールがないか調べてみた。
結果、今のところAzkabanが最適だという結論になってしまった。
<!--more-->

# Azkabanの不満

Project（ジョブのまとまり）の名前が英数字といくつかの記号でしか使えない。使い方がChinachuのエンコード管理なので、
Project名に「タイトル-日付」とかにしたい。

エンコードをずっとやっているとProjectがたまりまくって割りとうっとうしい。検索とかしやすくなると良いのだけど

# 要件

* フロー・ジョブの登録がシェルスクリプトなどからできる（recordedCommandで実行するので）
* ジョブの依存関係でDAGフローが作成できる
* フローの即時実行ができる
* フローの並列度を制御する
* できれば、登録したジョブ・フローの検索性が良いこと


# 調査結果

## [airflow](https://github.com/airbnb/airflow)

なんかAzkaban派だった人たちが移行しているらしいとのうわさをTwitterで見つけてチェックしてみた。
うーん、いまいち

* flowの実行制御ができていない？（ジョブ１個ずつ走らせないと動かないような）
* スケジュール実行（時間指定）がメインっぽいインターフェース

## [luigi](https://github.com/spotify/luigi)

シンプルなんだけど、機能不足

## [Rundeck](http://rundeck.org/)

割と良くできているように見えるけど、イマイチニーズに合わない。

* やっぱりスケジュール実行メインのインターフェース
* DAGのフローではなくステップ実行がメイン？
* タスク同時実行の並列度制御ができないらしい

# 結論

Azkabanでいいじゃん

# おまけ

調べてる過程で見つけた、[pythonのazkabanバインディング](https://pypi.python.org/pypi/azkaban)がまあまあ良い感じだった。
ごりごりと作るのはシェルスクリプトでやるのと似たようなものだけど、jobの一時ファイルを作らなくても良いとか、
pyファイル1個で全部実現できるとかメリットが色々ある。

ただ、日本語をコマンド内に記述すると上手くいかないため、Projectのpropertiesに入れる必要があった。
しかも、ただ入れただけじゃダメで、Javaの悪しき習慣である、native2ascii相当の処理をかけてやらないといけなかった。

で、native2ascii相当のことをするのに適当にpypi探して見つけたのが[zerok/pyjavaproperties-unicode](https://github.com/zerok/pyjavaproperties-unicode)。
ただし、こいつもバグがあって、変換しなければいけないUnicode文字が複数続いていると落ちるとか・・・
さくっと[PR](https://github.com/zerok/pyjavaproperties-unicode/pull/2)書いたので、そのうち反映されるかな・・・

まあ、このソースを参考にencode_unicode()を書けば良いだけなんだけど。



