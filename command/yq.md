# `yq` コマンド

YAML ファイルをクエリ、編集する。

## インストール

GitHub からダウンロードする。

```sh
mkdir -p "${HOME}/.local/bin"
curl -sSfL -o - https://github.com/mikefarah/yq/releases/download/v4.45.1/yq_linux_amd64.tar.gz | \
    tar -zxf - -O "./yq_linux_amd64" > "${HOME}/.local/bin/yq"
chmod +x "${HOME}/.local/bin/yq"
```

## 式

### ドット

取り出す値の `key` を `.` 区切りで指定する。

```sh
> yq '.a' <(cat <<EOF
a: 1
EOF
)

1
```

`key` にドットが含まれる場合は `"` で囲む。

```sh
> yq '.".a"' <(cat <<EOF
.a: 1
EOF
)

1
```

配列の場合にオブジェクトを期待して `key?` としてもエラーにならない。

```sh
> yq '.a?' <(cat <<EOF
- 1
- 2
- 3
EOF
)

```

### 配列

`[ ]` で配列の要素を取り出す。

```sh
> yq '.a[1]' <(cat <<EOF
a:
- 1
- 2
- 3
EOF
)

2
```

負の数を指定すると逆順にインデックスを指定できる。

```sh
> yq '.a[-1]' <(cat <<EOF
a:
- 1
- 2
- 3
EOF
)

3
```

範囲を指定して取り出す。

```{warning}
TODO: 期待した結果と異なる。
```

```sh
> yq '.a[1:2]' <(cat <<EOF
a:
- 1
- 2
- 3
EOF
)

!!map
- - 1
  - 2
  - 3
```

```sh
> yq '.a.[1:2]' <(cat <<EOF
a:
- 1
- 2
- 3
EOF
)

- 2
```

すべて取り出す。

```sh
> yq '.a[]' <(cat <<EOF
a:
- 1
- 2
- 3
EOF
)

1
2
3
```

### パイプ

`|` で式を繋いで値を取り出す。

```sh
> yq '.a[] | .b.c' <(cat <<EOF
a:
- b:
    c: 1
- b:
    c: 2
- b:
    c: 3
EOF
)

1
2
3
```

### 真偽値

値がnull, false の場合に `false` として扱われる。
0, 空文字、空配列、空マップは `true` として扱われる。

```sh
> yq --null-input '~ | not, false | not, 0 | not, "" | not, [] | not, {} | not'

true
true
false
false
false
false
```

### 既定値

値が未定義または false 扱いの場合に既定値を指定する。

```sh
> yq '.a // 1' <(cat <<EOF
a: ~
EOF
)

1
```

### 再帰

`..` で再帰的に value をクエリする。

```sh
> yq '..' <(cat <<EOF
a:
  b:
    c: 1
EOF
)

a:
  b:
    c: 1
b:
  c: 1
c: 1
1
```

`...` で再帰的に key,  value をクエリする。

```sh
> yq '...' <(cat <<EOF
a:
  b:
    c: 1
EOF
)

a:
  b:
    c: 1
a
b:
  c: 1
b
c: 1
c
1
```

## 演算

### 算術演算

数値を加算する。

```sh
> yq '.a += 1' <(cat <<EOF
a: 1
EOF
)

a: 2
```

値を取り出して加算する。

```sh
> yq '.a = .a + .b' <(cat <<EOF
a: 1
b: 2
EOF
)

a: 3
b: 2
```

### boolean 演算

`and` 演算する。

```sh
> yq '.a and true' <(cat <<EOF
a: false
EOF
)

false
```

`or` 演算する。

```sh
> yq '.a or true' <(cat <<EOF
a: false
EOF
)

true
```

`not` 演算する。

```sh
> yq '.a | not' <(cat <<EOF
a: false
EOF
)

true
```

### 比較演算子

数値を比較する。

```sh
> yq '.a < 2' <(cat <<EOF
a: 1
EOF
)

true
```

文字列を比較する。

```sh
> yq '.a < "bcd"' <(cat <<EOF
a: abc
EOF
)

true
```

時間を比較する。

