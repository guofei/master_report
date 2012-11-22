---
layout: post
title: "FriendSocket"
date: 2012-11-12 14:03
comments: true
categories: friendsocket
---
### FriendSocketのコンタクトリスト問題の修正

前はeventでコンタクトリストを取得した
```js
s.on_get_friend = function(list){};
```

少し不便だと思って、以下のようにした
```js
s = friendsocketio.listen(host, name, password, appname);
    var list = s.contactlist();
    s.on("connection", function(friend) {
});
```

しかし、xmppからのデータがいつに来るかどうかわからないので、listがほとんど何にも取得できない

```
	     xmpp                       friendsocket
	      |                             |
	      |                             |
	   onlogined                        |
	      |                             |
	      |          list               |
	   ongetlist    ------>             |
	      |                             |s.contactlist = list
	      |                             |
	   onconnectmessage       --->      |
		                            onconnect
```

以下のように書かなければならない
```js
s = friendsocketio.listen(host, name, password, appname);
s.on("connection", function(friend) {
    var list = s.contactlist();
});
```

### os研究会
プレゼンテーションを作っている。しかし、今回は２人分のものを発表するので、青沼さんの研究内容どういう風に発表すればいいかわからない。青沼さんのcsセミナーのものを使えるかどうか。発表内容のページごとに誰の研究かをきちんと区別する必要があるか。

### 来週
friendsocketの暗号化の部分と組み合わせる。  
発表内容を書く。
