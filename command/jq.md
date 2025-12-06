# `jq` コマンド

JSON ファイルをクエリ、編集する。

## 式

### ドット

取り出す値の `key` を `.` 区切りで指定する。

```sh
> jq '.a' <(cat <<EOF
{ "a": 1 }
EOF
)

1
```

`key` にドットが含まれる場合は `"` で囲む。

```sh
> jq '.".a"' <(cat <<EOF
{ ".a": 1 }
EOF
)

1
```

配列の場合にオブジェクトを期待して `key?` としてもエラーにならない。

```sh
> jq '.a?' <(cat <<EOF
[1, 2, 3]
EOF
)

```

### 配列

`[ ]` で配列の要素を取り出す。

```sh
> jq '.a[1]' <(cat <<EOF
{ "a": [1, 2, 3] }
EOF
)

2
```

負の数を指定すると逆順にインデックスを指定できる。

```sh
> jq '.a[-1]' <(cat <<EOF
{ "a": [1, 2, 3] }
EOF
)

3
```

範囲を指定して取り出す。

```sh
> jq '.a[1:2]' <(cat <<EOF
{ "a": [1, 2, 3] }
EOF
)

[
  2
]
```

すべて取り出す。

```sh
> jq '.a[]' <(cat <<EOF
{ "a": [1, 2, 3] }
EOF
)

1
2
3
```

### パイプ

`|` で式を繋いで値を取り出す。

```sh
> jq '.a[] | .b.c' <(cat <<EOF
{
  "a": [
    {"b": {"c": 1}},
    {"b": {"c": 2}},
    {"b": {"c": 3}}
  ]
}
EOF
)

1
2
3
```

### 真偽値

値がnull, false の場合に `false` として扱われる。
0, 空文字、空配列、空オブジェクトは `true` として扱われる。

```sh
> jq --null-input '{null: null | not, bool: false | not, number: 0 | not, string: "" | not, array: [] | not, object: {} | not}'

{
  "null": true,
  "bool": true,
  "number": false,
  "string": false,
  "array": false,
  "object": false
}
```

### 既定値

値が未定義または false 扱いの場合に既定値を指定する。

```sh
> jq '.a // 1' <(cat <<EOF
{ "a": null }
EOF
)

1
```

### 再帰

`..` で再帰的に value をクエリする。

```sh
> jq '..' <(cat <<EOF
{"a": {"b": {"c": 1}}}
EOF
)

{
  "a": {
    "b": {
      "c": 1
    }
  }
}
{
  "b": {
    "c": 1
  }
}
{
  "c": 1
}
1
```

`recurse` / `recurse(f)` / `recurse(f; cond)` で再帰的に value をクエリする。

```sh
> jq 'recurse' <(cat <<EOF
{ "a": {"b": {"c": 1}} }
EOF
)

{
  "a": {
    "b": {
      "c": 1
    }
  }
}
{
  "b": {
    "c": 1
  }
}
{
  "c": 1
}
1
```

`walk` で再帰的に value に関数を適用する。

```sh
> jq 'walk(tostring)' <(cat <<EOF
{ "a": {"b": {"c": 1}} }
EOF
)

"{\"a\":\"{\\\"b\\\":\\\"{\\\\\\\"c\\\\\\\":\\\\\\\"1\\\\\\\"}\\\"}\"}"
```

## 演算

### 算術演算

数値を加算する。

```sh
> jq '.a += 1' <(cat <<EOF
{ "a": 1 }
EOF
)

{
  "a": 2
}
```

値を取り出して加算する。

```sh
> jq '.a = .a + .b' <(cat <<EOF
{
  "a": 1,
  "b": 2
}
EOF
)

{
  "a": 3,
  "b": 2
}
```

### boolean 演算

`and` 演算する。

```sh
> jq '.a and true' <(cat <<EOF
{ "a": false }
EOF
)

false
```

`or` 演算する。

```sh
> jq '.a or true' <(cat <<EOF
{ "a": false }
EOF
)

true
```

`not` 演算する。

```sh
> jq '.a | not' <(cat <<EOF
{ "a": false }
EOF
)

true
```

### 比較演算子