```sh
> yq '.a < "2025-03-16T01:02:04Z"' <(cat <<EOF
a: 2025-03-16T01:02:03Z
EOF
)

true
```

### 文字列演算

#### 連結

文字列を連結する。

```sh
> yq '.a += "c"' <(cat <<EOF
a: b
EOF
)

a: bc
```

配列を連携する。

```sh
> yq 'join(",")' <(cat <<EOF
- a
- b
- c
EOF
)

a,b,c
```

#### 分割

文字列を分割する。

```sh
> yq '.a / "c"' <(cat <<EOF
a: bcd
EOF
)

- b
- d
```

指定した文字で分割する。

```sh
> yq '.a | split(",")' <(cat <<EOF
a: a,b,c
EOF
)

- a
- b
- c
```

#### 大文字 / 小文字

大文字に変換する。

```sh
> yq --null-input '"a" | upcase'

A
```

小文字に変換する。

```sh
> yq --null-input '"A" | downcase'

a
```

#### 空白除去

空白を削除する。

```sh
> yq '.[] | trim' <(cat <<EOF
- " a"
- "b "
- " c "
EOF
)

a
b
c
```

#### 正規表現

正規表現は Go の表記に従う。

```sh
> yq '.a | test("(?i)hello")' <(cat <<EOF
a: Hello, world
EOF
)

true
```

一致した情報を返却する。

```sh
> yq '.a | match("world")' <(cat <<EOF
a: hello, world
EOF
)

string: world
offset: 7
length: 5
captures: []
```

キャプチャを取得する。

```sh
> yq '.a | capture("(?<a>world)")' <(cat <<EOF
a: hello, world
EOF
)

a: world
```

正規表現を使用して文字列を置換する。

```sh
> yq '.a | sub("o", "O")' <(cat <<EOF
a: hello, world
EOF
)

hellO, wOrld
```

マッチした文字列を利用する。

```sh
> yq '.a | sub("(o)", "${1},")' <(cat <<EOF
a: hello, world
EOF
)

hello,, wo,rld
```

#### 文字列補間

文字列を式の評価結果で補間する。

```sh
> yq '.b = "\(.a) world"' <(cat <<EOF
a: hello
EOF
)

a: hello
b: hello world
```

### 時間演算

時間を加算する。

```sh
> yq '.a += "1h5m"' <(cat <<EOF
a: 2025-03-16T01:02:03.004Z
EOF
)

a: 2025-03-16T02:07:03Z
```

### 配列演算

配列に要素を追加する。

```sh
> yq '.a += 2' <(cat <<EOF
a:
- 1
EOF
)

a:
  - 1
  - 2
```

配列を連携する。
`*` に `+` フラグを付けると同じ操作となる。

```sh
> yq '.a = .a + .b' <(cat <<EOF
a:
- x:
  - 1
b:
- x:
  - 2
EOF
)

a:
  - x:
      - 1
  - x:
      - 2
b:
  - x:
      - 2

> yq '.a = .a *+ .b' <(cat <<EOF
a:
- x:
  - 1
b:
- x:
  - 2
EOF
)

a:
  - x:
      - 1
  - x:
      - 2
b:
  - x:
      - 2
```

子要素もマージする。

```sh
> yq '.a = .a *+d .b' <(cat <<EOF
a:
- x:
  - 1
b:
- x:
  - 2
EOF
)

a:
  - x:
      - 2
      - 2
  - x:
      - 2
b:
  - x:
      - 2
```

配列から一致する要素を削除する。

```sh
> yq '.a = .a - .b' <(cat <<EOF
a:
- 1
- 2
b:
- 2
EOF
)

a:
  - 1
b:
  - 2
```

### マップ演算

マップをマージする。

```sh
> yq '.a = .a + .b' <(cat <<EOF
a:
  c: 1
b:
  d: 2
EOF
)

a:
  c: 1
  d: 2
b:
  d: 2

> yq '.a = .a * .b' <(cat <<EOF
a:
  c: 1
b:
  d: 2
EOF
)

a:
  c: 1
  d: 2
b:
  d: 2
```

存在する項目のみマージする。

