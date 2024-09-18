# プロファイル

Bash を起動したときに読み込まれるプロファイルは以下となる。

Bash が呼び出すプロファイルと、シェルスクリプトが呼び出すプロファイルがある。

## ログインシェルの場合

ターミナルからログインした場合、 `--login` を指定して起動した場合、`su -` で起動した場合、`ssh` で接続して起動した場合にログインシェルと判断される。

ログインシェル(bash)の場合は以下が呼び出される。上から順に実行される。

```
/etc/profile (Bash が呼び出す)
 └─ /etc/bashrc (/etc/profile が呼び出す)

~/.bash_profile (Bash が呼び出す)
 └─ ~/.bashrc (~/.bash_profile が呼び出す)
     └─ /etc/bashrc (~/.bashrc が呼び出す)
```

*~/.bash_profile* が存在しない場合は *~/.bash_login* を Bash が呼び出す。
*~/.bash_login* が存在しない場合は *~/.profile* の Bash が呼び出す。
以降、*~/.bash_profile* を呼び出す箇所は同じ動きとなる。

例:
```sh
bash --login
```

例:
```sh
ssh <host>
```

例:
```sh
su -
```

ログインシェル(sh)の場合は以下が呼び出される。上から順に実行される。

```
/etc/profile (Bash が呼び出す)
 └─ /etc/bashrc (/etc/profile が呼び出す)

~/.profile (Bash が呼び出す)

$ENV で指定されたファイル (bash が呼び出す)
```

例:
```sh
sh --login
```

## 非対話シェルの場合

スクリプトファイルを指定した場合、`-c` を指定して起動した場合は非対話シェルと判断される。

非対話シェル(bash)の場合は以下が呼び出される。

```
$BASH_ENV で指定されたファイル (bash が呼び出す)
```

例:
```sh
bash -c <command>
```

例:
```sh
su bash -c <command>
```

`su -` コマンドで非対話シェルを起動した場合はログインシェルと同じプロファイルが呼び出される。上から順に実行される。

```
/etc/profile (Bash が呼び出す)
 └─ /etc/bashrc (/etc/profile が呼び出す)

~/.bash_profile (Bash が呼び出す)
 └─ ~/.bashrc (~/.bash_profile が呼び出す)
     └─ /etc/bashrc (~/.bashrc が呼び出す)

$BASH_ENV で指定されたファイル (bash が呼び出す)
```

例:
```sh
su - bash -c <command>
```

`ssh` でコマンドを指定した場合は非対話シェルになるが *~/.bashrc* が呼び出される。上から順に実行される。

```
~/.bashrc  (bash が呼び出す)
 └─ /etc/bashrc (~/.bashrc が呼び出す)
```

例:
```sh
ssh <host> <command>
```

非対話シェル(sh)の場合は何も呼び出されない。


## 上記以外の場合

上記以外の場合(bash)は以下が呼び出される。上から順に実行される。

```
~/.bashrc  (bash が呼び出す)
 └─ /etc/bashrc (~/.bashrc が呼び出す)
```

例:
```sh
bash
```

上記以外の場合(sh)は以下が呼び出される。

```
$ENV で指定されたファイル (bash が呼び出す)
```

例:
```sh
sh
```
