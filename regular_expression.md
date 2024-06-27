# 正規表現

## 量指定

パターンの後ろで指定して繰り返し回数を指定する。

| 演算子 | 説明 |
| ----- | ----- |
| ? | 0, 1 回 |
| * | 0 回以上 |
| + | 1 回以上 |
| {M,N}, {M}, {M,}, {,N} | M 回以上かる N 回以下 |

```sh
> [[ 'a' =~ ab? ]]; echo $?:$BASH_REMATCH
0:a
> [[ 'ab' =~ ab? ]]; echo $?:$BASH_REMATCH
0:ab
> [[ 'a' =~ ab* ]]; echo $?:$BASH_REMATCH
0:a
> [[ 'ab' =~ ab* ]]; echo $?:$BASH_REMATCH
0:ab
> [[ 'a' =~ ab+ ]]; echo $?:$BASH_REMATCH
1:
> [[ 'ab' =~ ab+ ]]; echo $?:$BASH_REMATCH
0:ab
> [[ 'abb' =~ ab+ ]]; echo $?:$BASH_REMATCH
0:abb
> [[ 'a' =~ ab{1,2} ]]; echo $?:$BASH_REMATCH
1:
> [[ 'ab' =~ ab{1,2} ]]; echo $?:$BASH_REMATCH
0:ab
> [[ 'abb' =~ ab{1,2} ]]; echo $?:$BASH_REMATCH
0:abb
> [[ 'abbb' =~ ab{1,2} ]]; echo $?:$BASH_REMATCH
0:abb
```

正規表現は最長マッチとなる(Greedy)。
量指定のあとに `?` を付けて `??`. `*?`. `+?`, `{M,N}?` とすると最短マッチとなる(最小量指定子/Reluctant)。
量指定のあとに `+` を付けて `?+`. `*+`. `++`, `{M,N}+` とすると最長マッチ以外のパターンとはマッチしない(絶対最大量指定子/Possessive)。
BASH, sed の正規表現では Greedy のみとなる。

```sh
> echo 'abc' | grep -P 'a(bc)*'; echo $?
abc
0
> echo 'abc' | grep -P 'a(bc)*?'; echo $?
abc
0
```

## 位置指定

| 演算子 | 説明 |
| ----- | ----- |
| ^ | 行頭 |
| $ | 行末 |
| \\< | 英単語の開始 |
| \\> | 英単語の終了 |

```sh
> [[ 'a' =~ ^a ]]; echo $?:$BASH_REMATCH
0:a
[[ '1a' =~ ^a ]]; echo $?:$BASH_REMATCH
1:
> [[ 'a' =~ a$ ]]; echo $?:$BASH_REMATCH
0:a
> [[ 'a1' =~ a$ ]]; echo $?:$BASH_REMATCH
1:
> echo 'abc abc' | sed -E 's/\<a/A/g'
Abc Abc
> echo 'abc abc' | sed -E 's/c\>/C/g'
abC abC
```

## 文字クラス

`[`, `]` で囲った文字のいづれかにマッチするか評価する。
`0-9` のようにハイフン(`-`)で文字範囲を指定することもできる。
`-` を文字として使用するには `[` の直後に指定する。
また、正規表現のメタ文字は文字クラス内では通常の文字として使用される。

```sh
> [[ 'a-' =~ [-*] ]]; echo $?:$BASH_REMATCH
0:-
```

`[^`, `]` で囲った以外の文字にマッチするか評価する(囲った文字にマッチしないか評価するのではない)。
`^` を文字として使用するには `[` の直後以外に指定する。

```sh
> [[ '^' =~ [^^] ]]; echo $?:$BASH_REMATCH
1:
```

POSIX のブラケット表現として以下がある。

| 文字 | 説明 |
| ---- | ---- |
| [:alnum:] | 英数字 |
| [:alpha:] | 英字 |
| [:blank:] | 空白 |
| [:antrl:] | 制御文字 |
| [:digit:] | 数字 |
| [:graph:] | 空白、制御文字以外 |
| [:lower:] | 英子文字 |
| [:print:] | 制御文字以外 |
| [:punct:] | 句読点 |
| [:space:] | 空白文字と改行 |
| [:upper:] | 英大文字 |
| [:xdigit:] | 16進数の数字 |

