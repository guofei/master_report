---
layout: post
title: "FriendSocketの暗号化"
date: 2012-11-19 15:46
comments: true
categories: [friendsocket,plugin]
---
### FriendSocketの暗号化
friendsocketはmac上のfirefox3.6で開発し、暗号化の部分はlinux上のfirefox(>10)開発した

暗号化の部分はfirefoxが提供したjsctypeで開発したが、対応するバージョンが少ない。  
そして、鍵を一回読んで暗号化と復号化を行っている。鍵を使わない時、javascript側でメーモリを解放する必要がある
```js
var key = load_pub_key("path/public.pem");
var data1 = encrypt(key, "hello1")
var data2 = encrypt(key, "hello2")
free_key(key);
```

今はnpapiで書き直しした
```js
var pub = plugin.load_public_key("/path/public.pem");
alert(pub);
var pri = plugin.load_private_key("/path/private.pem", "12345678");
alert(pri);
var ciphertext = plugin.encrypt(pub, "abc");
alert(ciphertext);
var text = plugin.decrypt(pri, ciphertext);
alert(text);
```

プラグインのインスタンスを起動する時、鍵をロードする。プラグインのインスタンスが消える時、鍵を解放する。今はlinuxのchromeとfirefox（firefox9と16）の両方で動けるようになった。  
しかし、firefox3.6で動けない。

mac版にしたら、firefox9で動けたが、firefox3.6でも動けない。

以下の二つの条件の一つを満たさなければならない  
プラグインをfirefox3.6で動く  
xmppのアドオンをfirefox9以上で動く

### OS研究会
OS研究会のスライド

論文
