# 変数

## ビルトインのシェル変数

$BASH_REMATCH
: ```=~``` で一致した値の配列(先頭が一致文字列全体、以降がグループごと)

$FUNCNAME
: 実行している関数名の配列(先頭がカレントの関数名)

$HOME
: カレントユーザのホームディレクトリの絶対パス

$IFS
: 区切り文字(既定値: スペース、タブ、改行)

$LINENO
: 行番号

$OLDPWD
: cd で移動前のディレクトリの絶対パス

$PWD
: カレントディレクトリの絶対パス

$REPLY
: read コマンドの既定の値の格納先

$SHELL
: カレントユーザのログインシェルの絶対パス

## 引数

$0
: 実行時に指定したスクリプト

$1..N
: スクリプトの引数

$@
: $1..N の配列

$*
: $@ を $IFS[0] で連結した文字列

$#
: $1..N の個数

## 配列

配列を定義する。

```sh
> ARRAY=(1 2 3 4 5)
>
> for V in ${ARRAY[@]}
> do
>   echo $V
> done
1
2
3
4
5
```

要素を追加する。

```sh
> ARRAY=(1 2 3 4 5)
> ARRAY+=(6)
> echo ${ARRAY[5]}
6
```

値を更新する。

```sh
> ARRAY=(1 2 3 4 5)
> ARRAY[4]=6
>
> for V in ${ARRAY[@]}
> do
>   echo $V
> done
1
2
3
4
6
```

配列を連結する。

```sh
> ARRAY=(1 2 3 4 5)
> declare -p ARRAY
declare -a ARRAY=([0]="1" [1]="2" [2]="3" [3]="4" [4]="5")

> declare -p IFS
declare -- IFS=$' \t\n'

# 空白で連結する。
> V="${ARRAY[*]}"
> declare -p V
declare -- V="1 2 3 4 5"

> echo -e $V
1 2 3 4 5

# '-' で連結する。
> (IFS='-'; V="${ARRAY[*]}"; declare -p V; IFS=$OLSIFS; echo -e $V)
declare -- V="1-2-3-4-5"
1-2-3-4-5
```

文字列を分割する。

```sh
# 空白で分割する。
> VALUE="1 2 3 4 5"
> ARRAY=($VALUE)
> declare -p ARRAY
declare -a ARRAY=([0]="1" [1]="2" [2]="3" [3]="4" [4]="5")

# '-' で分割する。
> VALUE="1-2-3-4-5"
> (IFS='-'; ARRAY=($VALUE); declare -p ARRAY)
declare -a ARRAY=([0]="1" [1]="2" [2]="3" [3]="4" [4]="5")
```

配列の要素数を取得する。

```sh
> ARRAY=(1 2 3 4 5)
> echo ${#ARRAY[@]}
5
```

## 特殊変数

$_
: 直前に実行したコマンドの最後の引数

$?
: 直前に実行したコマンドの戻り値

$$
: プロセス ID

$-
: シェルオプション

## 変数展開

### 既定値の指定

変数 ```val``` が未定義または空文字列の場合に値 ```b``` を ```A ```に代入する。

```sh
A=${val-'b'}
```

変数 ```val``` が未定義または空文字列の場合に値 ```a``` を返却する。

```sh
${val:-a}
```

変数 ```val``` が未定義または空文字列の場合に変数 ```val``` に値 ```a``` を代入する。

```sh
${val:=a}
```

### 設定確認

変数 ```val``` が未定義または空文字列の場合にエラー終了する。

```sh
${val:?"error message"}
```

変数 ```val``` が未定義または空文字列以外の場合に値 ```a``` を返却する。

```sh
${val:+a}
```

### 文字列操作

#### 文字数

変数 ```val``` の長さを返却する。

```sh
${#val}
```

#### 部分文字列

##### 範囲指定

指定した範囲の部分文字列を返却する。

```Start``` に開始インデックスを指定する。
負の値を指定した場合は末尾からのインデックスとなる。
※ 負の値を指定する場合は ```:``` との間に空白を入れる。

