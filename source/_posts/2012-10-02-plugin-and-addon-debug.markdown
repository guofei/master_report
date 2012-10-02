---
layout: post
title: "plugin and addon debug"
date: 2012-10-02 10:52
comments: true
categories: debug
---
### gdbでpluginをdebug
1.  chromeを立ち上げる時、
```sh
/usr/bin/google-chrome --plugin-launcher='xterm -e gdb --args'
```
2.  pluginをロードするページを開く
3.  GDB付きのxterm windowが出てきて、そのwindowで.soのファイルをロードし、直接Debugできる。

例：
```sh
/usr/bin/google-chrome --plugin-launcher='xterm -e gdb --args'
directory ~/path/to/npapi-sdk/samples/unix-basic/
breakpoint
start あるいは　run
```

### chromebugでaddonをdebug
1.  同じバージョンchromebug+firebugをインストール
2.  ./firefox-bin –chromebugで実行すると、デバッグモードに入れる.(/Applications/Firefox 3.6.app/Contents/MacOS/firefox-bin)
3.  breakpointなどを設定できるし、マウスを変数名に指すと、値が出力してくれる。結構使いやすい