```sh
> yq '.a = .a *? .b' <(cat <<EOF
a:
  c: 1
b:
  c: 2
  d: 3
EOF
)

a:
  c: 2
b:
  c: 2
  d: 3
```

存在しない項目のみマージする。

```sh
> yq '.a = .a *n .b' <(cat <<EOF
a:
  c: 1
b:
  c: 2
  d: 3
EOF
)

a:
  c: 1
  d: 3
b:
  c: 2
  d: 3
```

## 関数

### `all`

配列の要素に false がなければ true を返却する。

```sh
> yq '.a | all, .b | all, .c | all' <(cat <<EOF
a:
- true
b:
- false
c: []
EOF
)

true
false
true
```

### `all_c`

引数に指定した式が false にならなければ true を返却する。

```sh
> yq '.a | all_c(. == 1), .b | all_c(. == 1)' <(cat <<EOF
a:
- 1
b:
- 2
EOF
)

true
false
```

### `any`

配列の要素に true があれば true を返却する。

```sh
> yq '.a | any, .b | any, .c | any' <(cat <<EOF
a:
- true
b:
- false
c: []
EOF
)

true
false
false
```

### `any_c`

引数に指定した式が true になれば true を返却する。

```sh
> yq '.a | any_c(. == 1), .b | any_c(. == 1)' <(cat <<EOF
a:
- 1
b:
- 2
EOF
)

true
false
```

### `array_to_map`

配列をインデックス付きのマップに変換する。

```sh
> yq '.a |= array_to_map' <(cat <<EOF
a:
- b
- c
EOF
)

a:
  0: b
  1: c
```

### `column`

値がマッチする箇所のカラム数を返却する。文頭は 1 になる。
それぞれ `1`, `2`, `3` がマッチする箇所の先頭からのカラム数を返却する。

```sh
> yq '.a | column, .b | column, .c | column' <(cat <<EOF
a: 1
b:  2
c:   3
EOF
)

4
5
6
```

### `contains`

引数に指定した値またはオブジェクトが一致する場合に true を返却する。

配列の要素に部分一致する場合に true を返却する。

```sh
> yq '.a | contains(["de"])' <(cat <<EOF
a:
- abc
- def
EOF
)

true
```

文字列に部分一致する場合に true を返却する。

```sh
> yq '.a[] | contains("de")' <(cat <<EOF
a:
- abc
- def
EOF
)

false
true
```

### `del`

引数に一致する項目を削除する。

```sh
> yq 'del(.a)' <(cat <<EOF
a: 1
b: 2
EOF
)

b: 2
```

配列から要素を削除する。

```sh
> yq 'del(.a[1])' <(cat <<EOF
a:
- 1
- 2
- 3
EOF
)

a:
  - 1
  - 3
```

### `document_index` / `di`

ドキュメントのインデックスを取得する。

```sh
> yq '.a | di' <(cat <<EOF
a: 1
---
a: 2
---
a: 3
EOF
)

0
---
1
---
2
```

### `delpaths`

第一引数の配列に一致する項目を削除する。

```sh
> yq 'delpaths([["a", "b", "c"]])' <(cat <<EOF
a:
  b:
    c: 3
EOF
)

a:
  b: {}
```

### `env`

環境変数の値を取得する。

```sh
> yq --null-input 'env(HOME)'

/root
```

### `envsubst`

`envsubst` と同等の機能となる。

```sh
> yq --null-input '"${HOME}" | envsubst'

/root
```

環境変数がない場合にエラーにする。

```sh
> yq --null-input '"${UNKNOWN}" | envsubst(nu)'

Error: variable ${UNKNOWN} not set
```

環境変数が空の場合にエラーにする。

```sh
> EMPTY= yq --null-input '"${EMPTY}" | envsubst(ne)'

Error: variable ${EMPTY} set but empty
```

エラーがある場合に中断する。

```sh
> EMPTY= yq --null-input '"${UNKNOWN} ${EMPTY}" | envsubst(nu, ne, ff)'

Error: variable ${UNKNOWN} not set
```

### `eval`

引数の式を評価する。

```sh
> yq 'eval(.exp)' <(cat <<EOF
exp: .a
a: 1
EOF
)

1
```

### `filename`

ファイル名を取得する。

