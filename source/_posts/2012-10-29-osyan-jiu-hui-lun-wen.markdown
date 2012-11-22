---
layout: post
title: "OS研究会論文"
date: 2012-10-29 13:37
comments: true
categories: 論文
---
### 論文

FriendSocketのAPIの説面を追加した。暗号化の部分を少し追加した。

動画協調アプリケーションとミドルウェアの部分が青沼さんが追加している

<table border=1>
<caption>FriendSocket API</caption>
<tr><td>関数</td><td>説明</td></tr>
<tr><td>frinedsocketio.connect()</td><td>サーバに接続する準備を行い、friendsocketclientオブジェクトを生成する。</td></tr>
<tr><td>frinedsocketio.listen()</td><td>クライアントからの接続を待ち、friendsocketserverオブジェクトを生成する。frinedsocketio.listen().friendsで friendsocketserverオブジェクトの取得する</td></tr>
</table>

<br>

<table border=1>
<caption>friendsocketclientオブジェクトのメッソド</caption>
<tr><td>メソッド</td><td>説明</td></tr>
<tr><td>on(e,f)</td><td>イベントを追加する。eはイベント名、fはcallbackの関数である。callback関数の引数は受信したデータである。</td></tr>
<tr><td>emit(e,d)</td><td>サーバに送信する、eはイベント名、dは送信したいデータの文字列である</td></tr>
<tr><td>close()</td><td>クライアントをcloseする</td></tr>
</table>

<br>

<table border=1>
<caption>friendsocketserverオブジェクトのメッソド</caption>
<tr><td>メソッド</td><td>説明</td></tr>
<tr><td>on("connection",f)</td><td>サーバに接続した時、callbackの関数fを呼び出す。引数はfriendsocketオブジェクトである</td></tr>
<tr><td>emit(e,d)</td><td>接続する全てのクライアントに送信する。</td></tr>
<tr><td>close(friend)</td><td>クライアントからの接続を切る。引数はconnectionの時に取得したfriendsocketオブジェクトである</td></tr>
</table>

<br>

<table border=1>
<caption>friendsocketオブジェクトのメッソド</caption>
<tr><td>メソッド</td><td>説明</td></tr>
<tr><td>on("disconnect",f)</td><td>クライアントからの接続が切られた時、callback関数を呼び出す</td></tr>
<tr><td>on(e,f)</td><td>イベントを追加する。eはイベント名、fはcallbackの関数である。callback関数の引数は受信したデータである</td></tr>
<tr><td>emit(e,d)</td><td>クライアントに送信する。eはイベント名、dは送信したいデータの文字列である</td></tr>
</table>

### FriendSocket APIのbugの修正
クライアント側から同じuserとappnameでサーバに接続すると、サーバ側が１回送信したのに、クライアント側は何回も受信する。

毎回クライアントを起動すると、
```js
var channel = XMPP.createChannel();
channel.on({event: 'message', direction: 'in', stanza: function(stanza) {return stanza.@appname == appanme}}, handleIncomingMessage);
```

クライアント側でchannelをreleaseしないと、channelがずっとメッセージの受信を受けっている。closeの時、releaseしたら、bugを修正した


### 暗号化の部分
```c
RSA *PEM_read_RSAPublicKey(FILE *fp, RSA **x, pem_password_cb *cb, void *u);
RSA *PEM_read_PublicKey(FILE *fp, EVP_PKEY **x, pem_password_cb *cb, void *u);
```
cbをNULLとして渡し、パースワードを直接uに入れたら鍵を読めるようになった。

opensslのソースコードを検索したらcallback引数のところ、(pem_password_cb *)でキャストしている。
```c pem_password_cb
typedef int pem_password_cb(char *buf, int size, int rwflag, void *userdata);
```
```c
修正前
key = PEM_read_RSAPrivateKey(fp, NULL, pass_cb, "My Private Key");
修正後
key = PEM_read_RSAPrivateKey(fp, NULL, (pem_password_cb *)pass_cb, "My Private Key");
```

しかし、まだ
```c
key = PEM_read_RSAPrivateKey(fp, NULL, pass_cb, "My Private Key");
```
に戻したら、うまく実行できた
