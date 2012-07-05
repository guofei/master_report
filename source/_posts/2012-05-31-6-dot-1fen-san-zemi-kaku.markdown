---
layout: post
title: "6.1分散ゼミ　カク"
date: 2012-05-31 23:08
comments: true
categories: xpcom
---
xulrunnerインストール  
http://ftp.mozilla.org/pub/mozilla.org/xulrunner/releases/13.0b6/runtimes/  
macの場合/Library/Frameworks/XUL.Frameworkにインストールされる

Gecko SDKをダウンロード  
https://developer.mozilla.org/en/Gecko_SDK  
Gecko 1.9.2 (Firefox 3.6)  
Gecko 12.0 (Firefox 12.0)  
xmppのプラグインは最新のfirefoxで動けない原因はここかもしれない

idlファイル
```
#include "nsISupports.idl"
[scriptable, uuid(c95498ae-16f5-4b46-8713-2cf79e30d4ef)]
interface myICalc : nsISupports
{
  long add(in long a, in long b);
};
```
$ ../../../xulrunner-sdk/sdk/bin/header.py -I ../../../xulrunner-sdk/idl -o myIcalc.h myICalc.idl  
$ ../../../xulrunner-sdk/sdk/bin/typelib.py -I ../../../xulrunner-sdk/idl -o myIcalc.xpt myICalc.idl

headerファイル
```
#ifndef __MYCALC_H__
#define __MYCALC_H__
#include "myICalc.h"
#define MY_CALC_CONTRACT_ID "@cozmixng.org/xpcom-sample/Calc;1"
#define MY_CALC_CLASS_NAME "Calculator"
#define MY_CALC_CID_STR "8931dfd3-edf3-4b7d-bd44-1da72064f0dc"
#define MY_CALC_CID {0x8931dfd3, 0xedf3, 0x4b7d, {0xbd, 0x44, 0x1d, 0xa7, 0x20, 0x64, 0xf0, 0xdc}}
class myCalc : public myICalc
{
public:
          NS_DECL_ISUPPORTS
          NS_DECL_MYICALC
          myCalc();
private:
          ~myCalc();
protected:
          /* additional members */
};
#endif
```
NS_DECL_ISUPPORTS: マクロ、nsISupportsクラスのメンバ  
NS_DECL_MYICALC: マクロ、myCalcクラスのメンバ  
この二つのマクロを利用し、コンストラクタ、デストラクタのみ書く  
```
#define NS_DECL_ISUPPORTS                                                     \
public:                                                                       \
  NS_IMETHOD QueryInterface(REFNSIID aIID,                                    \
                            void** aInstancePtr);                             \
  NS_IMETHOD_(nsrefcnt) AddRef(void);                                         \
  NS_IMETHOD_(nsrefcnt) Release(void);                                        \
protected:                                                                    \
  nsAutoRefCnt mRefCnt;                                                       \
  NS_DECL_OWNINGTHREAD                                                        \
public:
```
idlから生成したmyICalcクラスを継承する

cppファイル
```
#include "mozilla-config.h"
#include "myCalc.h"
NS_IMPL_ISUPPORTS1(myCalc, myICalc)
myCalc::myCalc()
{
        /* member initializers and constructor code */
}
myCalc::~myCalc()
{
        /* destructor code */
}
/* long add (in long a, in long b); */
NS_IMETHODIMP myCalc::Add(PRInt32 a, PRInt32 b, PRInt32 *_retval)
{
        *_retval = a + b;
        return NS_OK;
}
```

```
#define NS_IMPL_ISUPPORTS1(_class, _interface)                                \
  NS_IMPL_ADDREF(_class)                                                      \
  NS_IMPL_RELEASE(_class)                                                     \
  NS_IMPL_QUERY_INTERFACE1(_class, _interface)
```

最後にこれらのファイルをコンパイルし、.soファイルが生成されたら完成