```sh
> yq 'filename' <(cat <<EOF
a: 1
EOF
)

/dev/fd/63
```

### `file_index` / `fi`

ファイル名を取得する。

```sh
> yq 'fi' <(cat <<EOF
a: 1
EOF
) <(cat <<EOF
b: 1
EOF
)

0
1
```

### `filter`

引数の条件に一致する項目を取り出す。

```sh
> yq 'filter(. < 2)' <(cat <<EOF
a: 1
b: 2
EOF
)

- 1
```

### `flatten`

ネストした配列を平坦化する。

```sh
> yq 'flatten' <(cat <<EOF
-
  - a
  - b
-
  - c
  - d
EOF
)

- a
- b
- c
- d
```

### `foot_comment`

後行のコメントを取得する。
コメントの前に空行がないと取得できない。

```sh
> yq '. | foot_comment' <(cat <<EOF
a: 1
b: 2

# comment#1
EOF
)

comment#1
```

### `format_datetime`

```{warning}
TODO
```

### `from_entries`

key-value のマップから変換する。

```sh
> yq '. | from_entries' <(cat <<EOF
- key: a
  value: 1
- key: b
  value: 2
EOF
)

a: 1
b: 2
```

### `from_unix`

タイムスタンプを変換する。

```sh
> yq --null-input '1675301929 | from_unix'

2023-02-02T10:38:49+09:00
```

### `group_by`

引数に指定したキーの値でグルーピングする。

```sh
> yq 'group_by(.a)' <(cat <<EOF
- a: 1
  b: 1
- a: 1
  b: 2
- a: 2
  b: 3
- a: 3
  b: 4
EOF
)

- - a: 1
    b: 1
  - a: 1
    b: 2
- - a: 2
    b: 3
- - a: 3
    b: 4
```

### `has`

引数に指定した値のキーがある場合に true を返却する。

```sh
> yq 'has("a")' <(cat <<EOF
a: 1
b: 2
EOF
)

true
```

引数に指定した値の配列のインデックスがある場合に true を返却する。

```sh
> yq 'has(1)' <(cat <<EOF
- 1
- 2
EOF
)

true
```

### `head_comment`

前行のコメントを取得する。

```sh
> yq '.b | key | head_comment' <(cat <<EOF
a: 1
# comment#1
b: 2
EOF
)

comment#1
```

前行にコメントを追加する。

```sh
> yq '(.b | key) head_comment="comment#1"' <(cat <<EOF
a: 1
b: 2
EOF
)

a: 1
# comment#1
b: 2
```

### `ireduce`

```{warning}
TODO:
```

### `key`

キー名を取得する。

```sh
> yq '.a | key' <(cat <<EOF
a: 1
EOF
)

a
```

配列インデックスを取得する。

```sh
> yq '.a[1] | key' <(cat <<EOF
a:
- b
- c
EOF
)

1
```

### `keys`

キー名を取得する。

```sh
> yq '.a | keys' <(cat <<EOF
a:
  b: 1
  c: 1
EOF
)

- b
- c
```

配列インデックスを取得する。

```sh
> yq '.a | keys' <(cat <<EOF
a:
- b
- c
EOF
)

- 0
- 1
```

### `kind`

値の種類を取得する。
`scalar`, `seq`, `map` のいづれかを返却する。

```sh
> yq --null-input '[0, "a", true, [], {}, null] | .. | kind'

seq
scalar
scalar
scalar
seq
map
scalar
```

### `load`

```{warning}
TODO
```

### `line`

行数を返却する。

```sh
> yq '.b | line' <(cat <<EOF
a: 1
b: 2
EOF
)

2
```

### `length`

文字列の長さ、配列の要素数、マップの要素数を返却する。

```sh
> yq --null-input '[0, "a", true, [], {}, null] | .. | length'

6
1
1
4
0
0
0
```

### `line_comment`

行のコメントを取得する。

```sh
> yq '.a | line_comment' <(cat <<EOF
a: 1 # comment#1
EOF
)

comment#1
```

行にコメントを追加する。

```sh
> yq '.a line_comment="comment#1"' <(cat <<EOF
a: 1
EOF
)

a: 1 # comment#1
```

