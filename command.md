# コマンド

## ビルトインのコマンド

### ```:``` コマンド

```yes``` と同じ。

### ```exec``` コマンド

新たなプロセスを生成せずにコマンドを実行する。

```sh
exec CMD
```

現在のプロセスのリダイレクト先を変更する。以降の標準出力が ```OUTPUT``` になる。

```sh
exec > OUTPUT
```

### ```printf``` コマンド

```-v``` オプションで標準出力の代わりに変数に代入する。

```sh
printf -v val "%d" 1
```

### ```read``` コマンド

標準入力から $IFS で区切った文字の配列を代入する。

```sh
read val1 val2 ...
```

行全体を 1 つの変数で読み取る場合は $IFS を空文字に設定する。

```sh
IFS= read line
```

$IFS にエスケープ文字を設定する場合は $ を付けてシングルクォートで囲む。

```sh
IFS=$'\n'
```

### ```set``` コマンド

シェルのオプションを確認する。

```sh
set -o
```

引数を設定する。

```sh
set 'a' 'b' 'c'
echo $*
```

### ```trap``` コマンド

0(EXIT) や ```kill -l``` のシグナルを捕捉してコマンドを実行する。

EXIT シグナルで ```CMD``` を実行する。

```sh
trap CMD EXIT
```

特殊なシグナルは以下がある。

| シグナル名 | 説明 |
| --------- | ---- |
| DEBUG | コマンドの実行前 |
| ERR | コマンド失敗時 |
| EXIT | シェル終了時 |
| RETURN | 関数実行後 |


### ```builtin``` コマンド

エイリアスや関数ではなく組み込みコマンドを実行する。

```sh
builtin pwd
```

### ```command``` コマンド

エイリアスや関数ではなくコマンドを実行する。

```sh
command sed
```

### ```declare``` コマンド

変数を参照や定義する。

```sh
declare -p
```

### ```select``` コマンド

番号付きの選択肢を表示する。

```sh
 select f in *
 do
     echo $f
     break
 done
```

## `awk` コマンド

入力行をレコード、それをデミリタで区切った値をフィールドとして処理する。
`BEGIN`, `END` ブロックは省略できる。

```
BEGIN {
    # 開始時に1一度だけ実行される。
    command1
}
[ADDRESS] {
    # 入力行ごとに実行される。
    command2
}
END {
    # 最後に1一度だけ実行される。
    command3
}
```

```sh
> echo -e 'a\nb' | awk '
BEGIN { print "start" }
{ print $1 }
END { print "end" }'
start
a
b
end
```

既定では空白とタブでレコードを区切る。
`$1` 以降にフィールドが格納される。`$0` は行全体が格納される。

```sh
> echo -e 'a b c' | awk '{ print $2 }'
b
```

`ADDRESS` に処理する行の条件を指定する。
`ADDRESS` を省略した場合はすべての行を対象にする。

```sh
> echo -e 'a\nb\nc' | awk '/b/ { print $1 }'
b
```

`command` は複数記述でき、各 `command` はセミコロンまたは改行で区切る。

`#` で始まる行はコメントとして扱う。

### ADDRESS

`ADDRESS` には処理する行の条件を指定する。

以下のパターンを `&&`, `||`. `!` , 3項演算子で組み合わせることができる。

#### 条件マッチ

`ADDRESS` に下記の形式で条件を指定する。

```
Value1 Op Value2 { COMMAND }
```

`Value` はフィールド(`$1`, `$2`...)など変数や値を指定する。
`Op` は関係演算子を指定する。

```sh
> echo -e 'a\nb\nc' | awk 'NR == 2 { print $1 }'
b
```

#### パターンマッチ

`ADDRESS` に下記の形式で条件を指定する。

```
[Target] [[!]~] /[PATTERN]/ { COMMAND }
```

`Target` はフィールド(`$1`, `$2`...)を指定する。省略した場合は行全体がマッチング対象となる。
`PATTERN` は正規表現を指定する。

```sh
> echo -e 'a1 a2 a3\nb1 b2 b3\nc1 c2 c3' | awk '/b/ { print $2 }'
b2
```

`!` を付けると真偽が反転する。

```sh
> echo -e 'a1 a2 a3\nb1 b2 b3\nc1 c2 c3' | awk '$2 !~ /b2/ { print $3 }'
a3
c3
```

#### 範囲マッチ

`ADDRESS` に下記の形式で条件を指定する。

```
PATTERN1,PATTERN2 { COMMAND }
```

`PATTERN1` と `PATTERN2` に一致する範囲が処理の対象となる。

```sh
> echo -e 'a1 a2 a3\nb1 b2 b3\nc1 c2 c3\nd1 d2 d3' | awk '/b/,/c/ { print $2 }'
b2
c2
```

`PATTERN` には条件、パターンマッチが指定できる。

```sh
> echo -e 'a1 a2 a3\nb1 b2 b3\nc1 c2 c3\nd1 d2 d3' | awk 'NR == 2,/c/ { print $2 }'
b2
c2
```

### デミリタ

