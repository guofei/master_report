---
layout: post
title: "Friendsocket APIの修正"
date: 2012-10-01 14:16
comments: true
categories: friendsocket
---
### Socket IOに似たようなAPIを書き換えた
socket io
```js
var socket = io.connect('http://localhost');
    socket.on('news', function (data) {
        console.log(data);
        socket.emit('my other event', { my: 'data' });
});
io.sockets.on('connection', function (socket) {
    socket.emit('news', { hello: 'world' });
    socket.on('my other event', function (data) {
        console.log(data);
    });
});
```

friendsocket

クライアントのイベント：connect

サーバのイベント:connection,disconnect

複数のクライアントからの接続
```js
client_test1_1 = function() {
    var friend;
    friend = friendsocketio.connect("client@kaku-desktop.softlab.cs.tsukuba.ac.jp", "client", "server@kaku-desktop.softlab.cs.tsukuba.ac.jp", "app1", "kaku-desktop.softlab.cs.tsukuba.ac.jp");
    friend.on("connect", function(m) {
        friend.on("all", function(m) {
            alert(m);
        });
        friend.emit("event", "hello server 1");
  });
};

client_test1_2 = function() {
    var friend;
    friend = friendsocketio.connect("client2@kaku-desktop.softlab.cs.tsukuba.ac.jp", "client2", "server@kaku-desktop.softlab.cs.tsukuba.ac.jp", "app1", "kaku-desktop.softlab.cs.tsukuba.ac.jp");
    //接続が行った時
    friend.on("connect", function(m) {
        //"all"イベントを作成
        friend.on("all", function(m) {
            alert(m);
        });
    });
};
server_test1 = function() {
    var server;
    server = friendsocketio.listen("server@kaku-desktop.softlab.cs.tsukuba.ac.jp", "server", "app1", "kaku-desktop.softlab.cs.tsukuba.ac.jp");
    //server.on("connection", function(friend)){}のほうが良い？？
    server.friends.on("connection", function(friend) {
        //"event"イベントを作成
        friend.on("event", function(data, name) {
            //全てのユーザに送信
            server.friends.emit("all", "hello everyone");
            alert(name);
            alert(data);
        });
    });
};
```

複数のサーバを起動する
```js
client_test2 = function() {
    var friend;
    friend = friendsocketio.connect("client@kaku-desktop.softlab.cs.tsukuba.ac.jp", "client", "server@kaku-desktop.softlab.cs.tsukuba.ac.jp", "app2", "kaku-desktop.softlab.cs.tsukuba.ac.jp");
    friend.on("connect", function(m) {
        friend.emit("event", "hello server 2");
        //close
        friend.close();
    });
};

server_test2 = function() {
    var server;
    server = friendsocketio.listen("server@kaku-desktop.softlab.cs.tsukuba.ac.jp", "server", "app2", "kaku-desktop.softlab.cs.tsukuba.ac.jp");
    server.friends.on("connection", function(friend) {
        friend.on("event", function(data, name) {
            alert(data);
            alert(name);
        });
        //接続が切れた時
        friend.on("disconnect", function(m) {
            alert("user disconnected");
        });
    });
};
```

### 環境
#### firefox 3.6,xmpp4moz
voyagerの/Users/kaku/Public/firefox 3.6 linux mac xmpp4moz/

#### xmppサーバ
研究室の中しかアクセスできないxmppサーバをたてた。

username:client@kaku-desktop.softlab.cs.tsukuba.ac.jp password:client  
username:client2@..., password:client2  
username:server@..., password:server など

管理画面：
http://kaku-desktop.softlab.cs.tsukuba.ac.jp:9090/

ユーザ名：admin　パースワード:admin
