# ```sed``` コマンド

以下のフォーマットでコマンドを記載して各行を処理をする。

```
[ADDRESS][!]COMMAND[ARG]...
```

複数の命令を指定する場合はセミコロンで区切るか、複数の `-e` オプションを指定する。

`ADDRESS` を指定しない場合はすべての入力行を処理する。

`sed` は入力行を作業領域(パターンスペース)に読み込みコマンドを実行する。
すべてのコマンドを実行後に次の行をパターンスペースに読み込み、次の行を処理する。

パターンスペースはコマンド `h` でホールドスペースにコピーして保存することができる。
保存した内容はコマンド `g` でパターンスペースに戻すことができる。

## ADDRESS

`ADDRESS`　には正規表現(`/PATTERN/`)や行数(数字)を指定できる。

行数を指定する。

```sh
> echo -e 'a\nb\nc' | sed -e '2ad'
a
b
d
c
```

開始行数とその位置からの行数を指定する。

```sh
> echo -e 'a\nb\nc' | sed -e '2~1ad'
a
b
d
c
d
> echo -e 'a\nb\nc' | sed -e '2,+1ad' # GNU 拡張
a
b
d
c
d
> echo -e 'a\nb\nc' | sed -e '2,~2ad' # GNU 拡張、N=2 の倍数の行数になるまで
a
b
d
c
d
```

最終行を指定する。

```sh
> echo -e 'a\nb\nc' | sed -e '$ad'
a
b
c
d
```

正規表現を指定する。

```sh
> echo -e 'a\nb\nc' | sed -e '/b/ad'
a
b
d
c
```

カンマで区切って範囲を指定する。

```sh
> echo -e 'a\nb\nc' | sed -e '2,3ad'
a
b
d
c
d
> echo -e 'a\nb\nc' | sed -e '/b/,/c/ad'
a
b
d
c
d
```

ファイルの先頭から一致する行が見つかるまでの範囲を指定する(2番目の引数は正規表現のみ)(GNU 拡張)。

```sh
> echo -e 'a\nb\nc' | sed -e '0,/a/ad'
a
d
b
c
> echo -e 'a\nb\nc' | sed -e '1,/a/ad'
a
d
b
d
c
d
> echo -e 'a\nb\nc' | sed -e '0,1ad'
sed: -e expression #1, char 4: 無効な行アドレス 0 の使用方法です
```

## エスケープシーケンス

* `\a`
  ```sh
  > echo -e 'a\ac' | sed -e 's/\a/b/'
  abc
  ```
* `\f`
  ```sh
  > echo -e 'a\fc' | sed -e 's/\f/b/'
  abc
  ```
* `\n`
  ```sh
  > echo -e 'a\nc' | sed -e 's/\n/b/'
  a
  c
  ```
* `\r`
  ```sh
  > echo -e 'a\rc' | sed -e 's/\r/b/'
  abc
  ```
* `\t`
  ```sh
  > echo -e 'a\tc' | sed -e 's/\t/b/'
  abc
  ```
* `\v`
  ```sh
  > echo -e 'a\vc' | sed -e 's/\v/b/'
  abc
  ```

FreeBSD sed の場合は `\a`, `\f`, `\v` は下記のエラーが発生する。
同じエラーで `\s`, `\w` などの文字クラスも使えない。

```
RE error: trailing backslash (\)
```

## 正規表現

