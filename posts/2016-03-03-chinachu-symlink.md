<!--
.. date: 2016-03-03T00:54:41+09:00
.. socialsharing: true
.. tags: chinachu
.. title: エンコードしない人にもオススメのChinachuを便利にするたった一行のrecordedCommand
-->

自宅の家電レコと比べたときの Chinachu の弱点は未視聴管理ができないことだと思っていました。
でも、ちょっと考えたら簡単な方法で未視聴管理（もどき）が出来ました。それが以下の recordedCommand に登録するシェルスクリプト（実際には他にも色々やってますけど）

<!--more-->

```bash
#!/bin/bash

ln -s "$1" "/video/未視聴"
```

ちなみに`/video`を samba で共有してそこ経由で視聴しています。
さらに`/video/未視聴`は0755にしていますので chinachu ユーザで書いたものを samba のユーザで削除することができます。というわけで、このシンボリックリンクで録画を見て、見終わったらシンボリックリンクを削除することで、（手動だけど）簡単に未視聴管理ができるのでした。
これはだいぶ便利なのでオススメ。

自動エンコードしてる人はエンコード完了したらエンコード後のファイルにシンボリックリンク貼るといいんじゃないでしょうかね。

@moribuden さんのツイートで思い出したので記事にしておきます。@moribuden さんは更に一歩進んで inotify を使って削除も自動化しようとしています。できたら面白いですね。
・・・こっちが先に作っちゃおうかな（笑）
