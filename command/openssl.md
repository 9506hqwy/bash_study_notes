# `openssl` コマンド

ダイジェスト, エンコードや X.509 証明書を操作する。

## ダイジェスト

サポートしている方式を確認する。

```sh
> openssl dgst --list
Supported digests:
-blake2b512                -blake2s256                -md4
-md5                       -md5-sha1                  -ripemd
-ripemd160                 -rmd160                    -sha1
-sha224                    -sha256                    -sha3-224
-sha3-256                  -sha3-384                  -sha3-512
-sha384                    -sha512                    -sha512-224
-sha512-256                -shake128                  -shake256
-sm3                       -ssl3-md5                  -ssl3-sha1
-whirlpool
```

### ハッシュ

標準入力の MD5 を計算する。

```sh
> echo a | md5sum
60b725f10c9c85c70d97880dfe8191b3  -

> echo a | openssl dgst -md5 -hex
MD5(stdin)= 60b725f10c9c85c70d97880dfe8191b3

# ":" 区切り
> echo a | openssl dgst -md5 -hex -c
MD5(stdin)= 60:b7:25:f1:0c:9c:85:c7:0d:97:88:0d:fe:81:91:b3

# "ハッシュ 入力" の形式
> echo a | openssl dgst -md5 -hex -r
60b725f10c9c85c70d97880dfe8191b3 *stdin
```

ファイルの SHA512 を計算する。

```sh
> openssl dgst -sha512 -binary <(echo a) | base64
FisLMvAkgtWsoKfJPdA86sOs1+QQpfGPP7mQ/JWK4N9vMiM7kYMer5nKWBqMTd+ci6MVrEgtttTq
Acx4hKY1vg==

# ファイルに出力
> openssl dgst -sha512 -binary -out stdin.sha512 <(echo a)
> base64 stdin.sha512
FisLMvAkgtWsoKfJPdA86sOs1+QQpfGPP7mQ/JWK4N9vMiM7kYMer5nKWBqMTd+ci6MVrEgtttTq
Acx4hKY1vg==
```

### HMAC

HMAC-SHA256 を計算する。

```sh
> echo a | sha256hmac -K b
b7b5d7c8dfe2ca679ae032dfb28e970a07f5f0a864fe3e2f609624fd55c9e56f  -

> echo a | openssl dgst -sha256 -hmac b -hex
SHA2-256(stdin)= b7b5d7c8dfe2ca679ae032dfb28e970a07f5f0a864fe3e2f609624fd55c9e56f
```

## エンコード

サポートしている方式を確認する。

```sh
> openssl enc -list
Supported ciphers:
-aes-128-cbc               -aes-128-cfb               -aes-128-cfb1
-aes-128-cfb8              -aes-128-ctr               -aes-128-ecb
-aes-128-ofb               -aes-192-cbc               -aes-192-cfb
-aes-192-cfb1              -aes-192-cfb8              -aes-192-ctr
-aes-192-ecb               -aes-192-ofb               -aes-256-cbc
-aes-256-cfb               -aes-256-cfb1              -aes-256-cfb8
-aes-256-ctr               -aes-256-ecb               -aes-256-ofb
-aes128                    -aes128-wrap               -aes128-wrap-pad
-aes192                    -aes192-wrap               -aes192-wrap-pad
-aes256                    -aes256-wrap               -aes256-wrap-pad
-aria-128-cbc              -aria-128-cfb              -aria-128-cfb1
-aria-128-cfb8             -aria-128-ctr              -aria-128-ecb
-aria-128-ofb              -aria-192-cbc              -aria-192-cfb
-aria-192-cfb1             -aria-192-cfb8             -aria-192-ctr
-aria-192-ecb              -aria-192-ofb              -aria-256-cbc
-aria-256-cfb              -aria-256-cfb1             -aria-256-cfb8
-aria-256-ctr              -aria-256-ecb              -aria-256-ofb
-aria128                   -aria192                   -aria256
-bf                        -bf-cbc                    -bf-cfb
-bf-ecb                    -bf-ofb                    -blowfish
-camellia-128-cbc          -camellia-128-cfb          -camellia-128-cfb1
-camellia-128-cfb8         -camellia-128-ctr          -camellia-128-ecb
-camellia-128-ofb          -camellia-192-cbc          -camellia-192-cfb
-camellia-192-cfb1         -camellia-192-cfb8         -camellia-192-ctr
-camellia-192-ecb          -camellia-192-ofb          -camellia-256-cbc
-camellia-256-cfb          -camellia-256-cfb1         -camellia-256-cfb8
-camellia-256-ctr          -camellia-256-ecb          -camellia-256-ofb
-camellia128               -camellia192               -camellia256
-cast                      -cast-cbc                  -cast5-cbc
-cast5-cfb                 -cast5-ecb                 -cast5-ofb
-chacha20                  -des                       -des-cbc
-des-cfb                   -des-cfb1                  -des-cfb8
-des-ecb                   -des-ede                   -des-ede-cbc
-des-ede-cfb               -des-ede-ecb               -des-ede-ofb
-des-ede3                  -des-ede3-cbc              -des-ede3-cfb
-des-ede3-cfb1             -des-ede3-cfb8             -des-ede3-ecb
-des-ede3-ofb              -des-ofb                   -des3
-des3-wrap                 -desx                      -desx-cbc
-id-aes128-wrap            -id-aes128-wrap-pad        -id-aes192-wrap
-id-aes192-wrap-pad        -id-aes256-wrap            -id-aes256-wrap-pad
-id-smime-alg-CMS3DESwrap  -idea                      -idea-cbc
-idea-cfb                  -idea-ecb                  -idea-ofb
-rc2                       -rc2-128                   -rc2-40
-rc2-40-cbc                -rc2-64                    -rc2-64-cbc
-rc2-cbc                   -rc2-cfb                   -rc2-ecb
-rc2-ofb                   -rc4                       -rc4-40
-rc5-cbc                   -rc5-cfb                   -rc5-ecb
-rc5-ofb                   -seed                      -seed-cbc
-seed-cfb                  -seed-ecb                  -seed-ofb
```

