# コマンド連結

## プロセス置換(標準出力)

```CMD``` の標準出力が */dev/fd/N* に割り当てられファイルとして扱える。

```sh
<(CMD)
```

例

```sh
cat <(echo test)
```

## プロセス置換(標準入力)

```CMD``` の標準入力が */dev/fd/N* に割り当てられファイルとして扱える。

```sh
>(CMD)
```
