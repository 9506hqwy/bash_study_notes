# `paste` コマンド

ファイルをマージする。

```sh
> cat > a.txt <<EOF
0
1
2
EOF
```

```sh
> cat > b.txt <<EOF
a
b
c
EOF
```

タブで各行をマージする。

```sh
> paste a.txt b.txt
0       a
1       b
2       c
```

カンマで各行をマージする。

```sh
> paste -d , a.txt b.txt
0,a
1,b
2,c
```

タブで区切りながらファイルごと出力する。

```sh
> paste -s a.txt b.txt
0       1       2
a       b       c
```

カンマで区切りながらファイルごと出力する。

```sh
> paste -s -d , a.txt b.txt
0,1,2
a,b,c
```