### `max`

最大値を返却する。

```sh
> yq --null-input '[4, 2, 5, 9, 1] | max'

9
```

### `map`

配列の各要素に引数の式を評価する。

```sh
> yq --null-input '[4, 2, 5, 9, 1] | map(. * 2)'

- 8
- 4
- 10
- 18
- 2
```

### `map_values`

配列の各要素に引数の式を評価する。

```sh
> yq 'map_values(. * 2)' <(cat <<EOF
a: 1
b: 2
EOF
)

a: 2
b: 4
```

### `min`

最小値を返却する。

```sh
> yq --null-input '[4, 2, 5, 9, 1] | min'

1
```

### `now`

現在の時刻を出力する。

```sh
> yq --null-input 'now'

2025-03-16T14:31:31+09:00
```

### `omit`

指定した値の要素を取り除く。

```sh
> yq 'omit(["a"])' <(cat <<EOF
a: 1
b: 2
EOF
)

b: 2
```

### `parent`

親要素を返却する。

```sh
> yq '.a.b | parent' <(cat <<EOF
a:
  b: 2
EOF
)

b: 2
```

引数に深さを指定する。

```sh
> yq '.a.b.c | parent(2)' <(cat <<EOF
a:
  b:
    c: 3
EOF
)

b:
  c: 3
```

### `path`

式が一致したキーの配列を取得する。

```sh
> yq '.a.b.c | path' <(cat <<EOF
a:
  b:
    c: 3
EOF
)

- a
- b
- c
```

### `pick`

引数に一致する項目のみ取得する。

```sh
> yq 'pick(["a"])' <(cat <<EOF
a: 1
b: 2
EOF
)

a: 1
```

配列から指定した要素以外を削除する。

```sh
> yq 'pick([0, 2])' <(cat <<EOF
- 1
- 2
- 3
EOF
)

- 1
- 3
```

### `pivot`

行列の行と列を入れ替える

```sh
> yq 'pivot' <(cat <<EOF
-
  - 1
-
  - 2
-
  - 3
EOF
)

- - 1
  - 2
  - 3
```

### `reverse`

配列を逆順にする。

```sh
> yq 'reverse' <(cat <<EOF
- 1
- 2
- 3
EOF
)

- 3
- 2
- 1
```

### `select`

引数に指定した式に一致する項目を取得する。

```sh
> yq '.[] | select(.a == 2)' <(cat <<EOF
- a: 1
- a: 2
- a: 3
EOF
)

a: 2
```

比較にワイルドカードが使用できる。

```sh
> yq '.[] | select(. == "*e*")' <(cat <<EOF
- abc
- def
- ghi
EOF
)

def
```

比較に正規表現が使用できる。

```sh
> yq '.[] | select(test("^.e.$"))' <(cat <<EOF
- abc
- def
- ghi
EOF
)

def
```

### `setpath`

第一引数の配列に一致する項目に第2引数に指定した値で更新する。

```sh
> yq 'setpath(["a", "b", "c"]; 4)' <(cat <<EOF
a:
  b:
    c: 3
EOF
)

a:
  b:
    c: 4
```

### `shuffle`

配列をシャッフルする。

```sh
> yq 'shuffle' <(cat <<EOF
- 1
- 2
- 3
EOF
)

- 1
- 3
- 2
```

### `sort`

配列を value でソートする。

```sh
> yq 'sort' <(cat <<EOF
- 2
- 1
- 3
EOF
)

- 1
- 2
- 3
```

### `sort_by`

引数に指定した式で配列をソートする。

```sh
> yq 'sort_by(.)' <(cat <<EOF
- 2
- 1
- 3
EOF
)

- 1
- 2
- 3
```

複数をキーにできる。

```sh
> yq 'sort_by(.a, .b)' <(cat <<EOF
- a: 2
  b: 2
- a: 2
  b: 1
- a: 1
  b: 2
EOF
)

- a: 1
  b: 2
- a: 2
  b: 1
- a: 2
  b: 2
```

### `sort_keys`

引数に指定した式の項目を key でソートする。

