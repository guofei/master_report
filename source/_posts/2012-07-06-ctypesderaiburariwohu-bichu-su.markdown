---
layout: post
title: "Ctypesでライブラリを呼び出す"
date: 2012-07-06 03:10
comments: true
categories:
---
### OpenSSL暗号PKI SSL/TLSライブラリの詳細
読んでいる

### ctypes
javascriptから.soファイルを読んでみたが、ずっと問題になっている
```
Components.utils.import('resource://gre/modules/ctypes.jsm');
var m = ctypes.open('/usr/lib/libc.dylib');
alert(m);
```
で呼び出すと、[object Library]が出たが、

```
Components.utils.import('resource://gre/modules/ctypes.jsm');
var r = ctypes.open('/Users/kaku/ctypesrsa/components/libjsrsa.so');
alert(r);
```
で何も出なかった。.soファイルを.dylibにしても同じ

しかし、firefox９でやってみると、うまく出た。xmpp拡張のほうはfirefox4以下しか動けない。

firefox9で暗号化と復号化をやったら、暗号化したものはうまく復号化できなかった。javascriptで出力した暗号文をCで復号化すると、うまく復号化できたので、自分が書いたライブラリの問題だと思う。
```
Components.utils.import('resource://gre/modules/ctypes.jsm');
var r = ctypes.open('/Users/kaku/ctypesrsa/components/libjsrsa.dylib');
var encrypt = r.declare('public_encrypt', ctypes.default_abi, ctypes.unsigned_char.ptr, ctypes.unsigned_char.ptr, ctypes.unsigned_char.ptr);
var ciphertext = encrypt('-----BEGIN RSA PUBLIC KEY----...-----END RSA PUBLIC KEY-----\n', 'abc').readString();
alert(ciphertext);
var decrypt = r.declare('private_decrypt', ctypes.default_abi, ctypes.unsigned_char.ptr, ctypes.unsigned_char.ptr, ctypes.unsigned_char.ptr);
var text = decrype('-----BEGIN RSA PRIVATE KEY-----...-----\n', ciphertext).readString();
alert(text);
r.close();
```

### 来週
OpenSSLの本を読み続く。
問題になる部分を修正。
firefox３で動けない原因を探す。
