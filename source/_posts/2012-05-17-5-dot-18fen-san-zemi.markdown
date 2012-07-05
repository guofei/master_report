---
layout: post
title: "5.18分散ゼミ"
date: 2012-05-17 22:57
comments: true
categories: [openpgp, xmpp]
---
##gloox
c++で書かれたxmppライブラリ

OpenPGPをサポートする

しかし、keyの生成やkeyで暗号化や復号化などが自分で行わなければいけない


##straceでgajimの動作を調べた
strace -f -F -o ~/gajim.txt gajim
```sh
3930  execve("/bin/sh", ["sh", "-c", "gpg -h >/dev/null 2>&1"], [/* 40 vars */]) = 0
3931  execve("/usr/bin/gpg", ["gpg", "-h"], [/* 40 vars */]) = 0
```

しかでない

```sh
3931  access("/home/kaku/.gnupg/gpg.conf-1.4.10", R_OK) = -1 ENOENT (No such file or directory)
3931  access("/home/kaku/.gnupg/gpg.conf-1.4", R_OK) = -1 ENOENT (No such file or directory)
3931  access("/home/kaku/.gnupg/gpg.conf-1", R_OK) = -1 ENOENT (No such file or directory)
3931  access("/home/kaku/.gnupg/gpg.conf", R_OK) = 0
3931  access("/home/kaku/.gnupg/options", R_OK) = -1 ENOENT (No such file or directory)
3931  stat64("~/.gnupg", 0xbfc3d1d0)    = -1 ENOENT (No such file or directory)
3931  stat64("/home/kaku/.gnupg/gpg.conf", {st_mode=S_IFREG|0600, st_size=9364, ...}) = 0
3931  stat64("/home/kaku/.gnupg", {st_mode=S_IFDIR|0700, st_size=4096, ...}) = 0
```

gpg.confをアクセスしたので、そのファイルを開いてみると、

```
keyserver hkp://keys.gnupg.net
```

keysever：keyの交換を行うサーバー
```
gpg --send-key id
```
を実行すると、そのkeyをサーバーに送る


###keyの生成が自分でやる必要があるらしいので、手動でkeyを生成
生成したとき、面白いものが出てきた
```
今から長い乱数を生成します。キーボードを打つとか、マウスを動かす
とか、ディスクにアクセスするとかの他のことをすると、乱数生成子で
乱雑さの大きないい乱数を生成しやすくなるので、お勧めいたします。
+++++++++++++++.++++++++++....++++++++++++++++++++.++++++++++++++++++++++++++++++++++++++++..++++++++++++++++++++++++++++++++++++++++.+++++>.++++++++++...........+++++
十分な長さの乱数が得られません。OSがもっと乱雑さを収集
できるよう、何かしてください! (あと282バイトいります)
```
最初はなかなか進まないが、裏でネットワークでファイルを送るとか、いろいろやるとうまく生成した

gajimでgpg keyの選択のメニューが出てきた。

keyの交換keyserverは自動的にやってくれると思ったが、うまく行かない


```
5607  execve("/usr/local/sbin/gpg", ["gpg", "--status-fd", "34", "--batch", "--no-tty", "--armor", "--no-secmem-warning", "--verify-options", "no-show-photo", "--verify", "--enable-special-filenames", "-", "-&30"], [/* 40 vars */]) = -1 ENOENT (No such file or directory)
5607  execve("/usr/local/bin/gpg", ["gpg", "--status-fd", "34", "--batch", "--no-tty", "--armor", "--no-secmem-warning", "--verify-options", "no-show-photo", "--verify", "--enable-special-filenames", "-", "-&30"], [/* 40 vars */]) = -1 ENOENT (No such file or directory)
5607  execve("/usr/sbin/gpg", ["gpg", "--status-fd", "34", "--batch", "--no-tty", "--armor", "--no-secmem-warning", "--verify-options", "no-show-photo", "--verify", "--enable-special-filenames", "-", "-&30"], [/* 40 vars */]) = -1 ENOENT (No such file or directory)
5607  execve("/usr/bin/gpg", ["gpg", "--status-fd", "34", "--batch", "--no-tty", "--armor", "--no-secmem-warning", "--verify-options", "no-show-photo", "--verify", "--enable-special-filenames", "-", "-&30"], [/* 40 vars */]) = 0
5646  execve("/usr/local/sbin/gpg", ["gpg", "--status-fd", "35", "--batch", "--no-tty", "--armor", "--no-secmem-warning", "--verify-options", "no-show-photo", "--use-agent", "-b", "-u 28893B08"], [/* 40 vars */]) = -1 ENOENT (No such file or directory)
5646  execve("/usr/local/bin/gpg", ["gpg", "--status-fd", "35", "--batch", "--no-tty", "--armor", "--no-secmem-warning", "--verify-options", "no-show-photo", "--use-agent", "-b", "-u 28893B08"], [/* 40 vars */]) = -1 ENOENT (No such file or directory)
5646  execve("/usr/sbin/gpg", ["gpg", "--status-fd", "35", "--batch", "--no-tty", "--armor", "--no-secmem-warning", "--verify-options", "no-show-photo", "--use-agent", "-b", "-u 28893B08"], [/* 40 vars */]) = -1 ENOENT (No such file or directory)
5646  execve("/usr/bin/gpg", ["gpg", "--status-fd", "35", "--batch", "--no-tty", "--armor", "--no-secmem-warning", "--verify-options", "no-show-photo", "--use-agent", "-b", "-u 28893B08"], [/* 40 vars */]) = 0
```

error
```
~$ strace -f -F -o ~/gajim2 gajim
gpg: decryption failed: secret key not available
```
