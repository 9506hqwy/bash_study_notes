# `unshare` コマンド

名前空間を作成する。

下記の名前空間が作成できる。

| オプション | 対象           |
| :--------- | :------------- |
| --mount    | マウント       |
| --uts      | ホスト名       |
| --ipc      | プロセス間通信 |
| --net      | ネットワーク   |
| --pid      | プロセス ID    |
| --user     | UID, GID       |
| --cgroup   | cgroup         |
| --time     | uptime         |

## プロセスの名前空間の確認

procfs でプロセスの名前空間を確認する。
数値が名前空間となる。

```sh
ls -l /proc/$$/ns
```

```text
合計 0
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 cgroup -> 'cgroup:[4026531835]'
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 ipc -> 'ipc:[4026531839]'
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 mnt -> 'mnt:[4026531841]'
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 net -> 'net:[4026531840]'
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 pid -> 'pid:[4026531836]'
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 pid_for_children -> 'pid:[4026531836]'
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 time -> 'time:[4026531834]'
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 time_for_children -> 'time:[4026531834]'
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 user -> 'user:[4026531837]'
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:27 uts -> 'uts:[4026531838]'
```

## 名前空間 `mounts`

新たなマウント名前空間を作成して `bash` を起動する。

```sh
# 親プロセス
ls -l /proc/$$/ns/mnt
```

```text
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:36 mnt -> 'mnt:[4026531841]'
```

`sudo` するためユーザは root になる。

```sh
# 親プロセス
sudo unshare --mount /bin/bash
```

`mnt` 以外の名前空間は親プロセスの名前空間を継承している。

```sh
# 子プロセス
ls -l /proc/$$/ns/mnt
```

```text
lrwxrwxrwx. 1 root root 0 12月 13 22:36 mnt -> 'mnt:[4026532186]'
```

tmpfs を */mnt* にマウントする。

```sh
# 子プロセス
mount -t tmpfs tmpfs /mnt
```

マウントを確認する。

```sh
# 子プロセス
findmnt /mnt
```

```text
TARGET SOURCE FSTYPE OPTIONS
/mnt   tmpfs  tmpfs  rw,relatime,seclabel,inode64
```

親プロセスは参照できない。

```sh
# 親プロセス
findmnt /mnt
```

```text
```

## 名前空間 `UTS`

新たな Unix Timesharing System (UTS) 名前空間を作成して `bash` を起動する。

```sh
# 親プロセス
ls -l /proc/$$/ns/uts
```

```text
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 23:58 /proc/1710/ns/uts -> 'uts:[4026531838]'
```

`sudo` するためユーザは root になる。

```sh
# 親プロセス
sudo unshare --uts /bin/bash
```

`uts` 以外の名前空間は親プロセスの名前空間を継承している。

```sh
# 子プロセス
ls -l /proc/$$/ns/uts
```

```text
lrwxrwxrwx. 1 root root 0 12月 13 23:59 /proc/2011/ns/uts -> 'uts:[4026532186]'
```

ホスト名を変更する。`hostnamectl` は影響を受けない。

```sh
# 子プロセス
hostname child.home.local
```

ホスト名を確認する。

```sh
# 子プロセス
hostname
```

```text
child.home.local
```

親プロセスのホスト名は変更されない。

```sh
# 親プロセス
hostname
```

```text
docker.home.local
```

## 名前空間 `System V IPC`

キューを作成する。

```sh
# 親プロセス
ipcmk --queue
```

```text
Message queue id: 0
```

プロセス間共有を確認する。

```sh
# 親プロセス
ipcs
```

```text

------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages
0x5b0f0c7b 0          centos10   644        0            0

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status

------ Semaphore Arrays --------
key        semid      owner      perms      nsems

```

新たなプロセス間通信の名前空間を作成して `bash` を起動する。

```sh
# 親プロセス
ls -l /proc/$$/ns/ipc
```

```text
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:36 /proc/1690/ns/ipc -> 'ipc:[4026531839]'
```

`sudo` するためユーザは root になる。

```sh
# 親プロセス
sudo unshare --ipc bash
```

`ipc` 以外の名前空間は親プロセスの名前空間を継承している。

```sh
# 子プロセス
ls -l /proc/$$/ns/ipc
```

```text
lrwxrwxrwx. 1 root root 0 12月 14 00:36 /proc/2352/ns/ipc -> 'ipc:[4026532186]'
```

プロセス間共有を確認する。親プロセスのプロセス間通信は継承されない。

```sh
# 子プロセス
ipcs
```