```sh
> yq 'sort_keys(.)' <(cat <<EOF
b: 2
a: 1
c: 3
EOF
)

a: 1
b: 2
c: 3
```

### `split_doc`

項目ごとにドキュメントに分割する。

```sh
> yq '.[] | split_doc' <(cat <<EOF
- 1
- 2
- 3
EOF
)

1
---
2
---
3
```

### `strenv`

環境変数の文字列を取得する。

```sh
> yq --null-input 'strenv(HOME)'

/root
```

### `tag`

項目のタグを取得する。

```sh
> yq '.[] | tag' <(cat <<EOF
- 1
- a
- true
- [1, a]
- {1: a}
EOF
)

!!int
!!str
!!bool
!!seq
!!map
```

タグを指定する。

```sh
> yq '.a tag="!!mytag"' <(cat <<EOF
a: 1
EOF
)

a: !!mytag 1
```

タグを指定して型を変換できる。

```sh
> yq '.a tag="!!str"' <(cat <<EOF
a: 1
EOF
)

a: "1"
```

### `to_entries`

key-value のマップに変換する。

```sh
> yq '. | to_entries' <(cat <<EOF
a: 1
b: 2
EOF
)

- key: a
  value: 1
- key: b
  value: 2
```

### `to_number`

値を数値に変換する。

```sh
> yq '.[] | to_number' <(cat <<EOF
- "1"
- "1.1"
EOF
)

1
1.1
```

### `to_string`

値を文字列に変換する。

```sh
> yq '.[] |= to_string' <(cat <<EOF
- 1
- a
- true
- null
- [1, 2]
- b: c
EOF
)

- "1"
- a
- "true"
- "null"
- "[1, 2]"
- "b: c"
```

### `to_unix`

タイムスタンプを変換する。

```sh
> yq --null-input 'now | to_unix'

1742103244
```

### `tz`

タイムゾーンを変換する。

```sh
> yq '.a | tz("Asia/Tokyo")' <(cat <<EOF
a: 2025-03-16T01:02:03.004Z
EOF
)

2025-03-16T10:02:03+09:00
```

### `unique`

重複した値を除外する。

```sh
> yq 'unique' <(cat <<EOF
- 3
- 1
- 3
- 2
EOF
)

- 3
- 1
- 2
```

### `unique_by`

引数に指定した式を評価結果で除外する。

```sh
> yq 'unique_by(tag)' <(cat <<EOF
- 3
- a
- 2
- []
- b
EOF
)

- 3
- a
- []
```

### `with`

第一引数に指定した式をスコープにして第2引数の式を評価する。

```sh
> yq 'with(.a.b; .c[1] *= 2)' <(cat <<EOF
a:
  b:
    c:
      - 1
      - 2
      - 3
EOF
)

a:
  b:
    c:
      - 1
      - 4
      - 3
```

### `with_entries`

引数に指定した値をキーにする。

```sh
> yq '. | with_entries(.key |= "KEY_" + .)' <(cat <<EOF
a: 1
b: 2
EOF
)

KEY_a: 1
KEY_b: 2
```

### `with_dtf`

```{warning}
TODO
```

## 生成

入力を空にして新しく yaml 出力する。

```sh
> yq --null-input '.a = 1 | .b = 2'

a: 1
b: 2
```

配列を出力する。

```sh
> yq '[.a, .b]' <(cat <<EOF
a: 1
b: 2
EOF
)

- 1
- 2
```

マップを出力する。

```sh
> yq '{"c": .a, "d": .b}' <(cat <<EOF
a: 1
b: 2
EOF
)

c: 1
d: 2
```

## フォーマット変換

### CSV

配列を CSV に変換する。

```sh
> yq -o=csv <(cat <<EOF
- a: 1
  b: 2
- a: 3
  b: 4
- a: 5
  b: 6
EOF
)

a,b
1,2
3,4
5,6
```

CSV と配列に変換する。

```sh
> yq -p=csv -o=yaml <(cat <<EOF
a,b
1,2
3,4
5,6
EOF
)

- a: 1
  b: 2
- a: 3
  b: 4
- a: 5
  b: 6
```

### TSV

配列を TSV に変換する。

