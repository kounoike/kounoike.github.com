////
.. title: (Jenkins + GitBucket) * GitHub Organization Folder Plugin でウィーン
.. slug: index
.. date: 2016-09-11 16:58:38 UTC+09:00
.. tags: Jenkins,GitBucket
.. category:
.. link:
.. description:
.. type: text
////

## (Jenkins + GitBucket) * GitHub Organization Folder Plugin

Jenkins 2 も LTS になってだいぶ経ちましたね。Jenkins 2 の目玉といわれる
groovy スクリプトを使った Pipeline ジョブとその周辺のプラグイン類がなかなか面白いです。
今回はその中でも GitHub Organization Folder Plugin に注目してみました。

### Pipeline ジョブってナニ？

groovy スクリプトでビルド手順などを記述するものです。従来のように GUI でポチポチするのではなく、
SCM の管理下に Jenkinsfile というファイルを置いておくと、その内容にしたがってビルドやテストなどを
実行してくれるものです。

### GitHub Oraganization Folder Plugin ってナニ？

GitHub Organization Folder Plugin (以下GHO プラグイン) は、
GitHub の指定したユーザ／グループ (Organization) のリポジトリを全部チェックして、
Jenkinsfile があるリポジトリを探します。Jenkinsfile があると GHO プラグインが自動的に
リポジトリに対応するフォルダーを作成してくれます。
更にそれぞれのリポジトリに対してブランチや PR 単位でジョブを作成してくれます。
グループ (ユーザ) / リポジトリ / ブランチ (PR) という階層構造になります。

*Organization* Plugin という割りには、ユーザも同じように扱うところがちょっと意外ですね。

### それ GitBucket で出来ない？

GitHub にリポジトリが置いてある場合は普通に設定していけば GHO プラグインは使えるのですが、
今回は [GitBucket](https://github.com/gitbucket/gitbucket) で使うことに挑戦してみます。
GitBucket は


> A Git platform powered by Scala with easy installation, high extensibility & github API compatibility

と謳っています。API compatible なのだったら GHO プラグインも動いちゃったりするのではないでしょうか。

そう思って、HTTP Proxy を間に入れて通信を確認したりして調査した結果、GitBucket の 4.2.0 では
動きませんでした。そこで、原因を調査して対策を講じた PR を送った結果、4.3.0 以降では**条件付きで**
動くようになりました。

### どうすれば動くの？

GitBucket + Jenkins で GHO プラグインが動かなかった原因は以下の3点でした。

- API v3 の確認に使う API root エントリポイントが無かった (API のリストを返す API)
- API の認証に HTTP Basic 認証を使えなかった
- Jenkins の Git Branch Source Plugin で git リポジトリの URL を決めうちで指定していた

このうち、前の2つについては GitBucket 側の問題なので、PR を送って 4.3.0 にマージされたので、
問題はなくなりました。最後の Jenkins のプラグイン (GHO プラグインがこのプラグインを使っている)で、
Git のリポジトリ URL を http://server/user/repo.git と決めうちにしているところが問題でした。

GitBucket では http://server:port/gitbucket_prefix/git/user/repo.git となります。
Jenkins の Git Branch Source Plugin では、

- ポートの指定を無視する (HTTP なら80、HTTPS なら443になってしまう)
- サーバのルートで動いているものとしている (prefix が指定できない)
