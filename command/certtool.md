# `certtool` コマンド

X.509 証明書を操作する。

## 自己署名 CA 証明書

### 秘密鍵の作成

暗号方式 ed25519 を指定して秘密鍵を作成する。

```sh
> certtool --generate-privkey --key-type=ed25519 --outfile=ca.key
Generating a 256 bit EdDSA (Ed25519) private key ...

> file ca.key
ca.key: ASCII text
```

### 自己署名 CA 証明書の作成

証明書の情報を作成する。他の証明書に署名するため `cert_signing_key` を指定する。

```sh
> cat > ca.cfg << EOF
cn = Home
ca
cert_signing_key
EOF
```

証明書を作成する。

```sh
> certtool --generate-self-signed --load-privkey=ca.key --template=ca.cfg --outfile=ca.pem
Generating a self signed certificate...
X.509 Certificate Information:
        Version: 3
        Serial Number (hex): 06ade38084b1d0b1bb0eea6705d0d3c89417b19c
        Validity:
                Not Before: Thu Aug 07 14:08:01 UTC 2025
                Not After: Fri Aug 07 14:08:01 UTC 2026
        Subject: CN=Home
        Subject Public Key Algorithm: EdDSA (Ed25519)
        Algorithm Security Level: High (256 bits)
                Curve:  Ed25519
                X:
                        ea:8a:85:7d:47:03:d5:22:f3:0f:47:bf:d5:33:07:48
                        f0:a4:0c:a0:2b:2e:20:16:c3:c8:d2:ba:0b:a0:3a:d6
        Extensions:
                Basic Constraints (critical):
                        Certificate Authority (CA): TRUE
                Key Usage (critical):
                        Certificate signing.
                Subject Key Identifier (not critical):
                        ef943464f9cbbd28b2f1093a0a3398bd0ce6452a
Other Information:
        Public Key ID:
                sha1:ef943464f9cbbd28b2f1093a0a3398bd0ce6452a
                sha256:21b31b8116c5a941fe84fa35c48ba09cbf6f95555a042da26cf19eb53a57a3f7
        Public Key PIN:
                pin-sha256:IbMbgRbFqUH+hPo1xIugnL9vlVVaBC2ibPGetTpXo/c=



Signing certificate...

> file ca.pem
ca.pem: PEM certificate
```

公開鍵の情報を表示する。

```sh
> certtool --pubkey-info --infile=ca.pem
Public Key Information:
        Public Key Algorithm: EdDSA (Ed25519)
        Algorithm Security Level: High (256 bits)
                Curve:  Ed25519
                X:
                        ea:8a:85:7d:47:03:d5:22:f3:0f:47:bf:d5:33:07:48
                        f0:a4:0c:a0:2b:2e:20:16:c3:c8:d2:ba:0b:a0:3a:d6

Public Key Usage:
        Certificate signing.
Public Key ID:
        sha1:ef943464f9cbbd28b2f1093a0a3398bd0ce6452a
        sha256:21b31b8116c5a941fe84fa35c48ba09cbf6f95555a042da26cf19eb53a57a3f7
Public Key PIN:
        pin-sha256:IbMbgRbFqUH+hPo1xIugnL9vlVVaBC2ibPGetTpXo/c=


-----BEGIN PUBLIC KEY-----
(base64-ed string)
-----END PUBLIC KEY-----
```

## 自己署名サーバ証明書

自己署名 CA 証明書で署名したサーバ証明書を作成する。

