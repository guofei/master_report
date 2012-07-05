---
layout: post
title: "javascriptからXPCOMの関数を呼び出す"
date: 2012-06-28 22:41
comments: true
categories: XPCOM
---
### xpcom
JavaScriptからXPCOMの関数を呼び出す時、obj = Components.classes[cid].createInstance()；で呼び出すことができるが、自分が書いた関数を呼び出すとTypeError: Components.classes[cid] isundefinedというエラが出た。

/Users/kaku/Library/Application Support/Firefox/Profiles/7sbio35o.defaultディレクトリのcompreg.datの中で登録できるかどうかを調べられる。

grep rsa compreg.datで調べたら、正しい時は以下のようになるはずだが
```
abs:/.../xpcom-sample@cozmixng.org/components/myCalc.so,1173322260000
{8931dfd3-edf3-4b7d-bd44-1da72064f0dc},,,,abs:/.../myCalc.so
```
何も出てこなかった。

結構firefoxのバージョンとSDKのバージョンと自分の所のライブラリのバージョンと関わるらしいので、FireFox3.6 Firefox9などを色々試して、linux上も試したが、結局、ずっとエラのままだった。

### Ctypes
調べた途中で、Ctypesというものを見つけて、複数のバージョンの Firefox で利用できるもの
```
Components.utils.import('resource://gre/modules/ctypes.jsm');
var m = ctypes.open('/usr/lib/libc.dylib');

var c = m.declare('cabs',
                ctypes.default_abi,
                ctypes.double,
                ctypes.double);
alert(c(-1));
m.close();
```
普通のhtmlで利用できないが、拡張機能でうまく実行できた