```Length``` に文字列数を指定する。指定しない場合は末尾となる。
負の値を指定した場合は末尾からのインデックスとなる。

```sh
${val:<Start>[:<Length>]}
```

##### 前方パターンマッチ

変数 ```val``` から値 ```PATTERN``` に前方マッチした部分を取り除く。
```#PATTERN``` が最短一致、```##PATTERN``` が最長一致となる。

`PATTERN` は正規表現ではない。メタ文字は `*`, `?`, `[...]` が使用できる。

```sh
${val#PATTERN}
${val##PATTERN}
```

下記に記載するパターンマッチを使用する場合は `extglob` が有効になっている必要がある。

```sh
> shopt -s extglob # 有効化
> shopt -u extglob # 無効化
```

| パターン   | 説明                    |
| ---------- | ----------------------- |
| ?(PATTERN) | 0 または 1 回           |
| *(PATTERN) | 0 回以上                |
| +(PATTERN) | 1 回以上                |
| @(PATTERN) | いづれか 1 つ           |
| !(PATTERN) | いづれか 1 つ以外すべて |

`|` で区切って複数のパターンを指定できる。

##### 後方パターンパッチ

変数 ```val``` から値 ```PATTERN``` に後方マッチした部分を取り除く。
```%PATTERN``` が最短一致、```%%PATTERN``` が最長一致となる。

```sh
${val%PATTERN}
${val%%PATTERN}
```

#### 置換

変数 ```val``` 中の値 ```OLD``` を 値 ```NEW``` に置換する。

```/OLD``` の場合は初回一致した箇所のみ、```//OLD``` の場合は一致したすべての箇所を置換する。
```/#OLD``` の場合は先頭位置から一致場合に、```/%OLD``` の場合は末尾位置から一致した場合に置換する。

```OLD``` に ```*``` が含まれている場合は最長一致となる。

```sh
${val/OLD/NEW}
${val//OLD/NEW}
${val/#OLD/NEW}
${val/%OLD/NEW}
```

#### ブレース展開

以下の形式で連番を作成できる。

```sh
{<開始>..<終了>[..<ステップ>]}
```

```sh
echo {1..5..2}
```

```sh
echo {A..E..2}
```

#### パラメータ変換 (Parameter transformation)

変数を ```@``` 演算子で変換する。

```sh
val='\u'
echo ${val@P}
```

| 演算子 | 説明                                   |
| ------ | -------------------------------------- |
| Q      | シングルクォートで囲む                 |
| E      | echo -e と同じ                         |
| P      | プロンプトのエスケープを展開する       |
| A      | declare コマンドや変数の定義に展開する |
| a      | パラメータの属性に展開する             |

E は $'xxx' と同じ効果でエスケープを展開する。

```sh
> echo $'\xe3\x81\x82'
あ

> echo -e '\xe3\x81\x82'
あ

> A='\xe3\x81\x82'
> echo ${A@E}
あ
```

#### 大文字・小文字変換

先頭文字のみ大文字・小文字変換するには ```^```. ```,``` を使用する。

```sh
a='xyz'
echo ${a^}

a='XYZ'
echo ${a,}
```

すべての文字を変換するには ```^^```. ```,,``` を使用する。

```sh
a='xyz'
echo ${a^^}

a='XYZ'
echo ${a,,}
```

大文字と小文字を変換するには ```~``` を使用する。

```sh
a='XyZ'
echo ${a~}

a='XyZ'
echo ${a~~}
```

変換する文字を制限するには演算子の後に範囲を指定する。

```sh
a='xyz'
echo ${a^^[y-z]}
``````

### コマンドの実行結果

コマンド ```CMD``` の実行結果を返却する。

```sh
$(CMD)
`CMD`
```

## ローカル変数

関数内のみ有効なローカル変数は ```local``` を付ける。

```sh
func()
{
  local val=1
}
```

## 変数の文字参照

文字列で変数の値を参照する。変数 ```a``` の値を取得する。

```sh
VAL=a
${!VAL}
```

## 変数名の列挙

以下の形式で PREFIX の名前で始まる変数を列挙する。

```sh
${!PREFIX*}
${!PREFIX@}
```
