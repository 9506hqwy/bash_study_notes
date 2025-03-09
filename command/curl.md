# `curl` コマンド

ネットワーク接続する。

サポートしているプロトコルは以下で確認できる。

```sh
> curl --version
curl 8.9.1 (x86_64-redhat-linux-gnu) libcurl/8.9.1 OpenSSL/3.2.2 zlib/1.3.1.zlib-ng brotli/1.1.0 libidn2/2.3.7 libpsl/0.21.5 libssh/0.11.1/openssl/zlib nghttp2/1.64.0 OpenLDAP/2.6.8
Release-Date: 2024-07-31
Protocols: dict file ftp ftps gopher gophers http https imap imaps ipfs ipns ldap ldaps mqtt pop3 pop3s rtsp scp sftp smb smbs smtp smtps telnet tftp ws wss
Features: alt-svc AsynchDNS brotli GSS-API HSTS HTTP2 HTTPS-proxy IDN IPv6 Kerberos Largefile libz NTLM PSL SPNEGO SSL threadsafe TLS-SRP UnixSockets
```

既定では進捗率(`-#` オプション)とリクエストボディが出力される。

```sh
> curl http://127.0.0.1:8000/get
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/get"
}
```

`-s` オプションを指定することで進捗率やエラーを非表示にする。
`-s` と同時に `-S` オプションを指定することでエラーを表示する。

## メソッド

HTTP メソッドは `-X` オプションで指定する。
既定は `HTTP GET` が実行される。

```sh
> curl -i http://127.0.0.1:8000/get
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:26:15 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 187
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/get"
}
```

### HEAD

`HTTP HEAD` を実行するには `-I` オプションを使用する。

```sh
> curl -I http://127.0.0.1:8000/get
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:54:44 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 187
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

## 認証

### Basic

Basic 認証のユーザ名とバスワードを指定する。
認証方法を Basic に制限するには `--basic` のみ指定する。

```sh
> curl -u user:password -i http://127.0.0.1:8000/basic-auth/user/password
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:32:42 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 47
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "authenticated": true,
  "user": "user"
}
```

認証が失敗する場合は HTTP401 が発生する。

```sh
> curl -u user:password -i http://127.0.0.1:8000/basic-auth/unknown/password
HTTP/1.1 401 UNAUTHORIZED
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:34:06 GMT
Connection: keep-alive
WWW-Authenticate: Basic realm="Fake Realm"
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
Content-Length: 0
```

### Digest

Digest 認証のユーザ名とバスワードを指定する。

```sh
> curl --digest -u user:password -i http://127.0.0.1:8000/digest-auth/1/user/password
HTTP/1.1 401 UNAUTHORIZED
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:38:26 GMT
Connection: keep-alive
Content-Type: text/html; charset=utf-8
Content-Length: 0
WWW-Authenticate: Digest realm="me@kennethreitz.com", nonce="1c5b3efd88eb94d4da9ba75a4afb04d1", qop="auth, auth-int", opaque="b11e4bc3573a085cd6421d070cfa684a", algorithm=MD5, stale=FALSE
Set-Cookie: stale_after=never; Path=/
Set-Cookie: fake=fake_value; Path=/
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:38:26 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 47
Set-Cookie: fake=fake_value; Path=/
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "authenticated": true,
  "user": "user"
}
```

認証が失敗する場合は HTTP401 が発生する。

```sh
> curl --digest -u user:password -i http://127.0.0.1:8000/digest-auth/1/user/unknown
HTTP/1.1 401 UNAUTHORIZED
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:42:07 GMT
Connection: keep-alive
Content-Type: text/html; charset=utf-8
Content-Length: 0
WWW-Authenticate: Digest realm="me@kennethreitz.com", nonce="85783496f73bf5433f34bf01342ebc7d", qop="auth, auth-int", opaque="8e69151bc1ca2ae902e8f3e9a8960916", algorithm=MD5, stale=FALSE
Set-Cookie: stale_after=never; Path=/
Set-Cookie: fake=fake_value; Path=/
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

