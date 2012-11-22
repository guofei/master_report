---
layout: post
title: "コンピュータシステムシンポジウム論文"
date: 2012-10-15 02:08
comments: true
categories: 論文
---
### 論文
７ページ書いた

1. はじめに
2. 現在の分散型ブラウザの通信機能と問題点
3. 複数の IMS や SNS の利用
4. FriendSocket
5. グループ通信を実現するミドルウェア
6. 協調動画視聴アプリケーション
7. 実 装
8. 関連研究
9. おわりに


### Friendsocket API
サーバ側にクリックすると、クライアントに一括送信するプログラム

クライアント
```js
var client = function() {
    var friend;
    friend = friendsocketio.connect(name, password, peername, appname, host);
    friend.on("connect", function(m) {
                  friend.on("clicked", function(m) {
                                do_something(m);
                            });
              });
};
client();
```

サーバ
```js
var friend_list = new List();
var server = function() {
    var s;
    s = friendsocketio.listen(name, password, appname, host);
    s.friends.on("connection", function(friend) {
                          if(s.contactlist.contains(friend.name))
                              friend_list.push(friend);
                 });
};
function sendMsg() {
    var friend, i, len;
    for (i = 0, len = friend_list.length; i < len; i++) {
        friend = friend_list[i];
        friend.emit("clicked", "clicked");
    }
}
var el = document.getElementById("id");
el.addEventListener("Click", sendMsg, false);
server();
```

### ほかの人から聞かれた問題
XMPPの通信路を利用するらな、XMPPの中央サーバを利用しなければならない。前に話した中央を利用しないことと矛盾になる？

分散型ブラウザの通信機能はxmppだけではなく、Skypeも利用できる。skypeを使うと、中央サーバを使わない。FriendSocketもskypeの利用を実装つもりだが、やる人足りない。xmppのほうは中央サーバを使うが、全部暗号化する。

### 来週
22日面接がある。