数値を比較する。

```sh
> jq '.a < 2' <(cat <<EOF
{ "a": 1 }
EOF
)

true
```

文字列を比較する。

```sh
> jq '.a < "bcd"' <(cat <<EOF
{ "a": "abc" }
EOF
)

true
```

### 文字列演算

#### 連結

文字列を連結する。

```sh
> jq '.a += "c"' <(cat <<EOF
{ "a": "b" }
EOF
)

{
  "a": "bc"
}
```

配列を連携する。

```sh
> jq 'join(",")' <(cat <<EOF
["a", "b", "c"]
EOF
)

"a,b,c"
```

#### 分割

文字列を分割する。

```sh
> jq '.a / "c"' <(cat <<EOF
{ "a": "bcd" }
EOF
)

[
  "b",
  "d"
]
```

指定した文字で分割する。

```sh
> jq '.a | split(",")' <(cat <<EOF
{ "a": "a,b,c" }
EOF
)

[
  "a",
  "b",
  "c"
]
```

#### 大文字 / 小文字

大文字に変換する。

```sh
> jq --null-input '"a" | ascii_upcase'

"A"
```

小文字に変換する。

```sh
> jq --null-input '"A" | ascii_downcase'

"a"
```

#### 空白除去

空白を削除する。

```sh
> jq '.[] | ltrimstr(" ") | rtrimstr(" ")' <(cat <<EOF
[" a", "b ", " c "]
EOF
)

"a"
"b"
"c"
```

#### 正規表現

正規表現は Oniguruma の表記に従う。

```sh
> jq '.a | test("(?i)hello")' <(cat <<EOF
{ "a": "Hello, world" }
EOF
)

true
```

一致した情報を返却する。

```sh
> jq '.a | match("world")' <(cat <<EOF
{ "a": "Hello, world" }
EOF
)

{
  "offset": 7,
  "length": 5,
  "string": "world",
  "captures": []
}
```

キャプチャを取得する。

```sh
> jq '.a | capture("(?<a>world)")' <(cat <<EOF
{ "a": "Hello, world" }
EOF
)

{
  "a": "world"
}
```

正規表現を使用して文字列を置換する。

```sh
> jq '.a | sub("o"; "O"; "g")' <(cat <<EOF
{ "a": "Hello, world" }
EOF
)

"HellO, wOrld"
```

マッチした文字列を利用する。

```sh
> jq '.a | sub("(?<x>o)"; "\(.x),"; "g")' <(cat <<EOF
{ "a": "Hello, world" }
EOF
)

"Hello,, wo,rld"
```

#### 文字列補間

文字列を式の評価結果で補間する。

```sh
> jq '.b = "\(.a) world"' <(cat <<EOF
{ "a": "hello" }
EOF
)

{
  "a": "hello",
  "b": "hello world"
}
```

### 配列演算

配列に要素を追加する。

```sh
> jq '.a[.a | length] += 2' <(cat <<EOF
{ "a": [1] }
EOF
)

{
  "a": [
    1,
    2
  ]
}
```

配列を連携する。

```sh
> jq '.a = .a + .b' <(cat <<EOF
{
  "a": [{ "x": [1] }],
  "b": [{ "x": [2] }]
}
EOF
)

{
  "a": [
    {
      "x": [
        1
      ]
    },
    {
      "x": [
        2
      ]
    }
  ],
  "b": [
    {
      "x": [
        2
      ]
    }
  ]
}
```

配列から一致する要素を削除する。

```sh
> jq '.a = .a - .b' <(cat <<EOF
{
  "a": [1, 2],
  "b": [2]
}
EOF
)

{
  "a": [
    1
  ],
  "b": [
    2
  ]
}
```

### オブジェクト演算

オブジェクトをマージする。

```sh
> jq '.a = .a + .b' <(cat <<EOF
{
  "a": {"c": 1},
  "b": {"d": 2}
}
EOF
)

{
  "a": {
    "c": 1,
    "d": 2
  },
  "b": {
    "d": 2
  }
}

> jq '.a = .a * .b' <(cat <<EOF
{
  "a": {"c": 1},
  "b": {"d": 2}
}
EOF
)

{
  "a": {
    "c": 1,
    "d": 2
  },
  "b": {
    "d": 2
  }
}
```

