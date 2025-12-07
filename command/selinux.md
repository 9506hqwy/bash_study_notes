# SELinux

ツールをインストールする。

```sh
dnf install -y policycoreutils-python-utils setools-console
```

## モード

### Enforcing / Permissive

SELinux のモードを取得する。

```sh
getenforce
```

```text
Enforcing
```

`Permissive` モードに設定する。
`setenforce` は再起動すると元の設定に戻る。

```sh
setenforce 0
getenforce
```

```text
Permissive
```

SELinux の状態を確認する。

```sh
sestatus
```

```text
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   permissive
Mode from config file:          enforcing
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Memory protection checking:     actual (secure)
Max kernel policy version:      33
```

特定のドメインを `Permissive` に設定する。

```sh
semanage permissive -a httpd_t
```

### 無効化(Disabled)

SELinux を無効化するにはカーネルパラメータで `selinux=0` を指定して再起動する。

```sh
grubby --update-kernel ALL --args selinux=0
```

## モジュール

SELinux ポリシーをグループ化したものをモジュールとして管理する。

モジュールを確認する。

```sh
semodule -l | head -n 10
```

```text
abrt
accountsd
acct
afs
afterburn
aide
alsa
amanda
anaconda
apache
```

## コンテキスト(ラベル)

### コンテキストの確認

現在のユーザのコンテキストを確認する。

```sh
id -Z
```

```text
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

プロセスのコンテキストを確認する。

```sh
ps -Z
```

```text
LABEL                               PID TTY          TIME CMD
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 6991 pts/2 00:00:00 bash
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 10188 pts/2 00:00:00 ps
```

プロセスのコンテキストは procfs に保存される。

```sh
cat /proc/1/attr/prev
```

```text
system_u:system_r:kernel_t:s0
```

ファイルのコンテキストを確認する。

```sh
ls -lZ
```

```text
合計 8
-rw-------. 1 root root system_u:object_r:admin_home_t:s0 1112 10月 25 10:27 anaconda-ks.cfg
-rw-------. 1 root root system_u:object_r:admin_home_t:s0  775 10月 25 10:27 original-ks.cfg
```

ファイルのコンテキストは拡張属性に保存される。

```sh
getfattr -d -m . anaconda-ks.cfg
```

```text
# file: anaconda-ks.cfg
security.selinux="system_u:object_r:admin_home_t:s0"

```

### 属性の一覧

SELinux ユーザを確認する。

```sh
seinfo -u
```

```text

Users: 8
   guest_u
   root
   staff_u
   sysadm_u
   system_u
   unconfined_u
   user_u
   xguest_u
```

SELinux ロールを確認する。ユーザとロールは一対多の関係になる。

```sh
seinfo -r
```

```text

Roles: 15
   auditadm_r
   container_user_r
   dbadm_r
   guest_r
   logadm_r
   nx_server_r
   object_r
   secadm_r
   staff_r
   sysadm_r
   system_r
   unconfined_r
   user_r
   webadm_r
   xguest_r
```

SELinux タイプを確認する。ロールとタイプは一対一の関係になる。
サブジェクト(プロセス)に割り当てた SELinux タイプはドメインという。

```sh
seinfo -t | head -n 10
```

```text

Types: 4696
   NetworkManager_dispatcher_chronyc_script_t
   NetworkManager_dispatcher_chronyc_t
   NetworkManager_dispatcher_cloud_script_t
   NetworkManager_dispatcher_cloud_t
   NetworkManager_dispatcher_console_script_t
   NetworkManager_dispatcher_console_t
   NetworkManager_dispatcher_console_var_run_t
   NetworkManager_dispatcher_custom_t
```

クラス(オブジェクトの種類)を確認する。

```sh
seinfo -c | head -n 10
```

```text

Classes: 133
   alg_socket
   anon_inode
   appletalk_socket
   association
   atmpvc_socket
   atmsvc_socket
   ax25_socket
   binder
```

アクセス制御ルール(ブール)を確認する。

```sh
seinfo -b | head -n 10
```

```text

Booleans: 325
   abrt_anon_write
   abrt_handle_event
   abrt_upload_watch_anon_write
   auditadm_exec_content
   authlogin_nsswitch_use_ldap
   authlogin_radius
   authlogin_yubikey
   cdrecord_read_content
```

### ユーザのコンテキスト

Linux ユーザと SELinux ユーザのマッピングを確認する。
設定がないユーザの既定値は制限しない (`unconfined_u`)。

```sh
semanage login -l
```

```text

ログイン名                SELinux ユーザー         MLS/MCS 範囲           サービス

