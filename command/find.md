# `find` コマンド

ファイルを検索する。

```sh
find [ディレクトリ] [式]
```

式を指定しなければすべてのファイルが一致する。

```sh
> find dir1/
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

式はポジショナルオプション、グローバルオプション、条件、アクションを組み合わせる。

## ポジショナルオプション

時間の条件時に基準を現在ではなく日の始まりを使用する。

```sh
find dir1 -daystart -mmin -30
```

## グローバルオプション

検索するディレクトリの最大深さを指定する。

```sh
> find dir1/ -maxdepth 1
dir1/
dir1/dir2
dir1/file1
```

検索するディレクトリの最小深さを指定する。

```sh
> find dir1/ -mindepth 1
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

深さ優先で検索する。

```sh
> find dir1 -depth
dir1/dir2/dir3/file3
dir1/dir2/dir3
dir1/dir2/file2
dir1/dir2
dir1/file1
dir1
```

## 条件

### 時間

時間の条件は下記がある。
days は *24hour して小数点以下は切り捨てられる。

| オプション           | 説明                             |
| :------------------- | :------------------------------- |
| -amin [±]\<minites>  | アクセス時間                     |
| -anewer \<reference> | referehce より新しいアクセス時間 |
| -atime [±]\<days>    | アクセス時間                     |
| -cmin [±]\<minites>  | 状態変更時間                     |
| -cnewer \<reference> | referehce より新しい状態変更時間 |
| -ctime [±]\<days>    | 状態変更時間                     |
| -mmin [±]\<minites>  | 更新時間                         |
| -newer \<reference>  | referehce より新しい更新時間     |
| -mtime [±]\<days>    | 更新時間                         |

```sh
# 30min～now までに更新されたもの
> find dir1 -mmin -30
dir1/file1

# 50min～40min 前までに更新されたもの
> find dir1 -mmin -50 -mmin +40
dir1
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2

# 25min 前に更新されたもの
> find dir1 -mmin 25
dir1/file1
```

### ユーザ

ユーザ ID を指定する。

```sh
# ユーザ ID が 1 以下に一致するもの
> find dir1/ -uid -1
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

ユーザ ID またはユーザ名を指定する。

```sh
> find dir1/ -user root
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

ユーザ ID に対するユーザがない。

```sh
find dir1/ -nouser
```

### グループ

グループ ID を指定する。

```sh
# グループ ID が 1 以下に一致するもの
> find dir1/ -gid -1
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

グループ ID またはグループ名を指定する。

```sh
> find dir1/ -group root
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

グループ ID に対するグループがない。

```sh
find dir1/ -nogroup
```

### シンボリックリンク

シンボリックリンクに一致する。

```sh
find dir1/ -lname 'file*'
```

大文字・小文字を無視する。

```sh
find dir1/ -ilname 'file*'
```

### ファイル名

ファイル名に一致する。

```sh
> find dir1/ -name 'file*'
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

大文字・小文字を無視する。

```sh
> find dir1/ -iname 'file*'
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

### i ノード

```{warning}
TODO
```

### パス

ファイルパスに一致する。

```sh
> find dir1/ -path 'dir1/file*'
dir1/file1
```

大文字・小文字を無視する。

```sh
> find dir1/ -ipath 'dir1/file*'
dir1/file1
```

### 正規表現

Emacs 正規表現に一致する。

```sh
> find dir1/ -regex '.*file\(1\|2\)'
dir1/dir2/file2
dir1/file1
```

大文字・小文字を無視する。

```sh
> find dir1/ -iregex '.*file\(1\|2\)'
dir1/dir2/file2
dir1/file1
```

### 権限

権限に一致する。

```sh
# 644 の権限のもの
> find dir1/ -perm 644
dir1/dir2/dir3/file3
dir1/file1

# 755 の権限を満たすもの
> find dir1/ -perm -755
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/file2

# 755 の権限の一部を含むもの
> find dir1/ -perm /755
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1

# group に x 権限があるもの
> find dir1/ -perm -g+x
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/file2
```

読み取り可能なファイルとディレクトリに一致する。

```sh
> find dir1/ -readable
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

実行可能ファイルと検索可能なディレクトリに一致する。

```sh
> find dir1/ -executable
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/file2
```

### サイズ

サイズに一致する。

```sh
> find dir1/ -size 0k
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

### タイプ

種別に一致する。

```sh
# ファイル
> find dir1/ -type f
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1

# ディレクトリ
> find dir1/ -type d
dir1/
dir1/dir2
dir1/dir2/dir3
```

### その他

空ファイルまたはディレクトリに一致する。

```sh
> find dir1 -empty
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

すべて一致になる。

```sh
find dir1/ -true
```

すべて不一致になる。

```sh
find dir1/ -false
```

ファイルシステムを指定する。

```sh
> find dir1/ -fstype xfs
dir1/
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

## アクション