```sh
> yq -o=tsv <(cat <<EOF
- a: 1
  b: 2
- a: 3
  b: 4
- a: 5
  b: 6
EOF
)

a       b
1       2
3       4
5       6
```

### JSON

JSON に変換する。

```sh
> yq -o=json <(cat <<EOF
a: 1
b: c
d:
  - 1
  - 2
e:
  f: 1
EOF
)

{
  "a": 1,
  "b": "c",
  "d": [
    1,
    2
  ],
  "e": {
    "f": 1
  }
}
```

JSON から YAML に変換する。

```sh
> yq -p=json -o=yaml <(cat <<EOF
{
  "a": 1,
  "b": "c",
  "d": [
    1,
    2
  ],
  "e": {
    "f": 1
  }
}
EOF
)

a: 1
b: c
d:
  - 1
  - 2
e:
  f: 1
```

### Properties

ドット区切りの文字列のキーと値に変換する。

```sh
> yq -o=props <(cat <<EOF
a: 1
b: c
d:
  - 1
  - 2
e:
  f: 1
EOF
)

a = 1
b = c
d.0 = 1
d.1 = 2
e.f = 1
```

ドット区切りの文字列のキーと値から YAML に変換する。

```sh
> yq -p=props -o=yaml <(cat <<EOF
a = 1
b = c
d.0 = 1
d.1 = 2
e.f = 1
EOF
)

a: "1"
b: c
d:
  - "1"
  - "2"
e:
  f: "1"
```

### XML

```{warning}
TODO
```

### TOML

TOML から YAML に変換する。

```sh
> yq -p=toml <(cat <<EOF
[a]
b = 1

[c.d]
e = 2
f = 3
EOF
)

a:
  b: 1
c:
  d:
    e: 2
    f: 3
```

### シェル変数

シェル変数に変換する。

```sh
> yq -o=shell <(cat <<EOF
a: 1
b: c
d:
  - 1
  - 2
e:
  f: 1
EOF
)

a=1
b=c
d_0=1
d_1=2
e_f=1
```

## スタイル

スタイルを取得/設定するには `style` 関数を使用する。
スタイルを元に戻すには空文字を設定する。

### single

シングルクォートで囲む。

```sh
> yq '.a style="single"' <(cat <<EOF
a: 1
EOF
)

a: '1'
```

### double

ダブルクォートで囲む。

```sh
> yq '.a style="double"' <(cat <<EOF
a: 1
EOF
)

a: "1"
```

### tagged

タグ情報を追加する。

```sh
> yq '.a style="tagged"' <(cat <<EOF
a: 1
EOF
)

a: !!int 1
```

### literal

リテラルブロックに変換する([8.1.2. Literal Style](https://yaml.org/spec/1.2.2/#literal-style))。

```sh
> yq '.a style="literal"' <(cat <<EOF
a: 1
EOF
)

a: |-
  1
```

### folded

フォールドブロックに変換する([Block Folding](https://yaml.org/spec/1.2.2/#block-folding))。

```sh
> yq '.a style="folded"' <(cat <<EOF
a: 1
EOF
)

a: >-
  1
```

### flow

フローに変換する([7.3. Flow Scalar Styles](https://yaml.org/spec/1.2.2/#flow-scalar-styles))。

```sh
> yq '. style="flow"' <(cat <<EOF
a: 1
EOF
)

{a: 1}
```

JSON っぽい形式をクエリする。

```sh
> yq '.a.b[]' <<<'{"a": {"b": [1, 2]}}'
1
2
```

## 変数

`as` で変数 `$xxx` を定義する。
変数にはスカラー、配列、マップが代入できる。

```sh
> yq '.a as $x | .b + $x' <(cat <<EOF
a: 1
b: 2
EOF
)

3
```

## フロントマター

ファイルの先頭のドキュメントを処理する。

```sh
> yq -f process '.a = 3' <(cat <<EOF
---
a: 1
b: 2
---
xyz
EOF
)

---
a: 3
b: 2
---
xyz
```

フロントマターを処理して取り出す。

```sh
> yq -f extract '.a = 3' <(cat <<EOF
---
a: 1
b: 2
---
xyz
EOF
)

---
a: 3
b: 2
```
