# `awk` コマンド

入力行をレコード、それをデミリタで区切った値をフィールドとして処理する。
`BEGIN`, `END` ブロックは省略できる。

```text
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

## ADDRESS

`ADDRESS` には処理する行の条件を指定する。

以下のパターンを `&&`, `||`. `!` , 3項演算子で組み合わせることができる。

### 条件マッチ

`ADDRESS` に下記の形式で条件を指定する。

```text
Value1 Op Value2 { COMMAND }
```

`Value` はフィールド(`$1`, `$2`...)など変数や値を指定する。
`Op` は関係演算子を指定する。

```sh
> echo -e 'a\nb\nc' | awk 'NR == 2 { print $1 }'
b
```

### パターンマッチ

`ADDRESS` に下記の形式で条件を指定する。

```text
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

### 範囲マッチ

`ADDRESS` に下記の形式で条件を指定する。

```text
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

## デミリタ

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

## 変数

変数を定義する。

```sh
> echo 'a b c' | awk '
BEGIN { x = 1; y = 2 }
{ print $(x + y) }'
c
```

### ビルトイン変数

| 変数名   | 説明                                     |
| -------- | ---------------------------------------- |
| FS       | 入力のフィールドセパレータ               |
| OFS      | 出力のフィールドセパレータ               |
| RS       | 入力のレコードセパレータ                 |
| ORS      | 出力レコードドセパレータ                 |
| NF       | フィールド数                             |
| NR       | レコードの行数                           |
| FNR      | 入力ファイルごとのレコードの行数         |
| FILENAME | 入力ファイル名                           |
| CONVFMT  | 数字を文字列に変換するときの形式(`%.6g`) |
| ARGC     | 引数の数                                 |
| ARGV     | 引数の配列                               |
| ENVIRON  | 環境変数の配列                           |

## 制御文

`if`, `while`, `do-while`, `for` 文が使用できる。

### `if` 文

`if` 文で条件分岐する。

```sh
>  echo -e '1\n2\n3' | awk '{ if ($1 > 1) { print $1 } }'
2
3
```

### `while` 文

`while` 文で条件が一致するあいだ繰り返す。

```sh
>  echo -e '1\n2\n3' | awk '{ while ($1 < 3) { print $1; break } }'
1
2
```

### `do-while` 文

`do-while` 文で条件が一致するあいだ繰り返す。

```sh
>  echo -e '1\n2\n3' | awk '{ do { print $1; i++ } while (i < 2) }'
1
1
2
3
```

### `for` 文

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

## 関数定義

`function` で関数を定義できる。

```text
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

## ファイルの読み込み

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

## パイプ

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