デミリタは `-F` オプションか `FS` 変数で変更できる。
デミリタは正規表現が使用できる。

```sh
> echo 'a,b,c' | awk -F, '{ print $2 }'
b
```

```sh
> echo 'a,b,c' | awk 'BEGIN { FS = "," } { print $2 }'
b
```

```sh
> echo 'a,b,c' | awk -v FS=, '{ print $2 }'
b
```

```sh
> echo 'a,b,c' | awk '{ print $2 }' FS=,
b
```

### 変数

変数を定義する。

```sh
> echo 'a b c' | awk '
BEGIN { x = 1; y = 2 }
{ print $(x + y) }'
c
```

#### ビルトイン変数

| 変数名 | 説明 |
| ------ | ---- |
| FS | 入力のフィールドセパレータ |
| OFS | 出力のフィールドセパレータ |
| RS | 入力のレコードセパレータ |
| ORS | 出力レコードドセパレータ |
| NF | フィールド数 |
| NR | レコードの行数 |
| FNR | 入力ファイルごとのレコードの行数 |
| FILENAME | 入力ファイル名 |
| CONVFMT | 数字を文字列に変換するときの形式(`%.6g`) |
| ARGC | 引数の数 |
| ARGV | 引数の配列 |
| ENVIRON | 環境変数の配列 |

### 制御文

`if`, `while`, `do-while`, `for` 文が使用できる。

#### `if` 文

`if` 文で条件分岐する。

```sh
>  echo -e '1\n2\n3' | awk '{ if ($1 > 1) { print $1 } }'
2
3
```

#### `while` 文

`while` 文で条件が一致するあいだ繰り返す。

```sh
>  echo -e '1\n2\n3' | awk '{ while ($1 < 3) { print $1; break } }'
1
2
```

#### `do-while` 文

`do-while` 文で条件が一致するあいだ繰り返す。

```sh
>  echo -e '1\n2\n3' | awk '{ do { print $1; i++ } while (i < 2) }'
1
1
2
3
```

#### `for` 文

`for` 文で繰り返す。

```sh
> echo -e '1\n2\n3' | awk '{ for (i = 0; i < 2; i++) { print $1 } }'
1
1
2
2
3
3
```

### 関数定義

`function` で関数を定義できる。

```
function NAME(ARG1, ARG0, ...) {
    COMMAND
}
```

```sh
echo 'a' | awk '
function hello(arg) { return "Hello, " arg }
{ print hello("World!") }'
Hello, World!
```

引数はローカルスコープ、関数内の変数はグローバルスコープになる。

### ファイルの読み込み

`getline` で新しい入力行を読み込める。
`getline` は入力した行で `$0` を更新してスクリプトを継続する。

次の行を読み込む。

```sh
> echo -e 'a\nb\nc' | awk '{
    print "Before", $1
    getline
    print "After ", $1
}'
Before a
After  b
Before c
After  c
```

ファイルから読み取る。

```sh
> echo -e 'd\ne\nf' > a.txt
> echo -e 'a\nb\nc' | awk '{
    print "Before", $1
    getline < "a.txt"
    print "After ", $1
}'
Before a
After  d
Before b
After  e
Before c
After  f
```

標準入力から読み取る(パイプ側が `getline` の入力になる)。

```sh
> echo -e 'a\nb\nc' | awk '{
    print "Before", $1
    getline < "-"
    print "After ", $1
}' <(echo -e 'd\ne\nf')
Before d
After  a
Before e
After  b
Before f
After  c
```

変数に代入する。

```sh
> echo -e 'a\nb\nc' | awk '{
    print "Before", $1
    getline line
    print "After ", $1
    print "Line  ", line
}'
Before a
After  a
Line   b
Before c
After  c
Line   b
```

### パイプ

コマンドを実行してパイプして入力として取得する。
パイプを閉じるときは `close` を使用する。パイプを開くときと同じ文字列を指定する。

```sh
> echo 'a' | awk ' BEGIN{
    "uname -a" | getline
    print $3
    close("uname -a")
}'
6.2.0-36-generic
```

## ```sed``` コマンド

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

### ADDRESS

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

### 正規表現