拡張機能として以下のエスケープがある。

| 文字 | 説明 |
| ---- | ---- |
| \\d, \\D | 数字、数字以外 |
| \\w, \\W | 単語、単語以外 |
| \\s, \\S | 空白、空白以外 |

## 後方参照

正規表現のグループ化した箇所と同じパターンにマッチするか評価する。
以下の場合は英大文字3文字が 2 連続で出現する場合にマッチするか評価する。

```
([A-Z]{3}) \1
```

グループは `\1`, `\2`, `\3`, ... で指定する。

```sh
> echo 'abc abc' | sed -E 's/(...) \1/OK/'
OK
> echo 'abc abd' | sed -E 's/(...) \1/OK/'
abc abd
```

## 先読み

指定した `PATTERN` がマッチするかどうを評価するが位置は変更しない。

```sh
(?=PATTTERN)
```

```sh
> echo 'abc abc' | grep -P 'abc (?=...)'; echo $?
abc abc
0
> echo 'abc ab' | grep -P 'abc (?=...)'; echo $?
1
```

一致しないかどうかを評価する。

```sh
(?!PATTTERN)
```

```sh
> echo 'abc abc' | grep -P 'abc (?!...)'; echo $?
1
> echo 'abc ab' | grep -P 'abc (?!...)'; echo $?
abc ab
0
```

## 後読み

指定した `PATTERN` が評価する位置から逆方向にマッチするかどうを評価する。

```sh
(?<=PATTTERN)
```

```sh
> echo 'abc abc' | grep -P '(?<=...) abc'; echo $?
abc abc
0
> echo 'ab abc' | grep -P '(?<=...) abc'; echo $?
1
```

一致しないかどうかを評価する。

```sh
(?<!PATTTERN)
```

```sh
> echo 'aba abc' | grep -P '(?<!...) abc'; echo $?
1
> echo 'ab abc' | grep -P '(?<!...) abc'; echo $?
ab abc
0
```

## アトミックグループ

アトミックグループ内のパターンにマッチした場合は、そのマッチした箇所はバックトラックが発生しない。

```sh
(?>PATTERN)
```

```sh
> echo 'abc' | grep -P '(?>.+)'; echo $?
abc
0
> echo 'abc' | grep -P '(?>.+)c'; echo $?
1
```

## 条件分岐

if 式にによって評価する正規表現を分岐できる。

```sh
(?PATTERN1 PATTERN2 | PATTERN3)
```

```sh
> echo 'abc' | grep -P '(?(?=a)...|\d+)'; echo $?
abc
0
> echo '012' | grep -P '(?(?=a)...|\d+)'; echo $?
012
0
> echo 'bcd' | grep -P '(?(?=a)...|\d+)'; echo $?
1
```

## POSIX 標準

[9. Regular Expressions](https://pubs.opengroup.org/onlinepubs/9699919799.2018edition/basedefs/V1_chap09.html) に記載されている。

基本正規表現 (BRE) と拡張静企業減 (ERE) があり、BASH は ERE の文法を拡張したものに従う。

BRE では `+`, `?`, `(`, `)`, `{`. `}` を量指定やグループ化など正規表現の機能として使う場合は `\` でエスケープする必要がある。

## 正規表現処理

### NFA

パターンがマッチするかどうか文字位置ごとに確認する。
マッチしない場合は文字位置を戻し(バックトラック)、次のパターンを確認する。

複数のパターンにマッチさせるとき、
通常の場合は最初にマッチしたパターンが見つかればマッチングを終了するが、
POSIX の動作の場合はすべてのパターンを試して最長のものがマッチする。

### DFA

テキストを走査しマッチするパターンを絞り込む。
原理上、最短マッチングや後方参照はできない。