### Base64

Base64 でエンコードする。

```sh
> echo a | openssl enc -e -base64 | base64 -d
a
```

Base64 でデコードする。

```sh
> echo YQo= | openssl enc -d -base64 | base64
YQo=
```

### AES

AES で暗号化する。IV と共通鍵はパスフレーズから生成される。

`-pass` の指定方法は `man  openssl-passphrase-options` を確認する。

```sh
> echo a | openssl enc -e -aes256 -pass pass:b -pbkdf2 | base64
U2FsdGVkX1+zdNwKNXky+MsbEfFcyX0iy2wD46+TLQ8=

# iv と key を表示する。
> echo a | openssl enc -e -aes256 -pass pass:b -pbkdf2 -p -base64
salt=446CA7718358595F
key=DEA596BDA319080E57298CDFEC23489DEA1E66D08533C88925D42878474B7760
iv =3BDE36B0B81E4E2F7902D304CDA6B85C
U2FsdGVkX19EbKdxg1hZXzbLK5O+AP1B3hNWdTCJwsQ=
```

SALT が埋め込まれる。

```sh
> echo U2FsdGVkX1+zdNwKNXky+MsbEfFcyX0iy2wD46+TLQ8= | base64 -d | hexdump -C
00000000  53 61 6c 74 65 64 5f 5f  b3 74 dc 0a 35 79 32 f8  |Salted__.t..5y2.|
00000010  cb 1b 11 f1 5c c9 7d 22  cb 6c 03 e3 af 93 2d 0f  |....\.}".l....-.|
00000020
```

AES で復号化する。

```sh
> echo U2FsdGVkX1+zdNwKNXky+MsbEfFcyX0iy2wD46+TLQ8= | base64 -d | openssl enc -d -aes256 -pass pass:b -pbkdf2
a
```

IV と共通鍵を指定して AES で暗号化する。

```sh
> echo a | openssl enc -e -aes256 -K $(openssl rand -hex 32) -iv $(openssl rand -hex 16) -p -base64
salt=10B4B3BC39560000
key=363B4402459936A6357B7DAFDB523FC51CEE4444E272C3A9D100896C71D444ED
iv =2277C9FA5B703525A59E83E3C8815F44
j6nhEvjdUOeIj9meLoH3fQ==
```

SALT が埋め込まれない。

```sh
> echo j6nhEvjdUOeIj9meLoH3fQ== | base64 -d | hexdump -C
00000000  8f a9 e1 12 f8 dd 50 e7  88 8f d9 9e 2e 81 f7 7d  |......P........}|
00000010
```