## 関数

### `add`

`+` と同じ。

### `abs`

絶対値に返還する。

```sh
>  jq --null-input '-1 | abs'
1
```

### `all`

配列の要素に false がなければ true を返却する。

```sh
> jq '.[] | all' <(cat <<EOF
[
  [true],
  [false],
  []
]
EOF
)

true
false
true
```

### `all(condition)`

引数に指定した式が false にならなければ true を返却する。

```sh
> jq '.[] | all(. == 1)' <(cat <<EOF
[
  [1],
  [2]
]
EOF
)

true
false
```

### `any`

配列の要素に true があれば true を返却する。

```sh
> jq '.[] | any' <(cat <<EOF
[
  [true],
  [false],
  []
]
EOF
)

true
false
false
```

### `any(condition)`

引数に指定した式が true になれば true を返却する。

```sh
> jq '.[] | any(. == 1)' <(cat <<EOF
[
  [1],
  [2]
]
EOF
)

true
false
```

### `arrays`

配列にフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | arrays'

[]
```

### `booleans`

真偽値にフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | booleans'

false
```

### `bsearch`

```{warning}
TODO
```

### `combinations` / `combinations(n)`

配列の組み合わせを返却する。

```sh
> jq '. | combinations' <(cat <<EOF
[
  [1, 2],
  [3, 4]
]
EOF
)

[
  1,
  3
]
[
  1,
  4
]
[
  2,
  3
]
[
  2,
  4
]
```

### `contains`

引数に指定した値またはオブジェクトが一致する場合に true を返却する。

配列の要素に部分一致する場合は true を返却する。

```sh
> jq '.a | contains(["de"])' <(cat <<EOF
{ "a": ["abc", "def"] }
EOF
)

true
```

文字列に部分一致する場合は true を返却する。

```sh
> jq '.a[] | contains("de")' <(cat <<EOF
{ "a": ["abc", "def"] }
EOF
)

false
true
```

### `del`

引数に一致する項目を削除する。

```sh
> jq 'del(.a)' <(cat <<EOF
{ "a": 1, "b": 2 }
EOF
)

{
  "b": 2
}
```

配列から要素を削除する。

```sh
> jq 'del(.a[1])' <(cat <<EOF
{ "a": [1, 2, 3] }
EOF
)

{
  "a": [
    1,
    3
  ]
}
```

### `delpaths`

第一引数の配列に一致する項目を削除する。

```sh
> jq 'delpaths([["a", "b", "c"]])' <(cat <<EOF
{ "a": {"b": {"c": 3}} }
EOF
)

{
  "a": {
    "b": {}
  }
}
```

### `empty`

何も返さない。

```sh
> jq --null-input '[null, false, 0, "", [], {}, empty] | .[]'

null
false
0
""
[]
{}
```

### `endswith`

文字列の末尾が一致した場合に true を返却する。

```sh
> jq --null-input '"abcdef" | endswith("def")'

true
```

### `env` / `$ENV`

環境変数の値を取得する。

```sh
> jq --null-input 'env.HOME'

"/root"
```

変数は文字列になる。

```sh
> VAL=1 jq --null-input 'env.VAL'

"1"
```

### `error` / `error(message)`

エラーを発生させる。

```sh
> jq --null-input '"message" | error'

jq: error (at <unknown>): message
```

### `explode`

```{warning}
TODO
```

### `finites`

有限値にフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | finites'

0
```

### `flatten`

ネストした配列を平坦化する。

```sh
> jq 'flatten' <(cat <<EOF
[
  [["a", "b"]],
  [["c", "d"]]
]
EOF
)

[
  "a",
  "b",
  "c",
  "d"
]
```

### `floor`

数値を切り捨てる。

```sh
> jq --null-input '1.2 | floor'

1
```

### `from_entries`

key-value のオブジェクトから変換する。

```sh
> jq '. | from_entries' <(cat <<EOF
[
  { "key": "a", "value": 1 },
  { "key": "b", "value": 2 }
]
EOF
)