コマンドを実行する。

```sh
> find dir1/ -name 'file*' -exec echo 'file: {}' \;
file: dir1/dir2/dir3/file3
file: dir1/dir2/file2
file: dir1/file1

# コマンドを実行する前にプロンプトを表示する。
> find dir1/ -name 'file*' -ok echo 'file: {}' \;
< echo ... dir1/dir2/dir3/file3 > ? y
file: dir1/dir2/dir3/file3
< echo ... dir1/dir2/file2 > ?
< echo ... dir1/file1 > ?
```

一致した対象をまとめて指定してコマンドを実行する。

```sh
> find dir1/ -name 'file*' -exec echo 'file: ' {} \+
file:  dir1/dir2/dir3/file3 dir1/dir2/file2 dir1/file1
```

対象があるディレクトリでコマンドを実行する。

```sh
> find dir1/ -name 'file*' -execdir echo 'file: {}' \;
file: ./file3
file: ./file2
file: ./file1

# コマンドを実行する前にプロンプトを表示する。
> find dir1/ -name 'file*' -okdir echo 'file: {}' \;
< echo ... dir1/dir2/dir3/file3 > ? y
file: ./file3
< echo ... dir1/dir2/file2 > ? n
< echo ... dir1/file1 > ? n
```

ディレクトリごとに一致した対象をまとめて指定してコマンドを実行する。

```sh
> find dir1/ -name 'file*' -execdir echo 'file: ' {} \+
file:  ./file3
file:  ./file2
file:  ./file1
```

`ls` を出力する。

```sh
> find dir1/ -name 'file*' -ls
100663901      0 -rw-r--r--   1 root     root            0  6月  1 19:47 dir1/dir2/dir3/file3
 68048754      0 -rwxr-xr-x   1 root     root            0  6月  1 19:47 dir1/dir2/file2
 33806822      0 -rw-r--r--   1 root     root            0  6月  1 20:10 dir1/file1

# ファイルに出力する。
>  find dir1/ -name 'file*' -fls a.txt
> cat a.txt
100663901      0 -rw-r--r--   1 root     root            0  6月  1 19:47 dir1/dir2/dir3/file3
 68048754      0 -rwxr-xr-x   1 root     root            0  6月  1 19:47 dir1/dir2/file2
 33806822      0 -rw-r--r--   1 root     root            0  6月  1 20:10 dir1/file1
```

改行で区切って出力する。

```sh
# 標準出力に出力する。
> find dir1/ -name 'file*' -print
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1

# ファイルに出力する。
> find dir1/ -name 'file*' -fprint a.txt
> cat a.txt
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

null 文字で区切って出力する。

```sh
# 標準出力に出力する。
> find dir1/ -name 'file*' -print0
dir1/dir2/dir3/file3dir1/dir2/file2dir1/file1

# ファイルに出力する。
> find dir1/ -name 'file*' -fprint0 a.txt
> cat a.txt
dir1/dir2/dir3/file3dir1/dir2/file2dir1/file1
```

フォーマットを指定して標準出力に出力する。

```sh
# 標準出力に出力する。
> find dir1/ -name file3 -printf '%f %p\n'
file3 dir1/dir2/dir3/file3

# ファイルに出力する。
> find dir1/ -name file3 -fprintf a.txt '%f %p\n'
> cat a.txt
file3 dir1/dir2/dir3/file3

```

ディレクトリの場合にサブディレクトリをスキップする。

```sh
> find dir1/ -path "*dir2*" -print
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2

> find dir1/ -path "*dir2*" -prune
dir1/dir2
```

ファイルを削除する。

```sh
find dir1/ -name file2 -delete
```

処理を止める。

```sh
find dir1/ -path "*dir2*" -quit
```

## 組み合わせ

AND `-a` の両方に該当する。

```sh
> find dir1/ -type f -a -path '*dir2*'
dir1/dir2/dir3/file3
dir1/dir2/file2

# オペレータを指定したい場合は AND になる。
> find dir1/ -type f -path '*dir2*'
dir1/dir2/dir3/file3
dir1/dir2/file2
```

OR `-o` のどちらかに該当する。

```sh
# 条件は ( ) で囲って優先順位を決められる。
>  find dir1/ \( -type f -o -path '*dir2*' \)
dir1/dir2
dir1/dir2/dir3
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
```

NOT `!` は条件を否定する。

```sh
> find dir1/ ! -path '*dir2*'
dir1/
dir1/file1
```

## 複数の条件

`,` で区切って複数の条件を同時に実行する。
1つめの条件の結果は捨てられるのでファイルに出力する。

```sh
> find dir1/ -type f -fprint file.txt , -type d -fprint dir.txt
> cat file.txt
dir1/dir2/dir3/file3
dir1/dir2/file2
dir1/file1
> cat dir.txt
dir1/
dir1/dir2
dir1/dir2/dir3
```