HTTP/1.1 401 UNAUTHORIZED
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:42:07 GMT
Connection: keep-alive
Content-Type: text/html; charset=utf-8
Content-Length: 0
WWW-Authenticate: Digest realm="me@kennethreitz.com", nonce="4ae37b7fa7a23b263d0306d4c7bb4051", qop="auth, auth-int", opaque="eda7493422f14ae586fc0e022da8c670", algorithm=MD5, stale=FALSE
Set-Cookie: stale_after=never; Path=/
Set-Cookie: last_nonce=85783496f73bf5433f34bf01342ebc7d; Path=/
Set-Cookie: fake=fake_value; Path=/
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

### Bearer

Bearer 認証のトークンを指定する。

```sh
> curl --oauth2-bearer token -i http://127.0.0.1:8000/bearer
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:44:40 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 49
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "authenticated": true,
  "token": "token"
}
```

認証が失敗する場合は HTTP401 が発生する。

```sh
> curl -i http://127.0.0.1:8000/bearer
HTTP/1.1 401 UNAUTHORIZED
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 00:44:50 GMT
Connection: keep-alive
Content-Type: text/html; charset=utf-8
Content-Length: 0
WWW-Authenticate: Bearer
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
```

### *netrc* ファイル

netrc コマンドから情報を読み取って処理する。
`^^netrc` / `--netrc-file` オプションで指定する。

```sh
> cat > ~/text.netrc <<EOF
machine 127.0.0.1
login user
password password
EOF

> curl --netrc-file ~/text.netrc -i http://127.0.0.1:8000/basic-auth/user/password
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:53:52 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 47
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "authenticated": true,
  "user": "user"
}
```

## SSL / TLS

`-k` オプションでサーバ証明書の検証をスキップする。

## プロキシ

プロキシは環境変数 `HTTP_PROXY` / `HTTPS_PROXY` / `NO_PROXY` の設定に従う。

`--noproxy` オプションで環境変数 `NO_PROXY` の設定を上書きする。

下記をカンマ区切りで複数指定できる。

- `*`
- ドメイン名
- IP アドレス
- ネットワーク (192.168.1.0/24 など)

## ヘッダ

`-H` オプションで HTTP リクエストヘッダを追加する。

```sh
> curl -H "x-a: 1" -i http://127.0.0.1:8000/get
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:31:28 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 204
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1",
    "X-A": "1"
  },
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/get"
}
```

## クエリ

`--url-query` オプションでクエリを指定する。

```sh
> curl --url-query a=1 --url-query b=2 -i http://127.0.0.1:8000/get
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 03:47:07 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 226
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {
    "a": "1",
    "b": "2"
  },
  "headers": {
    "Accept": "*/*",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/get?a=1&b=2"
}
```

クエリは以下の形式で指定ができる。

- name=content
- =encodethis
- name@filename
- @fileonly
- +encoded-string

`--url-qurey` オプションをサポートしていない場合は `--get` オプションを使用して代用できる。
`-X GET` では機能しない。

```sh
> curl -d a=1 -d b=2 --get -i http://127.0.0.1:8000/get
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 04:19:29 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 226
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {
    "a": "1",
    "b": "2"
  },
  "headers": {
    "Accept": "*/*",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/get?a=1&b=2"
}
```

## ボディ

`-d` / `--data` オプションで指定する。
HTTP メソッドは自動的に `HTTP POST` になり、`Content-Type` は `application/x-www-form-urlencoded` となる。

```sh
> curl -d a=1 -d b=2 -i http://127.0.0.1:8000/post
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:01:48 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 368
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "data": "",
  "files": {},
  "form": {
    "a": "1",
    "b": "2"
  },
  "headers": {
    "Accept": "*/*",
    "Content-Length": "7",
    "Content-Type": "application/x-www-form-urlencoded",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "json": null,
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/post"
}
```

