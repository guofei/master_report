---
layout: post
title: "5.25分散ゼミ"
date: 2012-05-24 15:04
comments: true
categories: [pgp]
---
## pgp

$ gpg --batch --no-tty --armor --no-secmem-warning --verify-options no-show-photo --use-agent -b -u 28893B0  
gpg: skipped "28893B0": secret key not available  
gpg: signing failed: secret key not available  
$ gpg --batch --no-tty --armor --no-secmem-warning --verify-options no-show-photo --use-agent -b -u 28893B08  
入力待ちの状態になった

### public keyの交換
前回はaliceとbobobの二つの鍵を生成した  
alice.pub bobob.pub

相手の公開鍵を登録  
gpg --import alice.pub  
gpg --import bobob.pub

gpgの指紋を表示  
gpg --finggerprint

gpg鍵署名  
gpg --lsign-key userid（Local 署名）  
gpg --sign-key userid（Exportable署名）

信用データベースに登録  
gpg --edit-key userid

gajimでpublic keyの表示が出来るので、public keyを選択

### straceの結果
署名  
gpg --batch --no-tty --armor --no-secmem-warning --verify-options no-show-photo -b -u 28893B08  
署名の検証  
gpg --batch --no-tty --armor --no-secmem-warning --verify-options no-show-photo --verify --enable-special-filenames  
暗号化  
gpg --batch --no-tty --armor --recipient 6251A408 --no-secmem-warning --verify-options no-show-photo --encrypt  
復号化  
gpg --batch --no-tty --armor --no-secmem-warning --verify-options no-show-photo --decrypt -q -u 28893B08

### 署名
```xml
<presence id="13" from="test0001@gmail.com/GajimF68D4BC1" to="test0001@gmail.com/GajimF68D4BC1">
  <priority>50</priority>
  <c node="http://gajim.org" ver="I8g2pphLu5w2XvNV8HDES9gqRaw=" hash="sha-1" xmlns="http://jabber.org/protocol/caps"/>
  <x xmlns="jabber:x:signed">iJwEAAECAAYFAk+9738ACgkQUe7L3yiJOwhb/wP/a/9jfP0aSM0tp4QI/dGYuTqC
  　　0Wx1fh4oSROWIbj3vHLnjbMunEsZyei4mX+ctnMzQPiODwCg9rpe2v/gryTcSx88
  　　cIC1dsleD2AouUWspuMlOjJfyIkv9/VJuVwlTXTqKq7Kvf/x5+Enz51mzFBlNMNd
  　　+CUHr+/RuqJC8jWIQuc=
  　　=Pupl</x>
  <x xmlns="vcard-temp:x:update"><photo/></x>
</presence>
```

### 暗号化と復号化
~$ gpg --fingerprint
```
/home/kaku/.gnupg/pubring.gpg
-----------------------------
pub   1024R/28893B08 2012-05-17
      Key fingerprint = 3EDC 2B33 3648 6DDC 1CB9  84EF 51EE CBDF 2889 3B08
	  uid                  alice (key) <alice@alice.com>
	  sub   1024R/B6EAAF3A 2012-05-17

pub   1024R/6251A408 2012-05-09
      Key fingerprint = 301A A4C9 1E05 8612 B153  A8A0 677E 0B66 6251 A408
      uid                  bobob (key) <bob@bob.com>
      sub   1024R/3D3B85EB 2012-05-09
```

$ gpg --batch --no-tty --armor --recipient 28893B08 --no-secmem-warning --verify-options no-show-photo --encrypt  
文字列を入力し、Ctrl+Dを入力する
```
abc
-----BEGIN PGP MESSAGE-----
Version: GnuPG v1.4.10 (GNU/Linux)

hIwDxfzxLrbqrzoBA/4xGWNSkVxs+SfEgU2iriS5fX//LrG0MqdN90JSumwB/PGs
6iRQZGzbr8a76s88gDOdiDHcQznWdMLDygpeDM/r7uEW1KdGFAmQ8StktxUGASig
iva3Ra4o4bS4Wj2wwH4dB3Nb3Sq3ePIx83SZ5ttct4o/D4BeRcYS3YAAVLYyAtI/
AQF5c/CoTQ0FXJFm7n9c+/Q5/z75qo17VlmWxK7OPzDL/IcfvHpEC2RAXObMlF2Y
5kKZBAk11BNHSlHoXaUh
=0Gdv
-----END PGP MESSAGE-----
```

$ gpg --batch --no-tty --armor --no-secmem-warning --verify-options no-show-photo --decrypt -q -u B6EAAF3A  
暗号文を入力し、Ctrl+Dを入力する
```
-----BEGIN PGP MESSAGE-----
Version: GnuPG v1.4.10 (GNU/Linux)

hIwDxfzxLrbqrzoBA/4xGWNSkVxs+SfEgU2iriS5fX//LrG0MqdN90JSumwB/PGs
6iRQZGzbr8a76s88gDOdiDHcQznWdMLDygpeDM/r7uEW1KdGFAmQ8StktxUGASig
iva3Ra4o4bS4Wj2wwH4dB3Nb3Sq3ePIx83SZ5ttct4o/D4BeRcYS3YAAVLYyAtI/
AQF5c/CoTQ0FXJFm7n9c+/Q5/z75qo17VlmWxK7OPzDL/IcfvHpEC2RAXObMlF2Y
5kKZBAk11BNHSlHoXaUh
=0Gdv
-----END PGP MESSAGE-----
abc
```
