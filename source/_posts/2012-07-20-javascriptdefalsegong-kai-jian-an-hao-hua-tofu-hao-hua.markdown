---
layout: post
title: "javascriptでの公開鍵暗号化と復号化"
date: 2012-07-20 02:57
comments: true
categories: ctypes
---
### javascriptで暗号化ができて、復号化ができないバッグを修正した
```
エラー:malformed UTF-8 character sequence at offset
```
cとjavascriptでの型変換がうまくいかないと思ったが、cの関数の戻り値がutf-8の文字コード以外に別のものが入っている。

### sourcecode
```js
Components.utils.import('resource://gre/modules/ctypes.jsm');
var r = ctypes.open('/Users/kaku/Programs/M2/ctypesrsa/components/libjsrsa.dylib');

var encrypt = r.declare('public_encrypt', ctypes.default_abi, ctypes.char.ptr, ctypes.char.ptr, ctypes.uint32_t, ctypes.char.ptr, ctypes.uint32_t);
var ciphertext = encrypt(pubkey, pubkey.length, 'abc', 'abc'.length).readString();
alert(ciphertext);

var decrypt = r.declare('private_decrypt', ctypes.default_abi, ctypes.unsigned_char.ptr, ctypes.unsigned_char.ptr, ctypes.uint32_t, ctypes.unsigned_char.ptr, ctypes.uint32_t);
var text = decrypt(pkey, pkey.length, ciphertext, ciphertext.length).readString();
alert(text);
```

```c
char* str_join(const char* a, const char* b)
{
        if(a == NULL && b == NULL)
                return NULL;

        size_t la = 0;
        if(a)
                la = strlen(a);

        size_t lb = 0;
        if(b)
                lb = strlen(b);

        char* p = malloc(la + lb + 1);
        memcpy(p, a, la);
        memcpy(p + la, b, lb + 1);
        return p;
}

char* public_encrypt(const char *pubkey, int key_len, const char *from, int from_len)
{
        BIO *key_mem = BIO_new_mem_buf(pubkey, key_len);
        RSA *key = PEM_read_bio_RSAPublicKey(key_mem, NULL, NULL, NULL);
        BIO_free(key_mem);

        BIO *plaintext_mem = BIO_new_mem_buf(from, from_len);

        BIO *ciphertext_mem = BIO_new(BIO_s_mem());
        BIO *bio_base64 = BIO_new(BIO_f_base64());
        BIO_set_flags(bio_base64, BIO_FLAGS_BASE64_NO_NL);
        BIO *bio_chain = BIO_push(bio_base64, ciphertext_mem);

        int rsa_size = RSA_size(key);
        char *inbuf = malloc(rsa_size);
        char *outbuf = malloc(rsa_size);
        while(1){
                int read_size = rsa_size - 11;
                int inlen = BIO_read(plaintext_mem, inbuf, read_size);
                if(inlen <= 0)
                        break;

                memset(outbuf, 0, rsa_size);

                int outlen;
                if((outlen = RSA_public_encrypt(inlen, inbuf, outbuf, key, RSA_PKCS1_PADDING)) == -1){
                        print_error("failed to RSA_public_encrypt", ERR_get_error());
                        exit(-1);
                }

                int len = BIO_write(bio_chain, outbuf, outlen);
                int abc = 1;
        }
        BIO_flush(bio_chain);
        free(inbuf);
        free(outbuf);
        BIO_free(plaintext_mem);
        RSA_free(key);

        char *ciphertext = NULL;
        while(1){
                int read_size = 512;
                char *buf = calloc(read_size + 1, sizeof(char));
                int len = BIO_read(ciphertext_mem, buf, read_size);
                if(len <= 0)
                        break;
                ciphertext = str_join(ciphertext, buf);
        }
        BIO_free_all(bio_chain);

        return ciphertext;
}

char* private_decrypt(const char *prikey, int key_len, const char *from, int from_len)
{
        BIO *key_mem = BIO_new(BIO_s_mem());
        int len = BIO_write(key_mem, prikey, key_len);
        RSA *key = PEM_read_bio_RSAPrivateKey(key_mem, NULL, NULL, NULL);
        BIO_free(key_mem);

        BIO *bio_base64 = BIO_new(BIO_f_base64());
        BIO_set_flags(bio_base64, BIO_FLAGS_BASE64_NO_NL);
        BIO *mem_buf = BIO_new_mem_buf(from, from_len);
        mem_buf = BIO_push(bio_base64, mem_buf);

        BIO *plaintext_mem = BIO_new(BIO_s_mem());
        int plaintext_len = 0;
        int rsa_size = RSA_size(key);

        char *inbuf = malloc(rsa_size);
        char *outbuf = malloc(rsa_size);
        while(1){
                int read_size = rsa_size;
                int inlen = BIO_read(mem_buf, inbuf, read_size);
                if(inlen <= 0)
                        break;

                memset(outbuf, 0, rsa_size);

                int outlen;
                if((outlen = RSA_private_decrypt(inlen, inbuf, outbuf, key, RSA_PKCS1_PADDING)) == -1){
                        print_error("failed to RSA_private_decrypt", ERR_get_error());
                        exit(-1);
                }
                int len = BIO_write(plaintext_mem, outbuf, outlen);
                plaintext_len += len;
        }
        BIO_flush(plaintext_mem);
        free(inbuf);
        free(outbuf);
        BIO_free_all(mem_buf);
        RSA_free(key);

        char *plaintext = NULL;
        while(1){
                int read_size = 512;
                char *buf = calloc(read_size + 1, sizeof(char));
                int len = BIO_read(plaintext_mem, buf, read_size);
                if(len <= 0)
                        break;
                plaintext = str_join(plaintext, buf);
        }
        BIO_free(plaintext_mem);

        return plaintext;
}
```

### これから
署名などの機能を実現する。
CSセミナの発表資料を準備する
