# `gpg` コマンド

暗号化と署名を行う。

## 鍵の作成

### 対話的

対話的に鍵を作成する。
本名、メールアドレス、パスワードを入力して作成する。

```sh
> gpg --generate-key
gpg (GnuPG) 2.4.5; Copyright (C) 2024 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

注意: 全機能の鍵生成には "gpg --full-generate-key" を使います。

GnuPGはあなたの鍵を識別するためにユーザIDを構成する必要があります。

本名: user01
電子メール・アドレス: user01@home.local
次のユーザIDを選択しました:
    "user01 <user01@home.local>"

名前(N)、電子メール(E)の変更、またはOK(O)か終了(Q)? O
公開鍵と秘密鍵を作成し、署名しました。

pub   rsa3072 2025-07-30 [SC] [有効期限: 2028-07-29]
      111249CBA9BF4772B699EF978DC838A18D8546E2
uid                      user01 <user01@home.local>
sub   rsa3072 2025-07-30 [E] [有効期限: 2028-07-29]
```

公開鍵と秘密鍵の主鍵と副鍵が作成される。

鍵ファイルは *~/.gnupg/private-keys-v1.d/* に保存される。

```sh
> gpg --list-keys --with-keygrip 111249CBA9BF4772B699EF978DC838A18D8546E2
pub   rsa3072 2025-07-30 [SC] [有効期限: 2028-07-29]
      111249CBA9BF4772B699EF978DC838A18D8546E2
      Keygrip = E834E9C6F894427D556F8070842447C43179B4F0
uid           [  究極  ] user01 <user01@home.local>
sub   rsa3072 2025-07-30 [E] [有効期限: 2028-07-29]
      Keygrip = 8B917944D9A1662B24A8EEE3124961A5BE8818CD

> ls ~/.gnupg/private-keys-v1.d/E834E9C6F894427D556F8070842447C43179B4F0.key
/root/.gnupg/private-keys-v1.d/E834E9C6F894427D556F8070842447C43179B4F0.key

> ls ~/.gnupg/private-keys-v1.d/8B917944D9A1662B24A8EEE3124961A5BE8818CD.key
/root/.gnupg/private-keys-v1.d/8B917944D9A1662B24A8EEE3124961A5BE8818CD.key
```

### 非対話的

非対話的に鍵を作成する。パスワードを入力して作成する。

```sh
> gpg  --quick-generate-key "user02 <user02@home.local>" future-default default 0
公開鍵と秘密鍵を作成し、署名しました。

pub   ed25519 2025-07-31 [SC]
      58FC09345BFBAE7F69B4BCF4F642A228FBBE482D
uid                      user02 <user02@home.local>
sub   cv25519 2025-07-31 [E]
```

## 鍵の確認

公開鍵を確認する。

主鍵の用途の S は Sign (署名), C は Certify(承認) となる。
副鍵の用途は E は Encrypt(暗号化) となる。

```sh
> gpg --list-keys 111249CBA9BF4772B699EF978DC838A18D8546E2
pub   rsa3072 2025-07-30 [SC] [有効期限: 2028-07-29]
      111249CBA9BF4772B699EF978DC838A18D8546E2
uid           [  究極  ] user01 <user01@home.local>
sub   rsa3072 2025-07-30 [E] [有効期限: 2028-07-29]
```

秘密鍵を確認する。

```sh
> gpg --list-secret-keys 111249CBA9BF4772B699EF978DC838A18D8546E2
sec   rsa3072 2025-07-30 [SC] [有効期限: 2028-07-29]
      111249CBA9BF4772B699EF978DC838A18D8546E2
uid           [  究極  ] user01 <user01@home.local>
ssb   rsa3072 2025-07-30 [E] [有効期限: 2028-07-29]
```

## 暗号化

### 公開鍵と秘密鍵

user01 用にテキストファイルを暗号化する。
暗号化されたファイルの拡張子は *.asc* で新規に作成される。
`--armor` を指定しない場合は拡張子が *.gpg* でバイナリファイルが作成される。

```sh
> cat > a.txt <<EOF
user01 text.
EOF

> gpg --armor --encrypt --recipient user01 a.txt

> file a.txt.asc
a.txt.asc: PGP message Public-Key Encrypted Session Key (old)

> cat a.txt.asc
-----BEGIN PGP MESSAGE-----

hQGMA3BIzyMNWhFqAQwAsYo9Wd/8xmG5sPw1y1hNVpitJyuLzKWCss9wk8MSbOso
1Pwo0BD1cVmvXw4iKdU8wUH7oKDC0929JKZmBgU4XaszLqRmgftAuBEqbzg5VGlS
5tlLixh7SN/a3nJ6U2l5PRx+iqwBkSS2ZSdr4xGT8gkB4vuiBxqidZ3oHTLbs1fj
4RQKm/SZNLH3CAWdKLes2YCx6LJCthKxJrOwzEw/glsEevPU7FRdX0B3tycky/Us
SGTQQn1qAV8nBgiXLMUS0wUCW2Jvg1lEYSsHrdxm8yqVNS8jyDS7beOhfxCY+Mkv
Jw6cnkvBiOjYQLlE3uE8CDZoR8XDqsbs5OmVjEzJcYaQab4g+GLEZIx94xcYGYUY
xaqdlCe2szbgHErzyOYUxkOpAs+8YvyHCL7WsoVcEMuyslnds1OVsLgU+kh1M6sA
6R7/0lVgEv3v4USoNmXFtOESwj+sHXdPdi6ENTaZW34horgJEf/DoRU1mD4cLhdU
c9C5IXGddWfTY3LC5+Zo1FcBCQIQ6frRUId6WoSXM9SKNaIoOhY0hIUuzrvNt7rB
2UolYtTIZecnXM/qFvd5c8ZI7QLU2oHqsnBnxTc2WaI+pQTmhhSy1xuujnPanznu
pHDHM9Qm47o=
=SFbt
-----END PGP MESSAGE-----

> gpg --encrypt --recipient user01 a.txt

> file a.txt.gpg
a.txt.gpg: PGP RSA encrypted session key - keyid: 7048CF23 0D5A116A RSA (Encrypt or Sign) 3072b
```

user01 で複合化する。パスワードの入力が求められる。

```sh
> gpg --decrypt --local-user user01 a.txt.asc
gpg: rsa3072鍵, ID 7048CF230D5A116A, 日付2025-07-30に暗号化されました
      "user01 <user01@home.local>"
user01 text.
```

ID は公開鍵の副鍵のフィンガープリント下位 64bit となる。

```sh
> gpg --list-keys --with-subkey-fingerprint 111249CBA9BF4772B699EF978DC838A18D8546E2
pub   rsa3072 2025-07-30 [SC] [有効期限: 2028-07-29]
      111249CBA9BF4772B699EF978DC838A18D8546E2
uid           [  究極  ] user01 <user01@home.local>
sub   rsa3072 2025-07-30 [E] [有効期限: 2028-07-29]
      EEA74F61C9110836F7D31CD17048CF230D5A116A
```

### 共通鍵

共通鍵を作成して暗号化する。パスワードを入力して作成する。
暗号化されたファイルの拡張子は *.asc* で新規に作成される。
`--armor` を指定しない場合は拡張子が *.gpg* でバイナリファイルが作成される。

```sh
> cat > a.txt <<EOF
user01 text.
EOF

> gpg --armor --symmetric a.txt

> file a.txt.asc
a.txt.asc: PGP message Symmetric-Key Encrypted Session Key (old)

> cat a.txt.asc
-----BEGIN PGP MESSAGE-----

jA0ECQMIOLG2zPQfhd/30kcBlhTLkHtgXWSfu/mRntasejMmp5ZK8i7u0TItaQ5v
+VQa08Ys80aaYVLisZ1l3nraPGYHURpzEz/xMsJ0DVga3n9YK2X8Rg==
=6N4m
-----END PGP MESSAGE-----

> gpg --symmetric a.txt

> file a.txt.gpg
a.txt.gpg: PGP symmetric key encrypted data - AES with 256-bit key salted & iterated - SHA256 .
```

## 署名

### テキストの署名

user01 の署名を追加する。パスワードの入力が求められる。
署名されたファイルの拡張子は *.asc* で新規に作成される。

```sh
> cat > a.txt <<EOF
user01 text.
EOF

> gpg --clear-sign --local-user user01 a.txt

> file a.txt.asc
a.txt.asc: PGP signed message

> cat a.txt.asc
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA256

user01 text.
-----BEGIN PGP SIGNATURE-----

iQGzBAEBCAAdFiEEERJJy6m/R3K2me+Xjcg4oY2FRuIFAmiKDMMACgkQjcg4oY2F
RuK9CwwAkO+kDb/dLdeQxQ+Mv1+nLUvSZoJoD4bZ0JuLvEvxGBNLoynVlSdzWXGa
9yTj+sEW/htZvJXAxzQNRq64YDmh9HG9yu3fwfDvHyp+hn5UOR11H1UxBjleNxzQ
4tgzaqSTKPf9KW2lL22FzIpbK7ZnRnbJj3XBgvOnJ4I868dl4E7xYf2uR4wh/b4t
XCFoIszbe44QL1qsngb3eVp+RQqgqyHey8wYbFF7GH9r6dP3/c3KQmA8aQAxERtA
oJx605hjtgSyw4jXGIPRi9W7tC0LO/1EpJjd7fBLFZHnhogvT8y6VLj1nVLLXgo7
gk6GNGfyao7BVjmwvq5sYDAQuwUsyAdJ7yyd3blXyzYRzZ0AP+dVrPZYqhT1VWXs
0vtBFTI4F5le7iEN0Jv8YhuQ5ZJiw6l3muLwvuUePXomxTLfqd25V3yedQjQ+aSS
o6dOCRan8DGUjyJ3OnWq1lfVKkjfga+FtOhC52D4Ycx9lTKnSTNVz/y1xW+GmeZR
/9u9zkfv
=ZFUh
-----END PGP SIGNATURE-----
```

署名を検証する。

```sh
> gpg --verify a.txt.asc
gpg: 2025年07月30日 21時14分59秒 JSTに施された署名
gpg:                RSA鍵111249CBA9BF4772B699EF978DC838A18D8546E2を使用
gpg: "user01 <user01@home.local>"からの正しい署名 [究極]
```

### 署名ファイルの作成

対象のファイルとは別に署名ファイルを作成する。パスワードの入力が求められる。
署名ファイルの拡張子は *.asc* で新規に作成される。
`--armor` を指定しない場合は拡張子が *.sig* でバイナリファイルが作成される。

```sh
> cat > a.txt <<EOF
user01 text.
EOF

> gpg --armor --detach-sign --local-user user01 a.txt

> file a.txt.asc
a.txt.asc: PGP signature Signature (old)

> ls
a.txt  a.txt.asc

> cat a.txt.asc
-----BEGIN PGP SIGNATURE-----

iQGzBAABCAAdFiEEERJJy6m/R3K2me+Xjcg4oY2FRuIFAmiKQJsACgkQjcg4oY2F
RuJy9AwAgkrX+XY9VYTG1BGMESJZi1zxxJJhc7ZdyHkEjtao20YvooXAou7H7phv
AyVhfVfYUYagKYH4ZoscTqpC/oUzu5qyMzWsqMaCBb/L8hkGc3mpyb4vnslZaUeG
bvi4UKnDsurOP9nQHi4pmQyTRRa642rHl39nd20CP1ZnNeqzd76gWNggY02noDt9
nZOyFvvpklXCfjAi6BrVYkv2dQr6jWZfKRR9zbVML8BoQgay4evUaxYSXKUMg73u
H7yDXkKvlqqo1hJIa63HQpOQqA0TiQy/O3xQtfL4LiBKOj20+0LwqkKCEn2oemep
Ktpt+4c3SLBTvZMo512rsJtp9+22g3vcLvxLeKYLl0J08BMMCy8YXKCsC6ZaSLlu
V6j488O02VkrCWqY2HqJvFAryxfTw/Bpshnneo+X1Gwp9UjmMhSWr+O7y+iydWxY
oSVRaDk6mlOetsr4cz8yXoAkWj6UXH9wDU5WDiP4uN8ZeJiBwR4NzU7mQSk9zJHg
b84OfHAO
=gYPY
-----END PGP SIGNATURE-----

> gpg --detach-sign --local-user user01 a.txt

> file a.txt.sig
a.txt.sig: data
```

### 署名ファイルの埋め込み

対象ファイルに署名ファイルに埋め込む。パスワードの入力が求められる。
署名されたファイルの拡張子は *.asc* で新規に作成される。
`--armor` を指定しない場合は拡張子が *.gpg* でバイナリファイルが作成される。

```sh
> cat > a.txt <<EOF
user01 text.
EOF

> gpg --armor --sign --local-user user01 a.txt

> file a.txt.asc
a.txt.asc: PGP message Compressed Data (old)

> ls
a.txt  a.txt.asc

> cat a.txt.asc
-----BEGIN PGP MESSAGE-----

owEB3wEg/pANAwAIAY3IOKGNhUbiAawYYgVhLnR4dGiKQhp1c2VyMDEgdGV4dC4K
iQGzBAABCAAdFiEEERJJy6m/R3K2me+Xjcg4oY2FRuIFAmiKQhoACgkQjcg4oY2F
RuId2gv7BGT6G0Tkx6R35V1Bjp8QM17i5magn5xdP09QkURnluAxcAQtAfxxXBPk
ka1vNAbpgYy4XTZ86WVx+5HTEf78VXF6r+wjZh5OuLIMFOi5txBuQX9OJRx8tYE2
FoA7sunTpjAMh85oFigrY58uQlJAgK9YsmQh7bcERQ2iMQXc4vXSaoh4br6/TjEa
QEPqZafd2/E8me3e3ZxZnsCL+yhoclZi6QUAdXFyHTbG1i/PVfGCs9/8TQHG0Wh4
4VUxDqZxNbS8MlQpP0djwOsFVcc/z4re8Edc6RKyJi/c77xkH0maV4FQto9BSsf/
zRVhFQ3Wp6WAUuFSqrdFvkkwFIO/xIw96aT8B0Fhu/BceUX3Pchb1gICf/u/hhU9
ujFugvnSsjv4LiBe/4FJTh0i9MfQEKENg68hfw3ai/GeoLk03h6kDELXJtdv3SIZ
9NySUUV6ltQz0pEpjysKAnqhJ3x0GLXEeR8MTQUuvti/FzrAQlQGRyo+eCBEtfpI
HWlkUPV2
=uGjJ
-----END PGP MESSAGE-----

> gpg --sign --local-user user01 a.txt

> file a.txt.gpg
a.txt.gpg: data
```

元の情報を取り出すには複合する。

```sh
> gpg --decrypt a.txt.asc
user01 text.
gpg: 2025年07月31日 20時56分38秒 JSTに施された署名
gpg:                RSA鍵111249CBA9BF4772B699EF978DC838A18D8546E2を使用
gpg: "user01 <user01@home.local>"からの正しい署名 [究極]
```

## 鍵の削除

公開鍵を削除する。秘密鍵がある場合は削除できない。

```sh
> gpg --delete-keys user02
gpg (GnuPG) 2.4.5; Copyright (C) 2024 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

gpg: この公開鍵に対する秘密鍵"user02"があります!
gpg: まず"--delete-secret-keys"オプションでこれを削除してください。
```

秘密鍵を削除する。
コンソールで 2 回、プロンプトで 2 回の同意が求められる。

```sh
> gpg --delete-secret-keys user02
gpg (GnuPG) 2.4.5; Copyright (C) 2024 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


sec  ed25519/F642A228FBBE482D 2025-07-31 user02 <user02@home.local>

この鍵を鍵リングから削除しますか? (y/N) y
これは秘密鍵です! 本当に削除しますか? (y/N) y
```

秘密鍵を削除したあとは公開鍵を削除できる。

```sh
> gpg --delete-keys user02
gpg (GnuPG) 2.4.5; Copyright (C) 2024 g10 Code GmbH
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


pub  ed25519/F642A228FBBE482D 2025-07-31 user02 <user02@home.local>

この鍵を鍵リングから削除しますか? (y/N) y
```

## 鍵のエクスポート

公開鍵をエクスポートする。

```sh
> gpg --armor --export --output public.key user01

> file public.key
public.key: PGP public key block Public-Key (old)
```

秘密鍵をエクスポートする (公開鍵も含まれている)。

```sh
> gpg --armor --export-secret-keys --output private.key user01

> file private.key
private.key: PGP private key block
```

## 鍵のインポート

公開鍵をインポートする。

```sh
> gpg --import public.key
gpg: 鍵F642A228FBBE482D: 公開鍵"user02 <user02@home.local>"をインポートしました
gpg:           処理数の合計: 1
gpg:             インポート: 1
```

インポートした公開鍵は信頼度が「不明」になる。

```sh
> gpg --list-keys user02
pub   ed25519 2025-07-31 [SC]
      58FC09345BFBAE7F69B4BCF4F642A228FBBE482D
uid           [  不明  ] user02 <user02@home.local>
sub   cv25519 2025-07-31 [E]
```

## ローカル署名

user02 の公開鍵を user01 で署名して信頼する。パスワードの入力が求められる。
`lsign` のローカル署名は他人と共有することはない。

```sh
> gpg --lsign-key --local-user user01 user02

pub  ed25519/F642A228FBBE482D
     作成: 2025-07-31  有効期限: 無期限      利用法: SC
     信用: 不明の     有効性: 不明の
sub  cv25519/703AE07DF07AAC24
     作成: 2025-07-31  有効期限: 無期限      利用法: E
[  不明  ] (1). user02 <user02@home.local>


pub  ed25519/F642A228FBBE482D
     作成: 2025-07-31  有効期限: 無期限      利用法: SC
     信用: 不明の     有効性: 不明の
  主鍵フィンガープリント: 58FC 0934 5BFB AE7F 69B4  BCF4 F642 A228 FBBE 482D

     user02 <user02@home.local>

本当にこの鍵にあなたの鍵"user01 <user01@home.local>"で署名してよいですか
(8DC838A18D8546E2)

署名は、エクスポート不可に設定されます。

本当に署名しますか? (y/N) y
```

user01 の主鍵で署名され、署名した公開鍵は信頼度が「充分」になる。

```sh
> gpg --list-signatures user02
pub   ed25519 2025-07-31 [SC]
      58FC09345BFBAE7F69B4BCF4F642A228FBBE482D
uid           [  充分  ] user02 <user02@home.local>
sig 3        F642A228FBBE482D 2025-07-31  [自己署名]
sig   L      8DC838A18D8546E2 2025-07-31  user01 <user01@home.local>
sub   cv25519 2025-07-31 [E]
sig          F642A228FBBE482D 2025-07-31  [自己署名]
```

## gpg-agent

`gpg` で使用するパスワードをキャッシュする。

`gpg` コマンドを実行すると自動的に下記が起動する。

```sh
gpg-agent --homedir /root/.gnupg --use-standard-socket --daemon
```

サービスの設定は下記になっている。

```sh
> systemctl --user status gpg-agent
○ gpg-agent.service - GnuPG cryptographic agent and passphrase cache
     Loaded: loaded (/usr/lib/systemd/user/gpg-agent.service; static)
     Active: inactive (dead)
TriggeredBy: ○ gpg-agent.socket
       Docs: man:gpg-agent(1)

> systemctl --user status gpg-agent.socket
○ gpg-agent.socket - GnuPG cryptographic agent and passphrase cache
     Loaded: loaded (/usr/lib/systemd/user/gpg-agent.socket; disabled; preset: disabled)
     Active: inactive (dead)
   Triggers: ● gpg-agent.service
       Docs: man:gpg-agent(1)
     Listen: /run/user/0/gnupg/S.gpg-agent (Stream)
```

### 起動

gpg-agent を起動する。

```sh
> gpgconf --launch gpg-agent
```

### 停止

gpg-agent を停止する。

```sh
> gpgconf --kill gpg-agent
```

### 端末の設定

パスワードのプロンプトが正常に表示できない場合は tty を設定する。

```sh
> export GPG_TTY=$(tty)

> gpg-connect-agent updatestartuptty /bye
OK
```

### SSH Agent

gpg-agent を ssh-agent の代わりに使用する。

#### SSH 鍵の作成

認証に利用可能な副鍵を作成する。パスワードの入力が求められる。

```sh
> gpg --quick-add-key 111249CBA9BF4772B699EF978DC838A18D8546E2 future-default auth 0
```

用途 A の Authenticate(認証) が副鍵が追加される。

```sh
> gpg --list-keys --with-subkey-fingerprint user01
pub   rsa3072 2025-07-30 [SC] [有効期限: 2028-07-29]
      111249CBA9BF4772B699EF978DC838A18D8546E2
uid           [  究極  ] user01 <user01@home.local>
sub   rsa3072 2025-07-30 [E] [有効期限: 2028-07-29]
      EEA74F61C9110836F7D31CD17048CF230D5A116A
sub   ed25519 2025-07-31 [A]
      0503C87B37BCAA92CBA7F140A24579908CADB545
```

SSH 公開鍵をエクスポートする。コメントに副鍵の下位 32bit がつく。

```sh
> gpg --export-ssh-key user01
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHBQn5HO1pDj2r068OHRGSAtu2tF2gPisxYV6k8PbFNF openpgp:0x8CADB545
```

#### SSH を有効化

gpg-agent を ssh-agent の代わりに使用する設定を有効化する。

```sh
# gpg-agent が起動するプロセスに SSH_AUTH_SOCK を設定する。
# 設定は不要かもしれない。
> echo enable-ssh-support >> ~/.gnupg/gpg-agent.conf

> gpg-connect-agent reloadagent /bye
OK

> export SSH_AUTH_SOCK="$(gpgconf --list-dirs agent-ssh-socket)"
```

#### SSH 鍵の登録

*~/.gnupg/sshcontrol* に認証用の副鍵の keygrip を登録する。

```text
78DD0D4D4969A90D767CD0A98BA6B5A996D88733
```

SSH 鍵を確認する。

```sh
# SSH_AUTH_SOCK を使用する。
> ssh-add -L
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKT1kocKwzk0+3xAMK7YCUB+uXiQ4cmUitWzsT+5Q9t5 (none)
```

※ [SSH 鍵の作成](./gpg.md#SSH-鍵の作成) とは別環境のため値は異なるが本来は一致する。

#### 接続確認

接続先に SSH 鍵を登録して接続する。パスワードの入力が求められる。

### パスワードの事前設定

事前設定の機能を有効化する。

```sh
> echo allow-preset-passphrase >> ~/.gnupg/gpg-agent.conf

> gpg-connect-agent reloadagent /bye
OK
```

keygrip にパスワードを事前に設定する。

```sh
>  echo 'パスワード' | /usr/libexec/gpg-preset-passphrase --preset 161E3BDA77302D2BD3D9D385747C4C04461ED252
```

パスワードの入力なしに使用できる。

```sh
> echo 'text' | gpg --clear-sign
```

`--forget` は失敗する。

```sh
> /usr/libexec/gpg-preset-passphrase --forget 161E3BDA77302D2BD3D9D385747C4C04461ED252
gpg-preset-passphrase: clearing passphrase failed: No inquire callback in IPC
```

## コミット署名

git のコミットに署名する。

コミットに署名する設定を有効化する。※ [鍵の作成](./gpg.md#鍵の作成) とは別環境のため値は異なる。

```sh
> git config --local user.name user01
> git config --local user.email user01@home.local
> git config --local user.signingkey D1D936BF6A908E4D19946D0D54127E964EB3502A
> git config --local commit.gpgsign true
```

コミットする。パスワードの入力が求められる。

```sh
> git commit -m "Initial commit."

> git log -1 --show-signature
commit 242be3742dd64bab41be6aad97fa84ab57f3ed34 (HEAD -> master)
gpg: 2025年08月02日 17時35分03秒 JSTに施された署名
gpg:                EDDSA鍵D1D936BF6A908E4D19946D0D54127E964EB3502Aを使用
gpg: "user01 <user01@home.local>"からの正しい署名 [究極]
Author: user01 <user01@home.local>
Date:   Sat Aug 2 17:35:03 2025 +0900

    Initial commit.
```

### vscode

vscode 上の操作でコミットに署名する。

```json
{
    "git.enableCommitSigning": true
}
```

#### Windows

vscode と git for windows の環境では
コミット時に pinentry-w32.exe が起動してパスワードを入力してコミットする。

#### Windows と Remote SSH

vscode の Remote SSH で Linux に接続した環境でコミットに署名する。

vscode 内のターミナルからのコミットは pinentry-curses が起動してパスワードを入力してコミットする。

vscode 操作のコミットでは pinentry が起動失敗してコミットが失敗する。
下記にように `gpg` コマンドを実行するか `gpg-preset-passphrase` を使用してで事前にパスワードを gpg-agent にキャッシュさせる。キャッシュさせたあとは vscode 操作でコミット時に署名ができる。

```sh
> echo '' | gpg --clear-sign
```

```{note}
下記の方法で Windows にある鍵で署名ができるかもしれない。

- gpg-agent を RemoteForward する。
  https://wiki.gnupg.org/AgentForwarding
  https://github.com/PowerShell/Win32-OpenSSH/issues/1564

- Windows 上で gpg-agent を ssh-agent として設定して ForwardAgent する。
```

gpg-agent を RemoteForward する。

git for windows の gpg-agent をフォワーディングするには git for windows に組み込みの ssh.exe を使用する。
公開鍵は転送できないのでリモートホスト側に追加する。

```{note}
git for windows の ssh.exe を使用する場合は SSH の config ファイルの権限は使用するユーザのみに変更する必要がある。

「プロセスが、存在しないパイプに書き込もうとしました。」

config ファイルのセキュリティの[詳細設定]で[継承の無効化]し親フォルダから継承している権限をすべて削除したあとで、
使用するユーザのフルコントロールを付与する。
```

RemoteForward はぞれぞれマシンの `gpgconf --list-dirs` の値を指定する。
設定方法は [AgentForwarding](https://wiki.gnupg.org/AgentForwarding) を参照する。

```{note}
--list-secret-keys 実行時に下記のメッセージが出力されるが秘密鍵は使用できるし署名に利用はできる。

gpg: problem with fast path key listing: 禁止 - ignored

参照: https://lists.gnupg.org/pipermail/gnupg-users/2024-April/067043.html
```

上記の状態で鍵を使用するとコミット時に pinentry-w32.exe が起動してパスワードを入力してコミットする。

#### Windows と Remote SSH と devcontainer

devcontainer の agent-socket がリモートホストの agent-extra-socket に
フォワーディングされているためコンテナ上でリモートホスト上の鍵の情報が使用できる。

```{note}
秘密鍵が参照できない場合はリモートホスト側に gpg-agent が起動していることを確認してコンテナを再起動する。
```

ただし、vscode 内のターミナルも vscode 操作でコミットする場合も
pinentry の起動が失敗してコミットが失敗するか、リモートホスト側にプロンプトが表示される。
pinentry-curses の場合はリモートホストにプロンプトに表示されてもパスワードを入力できなかった。

`gpg-preset-passphrase` も使用できない。リモートホストでキャッシュさせても効果がない。

```sh
> echo 'パスワード' | /usr/lib/gnupg/gpg-preset-passphrase --preset 71C99E04A46E915CA14A2FDFF02CF02390FB2D5E
gpg-preset-passphrase: caching passphrase failed: Forbidden
```

リモートホストで gpg-agent を RemoteForward した場合は agent-extra-socket を参照させる。

```sh
> ln -sf $(gpgconf --list-dir agent-socket) $(gpgconf --list-dir agent-extra-socket)
```

コンテナ上の agent-socket がリモートホスト上の agent-extra-socket/agent-socket を経由して
Windows の agent-extra-socket に転送される。

上記の状態で鍵を使用するとコミット時に pinentry-w32.exe が起動してパスワードを入力してコミットする。

## 参照

- [OpenPGP](https://datatracker.ietf.org/doc/html/rfc9580)
- [Git - Support prompting for GPG password](https://github.com/microsoft/vscode/issues/43809)