```text

------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status

------ Semaphore Arrays --------
key        semid      owner      perms      nsems

```

## 名前空間 `network`

新たなネットワーク名前空間を作成して `bash` を起動する。

```sh
# 親プロセス
ls -l /proc/$$/ns/net
```

```text
lrwxrwxrwx. 1 centos10 centos10 0 12月 14 13:01 /proc/1990/ns/net -> 'net:[4026531840]'
```

`sudo` するためユーザは root になる。

```sh
# 親プロセス
sudo unshare --net /bin/bash
```

`net` 以外の名前空間は親プロセスの名前空間を継承している。

```sh
# 子プロセス
ls -l /proc/$$/ns/net
```

```text
lrwxrwxrwx. 1 root root 0 12月 14 13:47 /proc/2317/ns/net -> 'net:[4026532186]'
```

ネットワードアダプタがループバックのみになる。

```sh
# 子プロセス
ip link show
```

```text
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
```

名前空間を接続するため veth peer を作成する。

```sh
# 親プロセス
sudo ip link add name veth0-h type veth peer name veth0-c
ip link show type veth
```

```text
5: veth0-c@veth0-h: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether ce:66:a7:6c:c9:83 brd ff:ff:ff:ff:ff:ff
6: veth0-h@veth0-c: <BROADCAST,MULTICAST,M-DOWN> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 82:5a:92:78:f2:53 brd ff:ff:ff:ff:ff:ff
```

veth0-c をネットワーク名前空間に割り当てる。

```sh
# 親プロセス
sudo ip link set dev veth0-c netns 2317
ip link show type veth
```

```text
6: veth0-h@if5: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 82:5a:92:78:f2:53 brd ff:ff:ff:ff:ff:ff link-netnsid 0
```

名前空間内に veth peer が移行する。

```sh
# 子プロセス
ip link show type veth
```

```text
5: veth0-c@if6: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether ce:66:a7:6c:c9:83 brd ff:ff:ff:ff:ff:ff link-netnsid 0
```

親プロセスに IP アドレスを割り当てて起動する。

```sh
# 親プロセス
sudo ip addr add 10.0.0.1/24 dev veth0-h
sudo ip link set dev veth0-h up
ip addr show veth0-h
```

```text
6: veth0-h@if5: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state LOWERLAYERDOWN group default qlen 1000
    link/ether 82:5a:92:78:f2:53 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.0.0.1/24 scope global veth0-h
       valid_lft forever preferred_lft forever
```

子プロセスに IP アドレスを割り当てて起動する。

```sh
# 子プロセス
sudo ip addr add 10.0.0.2/24 dev veth0-c
sudo ip link set dev veth0-c up
ip addr show veth0-c
```

```text
5: veth0-c@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether ce:66:a7:6c:c9:83 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.0.0.2/24 scope global veth0-c
       valid_lft forever preferred_lft forever
    inet6 fe80::cc66:a7ff:fe6c:c983/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
```

疎通を確認する。

```sh
# 子プロセス
ping 10.0.0.1
```

```text
PING 10.0.0.1 (10.0.0.1) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.0.1: icmp_seq=1 ttl=64 時間=0.048ミリ秒
```

## 名前空間 `pid`

新たなプロセス ID 名前空間を作成して `bash` を起動する。

```sh
# 親プロセス
ls -l /proc/$$/ns/pid
```

```text
lrwxrwxrwx. 1 centos10 centos10 0 12月 13 22:36 /proc/1710/ns/pid -> 'pid:[4026531836]'
```

`--fork` を追加する必要がある。プロセスの名前空間は新たな子プロセスに適用される。

```sh
# 親プロセス
sudo unshare --pid --fork bash
```

procfs をマウントしていないので名前空間は親プロセスの名前空間が見える。

```sh
# 子プロセス
ls -l /proc/$$/ns/pid
```

```text
lrwxrwxrwx. 1 root root 0 12月 13 22:09 /proc/1/ns/pid -> 'pid:[4026531836]'
```

プロセスを確認する。

```sh
# 子プロセス
echo $$
```

```text
1
```

procfs をマウントして新たなプロセス ID 名前空間を作成する。

```sh
# 親プロセス
sudo unshare --pid --fork --mount-proc=/proc bash
```

```sh
# 子プロセス
ls -l /proc/$$/ns/pid
```

```text
lrwxrwxrwx. 1 root root 0 12月 14 12:59 /proc/1/ns/pid -> 'pid:[4026532249]'
```

