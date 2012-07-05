---
layout: post
title: "5.11分散ゼミ"
date: 2012-05-09 21:00
comments: true
categories: [seminar, xmpp]
---

## 就職活動
中国北京のある会社に面接を受けました。先週はskypeで面接して、今回は会社で直接面接する必要があるので、二日間帰国しました。

いろいろ相談すると

* 給料は一ヶ月10万円
* 税金、保険などは一ヶ月25000円
* 家賃は一ヶ月45000円
* 食事は一ヶ月25000円

残りは５０００円だけ!

低すぎるので、悩んでいる

## xmpp暗号化
Gajimでend to endの暗号化をする前のXMLデーターを取得し、分析してみた

Simplified Encrypted Session Negotiation http://xmpp.org/extensions/xep-0217.html  
Encrypted Session Negotiation http://xmpp.org/extensions/xep-0116.html  
Offline Encrypted Sessions http://xmpp.org/extensions/xep-0187.html  
Stanza Encryption http://xmpp.org/extensions/xep-0200.html  
Current Jabber OpenPGP Usage http://xmpp.org/extensions/xep-0027.html

Alice -> Bob　フォームを送信
```xml
<message to="bob@gmail.com/Gajim681D2858" id="49" from="alice@gmail.com/GajimEE1BF740">
<feature xmlns="http://jabber.org/protocol/feature-neg">
<x type="form" xmlns="jabber:x:data">
<field var="FORM_TYPE" type="hidden">
  <value>urn:xmpp:ssn</value>
</field>
<field var="accept" type="boolean">
  <value>1</value>
  <required/>
</field>
<field var="logging" type="list-single">
  <required/>
  <option>
    <value>may</value>
  </option>
  <option>
    <value>mustnot</value>
  </option>
</field>
<field var="disclosure" type="list-single">
  <required/>
  <option>
    <value>never</value>
  </option>
</field>
<field var="security" type="list-single">
  <required/>
  <option>
    <value>e2e</value>
  </option>
</field>
<field var="crypt_algs" type="hidden">
  <value>aes128-ctr</value>
</field>
<field var="hash_algs" type="hidden">
  <value>sha256</value>
</field>
<field var="compress" type="hidden">
  <value>none</value>
</field>
<field var="stanzas" type="list-multi">
  <option>
    <value>message</value>
  </option>
</field>
<field var="init_pubkey" type="list-single">
  <option>
    <value>none</value>
  </option>
  <option>
    <value>key</value>
  </option>
  <option>
    <value>hash</value>
  </option>
</field>
<field var="resp_pubkey" type="list-single">
  <option>
    <value>none</value>
  </option>
  <option>
    <value>key</value>
  </option>
</field>
<field var="rekey_freq" type="hidden">
  <value>4294967295</value>
</field>
<field var="sas_algs" type="hidden">
  <value>sas28x5</value>
</field>
<field var="sign_algs" type="hidden">
  <value>http://www.w3.org/2000/09/xmldsig#rsa-sha256</value>
</field>
<field var="my_nonce" type="hidden">
  <value>/phXl5d0EjQ=</value>
</field>
<field var="modp" type="list-single">
  <option label="None">
    <value>5</value>
  </option>
  <option label="None">
    <value>14</value>
  </option>
</field>
<field var="dhhashes" type="hidden">
  <value>rJVVWlW+Ejedj12oa+jZNxhREGd2a8mJwpR8BxRVMnY=</value>
  <value>mt1tK2HOuyoVt9IIV20ozlZ90DHD5xEgVBa80KxyJMs=</value>
</field>
</x>
</feature>
<thread>CkLXnnbvEXPcgYFvxhcEiqoYCPvrJmvN</thread>
<nos:x value="disabled" xmlns:nos="google:nosave"/>
<arc:record otr="false" xmlns:arc="http://jabber.org/protocol/archive"/>
</message>
```

Bob -> Alice フォームを情報を返信
```xml
<message xmlns="jabber:client" to="alice@gmail.com/GajimEE1BF740" id="47">
<feature xmlns="http://jabber.org/protocol/feature-neg">
<x xmlns="jabber:x:data" type="submit">
<field var="FORM_TYPE" type="text-single">
  <value>urn:xmpp:ssn</value>
</field>
<field var="accept" type="text-single">
  <value>true</value>
</field>
<field var="crypt_algs" type="text-single">
  <value>aes128-ctr</value>
</field>
<field var="disclosure" type="text-single">
  <value>never</value>
</field>
<field var="logging" type="text-single">
  <value>may</value>
</field>
<field var="sign_algs" type="text-single">
  <value>http://www.w3.org/2000/09/xmldsig#rsa-sha256</value>
</field>
<field var="compress" type="text-single">
  <value>none</value>
</field>
<field var="init_pubkey" type="text-single">
  <value>none</value>
</field>
<field var="stanzas" type="text-single">
  <value>message</value>
</field>
<field var="hash_algs" type="text-single">
  <value>sha256</value>
</field>
<field var="sas_algs" type="text-single">
  <value>sas28x5</value>
</field>
<field var="resp_pubkey" type="text-single">
  <value>none</value>
</field>
<field var="security" type="text-single">
  <value>e2e</value>
</field>
<field var="rekey_freq" type="text-single">
  <value>4294967295</value>
</field>
<field var="modp" type="text-single">
  <value>5</value>
</field>
<field var="nonce" type="text-single">
  <value>/phXl5d0EjQ=</value>
</field>
<field var="my_nonce" type="text-single">
  <value>yYs4UP2i72w=</value>
</field>
<field var="counter" type="text-single">
  <value>wxe+LnrR3HdVfkOtmLnp2Q==</value>
</field>
<field var="dhkeys" type="text-single">
  <value>q08JZVv/ESZF6G5SXN9PTjJcu8qJbhGqx+6eAlI093Yl7tS+X0WQqkxAJjPdhcxTP1AdUqQzpkDL9lC7rhIzmIZQdF1abHgJRgRASmKS0NLPsnN7+GCV0VmzDxsOW+VsXCuWvY+zAyJIktfSM/t9ZV7A/pKMCSbiUJiLIG1UlqmaB2GUg8c4F3UqHUxCiNjx6r8s3oBSiwANcIXQ9blO9tFjQUY+Tgl4zvUTzZcbYo4YgBgXuA0gTCsCRguwcCq8</value>
</field>
</x>
</feature>
<thread>CkLXnnbvEXPcgYFvxhcEiqoYCPvrJmvN</thread>
</message>
```
field

