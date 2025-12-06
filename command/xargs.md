# `xargs` コマンド

標準入力を空白、タブ、改行で複数に分割してコマンドを複数回実行する。

コマンドを指定しない場合は `echo` となる。

```sh
> echo 'a b c' | xargs
a b c
```

分割数を指定しない場合は既定値の収まる範囲で自動的に分割される。

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
