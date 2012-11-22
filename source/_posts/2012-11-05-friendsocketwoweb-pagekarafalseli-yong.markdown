---
layout: post
title: "FriendSocketをweb pageからの利用"
date: 2012-11-05 15:04
comments: true
categories: friendsocket
---
### webページから利用できるようになった
カスタム DOM イベントを利用する

```html
<script type="text/javascript" src="path/to/callextension.js"></script>
<script>
client_test = function() {
    var friend;
    friend = friendsocketio.connect(host, "client@kaku-desktop.softlab.cs.tsukuba.ac.jp", "client", "server@kaku-desktop.softlab.cs.tsukuba.ac.jp", "app2");
    friend.on("connect", function(m) {
        friend.emit("event", "hello server 2");
    });
    friend.on("disconnect", function(m) {
        alert("disconnected by server");
    });
};
CallExtension(client_test);
</script>
```

callextension.js
```js
function CallExtension(f)
{
    var element = document.createElement("MyExtensionDataElement");
    element.setAttribute("attribute1", f);
    element.setAttribute("attribute2", "hello world");
    document.documentElement.appendChild(element);
    var evt = document.createEvent("Events");
    evt.initEvent("MyExtensionEvent", true, false);
    element.dispatchEvent(evt);
}
```

#### 問題
アドオン側でclienttest関数の文字列を取得し、eval()で実行している。普通のJavaScriptで実行できないもの（アドオンの機能など）をclienttest()で書いてアドオンに送ると実行出来るようになる。さらに、XPCOMのモジュールのコードをclienttest関数で書いて、アドオンに送ると、ファイルなどのアクセスも出来る。