秘密鍵は [秘密鍵の作成](#秘密鍵の作成) と同じ手順で作成する。

### サーバ証明書

証明書の情報を作成する。
TLS サーバ(tls_www_server), 暗号化(encryption_key), 署名(signing_key) を指定する。

```sh
> cat > server.info << EOF
organization = Home
cn = $(hostname -s)
dns_name = $(hostname -s)
dns_name = $(hostname -f)
ip_address = $(nmcli -g ipv4.addresses c s enp7s0 | cut -d '/' -f 1)
tls_www_server
encryption_key
signing_key
EOF
```

サーバ証明書を作成する。

```sh
> certtool --generate-certificate \
    --load-privkey server.key \
    --load-ca-certificate ca.pem \
    --load-ca-privkey ca.key \
    --template server.info \
    --outfile server.pem
Generating a signed certificate...

Expiration time: Sat Aug  8 01:13:44 2026
CA expiration time: Fri Aug  7 23:08:01 2026
X.509 Certificate Information:
        Version: 3
        Serial Number (hex): 6a21e99b09aa02823f0385d3dc587e90ec281c38
        Validity:
                Not Before: Thu Aug 07 16:13:44 UTC 2025
                Not After: Fri Aug 07 16:13:44 UTC 2026
        Subject: CN=centos9,O=Home
        Subject Public Key Algorithm: EdDSA (Ed25519)
        Algorithm Security Level: High (256 bits)
                Curve:  Ed25519
                X:
                        87:1c:95:d4:16:ca:ca:eb:f6:fb:ff:25:57:4a:bd:db
                        cc:e8:82:21:91:4e:09:8e:4b:5d:97:06:27:9c:d1:c2
        Extensions:
                Basic Constraints (critical):
                        Certificate Authority (CA): FALSE
                Subject Alternative Name (not critical):
                        DNSname: centos9
                        DNSname: centos9.home.local
                        IPAddress: 172.16.1.2
                Key Purpose (not critical):
                        TLS WWW Server.
                Key Usage (critical):
                        Digital signature.
                Subject Key Identifier (not critical):
                        8e7dbf3830fb64a8744464a960c6172d7471ef2a
                Authority Key Identifier (not critical):
                        ef943464f9cbbd28b2f1093a0a3398bd0ce6452a
Other Information:
        Public Key ID:
                sha1:8e7dbf3830fb64a8744464a960c6172d7471ef2a
                sha256:4b6e72e6480ee3d9cf03e4539f98dce6ea3419710014d070ff41ecb55f82dbf4
        Public Key PIN:
                pin-sha256:S25y5kgO49nPA+RTn5jc5uo0GXEAFNBw/0HstV+C2/Q=



Signing certificate...

> file server.pem
server.pem: PEM certificate
```

公開鍵の情報を表示する。

```sh
> certtool --pubkey-info --infile=server.pem
Public Key Information:
        Public Key Algorithm: EdDSA (Ed25519)
        Algorithm Security Level: High (256 bits)
                Curve:  Ed25519
                X:
                        87:1c:95:d4:16:ca:ca:eb:f6:fb:ff:25:57:4a:bd:db
                        cc:e8:82:21:91:4e:09:8e:4b:5d:97:06:27:9c:d1:c2

Public Key Usage:
        Digital signature.
Public Key ID:
        sha1:8e7dbf3830fb64a8744464a960c6172d7471ef2a
        sha256:4b6e72e6480ee3d9cf03e4539f98dce6ea3419710014d070ff41ecb55f82dbf4
Public Key PIN:
        pin-sha256:S25y5kgO49nPA+RTn5jc5uo0GXEAFNBw/0HstV+C2/Q=


-----BEGIN PUBLIC KEY-----
(base64-ed string)
-----END PUBLIC KEY-----
```

## 自己署名クライアント証明書

自己署名 CA 証明書で署名したクライアント証明書を作成する。

秘密鍵は [秘密鍵の作成](#秘密鍵の作成) と同じ手順で作成する。

### クライアント証明書

証明書の情報を作成する。
TLS クライアント(tls_www_client), 暗号化(encryption_key), 署名(signing_key) を指定する。

```sh
> cat > client.info << EOF
country = A
state = B
locality = C
organization = Home
cn = $(hostname -s)
tls_www_client
encryption_key
signing_key
EOF
```

クライアント証明書を作成する。

```sh
> certtool --generate-certificate \
    --load-privkey client.key \
    --load-ca-certificate ca.pem \
    --load-ca-privkey ca.key \
    --template client.info \
    --outfile client.pem
Generating a signed certificate...

Expiration time: Sat Aug  8 01:38:21 2026
CA expiration time: Fri Aug  7 23:08:01 2026
X.509 Certificate Information:
        Version: 3
        Serial Number (hex): 7d7291be8aa626d6731660a81a6eb5e7ca6da167
        Validity:
                Not Before: Thu Aug 07 16:38:21 UTC 2025
                Not After: Fri Aug 07 16:38:21 UTC 2026
        Subject: CN=centos9,O=Home,L=C,ST=B,C=A
        Subject Public Key Algorithm: EdDSA (Ed25519)
        Algorithm Security Level: High (256 bits)
                Curve:  Ed25519
                X:
                        b6:42:bc:53:1a:6a:f7:ac:82:38:53:0c:b4:ab:11:99
                        bd:72:b6:89:4d:35:6b:8c:d5:b6:18:6c:80:3d:a9:cb
        Extensions:
                Basic Constraints (critical):
                        Certificate Authority (CA): FALSE
                Key Purpose (not critical):
                        TLS WWW Client.
                Key Usage (critical):
                        Digital signature.
                Subject Key Identifier (not critical):
                        37157ae28cdd12e48bea7bcee81963bca4bb6d6f
                Authority Key Identifier (not critical):
                        ef943464f9cbbd28b2f1093a0a3398bd0ce6452a
Other Information:
        Public Key ID:
                sha1:37157ae28cdd12e48bea7bcee81963bca4bb6d6f
                sha256:75114ed244122e435849cef5f9210aeaab3e2b0cf3bb83d7c68d80e25d4c80ac
        Public Key PIN:
                pin-sha256:dRFO0kQSLkNYSc71+SEK6qs+Kwzzu4PXxo2A4l1MgKw=



Signing certificate...

> file client.pem
client.pem: PEM certificate
```

公開鍵の情報を表示する。

```sh
> certtool --pubkey --infile=client.pem
Public Key Information:
        Public Key Algorithm: EdDSA (Ed25519)
        Algorithm Security Level: High (256 bits)
                Curve:  Ed25519
                X:
                        b6:42:bc:53:1a:6a:f7:ac:82:38:53:0c:b4:ab:11:99
                        bd:72:b6:89:4d:35:6b:8c:d5:b6:18:6c:80:3d:a9:cb

Public Key Usage:
        Digital signature.
Public Key ID:
        sha1:37157ae28cdd12e48bea7bcee81963bca4bb6d6f
        sha256:75114ed244122e435849cef5f9210aeaab3e2b0cf3bb83d7c68d80e25d4c80ac
Public Key PIN:
        pin-sha256:dRFO0kQSLkNYSc71+SEK6qs+Kwzzu4PXxo2A4l1MgKw=


-----BEGIN PUBLIC KEY-----
(base64-ed string)
-----END PUBLIC KEY-----
```

## フィンガープリント

フィンガープリントを表示する。

```sh
> certtool --fingerprint --infile=server.pem
915e493dcc3ee0ce2e5063a63d797c75fdf1759f
```

## サブジェクト鍵識別子

Subject Key Identifier を表示する。

```sh
> certtool --key-id --infile=server.pem
8e7dbf3830fb64a8744464a960c6172d7471ef2a
```

## 形式変換

### Distinguished Encoding Rules (DER)

PEM を DER に変換する。

```sh
> certtool --certificate-info --infile=server.pem --outder --outfile=server.der

> file server.der
server.der: Certificate, Version=3
```

### PKCS#12 (PFX)

PEM を PFX に変換する。

```sh
> certtool --load-privkey=server.key --load-certificate=server.pem --p12-name="$(hostname -s)" --to-p12 --outfile=server.pfx
Generating a PKCS #12 structure...
Loading private key list...
Loaded 1 private keys.
Enter password:
Confirm password:

> file server.pfx
server.pfx: ASCII text
```

証明書の情報を表示する。

```sh
> certtool --p12-info --infile=server.pfx
MAC info:
        MAC: SHA256 (2.16.840.1.101.3.4.2.1)
        Salt: 56f149d159179280
        Salt size: 8
        Iteration count: 600000

Enter password:
BAG #0
        Type: Encrypted
        Cipher: AES-128-CBC
        Schema: PBES2-AES128-CBC (2.16.840.1.101.3.4.1.2)
        Salt: 501d2e82d2f131b1c2257fdfa42e9bc3908996bb17
        Salt size: 21
        Iteration count: 600000

        Decrypting...
        Elements: 1
        Type: Certificate
        Friendly name: centos9
        Key ID: 8E:7D:BF:38:30:FB:64:A8:74:44:64:A9:60:C6:17:2D:74:71:EF:2A
-----BEGIN CERTIFICATE-----
(base64-ed string)
-----END CERTIFICATE-----

BAG #1
        Elements: 1
        Type: PKCS #8 Encrypted key
        PKCS #8 information:
                Cipher: AES-128-CBC
                Schema: PBES2-AES128-CBC (2.16.840.1.101.3.4.1.2)
                Salt: d5c7b8db4b42deb9e256b5b823d0
                Salt size: 14
                Iteration count: 600000

        Friendly name: centos9
        Key ID: 8E:7D:BF:38:30:FB:64:A8:74:44:64:A9:60:C6:17:2D:74:71:EF:2A
-----BEGIN ENCRYPTED PRIVATE KEY-----
(base64-ed string)
-----END ENCRYPTED PRIVATE KEY-----
```
