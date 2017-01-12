<!-- 
.. title: ACR39-NTTCom on Linux
.. slug: 2017-01-13-acr39-nttcom
.. date: 2017-01-13 01:25:13 UTC+09:00
.. tags: B-CAS,Linux,ubuntu,Raspbian
.. category: 
.. link: 
.. description: 
.. type: text
-->

ちょっと地デジ受信のテストにB-CAS読めるマシンを増やしたくて、[ACR39-NTTCom](https://www.amazon.co.jp/dp/B017Y8QV4O/)を買った。
ところがこいつはそのままではLinuxでは認識してくれなかったのでメモ。

[メーカーページ](http://www.acs.com.hk/en/driver/302/acr39u-smart-card-reader/) から `PC/SC Driver Package` をダウンロードして展開、中にあるパッケージから使ってるディストリビューションに合わせたものをインストールする。Debian/EPEL/Fedora/SUSE/Raspbian/Ubuntuとある。RaspbianもあるのでRaspberry Piでも大丈夫。

後は普通にPC/SC関連のパッケージを入れたりする。ただ今回は既に入っていたので、`sudo service pcscd restart`すればOKだった。

これで地デジカード（直感とは逆に色付きの面を下にすることに注意）をさせばpcsc_scanとかで認識する。
