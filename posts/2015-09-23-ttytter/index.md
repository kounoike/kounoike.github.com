<!--
.. date: 2015-09-23
.. description: ""
.. layout: post
.. tags: ttytter, ubuntu
.. title: "ttytterのセットアップ"
.. socialsharing: true
-->

# 何それ

コマンドラインから使えるTwitterクライアント。Chinachu のフックスクリプトに挟んで遊んでみようかと思って。
<!--more-->

# インストール

```
$ sudo apt-get install ttytter
```

# 初期設定

[TTYtterを使ったったー](http://qiita.com/kamayaplus/items/ba6dbe4cd3a335bf60ba) などの記事もあるので簡単に。

## 初回起動

```
$ ttytter
trying to find cURL ... /usr/bin/curl
-- Streaming API disabled (no -dostream) (TTYtter will use REST API only)
-- no version check performed (use /vcheck, or -vcheck to check on startup)

+----------------------------------------------------------------------------+
|| WELCOME TO TTYtter: Authorize TTYtter by signing into Twitter with OAuth ||
+----------------------------------------------------------------------------+
Looks like you're starting TTYtter for the first time, and/or creating a
keyfile. Welcome to the most user-hostile, highly obfuscated, spaghetti code
infested and obscenely obscure Twitter client that's out there. You'll love it.

TTYtter generates a keyfile that contains credentials for you, including your
access tokens. This needs to be done JUST ONCE. You can take this keyfile with
you to other systems. If you revoke TTYtter's access, you must remove the
keyfile and start again with a new token. You need to do this once per account
you use with TTYtter; only one account token can be stored per keyfile. If you
have multiple accounts, use -keyf=... to specify different keyfiles. KEEP THESE
FILES SECRET.

** This wizard will overwrite /home/chinachu/.ttytterkey
Press RETURN/ENTER to continue or CTRL-C NOW! to abort.
```

初回起動で、~/.ttytterkeyを書き込むよ、と言っているようだ。

```
Request from http://api.twitter.com/oauth/request_token ...... FAILED!: "SSL is required
"
unable to fetch token. here are some possible reasons:
 - root certificates are not updated (see documentation)
 - you entered your authentication information wrong
 - your computer's clock is not set correctly
 - Twitter farted
fix these possible problems, or try again later.
```

・・・ダメじゃん。

```
$ sudo update-ca-certificates
```

こんな感じか・・・というのは関係なく、ttytterに-sslオプションを付ければ良いだけだったかもしれない。（CAのアップデートが効いたのかもしれない）


```
$ ttytter -ssl
-- using SSL for default URLs.
trying to find cURL ... /usr/bin/curl
-- Streaming API disabled (no -dostream) (TTYtter will use REST API only)
-- no version check performed (use /vcheck, or -vcheck to check on startup)

+----------------------------------------------------------------------------+
|| WELCOME TO TTYtter: Authorize TTYtter by signing into Twitter with OAuth ||
+----------------------------------------------------------------------------+
Looks like you're starting TTYtter for the first time, and/or creating a
keyfile. Welcome to the most user-hostile, highly obfuscated, spaghetti code
infested and obscenely obscure Twitter client that's out there. You'll love it.

TTYtter generates a keyfile that contains credentials for you, including your
access tokens. This needs to be done JUST ONCE. You can take this keyfile with
you to other systems. If you revoke TTYtter's access, you must remove the
keyfile and start again with a new token. You need to do this once per account
you use with TTYtter; only one account token can be stored per keyfile. If you
have multiple accounts, use -keyf=... to specify different keyfiles. KEEP THESE
FILES SECRET.

** This wizard will overwrite /home/chinachu/.ttytterkey
Press RETURN/ENTER to continue or CTRL-C NOW! to abort.


Request from https://api.twitter.com/oauth/request_token ... SUCCEEDED!

1. Visit, in your browser, ALL ON ONE LINE,

https://api.twitter.com/oauth/authorize?oauth_token=XXXXXXXXXXXXXXXXXXXXXXXXXXXX

2. If you are not already signed in, fill in your username and password.

3. Verify that TTYtter is the requesting application, and that its permissions
are as you expect (read your timeline, see who you follow and follow new
people, update your profile, post tweets on your behalf and access your
direct messages). IF THIS IS NOT CORRECT, PRESS CTRL-C NOW!

4. Click Authorize app.

5. A PIN will appear. Enter it below.

Enter PIN> XXXXXXX

Request from https://api.twitter.com/oauth/access_token ... SUCCEEDED!
Written keyfile /home/chinachu/.ttytterkey

Now, restart TTYtter to use this keyfile.
(To choose between multiple keyfiles other than the default .ttytterkey,
 tell TTYtter where the key is using -keyf=... .)
```

途中の「Enter PIN」のところで止まっているので、1. に表示されている URL にブラウザでアクセスしてTwitter連携認証を取る。そのときにPINコードが表示されるので、ttytterに入力してやれば良い。
これで、~/.ttytterkeyが出来上がる。

## 使い方

`ttytter -ssl` でタイムラインの取得が可能。`ttytter -ssl -status="メッセージ"` で「メッセージ」と投稿することが可能。