他に `--data-binary` / `--data-raw` / `--data-urlencode` がある。

データは以下の形式で指定ができる。

- content
- =content
- name=content
- @filename
- name@filename

### HTTP Form

`-F` オプションで `multipart/form-data` となる。

```sh
> curl -F a=1 -F b=2 -i http://127.0.0.1:8000/post
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:26:32 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 413
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "data": "",
  "files": {},
  "form": {
    "a": "1",
    "b": "2"
  },
  "headers": {
    "Accept": "*/*",
    "Content-Length": "246",
    "Content-Type": "multipart/form-data; boundary=------------------------DcQWDNLTyCGvMVVWFgjOwS",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "json": null,
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/post"
}
```

### JSON

`--json` オプションで `application/json` となる。`ACCEPT` ヘッダも `application/json` となる。

```sh
> curl --json '{"a": 1}' -i http://127.0.0.1:8000/post
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:35:55 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 355
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "data": "{\"a\": 1}",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "application/json",
    "Content-Length": "8",
    "Content-Type": "application/json",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "json": {
    "a": 1
  },
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/post"
}
```

### ファイルのアップロード

`-T` オプションでファイルをアップロードできる。
HTTP メソッドは  `HTTP PUT` となり、`Content-Type` は指定されない。

```sh
> cat > a.txt <<EOF
Hello, world!
EOF

> curl -T a.txt -i http://127.0.0.1:8000/anything/
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 03:03:44 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 325
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "data": "Hello, world!\n",
  "files": {},
  "form": {},
  "headers": {
    "Accept": "*/*",
    "Content-Length": "14",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "json": null,
  "method": "PUT",
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/anything/a.txt"
}
```

指定した URL の最後が "/" の場合はファイル名が自動的につく。

## ステータスコード

### リダイレクト (300)

`-L` オプションで自動的にリダイレクトする。

```sh
> curl -i http://127.0.0.1:8000/status/302
HTTP/1.1 302 FOUND
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:40:18 GMT
Connection: keep-alive
location: /redirect/1
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
Content-Length: 0
```

リダイレクトは複数回あっても機能する。
`--max-redirs` オプションで制限できる。

```sh
> curl -L -i http://127.0.0.1:8000/status/302
HTTP/1.1 302 FOUND
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:40:26 GMT
Connection: keep-alive
location: /redirect/1
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
Content-Length: 0

HTTP/1.1 302 FOUND
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:40:26 GMT
Connection: keep-alive
Content-Type: text/html; charset=utf-8
Content-Length: 215
Location: /get
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:40:26 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 187
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/get"
}
```

HTTP 301, 302, 303 の場合は HTTP メソッドを `HTTP GET` に変更する。
`--port301` / `--port302` / `--port303` で制限できる。

### エラー (400, 500)

既定では HTTP ステータスコードが 400 以上でも curl コマンドは正常終了する。

```sh
> curl -i http://127.0.0.1:8000/status/400
HTTP/1.1 400 BAD REQUEST
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:21:26 GMT
Connection: keep-alive
Content-Type: text/html; charset=utf-8
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
Content-Length: 0

> echo $?
0
```

`-f` オプションを指定することで curl コマンドを異常終了させる。

```sh
> curl -f -i http://127.0.0.1:8000/status/400
HTTP/1.1 400 BAD REQUEST
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:21:39 GMT
Connection: keep-alive
Content-Type: text/html; charset=utf-8
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
Content-Length: 0

curl: (22) The requested URL returned error: 400

> echo $?
22
```

## HTTP ヘッダの表示

`-D` で HTTP ヘッダを出力先を指定する。
`-` を指定することで標準出力に出力する。標準出力を指定すると `-i` と同じ。

```sh
# curl -D - http://127.0.0.1:8000/get
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 01:15:41 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 187
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/get"
}
```

## 名前解決

