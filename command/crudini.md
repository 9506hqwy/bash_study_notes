# `crudini` コマンド

ini ファイルを操作する。

```sh
> cat > a.ini <<EOF
key0 = value0

[sec1]
key1 = value1

[sec2]
key2 = value 2
key3 = value3
EOF
```

```sh
> cat > b.ini <<EOF
[sec1]
key1 = VALUE1

[sec2]
key2 = value 2
key3 = value3
key4 = value4

[sec3]
key5 = value5
EOF
```

## 値の取得

ini ファイルから値を取得する。
セクション名がない値のセクションは `DEFAULT` となる。

```sh
# セクションの一覧を取得する。
> crudini --get a.ini
DEFAULT
sec1
sec2

# キーの一覧を取得する。
> crudini --get a.ini sec2
key2
key3

# 値を取得する。
> crudini --get a.ini sec2 key2
value 2
```

シェル変数の形式で出力する。

```sh
> crudini --get --format=sh a.ini
key0=value0
sec1_key1=value1
sec2_key2='value 2'
sec2_key3=value3
```

セクション、キー、値を 1 行で出力する。

```sh
> crudini --get --format=lines a.ini
[ DEFAULT ] key0 = value0
[ sec1 ] key1 = value1
[ sec2 ] key2 = value 2
[ sec2 ] key3 = value3
```

## 値の更新

ini ファイルに値を追加する。
`--output` を指定しない場合は入力ファイルが書き換わる。
`DEFAULT` に値を追加する場合は `""` を指定する。

```sh
# セクションを追加する。
> crudini --set --output=- a.ini sec3
key0 = value0

[sec1]
key1 = value1

[sec2]
key2 = value 2
key3 = value3

[sec3]

# キーを追加する。
> crudini --set --output=- a.ini sec2 key4
key0 = value0

[sec1]
key1 = value1

[sec2]
key2 = value 2
key3 = value3
key4 =

# 値を追加する。
> crudini --set --output=- a.ini "" key4 'value 4'
key0 = value0
key4 = value 4

[sec1]
key1 = value1

[sec2]
key2 = value 2
key3 = value3

# 値を追加する。
> crudini --set --output=- --list a.ini sec1 key1 2
key0 = value0

[sec1]
key1 = value1, 2

[sec2]
key2 = value 2
key3 = value3

# セパレータを指定して値を追加する。
> crudini --set --output=- --list --list-sep=, a.ini sec1 key1 2
key0 = value0

[sec1]
key1 = value1,2

[sec2]
key2 = value 2
key3 = value3
```

## 値の削除

ini ファイルから値を削除する。
`--output` を指定しない場合は入力ファイルが書き換わる。
`DEFAULT` に値を追加する場合は `""` を指定する。

```sh
# セクションを削除する。
> crudini --del --output=- a.ini sec1
key0 = value0

[sec2]
key2 = value 2
key3 = value3

# キーを削除する。
> crudini --del --output=- a.ini sec2 key2
key0 = value0

[sec1]
key1 = value1

[sec2]
key3 = value3

# 値を削除する。
> crudini --del --list --output=- a.ini sec1 key1 value1
key0 = value0

[sec1]
key1 =

[sec2]
key2 = value 2
key3 = value3
```

## ファイルのマージ

ini ファイルをマージする。

```sh
# ini ファイルをマージする。
> crudini --merge --output=- a.ini < b.ini
key0 = value0

[sec1]
key1 = VALUE1

[sec2]
key2 = value 2
key3 = value3
key4 = value4

[sec3]
key5 = value5

# セクションをマージする。
> crudini --merge --output=- a.ini sec1 <<< "key4 = value4"
key0 = value0

[sec1]
key1 = value1
key4 = value4

[sec2]
key2 = value 2
key3 = value3
```
