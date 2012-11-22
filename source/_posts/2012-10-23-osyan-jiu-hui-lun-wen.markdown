---
layout: post
title: "OS研究会論文"
date: 2012-10-23 13:32
comments: true
categories: 論文
---
### 面接
難しかった

日本でどのぐらい働きたいと聞かれて、５年ぐらいと答えると、５年間でせっかく人材として育ったのに、会社をやめるのはよくない

分散OSの話もあって、自分は分散OSが難しいですと話したら、どこが難しいと聞かれた。

### 論文
９章＝＞７章

元の画像は見づらいので、ソースコードを画像からテキストに変えた  
実装をそれぞれの章に入れた

これからやるところ：  
関連研究のところにBrowser DBusを追加  
暗号化の部分を追加  
動画アプリケーションの話

### 暗号化
鍵を読み込む時、パスフレーズの取得方法がわからない
```c
int PEM_write_RSAPrivateKey(FILE *fp, RSA *x,
                            const EVP_CIPHER *enc, //暗号化アルゴリズム
							unsigned char *kstr, int klen, //パスフレーズと長さ
							pem_password_cb *cb, void *u); //callback
PEM_write_RSAPrivateKey(seckey, key, EVP_des_ede3_cbc(), "12345678", 8, NULL, NULL);
PEM_write_RSAPrivateKey(seckey, key, EVP_des_ede3_cbc(), NULL, 0, pass_cb, "My Private Key");
下のような鍵が生成される
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,81CB5FEC487EB67F
Ux3JFs0KSohF1GYs0rJ2VtQ+s5pjXJVsjPktIh9PzU+XLTAEOWdyWR/3fE5+hgZK
......
EUPfsZzEF2no/EMs6aCj5AdpJQu0PgHPU4oZmjTW0LU=
-----END RSA PRIVATE KEY-----
```

```c
RSA *PEM_read_RSAPublicKey(FILE *fp, RSA **x, pem_password_cb *cb, void *u);
```

```c
int pass_cb(char *buf, int size, int rwflag, void *u);
{
        int len;
		char *tmp;
		/* We'd probably do something else if 'rwflag' is 1 */
		printf("Enter pass phrase for \"%s\"\n", u);
		/* get pass phrase, length 'len' into 'tmp' */
		tmp = "hello";
		len = strlen(tmp);
		if (len <= 0) return 0;
		/* if too long, truncate */
		if (len > size) len = size;
		memcpy(buf, tmp, len);
		return len;
}
key = PEM_read_RSAPrivateKey(fp, NULL, pass_cb, "My Private Key");
```