IV と共通鍵を指定して AES で復号化する。

```sh
> echo j6nhEvjdUOeIj9meLoH3fQ== | base64 -d | openssl enc -d -aes256 \
    -K 363B4402459936A6357B7DAFDB523FC51CEE4444E272C3A9D100896C71D444ED \
    -iv 2277C9FA5B703525A59E83E3C8815F44
a
```

### RSA

公開鍵方式を使用してデータ暗号化を行うための鍵を作成する。

秘密鍵を作成する。

```sh
> openssl genrsa -out rsa.key

> file rsa.key
rsa.key: ASCII text
```

公開鍵を作成する。

```sh
> openssl rsa -in rsa.key -out rsa.pubkey --outform PEM -pubout
writing RSA key

> file rsa.pubkey
rsa.pubkey: ASCII text
```

公開鍵を使用して暗号化する。

```sh
> echo a | openssl pkeyutl -encrypt -pubin -inkey rsa.pubkey -out data.out

> file data.out
data.out: data
```

秘密鍵を使用して復号化する。

```sh
> openssl pkeyutl -decrypt -in data.out -inkey rsa.key
a
```

### 署名

秘密鍵を使用して署名する。

```sh
> echo a | openssl pkeyutl -sign -inkey rsa.key -out data.out

> file data.out
data.out: PGP Secret Sub-key -
```

秘密鍵を使用してデータを取り出す。

```sh
> openssl pkeyutl -verifyrecover -in data.out -inkey rsa.key
a
```

公開鍵を使用して署名を検証する。

```sh
> echo a | openssl pkeyutl -verify -sigfile data.out -pubin -inkey rsa.pubkey
Signature Verified Successfully
```

## 証明書

サポートしている公開鍵暗号の方式を確認する。

```sh
> openssl list -public-key-algorithms | head -n 10
Legacy:
 Name: OpenSSL RSA method
        Type: Builtin Algorithm
        OID: rsaEncryption
        PEM string: RSA
 Name: rsa
        Alias for: rsaEncryption
 Name: OpenSSL PKCS#3 DH method
        Type: Builtin Algorithm
        OID: dhKeyAgreement
```

### 自己署名 CA 証明書

#### 秘密鍵の作成

暗号方式 ed25519 を指定して秘密鍵を作成する。

```sh
> openssl genpkey -algorithm ED25519 -out ca.key --outform PEM

> file ca.key
ca.key: ASCII text
```

#### 公開鍵の作成

公開鍵を作成する (証明書の作成には使用しない)。

```sh
> openssl pkey -in ca.key -out ca.pubkey --outform PEM -pubout

> file ca.pubkey
ca.pem: ASCII text
```

#### 自己署名 CA 証明書の作成

証明書の情報を作成する。他の証明書に署名するため `keyCertSign` を指定する。

```sh
> cat > ca.cfg << EOF
[ v3_ca ]
subjectKeyIdentifier = hash
basicConstraints = critical,CA:true
keyUsage = critical,keyCertSign
EOF
```

証明書を作成する。

```sh
> openssl x509 -new -key ca.key -out ca.pem -set_subject "/CN=Home" -extfile ca.cfg  -extensions v3_ca

> file ca.pem
ca.pem: PEM certificate
```

```{note}
下記のコマンドと同じ？

> openssl req -x509 -new -key ca.key -out ca.pem -subj "/CN=Home" -config ca.cfg  -extensions v3_ca
```

証明書を確認する。

```sh
> openssl x509 -in ca.pem -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            67:ff:b2:48:c3:fe:8d:c9:fd:6c:7b:b6:37:f6:a0:17:04:fb:cb:13
        Signature Algorithm: ED25519
        Issuer: CN=Home
        Validity
            Not Before: Aug 21 08:49:03 2025 GMT
            Not After : Sep 20 08:49:03 2025 GMT
        Subject: CN=Home
        Subject Public Key Info:
            Public Key Algorithm: ED25519
                ED25519 Public-Key:
                pub:
                    92:5b:83:b9:52:38:59:1d:9f:c6:c1:00:50:54:4c:
                    3b:cd:43:d6:2f:d2:be:6f:f1:b8:46:02:c1:00:de:
                    7b:49
        X509v3 extensions:
            X509v3 Subject Key Identifier:
                92:15:BC:D8:7E:B6:80:C4:C9:1D:71:A3:4D:38:87:C3:AC:CD:93:38
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Key Usage: critical
                Certificate Sign
    Signature Algorithm: ED25519
    Signature Value:
        9e:20:9a:8a:1a:24:8b:41:70:6f:5f:63:fc:18:80:12:a7:9b:
        07:59:b8:80:8b:08:06:5f:c7:c5:fd:c6:be:d2:a0:45:2d:e1:
        fe:64:c5:a0:71:a3:fb:6b:c3:f1:7f:fb:c8:8b:a0:3d:bb:12:
        d9:ee:5e:b3:65:d2:35:ca:bc:08
-----BEGIN CERTIFICATE-----
(base64-ed string)
-----END CERTIFICATE-----
```