`--connect-to` オプションで接続先を指定する。
ホストやポートを省略した場合は同じ値が使用される。

```sh
> curl --connect-to localsrv:80:127.0.0.1:8000 -i http://localsrv/get
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 02:34:14 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 175
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "localsrv",
    "User-Agent": "curl/8.9.1"
  },
  "origin": "10.88.0.1",
  "url": "http://localsrv/get"
}
```

`--resolve` オプションの場合は接続先のポート番号は指定できない。

```sh
> curl --resolve localsrv:8000:127.0.0.1 -i http://localsrv:8000/get
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 02:36:58 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 185
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "localsrv:8000",
    "User-Agent": "curl/8.9.1"
  },
  "origin": "10.88.0.1",
  "url": "http://localsrv:8000/get"
}
```

## 出力

`-o` オプションで出力先のファイル名を指定する。
出力を抑制にするには `/dev/null` (Linux), `nil` (Windows) を指定する。

`--output-dir` オプションで出力先のディレクトリを指定する。

`-O` オプションで URL のファイル名を使用する。

`-J` オプションでレスポンスヘッダの `Content-Disposition` で指定されたファイル名を使用する。

`--clobber` オプションで出力先に指定したファイルを上書きする。

`--stderr` オプションで標準エラーをファイルにリダイレクトする。

ファイル名を指定する箇所で `-` を指定すると標準入力または標準出力になり、`%` を指定すると標準エラーとなる。

### 追加情報

`-w` オプションで形式を指定して情報を出力することができる。
指定できるオプションはヘルプを参照する。

```sh
> curl -s -w '%{json}\n' -o /dev/null http://127.0.0.1:8000/get | jq
{
  "certs": "",
  "conn_id": 0,
  "content_type": "application/json",
  "errormsg": null,
  "exitcode": 0,
  "filename_effective": "/dev/null",
  "ftp_entry_path": null,
  "http_code": 200,
  "http_connect": 0,
  "http_version": "1.1",
  "local_ip": "127.0.0.1",
  "local_port": 60716,
  "method": "GET",
  "num_certs": 0,
  "num_connects": 1,
  "num_headers": 7,
  "num_redirects": 0,
  "num_retries": 0,
  "proxy_ssl_verify_result": 0,
  "proxy_used": 0,
  "redirect_url": null,
  "referer": null,
  "remote_ip": "127.0.0.1",
  "remote_port": 8000,
  "response_code": 200,
  "scheme": "http",
  "size_download": 187,
  "size_header": 230,
  "size_request": 80,
  "size_upload": 0,
  "speed_download": 122703,
  "speed_upload": 0,
  "ssl_verify_result": 0,
  "time_appconnect": 0.000000,
  "time_connect": 0.000203,
  "time_namelookup": 0.000032,
  "time_pretransfer": 0.000240,
  "time_redirect": 0.000000,
  "time_starttransfer": 0.001486,
  "time_total": 0.001524,
  "url": "http://127.0.0.1:8000/get",
  "url.fragment": null,
  "url.host": "127.0.0.1",
  "url.options": null,
  "url.password": null,
  "url.path": "/get",
  "url.port": "8000",
  "url.query": null,
  "url.scheme": "http",
  "url.user": null,
  "url.zoneid": null,
  "url_effective": "http://127.0.0.1:8000/get",
  "urle.fragment": null,
  "urle.host": "127.0.0.1",
  "urle.options": null,
  "urle.password": null,
  "urle.path": "/get",
  "urle.port": "8000",
  "urle.query": null,
  "urle.scheme": "http",
  "urle.user": null,
  "urle.zoneid": null,
  "urlnum": 0,
  "xfer_id": 0,
  "curl_version": "libcurl/8.9.1 OpenSSL/3.2.2 zlib/1.3.1.zlib-ng brotli/1.1.0 libidn2/2.3.7 libpsl/0.21.5 libssh/0.11.1/openssl/zlib nghttp2/1.64.0 OpenLDAP/2.6.8"
}
```

