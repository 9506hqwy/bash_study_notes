# プロファイル

Bash を起動したときに読み込まれるプロファイルは以下となる。

Bash が呼び出すプロファイルと、シェルスクリプトが呼び出すプロファイルの2つがある。

## ログインシェルの場合

ターミナルからログインした場合、 `--login` を指定して起動した場合、`su -` で起動した場合、`ssh` で接続して起動した場合にログインシェルと判断される。

ログインシェル(bash)の場合は以下が呼び出される。上から順に実行される。

```text
/etc/profile (Bash が呼び出す)
 └─ /etc/bashrc (/etc/profile が呼び出す)

~/.bash_profile (Bash が呼び出す)
 └─ ~/.bashrc (~/.bash_profile が呼び出す)
     └─ /etc/bashrc (~/.bashrc が呼び出す)
```

*~/.bash_profile* が存在しない場合は *~/.bash_login* を Bash が呼び出す。
*~/.bash_login* が存在しない場合は *~/.profile* を Bash が呼び出す。
以降、*~/.bash_profile* を呼び出す箇所は同じ動きとなる。

```c
// bash 5.2.32 shell.c#L1188
maybe_execute_file (SYS_PROFILE, 1);

if (act_like_sh) /* sh */
    maybe_execute_file ("~/.profile", 1);
else if ((maybe_execute_file ("~/.bash_profile", 1) == 0) &&
            (maybe_execute_file ("~/.bash_login", 1) == 0)) /* bash */
    maybe_execute_file ("~/.profile", 1);
```

例を記載する。

```sh
bash --login
```

例を記載する。

```sh
ssh <host>
```

例を記載する。

```sh
su -
```

ログインシェル(sh)の場合は以下が呼び出される。上から順に実行される。

```text
/etc/profile (Bash が呼び出す)
 └─ /etc/bashrc (/etc/profile が呼び出す)

~/.profile (Bash が呼び出す)

$ENV で指定されたファイル (bash が呼び出す)
```

例を記載する。

```sh
sh --login
```

## 非対話シェルの場合

スクリプトファイルを指定した場合、`-c` を指定して起動した場合は非対話シェルと判断される。

非対話シェル(bash)の場合は以下が呼び出される。

```text
$BASH_ENV で指定されたファイル (bash が呼び出す)
```

```c
// bash 5.2.32 shell.c#L1208
execute_env_file (get_string_value ("BASH_ENV"));
```

例を記載する。

```sh
bash -c <command>
```

例を記載する。

```sh
su bash -c <command>
```

`su -` コマンドで非対話シェルを起動した場合はログインシェルと同じプロファイルが呼び出される。上から順に実行される。

```text
/etc/profile (Bash が呼び出す)
 └─ /etc/bashrc (/etc/profile が呼び出す)

~/.bash_profile (Bash が呼び出す)
 └─ ~/.bashrc (~/.bash_profile が呼び出す)
     └─ /etc/bashrc (~/.bashrc が呼び出す)

$BASH_ENV で指定されたファイル (bash が呼び出す)
```

例を記載する。

```sh
su - bash -c <command>
```

`ssh` でコマンドを指定した場合は非対話シェルになるが *~/.bashrc* が呼び出される。上から順に実行される。

```text
~/.bashrc  (bash が呼び出す)
 └─ /etc/bashrc (~/.bashrc が呼び出す)
```

```c
// bash 5.2.32 shell.c#L1159
maybe_execute_file (bashrc_file, 1);
return;
```

例を記載する。

```sh
ssh <host> <command>
```

非対話シェル(sh)の場合は何も呼び出されない。

## 上記以外の場合

上記以外の場合(bash)は以下が呼び出される。上から順に実行される。

```text
~/.bashrc  (bash が呼び出す)
 └─ /etc/bashrc (~/.bashrc が呼び出す)
```

```c
// bash 5.2.32 shell.c#L1244
maybe_execute_file (bashrc_file, 1);
```

例を記載する。

```sh
bash
```

上記以外の場合(sh)は以下が呼び出される。

```text
$ENV で指定されたファイル (bash が呼び出す)
```

```c
// bash 5.2.32 shell.c#L1248
execute_env_file (get_string_value ("ENV"));
```

例を記載する。

```sh
sh
```

## ログイン時のシェル判定

シェル名の前に `-` を付けるとログインシェルと判断される。

```c
// bash 5.2.32 shell.c#L1800
if (argv0 && *argv0 == '-')
{
    if (*shell_name == '-')
        shell_name++;
    login_shell = 1;
}
```

[login](https://github.com/util-linux/util-linux/blob/v2.40.2/login-utils/login.c#L1561) コマンドは
*/etc/passed* からシェルを読み取って `-` 付きで実行する。

[systemd](https://github.com/systemd/systemd/blob/v256.6/src/run/run.c#L219) コマンドは
run0 時に環境変数 `SHELL` または */etc/passed* からシェルを読み取って `-` 付きで実行する。

## 環境変数 `PATH`

bash で既定値が設定される。

```sh
> strings /bin/bash | grep '/usr/bin'
/usr/local/bin:/usr/bin
```

sudo でコマンドを起動した場合は sudo で既定値が設定される。

```sh
> cat /etc/sudoers | grep -i 'path'
Defaults    secure_path = /sbin:/bin:/usr/sbin:/usr/bin
```

ssh で接続した場合は ssh で既定値が設定される。

```sh
> cat /etc/ssh/sshd_config | grep -i 'path'
# This sshd was compiled with PATH=/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin
```