公開鍵を確認する。

```sh
> openssl x509 -in ca.pem -pubkey -nocert
-----BEGIN PUBLIC KEY-----
(base64-ed string)
-----END PUBLIC KEY-----
```

### 自己署名サーバ証明書

自己署名 CA 証明書で署名したサーバ証明書を作成する。

秘密鍵は [秘密鍵の作成](#秘密鍵の作成) と同じ手順で作成する。

#### サーバ証明書

証明書の情報を作成する。
TLS サーバ(serverAuth), 署名(digitalSignature) を指定する ([RFC 5280](https://www.rfc-editor.org/rfc/rfc5280.txt))。

```sh
> cat > server.info << EOF
[ usr_cert ]
basicConstraints=critical,CA:FALSE
keyUsage = critical,digitalSignature
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer
subjectAltName=DNS:$(hostname -s),DNS:$(hostname -f),IP:$(nmcli -g ipv4.addresses c s enp7s0 | cut -d '/' -f 1)
extendedKeyUsage = serverAuth
EOF
```

署名リクエストを作成する。

```sh
> openssl req -new -subj "/CN=$(hostname -s)/O=Home" -key server.key -out server.csr

> file server.csr
server.csr: PEM certificate request
```

サーバ証明書を作成する。

```sh
> openssl x509 -req -in server.csr -out server.pem -extfile server.info -extensions usr_cert -CA ca.pem -CAkey ca.key
Certificate request self-signature ok
subject=CN=centos9, O=Home

> file server.pem
server.pem: PEM certificate
```

```{note}
openssl req -x509 や openssl x509 コマンドで CSR を作成せずに作成できる？
```

証明書を確認する。

```sh
> openssl x509 -in server.pem -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            2d:ba:78:2d:dd:bb:6d:ca:78:6b:5a:2a:a5:9c:7e:e3:27:3d:d4:7b
        Signature Algorithm: ED25519
        Issuer: CN=Home
        Validity
            Not Before: Aug 21 10:38:39 2025 GMT
            Not After : Sep 20 10:38:39 2025 GMT
        Subject: CN=centos9, O=Home
        Subject Public Key Info:
            Public Key Algorithm: ED25519
                ED25519 Public-Key:
                pub:
                    6d:50:6b:e5:25:51:e7:03:d3:24:c6:c2:70:5b:db:
                    d0:a9:c4:7a:99:56:37:0a:37:b2:74:6f:60:55:6d:
                    a5:29
        X509v3 extensions:
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Key Usage: critical
                Digital Signature
            X509v3 Subject Key Identifier:
                3B:9F:EB:7B:34:A1:D8:E3:04:F2:F4:F4:6F:44:50:EC:2F:3E:30:E7
            X509v3 Authority Key Identifier:
                92:15:BC:D8:7E:B6:80:C4:C9:1D:71:A3:4D:38:87:C3:AC:CD:93:38
            X509v3 Subject Alternative Name:
                DNS:centos9, DNS:centos9.home.local, IP Address:172.16.1.2
            X509v3 Extended Key Usage:
                TLS Web Server Authentication
    Signature Algorithm: ED25519
    Signature Value:
        b7:91:ca:26:25:c1:07:44:08:53:e6:af:c5:41:8b:37:7d:e3:
        3c:25:df:f2:ef:a7:3a:ee:e2:49:6f:e9:bf:9f:ab:f6:e9:5f:
        fd:8a:3e:fb:0c:b1:62:67:5b:5f:63:76:25:13:e9:e9:a0:bd:
        31:03:6d:b6:15:e5:c0:07:5f:07
-----BEGIN CERTIFICATE-----
(base64-ed string)
-----END CERTIFICATE-----
```

公開鍵の情報を表示する。

```sh
> openssl x509 -in server.pem -pubkey -nocert
-----BEGIN PUBLIC KEY-----
(base64-ed string)
-----END PUBLIC KEY-----
```

### 自己署名クライアント証明書

自己署名 CA 証明書で署名したクライアント証明書を作成する。

秘密鍵は [秘密鍵の作成](#秘密鍵の作成) と同じ手順で作成する。

#### クライアント証明書

証明書の情報を作成する。
TLS クライアント(clientAuth), 署名(digitalSignature) を指定する。

```sh
> cat > client.info << EOF
[ usr_cert ]
basicConstraints=critical,CA:FALSE
keyUsage = critical,digitalSignature
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer
extendedKeyUsage = clientAuth
EOF
```

署名リクエストを作成する。

```sh
> openssl req -new -subj "/CN=$(hostname -s)/O=Home/L=C/ST=B/C=JP" -key client.key -out client.csr

> file client.csr
client.csr: PEM certificate request
```

クライアント証明書を作成する。

```sh
> openssl x509 -req -in client.csr -out client.pem -extfile client.info -extensions usr_cert -CA ca.pem -CAkey ca.key
Certificate request self-signature ok
subject=CN=centos9, O=Home, L=C, ST=B, C=JP

> file client.pem
client.pem: PEM certificate
```

```{note}
openssl req -x509 や openssl x509 コマンドで CSR を作成せずに作成できる？
```

証明書を確認する。

```sh
> openssl x509 -in client.pem -text
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            7b:72:14:7f:c8:81:b0:d8:dc:7b:e2:38:6b:a2:39:fa:db:ce:3e:5d
        Signature Algorithm: ED25519
        Issuer: CN=Home
        Validity
            Not Before: Aug 21 10:55:13 2025 GMT
            Not After : Sep 20 10:55:13 2025 GMT
        Subject: CN=centos9, O=Home, L=C, ST=B, C=JP
        Subject Public Key Info:
            Public Key Algorithm: ED25519
                ED25519 Public-Key:
                pub:
                    44:00:2c:f1:e7:6d:47:7f:56:8a:5f:4f:70:72:48:
                    6f:47:93:97:1b:cb:d0:20:de:da:37:84:72:46:e9:
                    7f:ed
        X509v3 extensions:
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Key Usage: critical
                Digital Signature
            X509v3 Subject Key Identifier:
                BA:5E:11:DB:09:AF:F8:94:20:94:E0:A9:0A:61:AB:DE:27:8E:23:7C
            X509v3 Authority Key Identifier:
                92:15:BC:D8:7E:B6:80:C4:C9:1D:71:A3:4D:38:87:C3:AC:CD:93:38
            X509v3 Extended Key Usage:
                TLS Web Client Authentication
    Signature Algorithm: ED25519
    Signature Value:
        6b:cc:7b:0d:c5:70:0c:98:83:3a:e5:c0:dd:50:16:98:91:03:
        18:e1:ea:ce:bc:bb:70:f4:62:35:82:68:6d:17:b4:94:27:ce:
        02:e1:84:01:bc:4e:57:30:6f:9f:a7:39:fa:9f:00:6a:7c:e8:
        79:58:e3:68:ac:f3:68:61:32:0c
-----BEGIN CERTIFICATE-----
(base64-ed string)
-----END CERTIFICATE-----
```

公開鍵の情報を表示する。

```sh
> openssl x509 -in client.pem -pubkey -nocert
-----BEGIN PUBLIC KEY-----
(base64-ed string)
-----END PUBLIC KEY-----
```

### フィンガープリント

フィンガープリントを表示する。

```sh
> openssl x509 -in server.pem -fingerprint -nocert
SHA1 Fingerprint=10:E4:55:13:63:B7:A4:FD:30:5A:BE:FE:05:CC:DA:4D:E5:BC:E2:F9
```

### サブジェクト鍵識別子

Subject Key Identifier を表示する。

```sh
> openssl x509 -in server.pem -ext subjectKeyIdentifier -nocert
X509v3 Subject Key Identifier:
    3B:9F:EB:7B:34:A1:D8:E3:04:F2:F4:F4:6F:44:50:EC:2F:3E:30:E7
```

### 形式変換

#### Distinguished Encoding Rules (DER)

PEM を DER に変換する。

```sh
> openssl x509 -in server.pem -outform DER -out server.der

> file server.der
server.der: Certificate, Version=3
```

#### PKCS#12 (PFX)

PEM を PFX に変換する。

```sh
> openssl pkcs12 -export -in server.pem -inkey server.key -out server.pfx -name "$(hostname -s)"
Enter Export Password:
Verifying - Enter Export Password:

> file server.pfx
server.pfx: data
```

証明書の情報を表示する。

```sh
> openssl pkcs12 --info -in server.pfx
Enter Import Password:
MAC: sha256, Iteration 2048
MAC length: 32, salt length: 8
PKCS7 Encrypted data: PBES2, PBKDF2, AES-256-CBC, Iteration 2048, PRF hmacWithSHA256
Certificate bag
Bag Attributes
    friendlyName: centos9
    localKeyID: 10 E4 55 13 63 B7 A4 FD 30 5A BE FE 05 CC DA 4D E5 BC E2 F9
subject=CN=centos9, O=Home
issuer=CN=Home
-----BEGIN CERTIFICATE-----
(base64-ed string)
-----END CERTIFICATE-----
PKCS7 Data
Shrouded Keybag: PBES2, PBKDF2, AES-256-CBC, Iteration 2048, PRF hmacWithSHA256
Bag Attributes
    friendlyName: centos9
    localKeyID: 10 E4 55 13 63 B7 A4 FD 30 5A BE FE 05 CC DA 4D E5 BC E2 F9
Key Attributes: <No Attributes>
Enter PEM pass phrase:
Verifying - Enter PEM pass phrase:
-----BEGIN ENCRYPTED PRIVATE KEY-----
(base64-ed string)
-----END ENCRYPTED PRIVATE KEY-----
```

### 証明書の検証

自己署名 CA 証明書で署名した証明書を検証する。

```sh
> openssl verify -CAfile ca.pem server.pem client.pem
server.pem: OK
client.pem: OK
```

### 接続確認

TLS の接続を確認する。

nginx にサーバ証明書を設定する。

```sh
> cat > /etc/nginx/conf.d/server.conf <<EOF
server {
    listen              443 ssl;
    ssl_certificate     /etc/nginx/ssl/server.pem;
    ssl_certificate_key /etc/nginx/ssl/server.key;
}
EOF
```

接続確認する。

```sh
> openssl s_client -connect 127.0.0.1:443 < /dev/null
Connecting to 127.0.0.1
CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 CN=centos9, O=Home
verify error:num=20:unable to get local issuer certificate
verify return:1
depth=0 CN=centos9, O=Home
verify error:num=21:unable to verify the first certificate
verify return:1
depth=0 CN=centos9, O=Home
verify return:1
---
Certificate chain
 0 s:CN=centos9, O=Home
   i:CN=Home
   a:PKEY: ED25519, 256 (bit); sigalg: ED25519
   v:NotBefore: Aug 21 11:34:53 2025 GMT; NotAfter: Sep 20 11:34:53 2025 GMT
---
Server certificate
-----BEGIN CERTIFICATE-----
(base64-ed string)
-----END CERTIFICATE-----
subject=CN=centos9, O=Home
issuer=CN=Home
---
No client certificate CA names sent
Peer signature type: ed25519
Peer Temp Key: X25519, 253 bits
---
SSL handshake has read 775 bytes and written 385 bytes
Verification error: unable to verify the first certificate
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Protocol: TLSv1.3
Server public key is 256 bit
This TLS version forbids renegotiation.
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 21 (unable to verify the first certificate)
---
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384

    ...
---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384

    ...
---
read R BLOCK
DONE
```

## その他

### ランダム値

12 バイトのランダムな値を生成する。

```sh
> openssl rand -hex 12
21bcedaa945f50fd8e70d0c9
```

### パスワードハッシュ

SHA256 でハッシュ化したパスワードを取得する。

prefix にハッシュ方式 `$5$` が付く。

```sh
> openssl passwd -5 Password
$5$VEH0Ku9/ZPzq7czQ$G6sjYmVi2AP7fVPVKbFGfapjMZ.K9i.wm3HbUq.m6f0
```

## 参考

- [openssl.cnf](https://docs.openssl.org/3.4/man5/config/)
- [x509v3_config](https://docs.openssl.org/3.4/man5/x509v3_config/)
