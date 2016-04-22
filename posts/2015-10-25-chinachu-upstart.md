<!--
.. date: 2015-10-25T14:21:02+09:00
.. draft: false
.. socialsharing: true
.. tags: chinachu, ubuntu
.. title: Chinachuのサービス化を簡単に書く（Ubuntu/upstart限定）
-->

upstart を使うとサービスの記述が簡単です。chinachu-wui と chinachu-operator の記述をしてみました。
<!--more-->
/etc/init/chinachu-wui.conf が以下の通り。（YOUR NAME/YOUR EMAIL は適当に置き換えて）

```text
description "Chinachu - WUI"
author "YOUR NAME<YOUR EMAIL>"

start on runlevel [2345]
stop on runlevel [016]

setuid chinachu
setgid chinachu

chdir /home/chinachu/chinachu
exec ./chinachu service wui execute
respawn
```

/etc/init/chinachu-operator.conf もほぼ同じ。
```text
description "Chinachu - operator"
author "YOUR NAME <YOUR EMAIL>"

start on runlevel [2345]
stop on runlevel [016]

setuid chinachu
setgid chinachu

chdir /home/chinachu/chinachu
exec ./chinachu service operator execute
respawn
```

この2つのファイルを/etc/init に置いたら `sudo initctl reload-configuration` して認識させる。
で、再起動するか、`sudo start chinachu-wui`、`sudo start chinachu-operator` するだけ。

/etc/init.d に書くのに比べてこんなに簡単なんてびっくりだ。