{
  "a": 1,
  "b": 2
}
```

### `fromdate`/ `fromdateiso8601`

タイムスタンプを変換する。

```sh
> jq --null-input '"2025-03-16T10:02:03Z" | fromdateiso8601'

1742119323
```

### `getpath`

引数に指定したキーの配列から値を返す。

```sh
> jq 'getpath(["a", "b", "c"])' <(cat <<EOF
{ "a": {"b": {"c": 3}} }
EOF
)

3
```

### `group_by`

引数に指定したキーの値でグルーピングする。

```sh
> jq 'group_by(.a)' <(cat <<EOF
[
  {"a": 1, "b": 1},
  {"a": 1, "b": 2},
  {"a": 2, "b": 3},
  {"a": 3, "b": 4}
]
EOF
)

[
  [
    {
      "a": 1,
      "b": 1
    },
    {
      "a": 1,
      "b": 2
    }
  ],
  [
    {
      "a": 2,
      "b": 3
    }
  ],
  [
    {
      "a": 3,
      "b": 4
    }
  ]
]
```

### `halt` / `halt_error` ? `halt_error(exit_code)`

`jq` の処理を止める。

```sh
> jq --null-input 'halt_error(1)'; echo $?

1
```

### `has`

引数に指定した値のキーがある場合に true を返却する。

```sh
> jq 'has("a")' <(cat <<EOF
{ "a": 1, "b": 2 }
EOF
)

true
```

引数に指定した値の配列のインデックスがある場合に true を返却する。

```sh
> jq 'has(1)' <(cat <<EOF
[ 1, 2 ]
EOF
)

true
```

### `in`

引数に指定したオブジェクトのキーがある場合に true を返却する。

```sh
> jq 'in({"a": 1})' <(cat <<EOF
"a"
EOF
)

true
```

引数に指定した配列のインデックスがある場合に true を返却する。

```sh
> jq 'in([1, 3])' <(cat <<EOF
1
EOF
)

true
```

### `index` / `rindex`

引数に指定した値のインデックスを返却する。

```sh
> jq --null-input '"0a23a567a9" | index("a")'

1
```

```sh
> jq --null-input '"0a23a567a9" | rindex("a")'

8
```

### `indices`

引数に指定した値のインデックスをすべて返却する。

```sh
> jq --null-input '"0a23a567a9" | indices("a")'

[
  1,
  4,
  8
]
```

### `inside`

指定したオブジェクトに部分一致する場合は true を返却する。

```sh
> jq 'inside({"a": 1})' <(cat <<EOF
{ "a": 1 }
EOF
)

true
```

### `implode`

```{warning}
TODO
```

### `iterables`

列挙型にフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | iterables'

[]
{}
```

### `keys` / `keys_unsorted`

キー名を取得する。

```sh
> jq '.a | keys' <(cat <<EOF
{
  "a": {
    "b": 1,
    "c": 1
  }
}
EOF
)

[
  "b",
  "c"
]
```

配列インデックスを取得する。

```sh
> jq '.a | keys' <(cat <<EOF
{ "a": ["b", "c"] }
EOF
)

[
  0,
  1
]
```

### `length`

文字列の長さ、配列の要素数、オブジェクトの要素数を返却する。
真偽値はエラーになる。

```sh
> jq --null-input '[0, "a", [], {}, null] | .. | length'

5
0
1
0
0
0
```

### `max` / `max_by`

最大値を返す。

```sh
> jq --null-input '[4, 2, 5, 9, 1] | max'

9
```

### `map`

配列の各要素に引数の式を評価する。

```sh
> jq --null-input '[4, 2, 5, 9, 1] | map(. * 2)'

[
  8,
  4,
  10,
  18,
  2
]
```

### `map_values`

配列の各要素に引数の式を評価する。

```sh
> jq 'map_values(. * 2)' <(cat <<EOF
{ "a": 1, "b": 2 }
EOF
)

{
  "a": 2,
  "b": 4
}
```

### `min` / `min_by`

最小値を返す。

```sh
> jq --null-input '[4, 2, 5, 9, 1] | min'

1
```

### `normals`

正規数にフィルタする。