親プロセスの親子関係を確認する。
親プロセスの PID 2057 が子プロセスの PID 1 に見える。

```sh
# 親プロセス
pstree -p -s 2010
```

```text
systemd(1)---tmux: server(1989)---bash(2010)---sudo(2027)---sudo(2055)---unshare(2056)---bash(2057)
```

## 名前空間 `user`

新たなユーザ名前空間を作成して `bash` を起動する。

```sh
# 親プロセス
ls -l /proc/$$/ns/user
```

```text
lrwxrwxrwx. 1 centos10 centos10 0 12月 14 13:01 /proc/1990/ns/user -> 'user:[4026531837]'
```

`sudo` するためユーザは root になる。

```sh
# 親プロセス
sudo unshare --user /bin/bash
```

`user` 以外の名前空間は親プロセスの名前空間を継承している。

```sh
# 子プロセス
ls -l /proc/$$/ns/user
```

```text
lrwxrwxrwx. 1 nobody nobody 0 12月 14 13:25 /proc/2208/ns/user -> 'user:[4026532186]'
```

uid/gid のマッピングを指定していないので既定値が割り当たる。

```sh
# 子プロセス
id
```

```text
uid=65534(nobody) gid=65534(nobody) groups=65534(nobody) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

親プロセスでは `sudo` しているため root で見える。

```sh
# 親プロセス
sudo ls -l /proc/2208/ns/user
```

```text
lrwxrwxrwx. 1 root root 0 12月 14 13:25 /proc/2208/ns/user -> 'user:[4026532186]'
```

プロセス ID (2208) の名前空間外ユーザ ID 0 (root) を名前空間内ユーザ ID 1000 (centos10) にマッピングする。

```sh
# 親プロセス
echo 1000 0 1 | sudo tee /proc/2208/uid_map
echo 1000 0 1 | sudo tee /proc/2208/gid_map
```

uid/gid のマッピングされることを確認する。

```sh
# 子プロセス
id
```

```text
uid=1000(centos10) gid=1000(centos10) groups=1000(centos10) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

## 名前空間 `cgroup`

```{note}
TODO
```

## 名前空間 `time`

新たな時間名前空間を作成して `bash` を起動する。

```sh
# 親プロセス
ls -l /proc/$$/ns/time
```

```text
lrwxrwxrwx. 1 centos10 centos10 0 12月 14 13:01 /proc/1990/ns/time -> 'time:[4026531834]'
```

`sudo` するためユーザは root になる。

```sh
# 親プロセス
sudo unshare --time /bin/bash
```

`time` 以外の名前空間は親プロセスの名前空間を継承している。

```sh
# 子プロセス
ls -l /proc/$$/ns/time
```

```text
lrwxrwxrwx. 1 root root 0 12月 14 14:27 /proc/2521/ns/time -> 'time:[4026532186]'
```

`date` は影響を受けない。

```sh
# 子プロセス
sudo date -s "2026-01-01 00:00:00"
```

```text
2026年  1月  1日 木曜日 00:00:00 JST
```

親プロセスにも反映される。

```sh
# 親プロセス
date
```

```text
2026年  1月  1日 木曜日 00:00:33 JST
```

boottime をズラして `bash` を起動する。

```sh
# 親プロセス
sudo unshare --time --boottime 16500 /bin/bash
```

子プロセスの起動時間を確認する。

```sh
# 子プロセス
uptime -s
```

```text
2025-12-31 14:53:46
```

親プロセスの起動時間を確認する。

```sh
# 親プロセス
uptime -s
```

```text
2025-12-31 19:28:46
```

設定を確認する。

```sh
# 子プロセス
cat /proc/$$/timens_offsets
```

```text
monotonic           0         0
boottime        16500         0
```

## 参考

- [The 7 most used Linux namespaces](https://www.redhat.com/en/blog/7-linux-namespaces)
- [Building containers by hand: The PID namespace](https://www.redhat.com/ja/blog/pid-namespace)
- [Building containers by hand using namespaces: The net namespace](https://www.redhat.com/en/blog/net-namespaces)
- [Building a container by hand using namespaces: The UTS namespace](https://www.redhat.com/ja/blog/uts-namespace)
- [Building a Linux container by hand using namespaces](https://www.redhat.com/en/blog/building-container-namespaces)
- [Building a container by hand using namespaces: The mount namespace](https://www.redhat.com/en/blog/mount-namespaces)
- [unshare --pid /bin/bash - fork cannot allocate memory](https://stackoverflow.com/questions/44666700/unshare-pid-bin-bash-fork-cannot-allocate-memory)