基本正規表現(BRE)のメタ文字は `.`, `*`, `\`, `^`, `$`, `[`。
拡張正規表現(`-E, -r` オプション/ERE)のメタ文字は基本正規表現のメタ文字に追加して `+`, `?`, `{`, `(`, `)`, `|`。

### ラベル(:)

命令に `:LABEL` の形式で指定する。

`b`, `t`, `T` の分岐先を設定する。

### コメント(#)

`#` 以降は行末までコメントとして扱う。

```sh
> echo a | sed -e 's/a/b/# a->b'
b
```

### 行数(=)

命令に `[ADDRESS]=` の形式で指定する。

```sh
> echo -e 'a\nb\nc' | sed -ne '/b/='
2
```

### 追加(a)

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

### 挿入(i)

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

### 終了(q/Q)

命令に `[ADDRESS]q[EXITCODE]` の形式で指定する。

```sh
> echo a | sed -e 'q1'; echo $?
a
1
```

### ファイルの読み込み(r)

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

### ファイルの読み込み(R)

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

### 分岐(b)

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

### 変更(c)

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

### 削除(d)

命令に `[ADDRESS]d` の形式で指定する。
パターンスペースを削除する。
パターンスペースが空になるとスクリプトが完了し次の入力行をパターンスペースに読み込まれる。

```sh
> echo -e 'a\nb' | sed -e '1d'
b
```

### 削除(D)

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

### 保存(h) / リストア(g)

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

### 保存(H) / リストア(G)

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


### リスト(l)

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

### 次の行を読む(n)

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

### 次の行を読む(N)

命令に `[ADDRESS]N` の形式で指定する。
パターンスペースに次の行を追加する。

```sh
> echo -e 'a\nb\nc' | sed -e '/a/{
    N
    d
}'
c
```

### 表示(p)

命令に `[ADDRESS]p` の形式で指定する。
入力を表示する。

```sh
> echo -e 'a\nb\nc' | sed -n -e 'p'
a
b
c
```

### 表示(P)

命令に `[ADDRESS]P` の形式で指定する。
パターンスペース内の先頭行を表示する。

```sh
> echo -e 'a\nb\nc' | sed -n -e '/a/{
    N
    P
}'
a
```


### 置換(s)

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

### 分岐(t/T)

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

### ファイルに書き込む(w)

命令に `ADDRESSw FILEPATH` の形式で指定する。

```sh
> echo -e 'a\nb\nc' | sed -n -e '/a/w a.txt'
> cat a.txt
a
```

### ファイルに書き込む(W)

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

### 交換(x)

パターンスペースとホールドスペースを交換する。

### 変換(y)

命令に `[ADDRESS]y/BEFORE/AFTER/` の形式で指定する。
BEFORE と AFTER を 1:1 で変換する。

```sh
> echo -e 'abc' | sed -e 'y/ab/AB/'
ABc
```

## ```tail``` コマンド

N 行目以降を取得する。

```sh
tail -n +N
```

## ```xargs``` コマンド

標準入力を空白、タブ、改行で複数に分割してコマンドを複数回実行する。

コマンドを指定しない場合は `echo` となる。

```sh
> echo 'a b c' | xargs
a b c
```

分割数を指定しない場合は既定値の収まる範囲で自動で分割される。

```sh
> echo 'a b c' | xargs -n 2 echo
a b
c
```

既定値は `--show-limits` で表示できる。

```sh
> xargs --show-limits
環境変数が 2553 バイトを占めます
POSIX の引数の長さ上限 (このシステム): 2092551
POSIX の最小の引数の長さの上限 (すべてのシステム): 4096
実際に使用できるコマンド長の最大値: 2089998
実際に使用しているコマンドバッファの大きさ: 131072
Maximum parallelism (--max-procs must be no greater): 2147483647
...
```

引数はコマンドの末尾に追加される。

```sh
> echo -e 'a\nb\nc' | xargs -L 2 echo args=
args= a b
args= c
```

引数の指定箇所は `-i` (`-L 1` となる)で指定する。`-i` は `-l`, `-n` と同時に指定できない。

```sh
> echo -e 'a\nb\nc' | xargs -i echo args={}
args=a
args=b
args=c
```

デミリタは `-d` で指定する。末尾に改行コードが追加される。

```sh
> echo 'a,b,c' | xargs -d , -i echo args={}
args=a
args=b
args=c

```

引数の数は個数(`-n`)、行数(`-l/-L`)で制限する。

```sh
> echo -e 'a b\nc d' | xargs
a b c d
> echo -e 'a b\nc d' | xargs -L 1
a b
c d
> echo -e 'a b\nc d' | xargs -L 2
a b c d
> echo -e 'a b\nc d' | xargs -n 1
a
b
c
d
> echo -e 'a b\nc d' | xargs -n 3
a b c
d
> echo -e 'a \nb\nc' | xargs -L 1 # -L は末尾に空白があると継続する。
a b
c
> echo -e ' a\n b\n c' | xargs -L 1 # -L は先頭に空白を無視する。
a
b
c
```

空白とタブはクォートでエスケープする。

```sh
> echo "'a b' c" | xargs -n 1
a b
c
> echo '"a b" c' | xargs -n 1
a b
c
```

改行はクォートでエスケープできない。

```sh
> echo -e "'a\nb' c" | xargs -n 1
xargs: シングルクオートが一致しません。デフォルトでは -O オプションを指定しない限り xargs でクォートは特別な意味を持ちます
> echo -e '"a\nb" c' | xargs -n 1
xargs: ダブルクオートが一致しません。デフォルトでは -O オプションを指定しない限り xargs でクォートは特別な意味を持ちます
```

クォートはバックスラッシュでエスケープする。

```sh
> echo -e 'a\n\"b\nc' | xargs -n 1
a
"b
c
```

引数はそのまま挿入される。

```sh
> echo '; touch a.txt' | xargs -i sh -c 'echo a {}'
a
> ls
a.txt
```
