---
layout: post
title: "6.15分散ゼミ 郭"
date: 2012-06-14 14:15
comments: true
categories: [openssl]
---
## インターンシップ
東京のあるiOSゲーム開発の会社のインターンシップに参加した

## OpenSSL
```c
char* public_encrypt(const unsigned char *pubkey, const unsigned char *from);
char* private_decrypt(const unsigned char *prikey, const unsigned char *from);
```
OpenSSLのライブラリを利用し、公開鍵で暗号化する関数と復号化する関数を書いた。一つはpublic keyと暗号化したい文字列を入れて、暗号化を行って、Base64の文字列を返す。もう一つはその逆である。

テストしたら、1.3MBくらいのデータを暗号化して、復号化すると、25秒かかった。後から高速化できるので、とりあえずXPCOMで利用出来るようにした方がいいと思って、XPCOMのほうを先にやる。

XPCOMのidlファイルに関数を追加し、xulrunner-sdk/sdk/bin/header.pyでheaderファイルを生成する時、
```
xpidl.IDLError: error: invalid syntax, jsIrsa.idl line 5:6
  char* public_encrypt(const unsigned char *pubkey, const unsigned char *from);
        ^
```
というエラが出た。

NPAPIの場合、NPStringがある、javascriptの引数を受け取りする時、NPVARIANT_TO_STRINGでjavascriptのオブジェクトをNSStringにして、NSStringをchar*にする必要がある。javascriptに値を返す時は逆にやらなければならない  
XPCOMも同じようにする必要がある。調べたら
```
IDL type | C++ Type
---------|------------
string   | char*
wstring  | PRUnichar*
AString  | nsAString
...      | ...
```

idlは以下のように書く
```c
interface jsIrsa : nsISupports
{
  string encrypt(in string pubkey, in string from);
  string decrypt(in string prikey, in string from);
};
```
constをinにする。inは読み込み専用

こうすると、idlから、.hと.cppのファイルが生成された。

前回と同じように.soファイルが生成された。

共有ライブラリが読み込まれたときに、中に入っている XPCOMを登録する機能が必要になる。その機能を crypt_module.cppとして実装する必要がある。

ウェブ上で以下のように書いているが、
```c
#include "nsIGenericFactory.h"
#include "myCalc.h"
NS_GENERIC_FACTORY_CONSTRUCTOR(myCalc)
static nsModuleComponentInfo components[] =
{
    {
	       MY_CALC_CLASS_NAME,
           MY_CALC_CID,
           MY_CALC_CONTRACT_ID,
           myCalcConstructor,
	}
};
NS_IMPL_NSGETMODULE("myCalcModule", components)
```

nsIGenericFactory.hはもう使われてない。調べたら、#include "mozilla/ModuleUtils.h"にするべき

```cpp
#include "mozilla/ModuleUtils.h"
#include "crypt.h"
NS_GENERIC_FACTORY_CONSTRUCTOR(Crypt)
NS_DEFINE_NAMED_CID(CRYPT_CID);
static const mozilla::Module::CIDEntry kCryptCIDs[] = {
        { &kCRYPT_CID, false, NULL, CryptConstructor },
        { NULL }
};
static const mozilla::Module::ContractIDEntry kCryptContracts[] = {
        { CRYPT_CONTRACT_ID, &kCRYPT_CID },
        { NULL }
};
static const mozilla::Module kCryptModule = {
        mozilla::Module::kVersion,
        kCryptCIDs,
        kCryptContracts,
        NULL
};
NSMODULE_DEFN(crypt_module) = &kCryptModule;
NS_IMPL_MOZILLA192_NSGETMODULE(&kCryptModule)
```

コンパイルすると、以下のエラが出た。
```
g++ -g -Wall -fno-rtti -fno-exceptions -shared -L/Users/kaku/xulrunner-sdk/lib -o crypt.so 
    crypt.o crypt_module.o -lxpcomglue -lnspr4 -lplds4
Undefined symbols for architecture x86_64:
 "vtable for mozilla::GenericModule", referenced from:
  mozilla::GenericModule::GenericModule(mozilla::Module const*)in crypt_module.o
ld: symbol(s) not found for architecture x86_64
collect2: ld returned 1 exit status
make: *** [crypt.so] Error 1
make: Target `all' not remade because of errors.
```

一回コンパイルして、失敗した後、
Makefileで
```
BITTINESS = -m32
CXX = c++ ${BITTINESS}
```
を追加し、cleanしないでもう一回コンパイルすると、コンパイルが成功した

make cleanしたら、以下のエラが出た
```
c++ -m32 -g -Wall -fno-rtti -fno-exceptions -shared -L/Users/kaku/xulrunner-sdk/lib -o
    crypt.so crypt.o crypt_module.o -lxpcomglue -lnspr4 -lplds4
ld: warning: ignoring file xulrunner-sdk/lib/libxpcomglue.a,
    file was built for archive which is not the architecture being linked (i386)
ld: warning: ignoring file xulrunner-sdk/lib/libnspr4.dylib,
    file was built for unsupported file format which is not the architecture being linked (i386)
ld: warning: ignoring file xulrunner-sdk/lib/libplds4.dylib,
    file was built for unsupported file format which is not the architecture being linked (i386)
Undefined symbols for architecture i386:
  "NS_TableDrivenQI(void*, QITableEntry const*, nsID const&, void**)", referenced from:
    Crypt::QueryInterface(nsID const&, void**)in crypt.o
  "vtable for mozilla::GenericModule", referenced from:
    mozilla::GenericModule::GenericModule(mozilla::Module const*)in crypt_module.o
ld: symbol(s) not found for architecture i386
collect2: ld returned 1 exit status
```