__default__          unconfined_u         s0-s0:c0.c1023       *
root                 unconfined_u         s0-s0:c0.c1023       *
```

SELinux ユーザと SELinux ロールの関係を確認する。

```sh
semanage user -l
```

```text

                ラベリング      MLS/       MLS/
SELinux ユーザー    プレフィックス    MCS レベル    MCS 範囲                         SELinux ロール

guest_u         user       s0         s0                             guest_r
root            user       s0         s0-s0:c0.c1023                 system_r staff_r sysadm_r unconfined_r
staff_u         user       s0         s0-s0:c0.c1023                 system_r staff_r sysadm_r unconfined_r
sysadm_u        user       s0         s0-s0:c0.c1023                 sysadm_r
system_u        user       s0         s0-s0:c0.c1023                 system_r unconfined_r
unconfined_u    user       s0         s0-s0:c0.c1023                 system_r unconfined_r
user_u          user       s0         s0                             user_r
xguest_u        user       s0         s0                             xguest_r
```

### ファイルのコンテキスト

ファイルのコンテキストルールを確認する。

```sh
semanage fcontext -l | head -n 10
```

```text
SELinux fcontext                                   タイプ                コンテキスト

/                                                  directory          system_u:object_r:root_t:s0
/.*                                                all files          system_u:object_r:default_t:s0
/[^/]+                                             regular file       system_u:object_r:etc_runtime_t:s0
/\.autofsck                                        regular file       system_u:object_r:etc_runtime_t:s0
/\.autorelabel                                     regular file       system_u:object_r:etc_runtime_t:s0
/\.ismount-test-file                               regular file       system_u:object_r:sosreport_tmp_t:s0
/\.journal                                         all files          <<None>>
/\.snapshots(/.*)?                                 all files          system_u:object_r:snapperd_data_t:s0
```

ファイルを作成すると親ディレクトリのコンテキストを継承する。
ただし、下記に記載するタイプの移行(`type_transition`)で別のコンテキストに移行できる。

### プロセスのコンテキスト

プロセスは起動元のコンテキストを継承する。
ただし、ドメインの移行(`type_transition`)により別のコンテキストに移行できる。

`passwd` の場合は下記になる。

ファイルは `passwd_exec_t` が設定される。

```sh
ls -lZ /usr/bin/passwd
```

```text
-rwsr-xr-x. 1 root root system_u:object_r:passwd_exec_t:s0 91416  5月 26  2025 /usr/bin/passwd
```

実行したプロセスは `passwd_t` が設定される。

```sh
ps -uZ | grep passwd
```

```text
unconfined_u:unconfined_r:passwd_t:s0-s0:c0.c1023 root 12624 0.0  0.0 7136 4008 pts/1 S+ 00:50   0:00 passwd
```

root (`unconfined_t`) から `passwd_t` ドメインに移行するルールを確認する。

```sh
sesearch -T -s unconfined_t -c process | grep passwd_t
```

```text
type_transition unconfined_t passwd_exec_t:process passwd_t;
```

ドメインの移行が許可されているか確認する。
`unconfined_t` ドメインが `domain` タイプのプロセスに `transition` が許可されている。

```sh
sesearch -A -s unconfined_t -t passwd_t -c process -p transition
```

```text
allow unconfined_t domain:process transition;
```

コマンドの実行が許可されているか確認する。

```sh
sesearch -A -s unconfined_t -t passwd_exec_t -c file -p execute
```

```text
allow files_unconfined_type file_type:file { append audit_access create execute execute_no_trans getattr ioctl link lock map mounton open quotaon read relabelfrom relabelto rename setattr swapon unlink watch watch_mount watch_reads watch_sb watch_with_perm write };
```

`passwd_exec_t` が `passwd_t` ドメインを起動できるか確認する。

```sh
sesearch -A -s passwd_t -t passwd_exec_t -c file -p entrypoint
```

```text
allow passwd_t passwd_exec_t:file { entrypoint execute getattr ioctl lock map open read };
```

そのため `unconfined_t` ドメインが `passwd_exec_t` タイプのファイルを実行すると `passwd_t` ドメインが起動する。

なお、`domain` など `_t` がつかないタイプはタイプ属性でタイプの集合になる。

```sh
seinfo -a domain -x | grep passwd_t
```

```text
        httpd_passwd_t
        passwd_t
        sysadm_passwd_t
```

### ポートのコンテキスト

ポートのコンテキストを確認する。

```sh
semanage port -l | head -n 10
```

```text
SELinux ポートタイプ                 プロト      ポート番号