* crypt_algs 暗号化アルゴリズム
* hash_algs hashアルゴリズム
* compress 圧縮アルゴリズム
* init_pubkey key,hash,noneの三つ
* resp_pubkey　key,hash,noneの三つ
* rekey_freq　key再交換するためのスタンザの最小値
* sas_algs　Short Authentication String generation algorithm
* modp まだ不明
* dhhashes He

p: 大きいな素数  
g: GF(p)[体_(数学)](http://ja.wikipedia.org/wiki/%E4%BD%93_(%E6%95%B0%E5%AD%A6))  
e: g^x mod p  
He = SHA256(e)  

* dhkeys d

Nb: bobのsession id

* count Ca

Ca: n-bit ランダム数字  
Cb: Ca XOR 2^(n-1)  
y: ランダム数字 2^(2n-1) < y < p-1  
d: g^y mod p  
K: HASH(e^y mod p)  

Alice -> Bob 証明書を送る
```xml
<message to="bob@gmail.com/Gajim681D2858" id="51" from="alice@gmail.com/GajimEE1BF740">
<feature xmlns="http://jabber.org/protocol/feature-neg">
<x type="result" xmlns="jabber:x:data">
<field var="FORM_TYPE" type="text-single">
  <value>urn:xmpp:ssn</value>
</field>
<field var="accept" type="text-single">
  <value>1</value>
</field>
<field var="nonce" type="text-single">
  <value>yYs4UP2i72w=</value>
</field>
<field var="rshashes" type="text-single">
  <value>zkbnC/o1qRPF+72CvCacQIn0/KRkn3lcjE/KAfkQuXE=</value>
</field>
<field var="dhkeys" type="text-single">
  <value>57TcfePevFWXaCcsnX3NDTVaXDzArJI7vGY+CRlc75jbsnlw5MDD9QHL9RH7ntgiG9+YliJI7rJ2NnkJgTb7sAelh8GH0vDQxCcN0jKBu6qYs8p+loTxB/uQuvu9EoDU3bGGszefSJdJR5fBJvRm5UiHMT1OUvpcBSq8JjSDO7nkijDHrQfXyNq6ES3m8MoOEhdLYWkO/xlv8x9xpXGlWt7ztpu5xcbIRmWugwTlPdpz2XCukJYyhULHTwmfO06d</value>
</field>
<field var="identity" type="text-single">
  <value>s/OOrDGf0XGdBFZNnhuasOK7HoG9ACLW/B9s3jkx+cw=</value>
</field>
<field var="mac" type="text-single">
  <value>/PshCshCat8by9RYsOizBDGOCk7C0kdkGfKGjVqrVBM=</value>
</field>
</x>
</feature>
<thread>CkLXnnbvEXPcgYFvxhcEiqoYCPvrJmvN</thread>
<nos:x value="disabled" xmlns:nos="google:nosave"/>
<arc:record otr="false" xmlns:arc="http://jabber.org/protocol/archive"/>
</message>
```

Bob -> Alice 証明書を送る
```xml
<message xmlns="jabber:client" to="alice@gmail.com/GajimEE1BF740" id="48">
<init xmlns="http://www.xmpp.org/extensions/xep-0116.html#ns-init">
<x xmlns="jabber:x:data" type="result">
<field var="FORM_TYPE" type="text-single">
  <value>urn:xmpp:ssn</value>
</field>
<field var="nonce" type="text-single">
  <value>/phXl5d0EjQ=</value>
</field>
<field var="srshash" type="text-single">
  <value>/tyH+oMCSh0G1/1JF2t0YTjszQmr/VRrwoi5lhMznbo=</value>
</field>
<field var="identity" type="text-single">
  <value>bdTTIAY+fQXUivs+xcBuy3Tb1Fe+3HLxfIWZaBavCvY=</value>
</field>
<field var="mac" type="text-single">
  <value>6UdXee7X/WAWl8sdsfs99DvvoCaaazJ0uSMZGZdUMhQ=</value>
</field>
</x>
</init>
<thread>CkLXnnbvEXPcgYFvxhcEiqoYCPvrJmvN</thread>
</message>
```
field

* nonce ランダム数字のhash値
* rshashes 前のsessionのhash値
* identity 暗号化された証明書
* mac 証明書の整合性

##わからないもの
MODP (Modular Exponential)

SAS(Short Authentication String)

MAC

HMAC
