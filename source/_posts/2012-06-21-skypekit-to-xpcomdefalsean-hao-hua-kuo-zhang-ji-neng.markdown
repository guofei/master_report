---
layout: post
title: "skypekit と xpcomでの暗号化拡張機能"
date: 2012-06-21 22:35
comments: true
categories: [skypekit, xpcom]
---
### SkypeKit
xpcomをコンパイルするとき、色々なところで詰まったので、skypekitについて調べた

SkypeKitは、Skypeデスクトップアプリケーションがなくても機能する。 SkypeKitは、ユーザに何も表示せず実行される、Skypeの「ヘッドレス」バージョンと考えられる。前のSkype Public APIは今後改善されることはないが、維持管理は行われている。

現在ではC++,Java,.net,Pythonのライブラリを提供している。ライブラリをダウンロードするとき、お金の支払う要求が来たので、やめた。検索すると、C,Ruby版のものが公開している。

ruby https://github.com/railsware/skypekit  
C https://github.com/railsware/libskypekit

```
require "skypekit"
$skype = Skypekit::Skype.new(:keyfile => ENV['SK_KEY'])
$skype.start
$skype.login(ENV['SK_USER'], ENV['SK_PASS'])
loop do
  event = $skype.get_event
  unless event
    sleep 5
    next
  end
  case event.type
  when :account_status
    if event.data.logged_in?
      puts "Congrats! We are Logged in!"
    end
    if event.data.logged_out?
      puts "Authentication failed: #{event.data.reason}"
    end
  when :chat_message
    message = event.data
    p message
    if message.body == "ping"
      $skype.send_chat_message(message.convo_id, "pong")
    end
  end
end
```

app2appなどの機能もあるが、ruby版は実装していない。C++のサンプルコードをネットで見つけた
``` cpp write
bool      cmdResult;
SEString  appName = "TestApp1";
SEString  streamName;
char      buffer[32 * 1024];
uint      bufSize = 32 * 1024;
SEBinary writeBuffer;
writeBuffer.set(&buffer, bufSize);
skype->App2AppWrite(appName, streamName, writeBuffer, cmdResult);
```

``` cpp read
void MySkype::OnApp2AppStreamListChange (
        const Sid::String &appname,
        const APP2APP_STREAMS &listType,
        const Sid::List_String &streams,
        const Sid::List_uint &receivedSizes)
{
        if (streams.size() != 0)
        {
                for (uint i=0; I < streams.size(); i++)
                {
                        if (listType == Skype::RECEIVED_STREAMS)
                        {
                                bool      readOk;
                                SEBinary  buffer;
                                App2AppRead(appname, streams[i], readOk, buffer);
                        };
                };
        };
};
```


### XPCOM
前回はコンパイルするとき、ld: symbol(s) not found for architecture x86_64のところで詰まった。ar x libxpcomglue.aでやって、file *.oで.oファイルを調べた
```
$ file *.o
nsArrayEnumerator.o:            Mach-O 64-bit object x86_64
nsArrayUtils.o:                 Mach-O 64-bit object x86_64
nsCOMArray.o:                   Mach-O 64-bit object x86_64
nsCOMPtr.o:                     Mach-O 64-bit object x86_64
nsCRTGlue.o:                    Mach-O 64-bit object x86_64
nsCategoryCache.o:              Mach-O 64-bit object x86_64
nsClassInfoImpl.o:              Mach-O 64-bit object x86_64
nsComponentManagerUtils.o:      Mach-O 64-bit object x86_64
nsCycleCollectionParticipant.o: Mach-O 64-bit object x86_64
nsCycleCollectorUtils.o:        Mach-O 64-bit object x86_64
nsDeque.o:                      Mach-O 64-bit object x86_64
nsEnumeratorUtils.o:            Mach-O 64-bit object x86_64
nsGlueLinkingOSX.o:             Mach-O 64-bit object x86_64
nsID.o:                         Mach-O 64-bit object x86_64
nsIInterfaceRequestorUtils.o:   Mach-O 64-bit object x86_64
。。。
```
全部x86_64のものである。色々調べたて、試したがうまく修正できない。BITTINESSを３２にしてコンパイルできる原因もわからないので、ずっとその辺で悩んでいた。

sdkの中にはいたら、libxpcomglue_s.aというライブラリがあって、ar xで.oファイルをGenericModule.oなどのファイルが取得した。前の64bitたけでコンパイルする時のエラは
```
Undefined symbols for architecture x86_64:
 "vtable for mozilla::GenericModule", referenced from:
   mozilla::GenericModule::GenericModule(mozilla::Module const*)in crypt_module.o
```
Makefileを修正して、sがついているライブラリをつかうと、新しいエラが出た。
```
Undefined symbols for architecture x86_64:
  "_moz_xmalloc", referenced from:
    mozilla::GenericModule::GetClassObject(nsIComponentManager*, nsID const&, nsID const&, void**)in libxpcomglue_s.a(GenericModule.o)
  "_moz_free", referenced from:
    mozilla::GenericModule::Release()     in libxpcomglue_s.a(GenericModule.o)
    mozilla::GenericFactory::Release()     in libxpcomglue_s.a(GenericFactory.o)
ld: symbol(s) not found for architecture x86_64
```
sdkでlibmozalloc.dylibを見つけたので、Makefileで追加すると、うまくコンパイルができた

## 次回
javascript側使えるようにする。