afs3_callback_port_t           tcp      7001
afs3_callback_port_t           udp      7001
afs_bos_port_t                 udp      7007
afs_fs_port_t                  tcp      2040
afs_fs_port_t                  udp      7000, 7005
afs_ka_port_t                  udp      7004
afs_pt_port_t                  tcp      7002
afs_pt_port_t                  udp      7002
```

## SELinux ポリシー

SELinux ポリシーの設定を確認する。

```sh
semanage boolean -l | head -n 10
```

```text
SELinux boolean                状態  初期値 説明

abrt_anon_write                (オフ   ,   オフ)  abrt が anon write できるようにします
abrt_handle_event              (オン   ,   オン)  abrt が handle event できるようにします
abrt_upload_watch_anon_write   (オン   ,   オン)  abrt が upload watch anon write できるようにします
auditadm_exec_content          (オン   ,   オン)  auditadm が exec content できるようにします
authlogin_nsswitch_use_ldap    (オフ   ,   オフ)  authlogin が nsswitch use ldap できるようにします
authlogin_radius               (オフ   ,   オフ)  authlogin が radius できるようにします
authlogin_yubikey              (オフ   ,   オフ)  authlogin が yubikey できるようにします
cdrecord_read_content          (オフ   ,   オフ)  cdrecord が read content できるようにします
```

ブールに含まれるルールを確認する。

```sh
sesearch -A -b abrt_anon_write
```

```text
allow abrt_t public_content_rw_t:dir { add_name create link remove_name rename reparent rmdir setattr unlink watch watch_reads write }; [ abrt_anon_write ]:True
allow abrt_t public_content_rw_t:dir { add_name remove_name write }; [ abrt_anon_write ]:True
allow abrt_t public_content_rw_t:dir { add_name remove_name write }; [ abrt_anon_write ]:True
allow abrt_t public_content_rw_t:dir { add_name remove_name write }; [ abrt_anon_write ]:True
allow abrt_t public_content_rw_t:file { append create link rename setattr unlink watch watch_reads write }; [ abrt_anon_write ]:True
allow abrt_t public_content_rw_t:lnk_file { append create ioctl link lock rename setattr unlink watch watch_reads write }; [ abrt_anon_write ]:True
```

## ラベリング

### ユーザのラベリング

ユーザのコンテキストを変更する。

```sh
id -Z
```

```text
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

マッピングを追加する。

```sh
semanage login -a -s user_u -r s0 user01
semanage login -l
```

```text

ログイン名                SELinux ユーザー         MLS/MCS 範囲           サービス

__default__          unconfined_u         s0-s0:c0.c1023       *
root                 unconfined_u         s0-s0:c0.c1023       *
user01               user_u               s0                   *
```

再ログイン後に確認する。

```sh
id -Z
```

```text
user_u:user_r:user_t:s0
```

### ファイルのラベリング

ファイルのコンテキストを変更する。`user_home_t` が割り当たる。

```sh
touch a.txt
ls -lZ a.txt
```

```text
-rw-r--r--. 1 user01 user01 user_u:object_r:user_home_t:s0 0 12月  7 11:28 a.txt
```

一時的にコンテキストを変更する。

```sh
chcon -t passwd_file_t a.txt
ls -lZ a.txt
```

```text
-rw-r--r--. 1 user01 user01 user_u:object_r:passwd_file_t:s0 0 12月  7 11:28 a.txt
```

ユーザ user01 の書き込みは失敗する。

```sh
echo a > a.txt
```

```text
-bash: a.txt: 許可がありません
```

audit.log に拒否されたログが出力される。

```sh
ausearch -o file -sv no -ul user01
```

```text
time->Sun Dec  7 11:32:08 2025
type=PROCTITLE msg=audit(1765074728.251:518): proctitle="-bash"
type=SYSCALL msg=audit(1765074728.251:518): arch=c000003e syscall=257 success=no exit=-13 a0=ffffff9c a1=557da7272e00 a2=201 a3=0 items=0 ppid=5501 pid=5502 auid=1000 uid=1000 gid=1000 euid=1000 suid=1000 fsuid=1000 egid=1000 sgid=1000 fsgid=1000 tty=pts1 ses=7 comm="bash" exe="/usr/bin/bash" subj=user_u:user_r:user_t:s0 key=(null)
type=AVC msg=audit(1765074728.251:518): avc:  denied  { write } for  pid=5502 comm="bash" name="a.txt" dev="vda5" ino=137 scontext=user_u:user_r:user_t:s0 tcontext=user_u:object_r:passwd_file_t:s0 tclass=file permissive=0
```

一時的な変更のためもとに戻す (再起動では維持される)。

```sh
restorecon a.txt
ls -lZ a.txt
```

```text
-rw-r--r--. 1 user01 user01 user_u:object_r:user_home_t:s0 0 12月  7 11:28 a.txt
```

書き込みできる。

