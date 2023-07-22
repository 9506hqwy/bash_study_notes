# 制御構文

## 評価式

### 文字列比較

| 式     | 説明             |
| ------ | ---------------- |
| a == b | 等しい           |
| a != b | 等しくない       |
| -z a   | 空文字である     |
| -n a   | 空文字列ではない |

### 数字比較

| 式      | 説明       |
| ------- | ---------- |
| a -eq b | 等しい     |
| a -ne b | 等しくない |
| a -lt b | a < b      |
| a -le b | a <= b     |
| a -gt b | a > b      |
| a -ge b | a >= b     |

### 論理比較

| 式     | 説明       |
| ------ | ---------- |
| a -a b | a && b     |
| a -o b | a \|\| b   |
| !a     | !a         |

### ファイル評価

| 式   | 説明                   |
| ---- | ---------------------- |
| -d a | ディレクトリが存在する |
| -f a | ファイルが存在する     |

### ```[[``` 構文

```[[``` 構文は ```[``` コマンドと違い、

1. ```<``` や ```>``` をエスケープする必要がない。
2. ```-a```, ```-o``` は ```&&```, ```||``` が使用できる。
3. 変数展開を行う前に解析される。引用符を使う必要がない。
4. ```*``` は右辺に書いたときのみパターン文字列として扱う。
5. 演算子 ```=^``` で右辺を正規表現として扱う。

## 条件分岐

### if

```CONDITION``` の評価結果が真(ゼロ)であればブロック内の処理を実行する。

```sh
if CONDITION1; then
  # statement1
elif CONDITION2; then
  # statement2
else
  # statement3
fi
```

```statement``` で何も処理しない場合は ```:``` を記述する。

### case

変数 ```val ``` が ```PATTERN``` に一致した ```statement``` を実行する。

```sh
case "$val" in
  PATTERN1)
    # statement1
    ;;
  PATTERN2)
    # statement2
    ;;
esac
```

```PATTERN``` で * や ? を文字として扱うには引用符で囲む。

## ループ

### for

```LIST``` の要素を変数 ```val``` に代入して ```statment``` を実行する。

```sh
for val in LIST
do
  # statment
done
```

C 言語のような ```for``` は以下のように記述する。

```sh
for (( val=1; val < 10; val++ ))
do
  # statment
done
```

### while

```CONDITION``` の評価結果が真の間は ```statement``` を実行する。

```sh
while CONDITION
do
  # statement
done
```

### unitl

```CONDITION``` の評価結果が偽の間は ```statement``` を実行する。

```sh
until CONDITION
do
  # statement
done
```