基本正規表現(BRE)のメタ文字は `.`, `*`, `\`, `^`, `$`, `[`。
拡張正規表現(`-E, -r` オプション/ERE)のメタ文字は基本正規表現のメタ文字に追加して `+`, `?`, `{`, `(`, `)`, `|`。

```sh
> echo 'abc' | sed -e 's/\(bc\)/& &/'
abc bc
```

```sh
> echo 'aaa' | sed -e 's/a\{1,2\}/b/'
ba
```

```sh
> echo 'abc' | sed -E -e 's/(bc)/& &/'
abc bc
```

```sh
> echo 'aaa' | sed -E -e 's/a{1,2}/b/'
ba
```

## ラベル(:)

命令に `:LABEL` の形式で指定する。

`b`, `t`, `T` の分岐先を設定する。

## コメント(#)

`#` 以降は行末までコメントとして扱う。

```sh
> echo a | sed -e 's/a/b/# a->b'
b
```

## 行数(=)

命令に `[ADDRESS]=` の形式で指定する。

```sh
> echo -e 'a\nb\nc' | sed -ne '/b/='
2
```

## 追加(a)

命令に `[ADDRESS]aNEW` の形式で指定する。

`ADDRESS` 指定した次の行に `NEW` を追加する。
複数行を指定する場合はバックスラッシュでエスケープする。

```sh
> echo -e 'a\nb\nc' | sed -e '2ad'
a
b
d
c
```

FreeBSD sed の場合は command の後に改行が必要になる。

```sh
> echo -e 'a\nb\nc' | sed -e '2a\
d'
a
b
d
c
```

## 挿入(i)

命令に `[ADDRESS]iNEW` の形式で指定する。

`ADDRESS` 指定した行に `NEW` を追加する。
複数行を指定する場合はバックスラッシュでエスケープする。

```sh
> echo -e 'a\nb\nc' | sed -e '2id'
a
d
b
c
```

FreeBSD sed の場合は command の後に改行が必要になる。

```sh
> echo -e 'a\nb\nc' | sed -e '2i\
d'
a
d
b
c
```

## 終了(q/Q)

命令に `[ADDRESS]q[EXITCODE]` の形式で指定する。

```sh
> echo a | sed -e 'q1'; echo $?
a
1
```

## ファイルの読み込み(r)

命令に `[ADDRESS]r FILEPATH` の形式で指定する。
ADDRESS に指定した行にファイルの内容を挿入する。

```sh
> echo d > d.txt
> echo -e 'a\nb\nc' | sed -e '/\(a\|c\)/r d.txt'
a
d
b
c
d
```

## ファイルの読み込み(R)

命令に `[ADDRESS]R FILEPATH` の形式で指定する。
`ADDRESS` に指定した行にファイルのマッチ回数と同じ行を挿入する。
ファイルに対象の行がない場合は何もしない。

```sh
> echo -e 'd\ne' > d.txt
> echo -e 'a\nb\nc' | sed -e '/\(a\|c\)/R d.txt'
a
d
b
c
e
```

## 分岐(b)

命令に `[ADDRESS]b [LABEL]` の形式で指定する。
`LABEL` を省略した場合はスクリプトの末尾に分岐する。

```sh
> cat > p.txt <<EOF
ab
b
ac
EOF
> echo a | sed -f p.txt
a
b
```

## 変更(c)

命令に `[ADDRESS]cNEW` の形式で指定する。

`ADDRESS` 指定した行を `NEW` を変更する。
複数行を指定する場合はバックスラッシュでエスケープする。

```sh
> echo -e 'a\nb\nc' | sed -e '2cd'
a
d
c
> echo -e 'a\nb\nc' | sed -e '2,3cd'
a
d
> echo -e 'a\nb\nc' | sed -e '2,3{
    cd
}'
a
d
d
```

FreeBSD sed の場合は command の後に改行が必要になる。

```sh
> echo -e 'a\nb\nc' | sed -e '2c\
d'
a
d
c
```

## 削除(d)

命令に `[ADDRESS]d` の形式で指定する。
パターンスペースを削除する。
パターンスペースが空になるとスクリプトが完了し次の入力行をパターンスペースに読み込まれる。

```sh
> echo -e 'a\nb' | sed -e '1d'
b
```

## 削除(D)

命令に `[ADDRESS]D` の形式で指定する。
パターンスペース内の先頭行を削除してスクリプトの始めから処理する。

```sh
> echo -e 'a\nb\nc' | sed -e '/a/{
    N
    D
}'
b
c
```

## 保存(h) / リストア(g)

保存は命令に `[ADDRESS]h` の形式で指定する。
ホールドスペースをパターンスペースの内容で上書きする。

リストアは命令に `[ADDRESS]g` の形式で指定する。
パターンスペースをホールドスペースの内容で上書きする。

```sh
> echo -e 'a\nb\nc' | sed -e '/a/h; /c/g'
a
b
a
```

## 保存(H) / リストア(G)

保存は命令に `[ADDRESS]H` の形式で指定する。
ホールドスペースを改行とパターンスペースの内容で追記する。

リストアは命令に `[ADDRESS]G` の形式で指定する。
パターンスペースを改行とホールドスペースの内容で追記する。

```sh
> echo -e 'a\nb\nc' | sed -e '/a/H; /c/G'
a
b
c

a
```


## リスト(l)

命令に `[ADDRESS]l [WIDTH]` の形式で指定する。
非表示文字も含めて対象行を表示する。

```sh
> echo -e 'ab\nbd' | sed -n -e 'l'
ab$
bd$
```

```sh
> echo -e 'ab\nbd' | sed -n -e 'l 2'
a\
b$
b\
d$
```

## 次の行を読む(n)

命令に `[ADDRESS]n` の形式で指定する。
現在のパターンスペースを出力してパターンスペースに次の行を読み込む。
スクリプトの続きは読み込んだ行に適用される。

```sh
> echo -e 'a\nb\nc' | sed -e '/a/{
    n
    d
}'
a
c
```

## 次の行を読む(N)

命令に `[ADDRESS]N` の形式で指定する。
パターンスペースに次の行を追加する。

```sh
> echo -e 'a\nb\nc' | sed -e '/a/{
    N
    d
}'
c
```

## 表示(p)

命令に `[ADDRESS]p` の形式で指定する。
入力を表示する。

```sh
> echo -e 'a\nb\nc' | sed -n -e 'p'
a
b
c
```

## 表示(P)

命令に `[ADDRESS]P` の形式で指定する。
パターンスペース内の先頭行を表示する。

```sh
> echo -e 'a\nb\nc' | sed -n -e '/a/{
    N
    P
}'
a
```


## 置換(s)

命令に `[ADDRESS]s/PATTERN/REPLACE/[FLAG]` の形式で指定する。

```sh
> echo aaa | sed -e 's/a/b/'
baa
> echo aaa | sed -e 's/a/b/g'
bbb
> echo aaa | sed -e 's/a/b/p' # 置換した行を表示
baa
baa
> echo aaa | sed -e 's/a/b/w a.txt' # 置換した行をファイルに書き出す
baa
> echo aaa | sed -e 's/a/b/2' # 数字は 255 まで
aba
> echo aaa | sed -e 's/a/b/' -e 's/a/c/'
bca
> echo aaa | sed -e 's/a/b/; s/a/c/'
bca
```

命令をファイルから読み込む。

```sh
> echo aaa | sed -f <(echo 's/a/b/')
baa
```

置換が発生した行だけ出力する。

```sh
> echo aaa | sed -n -e 's/a/b/p'; echo $?
baa
0
> echo aaa | sed -n -e 's/c/b/p'; echo $?
0
```

`ADDRESS` にマッチした行を処理する。

```sh
> echo aaa | sed -e '/a/s/a/b/'; echo $?
baa
0
> echo aaa | sed -e '/c/s/a/b/'; echo $?
aaa
0
```

`!` は付けると反転する。

```sh
> echo aaa | sed -e '/a/!s/a/b/'
aaa
> echo aaa | sed -e '/c/!s/a/b/'
baa
```

カンマで区切って範囲を指定する。

```sh
> echo -e 'a\nb\nc' | sed -e '1,2s/./d/'
d
d
c
```

末尾行を指定する。

```sh
> echo -e 'a\nb\nc' | sed -e '$s/./d/'
a
b
d
```

命令を`{...}`でグループ化できる。

```sh
> echo -e 'a\nb\nc' | sed -e '1,2{
    s/a/d/
    s/b/e/
}'
d
e
c
```

`PATTERN`に一致した文字列に置換する(`&`)。

```sh
> echo aaa | sed -e 's/a/&b/'
abaa
```

グループ化した正規表現の文字列に置換する(`\N`, N は 1～9 まで)。

```sh
> echo aaa | sed -e 's/.\(a\)./\1/'
a
```

デミリタは `/` 以外でも問題ない。

```sh
> echo aaa | sed -e 's|a|b|g'
bbb
```

## 分岐(t/T)

命令に `t [LABEL]` の形式で指定する。
置換(s)コマンドが置換した場合(`t`)、置換しなかった場合(`T`)に分岐する。
`LABEL` を省略した場合はスクリプトの末尾に分岐する。

置換した場合にスクリプトを完了する。

```sh
> echo -e 'a\nb\nc' | sed -e '{
    s/a/b/
    t
    s/b/c/
}'
b
c
c
```

置換しなかった場合にスクリプトを完了する。

```sh
> echo -e 'a\nb\nc' | sed -e '{
    s/a/b/
    T
    s/b/c/
}'
c
b
c
```

## ファイルに書き込む(w)

命令に `ADDRESSw FILEPATH` の形式で指定する。

```sh
> echo -e 'a\nb\nc' | sed -n -e '/a/w a.txt'
> cat a.txt
a
```

## ファイルに書き込む(W)

命令に `ADDRESSW FILEPATH` の形式で指定する。
パターンスペース内の先頭行をファイルに書き込む。

```sh
> echo -e 'a\nb\nc' | sed -n -e '{
    N
    /a/W a.txt
}'
> cat a.txt
a
```

## 交換(x)

パターンスペースとホールドスペースを交換する。

## 変換(y)

命令に `[ADDRESS]y/BEFORE/AFTER/` の形式で指定する。
BEFORE と AFTER を 1:1 で変換する。

```sh
> echo -e 'abc' | sed -e 'y/ab/AB/'
ABc
```