```sh
echo a > a.txt
cat a.txt
```

```text
a
```

永続的にコンテキストを変更する。

```sh
semanage fcontext -a -t passwd_file_t -f f -r s0 -s unconfined_u /home/user01/a.txt
```

```text
/home/user01/a.txt                                 regular file       unconfined_u:object_r:passwd_file_t:s0
```

コンテキストをもとに戻す。

```sh
restorecon a.txt
ls -lZ a.txt
```

```text
-rw-r--r--. 1 user01 user01 user_u:object_r:passwd_file_t:s0 2 12月  7 11:41 a.txt
```

ユーザ user01 の書き込みは失敗する。

```sh
echo a >> a.txt
```

```text
-bash: a.txt: 許可がありません
```

必要な権限を確認する。

```sh
grep denied /var/log/audit/audit.log | grep a.txt | tail -n 1 | audit2allow
```

```text


#============= user_t ==============
allow user_t passwd_file_t:file append;
```

## コンテナ

user01 のマッピングを削除する。

```sh
semanage login -d user01
semanage login -l
```

```text

ログイン名                SELinux ユーザー         MLS/MCS 範囲           サービス

__default__          unconfined_u         s0-s0:c0.c1023       *
root                 unconfined_u         s0-s0:c0.c1023       *
```

マウントするディレクトリを作成する。

```sh
mkdir container
ls -ldZ container
```

```text
drwxr-xr-x. 2 user01 user01 unconfined_u:object_r:user_home_t:s0 6 12月  7 13:01 container/
```

コンテナを起動する。
`:Z` を指定するため再ラベリングを行いディレクトリのコンテキストが `container_file_t` タイプに変わる。
カテゴリはコンテナが変わるごとに別の値が割り当たる。

```sh
podman run --rm -it -v $(pwd)/container:/app:Z -w /app redhat/ubi10 /bin/bash
ls -ldZ
```

```text
drwxr-xr-x. 2 root root system_u:object_r:container_file_t:s0:c308,c584 6 Dec  7 04:01 .
```

プロセスのコンテキストを確認する。

`podman` は `container_runtime_t` ドメインで起動する。

```sh
ps -u user01 -Z | grep podman
```

```text
unconfined_u:unconfined_r:container_runtime_t:s0-s0:c0.c1023 7876 pts/1 00:00:01 podman
```

`bash` は `container_t` ドメインで起動する。
コンテナを `--privileged` で起動すると `bash` は `spc_t` (Super Privileged Container) ドメインで起動する。

```sh
ps -u user01 -Z | grep bash
```

```text
unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023 7667 pts/1 00:00:00 bash
system_u:system_r:container_t:s0:c308,c584 7734 pts/0 00:00:00 bash
```

podman の既定のコンテキストを確認する [container-selinux](https://github.com/containers/container-selinux/blob/main/container_contexts)。

```sh
cat /usr/share/containers/selinux/contexts
```

```text
process = "system_u:system_r:container_t:s0"
file = "system_u:object_r:container_file_t:s0"
ro_file="system_u:object_r:container_ro_file_t:s0"
kvm_process = "system_u:system_r:container_kvm_t:s0"
init_process = "system_u:system_r:container_init_t:s0"
engine_process = "system_u:system_r:container_engine_t:s0"
```

コンテナ設定の `ProcessLabel` がコンテナ内のプロセスに設定される [set_selinux_label](https://github.com/containers/crun/blob/main/src/libcrun/utils.c#L871-L882)。

`container_runtime_t` が `container_t` へドメインの移行は許可される。

```sh
sesearch -A -s container_runtime_t -t container_t -c process -p transition
```

```text
allow unconfined_domain_type container_domain:process { dyntransition transition };
```

`container_runtime_t` が `shell_exec_t` を実行できる。

```sh
sesearch -A -s container_runtime_t -t shell_exec_t -c file -p execute
```

```text
allow files_unconfined_type file_type:file { append audit_access create execute execute_no_trans getattr ioctl link lock map mounton open quotaon read relabelfrom relabelto rename setattr swapon unlink watch watch_mount watch_reads watch_sb watch_with_perm write };
```

`shell_exec_t` が `container_t` を起動できる。

```sh
sesearch -A -s container_t -t shell_exec_t -c file -p entrypoint
```

```text
allow svirt_sandbox_domain exec_type:file { entrypoint execute execute_no_trans getattr ioctl lock map open read };
```

## 参照

- [SELinux ユーザーおよび管理者のガイド](https://docs.redhat.com/ja/documentation/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/index)
- [SELinux の使用](https://docs.redhat.com/ja/documentation/red_hat_enterprise_linux/10/html/using_selinux/index)