```sh
> jq --null-input '[null, false, 1, "", [], {}] | .[] | normals'

1
```

### `now`

現在のタイムスタンプを出力する。

```sh
> jq --null-input 'now'

1748656879.57383
```

### `nulls`

null にフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | nulls'

null
```

### `numbers`

数値にフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | numbers'

0
```

### `objects`

オブジェクトにフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | objects'

{}
```

### `path`

式が一致したキーの配列を取得する。

```sh
> jq 'path(.a.b.c)' <(cat <<EOF
{ "a": {"b": {"c": 3}} }
EOF
)

[
  "a",
  "b",
  "c"
]
```

### `paths` / `paths(node_filter)`

指定した値のキーの配列を取得する。

```sh
> jq 'paths' <(cat <<EOF
{ "a": {"b": {"c": 3}} }
EOF
)

[
  "a"
]
[
  "a",
  "b"
]
[
  "a",
  "b",
  "c"
]
```

### `pick`

引数に一致する項目のみ取得する。

```sh
> jq 'pick(.a)' <(cat <<EOF
{ "a": 1, "b": 2 }
EOF
)

{
  "a": 1
}
```

配列から指定した要素以外を削除する。

```sh
> jq 'pick(.[0], .[2])' <(cat <<EOF
[1, 2, 3]
EOF

[
  1,
  null,
  3
]
```

### `range`

数値を列挙する。

```sh
> jq --null-input '[range(2; 5)]'

[
  2,
  3,
  4
]
```

### `repeat`

エラーが発生するまで繰り返し実行する。

```sh
> jq --null-input '1 | repeat(. * 2, error)?'

2
```

### `reverse`

配列を逆順にする。

```sh
> jq 'reverse' <(cat <<EOF
[ 1, 2, 3 ]
EOF
)

[
  3,
  2,
  1
]
```

### `scalars`

スカラーにフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | scalars'

null
false
0
""
```

### `select`

指定した式に一致する項目を取得する。

```sh
> jq '.[] | select(.a == 2)' <(cat <<EOF
[
  {"a": 1},
  {"a": 2},
  {"a": 3}
]
EOF
)

{
  "a": 2
}
```

### `setpath`

第一引数の配列に一致する項目を第2引数に指定した値で更新する。

```sh
> jq 'setpath(["a", "b", "c"]; 4)' <(cat <<EOF
{ "a": {"b": {"c": 3}} }
EOF
)

{
  "a": {
    "b": {
      "c": 4
    }
  }
}
```

### `sort`

配列を value でソートする。

```sh
> jq 'sort' <(cat <<EOF
[ 2, 1 ,3 ]
EOF
)

[
  1,
  2,
  3
]
```

### `sort_by`

引数に指定した式で配列をソートする。

```sh
> jq 'sort_by(.)' <(cat <<EOF
[ 2, 1 ,3 ]
EOF
)

[
  1,
  2,
  3
]
```

複数をキーにできる。

```sh
> jq 'sort_by(.a, .b)' <(cat <<EOF
[
  {"a": 2, "b": 2},
  {"a": 2, "b": 1},
  {"a": 1, "b": 2}
]
EOF
)

[
  {
    "a": 1,
    "b": 2
  },
  {
    "a": 2,
    "b": 1
  },
  {
    "a": 2,
    "b": 2
  }
]
```

### `sqrt`

平方根を取得する。

```sh
> jq --null-input '4 | sqrt'

2
```

### `startswith`

文字列の先頭が一致した場合に true を返却する。

```sh
> jq --null-input '"abcdef" | startswith("abc")'

true
```

### `strings`

文字列にフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | strings'

""
```

### `transpose`

```{warning}
TODO
```

### `to_entries`

key-value のオブジェクトに変換する。

```sh
> jq '. | to_entries' <(cat <<EOF
{ "a": 1, "b": 2 }
EOF
)

[
  {
    "key": "a",
    "value": 1
  },
  {
    "key": "b",
    "value": 2
  }
]
```

### `todate` / `todateiso8601`

タイムスタンプを変換する。

```sh
> jq --null-input 'now | todateiso8601'

"2025-05-31T01:59:25Z"
```

### `tonumber`

値を数値に変換する。

```sh
> jq '.[] | tonumber' <(cat <<EOF
[ "1", "1.1" ]
EOF
)

1
1.1
```

### `tostring`

値を文字列に変換する。

```sh
> jq '.[] |= tostring' <(cat <<EOF
[ 1, "a", true, null, [1, 2], {"b": "c"}]
EOF
)

[
  "1",
  "a",
  "true",
  "null",
  "[1,2]",
  "{\"b\":\"c\"}"
]
```

### `type`

空の文字列を取得する。

```sh
> jq '.[] | type' <(cat <<EOF
[ 1, "a", true, null, [1, 2], {"b": "c"}]
EOF
)

"number"
"string"
"boolean"
"null"
"array"
"object"
```

### `unique`

重複した値を除外する。

```sh
> jq 'unique' <(cat <<EOF
[ 3, 1, 3, 2 ]
EOF
)

[
  1,
  2,
  3
]
```

### `unique_by`

引数に指定した式を評価結果で除外する。

```sh
> jq 'unique_by(type)' <(cat <<EOF
[ 3, "a", 2, [], "b" ]
EOF
)

[
  [],
  3,
  "a"
]
```

### `until`

条件に一致しないとき繰り返し実行する。

```sh
> jq --null-input '1 | until(. > 3; . * 2)'

4
```

### `values`

null 以外にフィルタする。

```sh
> jq --null-input '[null, false, 0, "", [], {}] | .[] | values'

false
0
""
[]
{}
```

### `while`

条件に一致するとき繰り返し実行する。

```sh
> jq --null-input '1 | while(. < 3; . * 2)'

1
2
```

### `with_entries`

引数に指定した値をキーにする。

```sh
> jq '. | with_entries(.key |= "KEY_" + .)' <(cat <<EOF
{ "a": 1, "b": 2 }
EOF
)

{
  "KEY_a": 1,
  "KEY_b": 2
}
```

### `$__loc__`

ファイル名と行番号を返却する。

```sh
>  jq --null-input '$__loc__'
{
  "file": "<top-level>",
  "line": 1
}
```

## 生成

入力を空にして新しく json 出力する。

```sh
> jq --null-input '.a = 1 | .b = 2'

{
  "a": 1,
  "b": 2
}
```

配列を出力する。

```sh
> jq '[.a, .b]' <(cat <<EOF
{
  "a": 1,
  "b": 2
}
EOF
)

[
  1,
  2
]
```

オブジェクトを出力する。

```sh
> jq '{"c": .a, "d": .b}' <(cat <<EOF
{
  "a": 1,
  "b": 2
}
EOF
)

{
  "c": 1,
  "d": 2
}
```

## エスケープ

文字列をエスケープする。

```sh
# text
> jq --null-input '1 | @text'

"1"

# json
> jq --null-input '"a" | @json'

"\"a\""

# html
> jq --null-input '"<a/>" | @html'

"&lt;a/&gt;"

# uri
> jq --null-input '"/ " | @uri'

"%2F%20"

# sh
> jq --null-input '"/ " | @sh'

"'/ '"

# base64
> jq --null-input '"abc" | @base64'

"YWJj"

# base64d
> jq --null-input '"YWJj" | @base64d'

"abc"
```

## フォーマット変換

### CSV

配列を CSV に変換する。

```sh
> jq -r '.[] | @csv' <(cat <<EOF
[
  [ 1, 2 ],
  [ 3, 4 ],
  [ 5, 6 ]
]
EOF
)

1,2
3,4
5,6
```

### TSV

配列を TSV に変換する。

```sh
> jq -r '.[] | @tsv' <(cat <<EOF
[
  [ 1, 2 ],
  [ 3, 4 ],
  [ 5, 6 ]
]
EOF
)

1       2
3       4
5       6
```

## 変数

`as` で変数 `$xxx` を定義する。
変数にはスカラー、配列、オブジェクトが代入できる。

```sh
> jq '.a as $x | .b + $x' <(cat <<EOF
{
  "a": 1,
  "b": 2
}
EOF
)

3
```