レスポンスヘッダの情報も取得できる。

```sh
> curl -s -w '%{header_json}\n' -o /dev/null http://127.0.0.1:8000/get | jq
{
  "server": [
    "gunicorn/19.9.0"
  ],
  "date": [
    "Sun, 09 Mar 2025 04:29:40 GMT"
  ],
  "connection": [
    "keep-alive"
  ],
  "content-type": [
    "application/json"
  ],
  "content-length": [
    "187"
  ],
  "access-control-allow-origin": [
    "*"
  ],
  "access-control-allow-credentials": [
    "true"
  ]
}
```

## タイムアウト

`-m` オプションでタイムアウトを指定できる。

```sh
> curl -m 3 -i http://127.0.0.1:8000/delay/5
curl: (28) Operation timed out after 3002 milliseconds with 0 bytes received

> echo $?
28
```

## 再試行

情報転送時に発生するエラーは `--retry` オプションで再試行回数を指定できる。

```sh
> curl --retry 2 -m 3 -i http://127.0.0.1:8000/delay/5
curl: (28) Operation timed out after 3002 milliseconds with 0 bytes received
Warning: Problem : timeout. Will retry in 1 seconds. 2 retries left.
curl: (28) Operation timed out after 3003 milliseconds with 0 bytes received
Warning: Problem : timeout. Will retry in 2 seconds. 1 retries left.
curl: (28) Operation timed out after 3002 milliseconds with 0 bytes received
```

接続時のエラーはも対象にする場合は `--retry-connrefused` / `--retry-all-errors` を指定する。

```sh
> curl --retry-connrefused --retry 2 -i http://127.0.0.1:8001/get
curl: (7) Failed to connect to 127.0.0.1 port 8001 after 0 ms: Could not connect to server
Warning: Problem : connection refused. Will retry in 1 seconds. 2 retries left.
curl: (7) Failed to connect to 127.0.0.1 port 8001 after 0 ms: Could not connect to server
Warning: Problem : connection refused. Will retry in 2 seconds. 1 retries left.
curl: (7) Failed to connect to 127.0.0.1 port 8001 after 0 ms: Could not connect to server
```

`--retry-delay` オプションで再試行間隔を指定できる。

## 変数

`--variable` オプションで変数を宣言してオプション内の値で指定できる。
変数は環境変数を使用することができる。環境変数を変数として使用することでシェルによる変数展開を避けプロセスに値が見えないようにできる。

変数を展開するオプションは `--expand-<オプション>` で指定する。

```sh
> curl --variable a=user --expand-user "{{a}}:password" -i http://127.0.0.1:8000/basic-auth/user/password
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 04:00:37 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 47
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "authenticated": true,
  "user": "user"
}
```

URL に変数を使用する。

```sh
# curl --variable path=get -i --expand-url http://127.0.0.1:8000/{{path}}
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 04:01:59 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 187
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "127.0.0.1:8000",
    "User-Agent": "curl/8.9.1"
  },
  "origin": "10.88.0.1",
  "url": "http://127.0.0.1:8000/get"
}
```

環境変数を使用する。環境変数が存在しない場合の既定値も設定できる。

```sh
> a=user curl --variable %a --expand-user "{{a}}:password" -i http://127.0.0.1:8000/basic-auth/user/password
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 04:06:46 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 47
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "authenticated": true,
  "user": "user"
}
```

変数を埋め込むときにフォーマットを変換することができる。
`:trim` / `:json` / `:url` / `:b64` が指定できる。

```sh
> curl --variable 'auth=user:password' --expand-header 'Authorization: Basic {{auth:b64}}' -i http://127.0.0.1:8000/basic-auth/user/password
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Sun, 09 Mar 2025 04:10:50 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 47
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
  "authenticated": true,
  "user": "user"
}
```

## 設定ファイル

*.curlrc* ファイルまたは `-K` オプションで既定のオプションを指定できる。
