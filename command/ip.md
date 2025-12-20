# `ip` コマンド

## 表示オプション

### `-s`

統計情報を表示する。

```sh
ip -s addr show eth0
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:00:26:06 brd ff:ff:ff:ff:ff:ff
    altname enx00155d002606
    inet 172.24.96.11/20 brd 172.24.111.255 scope global dynamic noprefixroute eth0
       valid_lft 67414sec preferred_lft 67414sec
    inet6 fe80::215:5dff:fe00:2606/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
    RX:  bytes packets errors dropped  missed   mcast
        398290    2299      0       0       0    1632
    TX:  bytes packets errors dropped carrier collsns
         54364     642      0       0       0       0
```

### `-d`

詳細表示する。

```sh
ip -d addr show eth0
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:00:26:06 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65521 numtxqueues 64 numrxqueues 64 gso_max_size 62780 gso_max_segs 65535 tso_max_size 62780 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 62780 gro_ipv4_max_size 65536 parentbus vmbus parentdev d2916833-b8d8-44ec-89dc-3f4cd7d84668
    altname enx00155d002606
    inet 172.24.96.11/20 brd 172.24.111.255 scope global dynamic noprefixroute eth0
       valid_lft 67491sec preferred_lft 67491sec
    inet6 fe80::215:5dff:fe00:2606/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

### `-r`

### `-h`

### `-iec`

### `-j`

JSON 形式で表示する。

```sh
ip -j addr show eth0 | jq
```

```json
[
  {
    "ifindex": 2,
    "ifname": "eth0",
    "flags": [
      "BROADCAST",
      "MULTICAST",
      "UP",
      "LOWER_UP"
    ],
    "mtu": 1500,
    "qdisc": "mq",
    "operstate": "UP",
    "group": "default",
    "txqlen": 1000,
    "link_type": "ether",
    "address": "00:15:5d:00:26:06",
    "broadcast": "ff:ff:ff:ff:ff:ff",
    "altnames": [
      "enx00155d002606"
    ],
    "addr_info": [
      {
        "family": "inet",
        "local": "172.24.96.11",
        "prefixlen": 20,
        "broadcast": "172.24.111.255",
        "scope": "global",
        "dynamic": true,
        "noprefixroute": true,
        "label": "eth0",
        "valid_life_time": 67459,
        "preferred_life_time": 67459
      },
      {
        "family": "inet6",
        "local": "fe80::215:5dff:fe00:2606",
        "prefixlen": 64,
        "scope": "link",
        "noprefixroute": true,
        "valid_life_time": 4294967295,
        "preferred_life_time": 4294967295
      }
    ]
  }
]
```

### `-p`

### `-f`

プロトコルファミリを指定して表示する。

| オプション  | ショートカット |
| :---------- | :------------- |
| `-f inet`   | `-4`           |
| `-f inet6`  | `-6`           |
| `-f bridge` | `-B`           |
| `-f mpls`   | `-M`           |
| `-f link`   | `-0`           |

IPv4 を表示する。

```sh
ip -4 add show eth0
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    altname enx00155d002606
    inet 172.24.96.11/20 brd 172.24.111.255 scope global dynamic noprefixroute eth0
       valid_lft 67151sec preferred_lft 67151sec
```

IPv6 を表示する。

```sh
ip -6 add show eth0
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    altname enx00155d002606
    inet6 fe80::215:5dff:fe00:2606/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

ネットワークプロトコルを表示しない。

```sh
ip -0 add show eth0
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:00:26:06 brd ff:ff:ff:ff:ff:ff
    altname enx00155d002606
```

### `-l`

### `-echo`

### `-br`

簡易表示する。

```sh
ip -br addr show eth0
```

```text
eth0             UP             172.24.96.11/20 fe80::215:5dff:fe00:2606/64
```

### `-o`

1 レコードを 1 行で表示する。

```sh
ip -o add show eth0
```

```text
2: eth0    inet 172.24.96.11/20 brd 172.24.111.255 scope global dynamic noprefixroute eth0\       valid_lft 66864sec preferred_lft 66864sec
2: eth0    inet6 fe80::215:5dff:fe00:2606/64 scope link noprefixroute \       valid_lft forever preferred_lft forever
```

### `-t`

### `-ts`

### `-b`

### `-rc`

### `-n`

### `-N`

値を数値で表示する。

```sh
ip -N addr show eth0
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group 0 qlen 1000
    link/[1] 00:15:5d:00:26:06 brd ff:ff:ff:ff:ff:ff
    altname enx00155d002606
    inet 172.24.96.11/20 brd 172.24.111.255 scope 0 dynamic noprefixroute eth0
       valid_lft 66532sec preferred_lft 66532sec
    inet6 fe80::215:5dff:fe00:2606/64 scope 253 noprefixroute
       valid_lft forever preferred_lft forever
```

### `-a`

### `-c`

## オブジェクト address

アドレスを管理する。

### アドレスの表示

アドレスを表示する。

```sh
ip addr show dev eth0
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:00:26:06 brd ff:ff:ff:ff:ff:ff
    altname enx00155d002606
    inet 172.24.96.11/20 brd 172.24.111.255 scope global dynamic noprefixroute eth0
       valid_lft 77965sec preferred_lft 77965sec
    inet6 fe80::215:5dff:fe00:2606/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

[print_addrinfo](https://github.com/iproute2/iproute2/blob/main/ip/ipaddress.c#L1515) で表示される。

| 項目                | 値                                      | 備考                                                                  |
| :------------------ | :-------------------------------------- | :-------------------------------------------------------------------- |
| family              | inet / inet6                            | inet:IPv4, inet6:IPv6                                                 |
| local               | 172.24.96.11 / fe80::215:5dff:fe00:2606 | アドレス                                                              |
| prfixlen            | 20 / 64                                 | ネットワークアドレス長                                                |
| broadcast           | 172.24.111.255                          | ブロードキャストアドレス                                              |
| scope               | global                                  | ループバック (host), ブロードキャストドメイン (link), その他 (global) |
| dynamic             | true                                    | DHCP / IPv6 ステートレスアドレス (true)                               |
| noprefixroute       | true / true                             | ルーティングテーブルを自動的に操作しない (true)                       |
| label               | eth0                                    | ラベル                                                                |
| valid_life_time     | 73576 / 4294967295                      | アドレスの有効期限                                                    |
| preferred_life_time | 73576 / 4294967295                      | アドレスの推奨期限                                                    |

他の項目は [デバイスの表示](#デバイスの表示) を参照する。

ブリッジのみを表示する。

```sh
ip addr show type bridge
```

```text
4: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 26:21:9e:72:d6:30 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever
```

### アドレスの制御

アドレスを追加する。

```sh
sudo ip addr add 10.0.0.1/24 dev eth0
ip addr show to 10.0.0.1/24
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    altname enx00155d002606
    inet 10.0.0.1/24 scope global eth0
       valid_lft forever preferred_lft forever
```

ルーティングを確認する。

```sh
ip route show exact 10.0.0.1/24
```

```text
10.0.0.0/24 dev eth0 proto kernel scope link src 10.0.0.1
```

アドレスを削除する。

```sh
sudo ip addr delete 10.0.0.1/24 dev eth0
ip addr show to 10.0.0.1/24
```

```text
```

### アドレス設定の保存と確認

アドレスの設定を保存する。

```sh
ip addr save dev eth0 > eth0.bin
ls -l eth0.bin
```

```text
-rw-r--r--. 1 centos10 centos10 164 12月 16 22:20 eth0.bin
```

保存した設定を確認する。

```sh
ip addr showdump < eth0.bin
```

```text
if2:
    inet 172.24.96.11/20 brd 172.24.111.255 scope global dynamic noprefixroute eth0
       valid_lft 85018sec preferred_lft 85018sec
if2:
    inet6 fe80::215:5dff:fe00:2606/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

インターフェイスの設定をフラッシュする。

```sh
sudo ip addr flush dev eth0
ip addr show dee eth0
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:00:26:06 brd ff:ff:ff:ff:ff:ff
    altname enx00155d002606
    inet 172.24.96.11/20 brd 172.24.111.255 scope global dynamic noprefixroute eth0
       valid_lft 84896sec preferred_lft 84896sec
```

設定をリストアする。
DHCP 設定のため既に割り当たっているとエラーが発生する。

```sh
sudo ip addr restore < eth0.bin
```

```text
Error: ipv4: Address already assigned.
Error: ipv6: address already assigned.
```

## オブジェクト addrlabel

IPv6 のアドレス選択で利用するポリシーテーブルのラベルを管理する。

## オブジェクト fou

Foo over UDP (FOU) を管理する。

## オブジェクト ila

IPv6 の Identifier Locator Addressing (ILA) を管理する。

## オブジェクト ioam

IPv6 の In-situ OAM (IOAM) を管理する。

## オブジェクト l2tp

L2TPv3 トンネルプロトコルを管理する。

## オブジェクト link

リンクデバイスを管理する。

### デバイスの表示

デバイスを表示する。`-d` を指定して詳細を表示する。

```sh
ip -d link show eth0
```

```text
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DEFAULT group default qlen 1000
    link/ether 00:15:5d:00:26:06 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65521 addrgenmode none numtxqueues 64 numrxqueues 64 gso_max_size 62780 gso_max_segs 65535 tso_max_size 62780 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 62780 gro_ipv4_max_size 65536 parentbus vmbus parentdev d2916833-b8d8-44ec-89dc-3f4cd7d84668
    altname enx00155d002606
```

[print_linkinfo](https://github.com/iproute2/iproute2/blob/main/ip/ipaddress.c#L976) で表示される。

| 項目                | 値                                   | 備考                                                                                                                       |
| :------------------ | :----------------------------------- | :------------------------------------------------------------------------------------------------------------------------- |
| ifindex             | 2                                    | インターフェイスインデックス                                                                                               |
| ifname              | eth0                                 | 名前                                                                                                                       |
| flags               | BROADCAST,MULTICAST,UP,LOWER_UP      | ブロードキャストが有効 (BROADCAST), マルチキャストが有効 (MULTICAST)、インターフェイスが有効 (UP), リンクアップ (LOWER_UP) |
| mtu                 | 1500                                 | 最大転送単位                                                                                                               |
| qdisc               | mq                                   | キューイング規則 (Queueing discipline)                                                                                     |
| operstate           | UP                                   | リンク状態。UNKNOWN,NOTPRESENT,DOWN,LOWERLAYERDOWN,TESTING,DORMANT,UP                                                      |
| linkmode            | DEFAULT                              | リンクモード。DEFAULT,DORMANT                                                                                              |
| group               | default                              | インターフェイスグループ。default(0)                                                                                       |
| txqlen              | 1000                                 | 転送キュー長                                                                                                               |
| link_type           | ether                                | デバイス種別。ether: Layer2 アドレス                                                                                       |
| address             | 00:15:5d:00:26:06                    | MAC アドレス                                                                                                               |
| broadcast           | ff:ff:ff:ff:ff:ff                    | プロードキャストアドレス                                                                                                   |
| promiscuity         | 0                                    | プロミスキャスモードが無効 (0)                                                                                             |
| allmulti            | 0                                    | すべてのマルチキャストパケット受信が無効 (0)                                                                               |
| min_mtu             | 68                                   | 最小 MTU                                                                                                                   |
| max_mtu             | 65521                                | 最大 MTU                                                                                                                   |
| linkinfo            | -                                    | ブリッジ情報                                                                                                               |
| inet6_addr_gen_mode | none                                 | SLAAC のアドレス生成モード。eui64,none,stable_secret,random                                                                |
| num_tx_queues       | 64                                   | 転送キュー数                                                                                                               |
| num_rx_queues       | 64                                   | 受信キュー数                                                                                                               |
| gso_max_size        | 62780                                | Generic Segmentation Offload (GSO) パケットの最大サイズ                                                                    |
| gso_max_segs        | 65535                                | GSO の最大セグメント数                                                                                                     |
| tso_max_size        | 62780                                | TCP Segmentation Offload (TSO) パケットの最大サイズ                                                                        |
| tso_max_segs        | 65535                                | TSO の最大セグメント数                                                                                                     |
| gro_max_size        | 65536                                | Generic Receive Offload (GRO) パケットの最大サイズ                                                                         |
| gso_ipv4_max_size   | 62780                                | GSO パケットの最大数                                                                                                       |
| gro_ipv4_max_size   | 65536                                | GRO パケットの最大数                                                                                                       |
| parentbus           | vmbus                                | 親デバイスのバス名                                                                                                         |
| parentdev           | d2916833-b8d8-44ec-89dc-3f4cd7d84668 | 親デバイス名                                                                                                               |
| altnames            | enx00155d002606                      | 別名 (en:イーサネット + x:アドレス + 00155d002606:MAC アドレス)                                                            |

ブリッジ情報は [print_linktype](https://github.com/iproute2/iproute2/blob/main/ip/ipaddress.c#L229) で表示される。

### デバイスの制御

デバイスを有効化する。

```sh
sudo ip link set eth0 up
ip -br link show eth0
```

```text
eth0             UP             00:15:5d:00:26:06 <BROADCAST,MULTICAST,UP,LOWER_UP>
```

デバイスを無効化する。

```sh
sudo ip link set eth0 down
ip -br link show eth0
```

```text
eth0             DOWN           00:15:5d:00:26:06 <BROADCAST,MULTICAST>
```

## オブジェクト macsec

Media Access Control Security (MACsec) を管理する。

## オブジェクト maddr

マルチキャストアドレスを管理する。

### マルチキャストアドレスの表示

マルチキャストアドレスを表示する。

```sh
ip -d maddr show dev eth0
```

```text
2:      eth0
        link  01:00:5e:00:00:01
        link  33:33:00:00:00:01
        link  33:33:ff:00:26:06
        inet  224.0.0.1
        inet6 ff02::1:ff00:2606
        inet6 ff02::1
        inet6 ff01::1
```

[print_maddr](https://github.com/iproute2/iproute2/blob/main/ip/ipmaddr.c#L210) で表示される。

| 項目    | 値                                                                        | 備考                         |
| :------ | :------------------------------------------------------------------------ | :--------------------------- |
| ifindex | 2                                                                         | インターフェイスインデックス |
| ifname  | eth0                                                                      | 名前                         |
| maddr   | link 01:00:5e:00:00:01 / link 33:33:00:00:00:01 / link 33:33:ff:00:26:06  | マルチキャスト MAC アドレス  |
|         | inet 224.0.0.1 / inet6 ff02::1:ff00:2606 / inet6 ff02::1 /  inet6 ff01::1 | マルチキャストアドレス       |

## オブジェクト monitor

netlink を監視する。

## オブジェクト mptcp

Multipath TCP (MPTCP) を管理する。

## オブジェクト mroute

マルチキャストルーティングキャッシュを管理する。

## オブジェクト mrule

マルチキャストルーティングポシリーを管理する。

## オブジェクト neigh

隣接するオブジェクト (ARP テーブル) を管理する。

### 隣接するオブジェクトの表示

隣接するオブジェクトを表示する。

```sh
ip -d neigh show dev eth0
```

```text
172.24.96.1 lladdr 00:15:5d:7d:a7:2d STALE
```

[print_neigh](https://github.com/iproute2/iproute2/blob/main/ip/ipneigh.c#L306) で表示される。

| 項目   | 値                | 備考                                                               |
| :----- | :---------------- | :----------------------------------------------------------------- |
| dst    | 172.24.96.1       | 隣接の宛先アドレス                                                 |
| lladdr | 00:15:5d:7d:a7:2d | 隣接の Layer2 アドレス                                             |
| STATE  | STALE             | permanent,noarp,stale,reachable,none,incomplete,delay,probe,failed |

アドレスを指定して隣接するオブジェクトを表示する。

```sh
ip neigh get 172.24.96.1 dev eth0
```

```text
172.24.96.1 dev eth0 lladdr 00:15:5d:7d:a7:2d STALE
```

### 隣接するオブジェクトの制御

隣接オブジェクトを追加する。

```sh
sudo ip neigh add 192.168.0.1 lladdr 11:22:33:44:55:66 dev eth0
ip neigh show to 192.168.0.1
```

```text
192.168.0.1 dev eth0 lladdr 11:22:33:44:55:66 PERMANENT
```

隣接オブジェクトを削除する。

```sh
sudo ip neigh del 192.168.0.1 dev eth0
ip neigh show to 192.168.0.1
```

```text
```

## オブジェクト netconf

NETCONF プロトロコルを監視する。

## オブジェクト netns

ネットワーク名前空間を管理する。

### 名前空間の表示

名前空間を表示する。

```sh
ip netns list
```

```text
net01
```

プロセスが所属する名前空間を表示する。

```sh
sudo ip netns identify 1895
```

```text
net01
```

名前空間に所属するプロセスを表示する。

```sh
sudo ip netns pids net01
```

```text
1895
```

名前空間 ID を表示する。

```sh
ip netns list-id
```

```text
nsid 0 (iproute2 netns name: net01)
```

### 名前空間の制御

名前空間を作成する。

```sh
sudo ip netns add net01
ip netns list
```

```text
net01
```

名前空間を削除する。

```sh
sudo ip netns del net01
ip netns
```

```text
```

名前空間に ID を割り当てる。

```sh
sudo ip netns set net01 auto
ip netns list
```

```text
net01 (id: 0)
```

### 名前空間の利用

名前空間に関連させてプロセスを起動する。

```sh
sudo ip netns exec net01 bash
echo $$
```

```text
1895
```

## オブジェクト nexthop

ネクストホップを管理する。

## オブジェクト ntable

Neighbour Table (ARP テーブル) を管理する。

## オブジェクト route

ルーティングテーブルを管理する。

### ルーティングの表示

ルーティングを表示する。

```sh
ip -d route show exact default
```

```text
unicast default via 172.24.96.1 dev eth0 proto dhcp scope global src 172.24.96.11 metric 100
```

[print_route](https://github.com/iproute2/iproute2/blob/main/ip/iproute.c#L768) で表示される。

| 項目     | 値           | 備考                                                                                               |
| :------- | :----------- | :------------------------------------------------------------------------------------------------- |
| type     | unicast      |                                                                                                    |
| dst      | default      | 宛先ネットワーク。デフォルトゲートウェア                                                           |
| gateway  | 172.24.96.1  | ゲートウェイアドレス                                                                               |
| dev      | eth0         | ゲートウェイデバイス                                                                               |
| protocol | dhcp         | 設定方法                                                                                           |
| scope    | global       | スコープ。[アドレスの表示](#アドレスの表示) を参照する。                                           |
| prefsrc  | 172.24.96.11 | 送信元アドレス                                                                                     |
| metric   | 100          | 優先順位                                                                                           |
| flags    | -            | dead,onlink,pervasive,offload,trap,notify,linkdown,unresolved,rt_offload,rt_trap,rt_offload_failed |

アドレスのルートを取得する。

```sh
ip route get 172.17.0.3
```

```text
172.17.0.3 dev docker0 src 172.17.0.1 uid 1000
    cache
```

### ルーティングの制御

デフォルトゲートウェイを追加する。

```sh
sudo ip route add default via 172.24.96.11
ip route show exact default
```

```text
default via 172.24.96.11 dev eth0
```

アドレスを指定してルーティングを追加する。

```sh
sudo ip route add 10.0.0.0/24 via 172.17.0.1
ip route show exact 10.0.0.0/24
```

```text
10.0.0.0/24 via 172.17.0.1 dev docker0 linkdown
```

デバイスを指定してルーティングを追加する。

```sh
sudo ip route add 10.0.0.0/24 dev eth0
ip route show exact 10.0.0.0/24
```

```text
10.0.0.0/24 dev eth0 scope link
```

ルーティングを削除する。

```sh
sudo ip route delete 10.0.0.0/24
ip route show exact 10.0.0.0/24
```

```text
```

### ルーティング設定の保存と確認

ルーティングの設定を保存する。

```sh
ip route save exact 172.17.0.0/16 > route.bin
ls -l route.bin
```

```text
-rw-r--r--. 1 centos10 centos10 64 12月 17 00:18 route.bin
```

保存した設定を確認する。

```sh
ip route showdump < route.bin
```

```text
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

ルーティングの設定をフラッシュする。

```sh
sudo ip route flush exact 172.17.0.0/16
ip route show exact 172.17.0.0/16
```

```text
```

設定をリストアする。
linkdown のためエラーが発生する。

```sh
sudo ip route restore < route.bin
```

```text
Error: Invalid rtm_flags - can not contain DEAD or LINKDOWN.
```

## オブジェクト rule

ルーティングポシリーを管理する。

## オブジェクト sr

IPv6 の Segment Routing を管理する。

## オブジェクト stats

インターフェイスの統計情報を表示する。

## オブジェクト tcpmetrics

TCP 情報のキャッシュを管理する。

## オブジェクト token

IPv6 のステートレスアドレス自動設定のトークンを管理する。

## オブジェクト tunnel

トンネルを管理する。

## オブジェクト tuntap

TUN (Layer3) / TAP デバイスを管理する。

### TAP / TUN デバイスの表示

TAP / TUN デバイスを表示する。

```sh
ip tuntap show
```

```text
tap0: tap persist
tun0: tun persist
```

### TAP / TUN デバイスの制御

TAP デバイスを作成する。

```sh
sudo ip tuntap add dev tap0 mode tap
ip -d link show tap0
```

```text
5: tap0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 8a:6d:11:0e:26:31 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65521
    tun type tap pi off vnet_hdr off persist on addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

TAP デバイスを削除する。

```sh
sudo ip tuntap del dev tap0 mode tap
ip link show tap0
```

```text
Device "tap0" does not exist.
```

TUN デバイスを作成する。

```sh
sudo ip tuntap add dev tun0 mode tun
ip -d link show tun0
```

```text
9: tun0: <POINTOPOINT,MULTICAST,NOARP> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 500
    link/none  promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    tun type tun pi off vnet_hdr off persist on addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

## オブジェクト vrf

Virtual Routing and Fowarding (VRF) を管理する。

## オブジェクト xfrm

IPsec ポリシーを管理する。

## TAP デバイス

仮想 Layer2 デバイスになる。

アドレスを設定してインターフェイスを有効化する。リンクアップはしない。

```sh
sudo ip addr add 10.0.0.1/24 dev tap0
sudo ip link set dev tap0 up
ip link show tap0
```

```text
6: tap0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 1000
    link/ether ae:37:9e:bb:aa:4a brd ff:ff:ff:ff:ff:ff
```

疎通は通る。

```sh
ping -c 1 10.0.0.1
```

```text
PING 10.0.0.1 (10.0.0.1) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.0.1: icmp_seq=1 ttl=64 時間=0.051ミリ秒

--- 10.0.0.1 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.051/0.051/0.051/0.000 ms
```

## TUN デバイス

仮想 Layer3 デバイスになる。

アドレスを設定してインターフェイスを有効化する。リンクアップはしない。

```sh
sudo ip addr add 10.0.0.1/24 dev tun0
sudo ip link set dev tun0 up
ip link show tun0
```

```text
9: tun0: <NO-CARRIER,POINTOPOINT,MULTICAST,NOARP,UP> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 500
    link/none
```

疎通は通る。

```sh
ping -c 1 10.0.0.1
```

```text
PING 10.0.0.1 (10.0.0.1) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.0.1: icmp_seq=1 ttl=64 時間=0.059ミリ秒

--- 10.0.0.1 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.059/0.059/0.059/0.000 ms
```

## VETH デバイス

仮想 Layer2 デバイスのペアを作成する。

```sh
sudo ip netns add net0a
sudo ip netns add net0b
sudo ip link add veth0a netns net0a type veth peer veth0b netns net0b
```

名前空間 net0a のデバイスを確認する。

```sh
sudo ip -d -n net0a link show veth0a
```

```text
2: veth0a@if2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 4a:e5:77:82:29:b4 brd ff:ff:ff:ff:ff:ff link-netns net0b promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net0b のデバイスを確認する。

```sh
sudo ip -d -n net0b link show veth0b
```

```text
2: veth0b@if2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether b6:af:09:1d:65:59 brd ff:ff:ff:ff:ff:ff link-netns net0a promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てる。

```sh
sudo ip -n net0a addr add 10.0.0.1/24 dev veth0a
sudo ip -n net0b addr add 10.0.0.2/24 dev veth0b
```

veth0a を有効化する。リンクアップはしない。

```sh
sudo ip -n net0a link set dev veth0a up
sudo ip -n net0a addr show dev veth0a
```

```text
2: veth0a@if2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 4a:e5:77:82:29:b4 brd ff:ff:ff:ff:ff:ff link-netns net0b
    inet 10.0.0.1/24 scope global veth0a
       valid_lft forever preferred_lft forever
```

veth0b を有効化する。リンクアップする。

```sh
sudo ip -n net0b link set dev veth0b up
sudo ip -n net0b addr show dev veth0b
```

```text
2: veth0b@if2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether b6:af:09:1d:65:59 brd ff:ff:ff:ff:ff:ff link-netns net0a
    inet 10.0.0.2/24 scope global veth0b
       valid_lft forever preferred_lft forever
    inet6 fe80::b4af:9ff:fe1d:6559/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
```

疎通を確認する。

```sh
sudo ip netns exec net0a ping -c 1 10.0.0.2
```

```text
PING 10.0.0.2 (10.0.0.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.0.2: icmp_seq=1 ttl=64 時間=0.030ミリ秒

--- 10.0.0.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.030/0.030/0.030/0.000 ms
```

## BRIDGE デバイス

仮想ネットワークスイッチを作成する。

```sh
sudo ip link add br0 type bridge
ip -d link show br0
```

```text
5: br0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 06:1d:b7:76:83:d3 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    bridge forward_delay 1500 hello_time 200 max_age 2000 ageing_time 30000 stp_state 0 priority 32768 vlan_filtering 0 vlan_protocol 802.1Q bridge_id 8000.0:0:0:0:0:0 designated_root 8000.0:0:0:0:0:0 root_port 0 root_path_cost 0 topology_change 0 topology_change_detected 0 hello_timer    0.00 tcn_timer    0.00 topology_change_timer    0.00 gc_timer    0.00 fdb_n_learned 0 fdb_max_learned 0 vlan_default_pvid 1 vlan_stats_enabled 0 vlan_stats_per_port 0 group_fwd_mask 0 group_address 01:80:c2:00:00:00 mcast_snooping 1 no_linklocal_learn 0 mcast_vlan_snooping 0 mst_enabled 0 mdb_offload_fail_notification 0 mcast_router 1 mcast_query_use_ifaddr 0 mcast_querier 0 mcast_hash_elasticity 16 mcast_hash_max 4096 mcast_last_member_count 2 mcast_startup_query_count 2 mcast_last_member_interval 100 mcast_membership_interval 26000 mcast_querier_interval 25500 mcast_query_interval 12500 mcast_query_response_interval 1000 mcast_startup_query_interval 3125 mcast_stats_enabled 0 mcast_igmp_version 2 mcast_mld_version 1 nf_call_iptables 0 nf_call_ip6tables 0 nf_call_arptables 0 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

veth を作成して名前空間 net0 とブリッジに接続する。

```sh
sudo ip netns add net0
sudo ip link add veth0 type veth peer veth0n netns net0
sudo ip link set veth0 master br0
ip -d link show veth0
```

```text
6: veth0@if2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop master br0 state DOWN mode DEFAULT group default qlen 1000
    link/ether 2a:53:c9:0f:51:83 brd ff:ff:ff:ff:ff:ff link-netns net0 promiscuity 1 allmulti 1 minmtu 68 maxmtu 65535
    veth
    bridge_slave state disabled priority 32 cost 2 hairpin off guard off root_block off fastleave off learning on flood on port_id 0x8001 port_no 0x1 designated_port 32769 designated_cost 0 designated_bridge 8000.2a:53:c9:f:51:83 designated_root 8000.2a:53:c9:f:51:83 hold_timer    0.00 message_age_timer    0.00 forward_delay_timer    0.00 topology_change_ack 0 config_pending 0 proxy_arp off proxy_arp_wifi off mcast_router 1 mcast_fast_leave off mcast_flood on bcast_flood on mcast_to_unicast off neigh_suppress off neigh_vlan_suppress off group_fwd_mask 0 group_fwd_mask_str 0x0 vlan_tunnel off isolated off locked off mab off addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

veth を作成して名前空間 net1 とブリッジに接続する。

```sh
sudo ip netns add net1
sudo ip link add veth1 type veth peer veth1n netns net1
sudo ip link set veth1 master br0
ip -d link show veth1
```

```text
7: veth1@if2: <BROADCAST,MULTICAST> mtu 1500 qdisc noop master br0 state DOWN mode DEFAULT group default qlen 1000
    link/ether ba:6d:5f:5c:e8:96 brd ff:ff:ff:ff:ff:ff link-netns net1 promiscuity 1 allmulti 1 minmtu 68 maxmtu 65535
    veth
    bridge_slave state disabled priority 32 cost 2 hairpin off guard off root_block off fastleave off learning on flood on port_id 0x8002 port_no 0x2 designated_port 32770 designated_cost 0 designated_bridge 8000.2a:53:c9:f:51:83 designated_root 8000.2a:53:c9:f:51:83 hold_timer    0.00 message_age_timer    0.00 forward_delay_timer    0.00 topology_change_ack 0 config_pending 0 proxy_arp off proxy_arp_wifi off mcast_router 1 mcast_fast_leave off mcast_flood on bcast_flood on mcast_to_unicast off neigh_suppress off neigh_vlan_suppress off group_fwd_mask 0 group_fwd_mask_str 0x0 vlan_tunnel off isolated off locked off mab off addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.0.1/24 dev veth0n
sudo ip -n net0 link set dev veth0n up

sudo ip -n net1 addr add 10.0.0.2/24 dev veth1n
sudo ip -n net1 link set dev veth1n up

sudo ip link set dev veth0 up
sudo ip link set dev veth1 up
```

ブリッジを有効化する。

```sh
sudo ip link set dev br0 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.0.2
```

```text
PING 10.0.0.2 (10.0.0.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.0.2: icmp_seq=1 ttl=64 時間=0.035ミリ秒

--- 10.0.0.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.035/0.035/0.035/0.000 ms
```

## VLAN デバイス

[BRIDGE デバイス](#bridge-デバイス) を作成済みとする。

名前空間 net0 に VLAN 100 のデバイスを作成する。

```sh
sudo ip -n net0 link add link veth0n name veth0n.100 type vlan id 100
sudo ip -d -n net0 link show veth0n.100
```

```text
3: veth0n.100@veth0n: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 22:d7:e9:0e:54:db brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 0 maxmtu 65535
    vlan protocol 802.1Q id 100 <REORDER_HDR> addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に VLAN 100 のデバイスを作成する。

```sh
sudo ip -n net1 link add link veth1n name veth1n.100 type vlan id 100
sudo ip -d -n net1 link show veth1n.100
```

```text
3: veth1n.100@veth1n: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether aa:98:39:28:d8:d6 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 0 maxmtu 65535
    vlan protocol 802.1Q id 100 <REORDER_HDR> addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev veth0n.100
sudo ip -n net0 link set dev veth0n.100 up

sudo ip -n net1 addr add 10.0.1.2/24 dev veth1n.100
sudo ip -n net1 link set dev veth1n.100 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.125ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.125/0.125/0.125/0.000 ms
```

名前空間 net2 を作成して VLAN 101 のデバイスを作成する。

```sh
sudo ip netns add net2
sudo ip link add veth2 type veth peer veth2n netns net2
sudo ip link set veth2 master br0

sudo ip -n net2 link add link veth2n name veth2n.101 type vlan id 101

sudo ip -n net2 addr add 10.0.1.3/24 dev veth2n.101

sudo ip link set dev veth2 up
sudo ip -n net2 link set dev veth2n up
sudo ip -n net2 link set dev veth2n.101 up

sudo ip -n net2 addr show
```

```text
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: veth2n@if8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether fa:70:fa:84:d0:a1 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet6 fe80::f870:faff:fe84:d0a1/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
3: veth2n.101@veth2n: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether fa:70:fa:84:d0:a1 brd ff:ff:ff:ff:ff:ff
    inet 10.0.1.3/24 scope global veth2n.101
       valid_lft forever preferred_lft forever
    inet6 fe80::f870:faff:fe84:d0a1/64 scope link proto kernel_ll
       valid_lft forever preferred_lft forever
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.3
```

```text
PING 10.0.1.3 (10.0.1.3) 56(84) バイトのデータ

(通らない)
```

`tcpdump` でパケットを確認する。VLAN が設定されている。

```sh
sudo tcpdump -c 1 -nel -i veth0
```

```text
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on veth0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
20:58:14.574920 22:d7:e9:0e:54:db > Broadcast, ethertype 802.1Q (0x8100), length 46: vlan 100, p 0, ethertype ARP (0x0806), Request who-has 10.0.1.3 tell 10.0.1.1, length 28
1 packet captured
1 packet received by filter
0 packets dropped by kernel
```

## VXLAN デバイス

[BRIDGE デバイス](#bridge-デバイス) を作成済みとする。

名前空間 net0 に VXLAN 100 のデバイスを作成する。

```sh
sudo ip -n net0 link add vxlan0 type vxlan id 100 dev veth0n remote 10.0.0.2 local 10.0.0.1 dstport 4789
sudo ip -d -n net0 link show vxlan0
```

```text
4: vxlan0: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 2e:7f:d3:a4:af:16 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    vxlan id 100 remote 10.0.0.2 local 10.0.0.1 dev veth0n srcport 0 0 dstport 4789 ttl auto ageing 300 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に VXLAN 100 のデバイスを作成する。

```sh
sudo ip -n net1 link add vxlan0 type vxlan id 100 dev veth1n remote 10.0.0.1 local 10.0.0.2 dstport 4789
sudo ip -d -n net1 link show vxlan0
```

```sh
3: vxlan0: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether be:85:da:c0:3f:3c brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    vxlan id 100 remote 10.0.0.1 local 10.0.0.2 dev veth1n srcport 0 0 dstport 4789 ttl auto ageing 300 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev vxlan0
sudo ip -n net0 link set dev vxlan0 up

sudo ip -n net1 addr add 10.0.1.2/24 dev vxlan0
sudo ip -n net1 link set dev vxlan0 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.049ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.049/0.049/0.049/0.000 ms
```

`tcpdump` でパケットを確認する。VXLAN が設定されている。

```sh
sudo ip netns exec net1 tcpdump -c 1 -nel -i veth1n
```

```text
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on veth1n, link-type EN10MB (Ethernet), snapshot length 262144 bytes
22:54:14.563340 6e:da:ea:46:94:fa > 22:1e:20:e3:69:12, ethertype IPv4 (0x0800), length 92: 10.0.0.1.40638 > 10.0.0.2.vxlan: VXLAN, flags [I] (0x08), vni 100
2e:7f:d3:a4:af:16 > Broadcast, ethertype ARP (0x0806), length 42: Request who-has 10.0.1.2 tell 10.0.1.1, length 28
1 packet captured
4 packets received by filter
0 packets dropped by kernel
```

名前空間 `net0` のフォワーディングテーブルを確認する。

```sh
sudo ip netns exec net0 bridge fdb show dev vxlan0
```

```text
00:00:00:00:00:00 dst 10.0.0.2 via veth0n self permanent
be:85:da:c0:3f:3c dst 10.0.0.2 self
```

名前空間 `net1` のフォワーディングテーブルを確認する。

```sh
sudo ip netns exec net1 bridge fdb show dev vxlan0
```

```text
00:00:00:00:00:00 dst 10.0.0.1 via veth1n self permanent
2e:7f:d3:a4:af:16 dst 10.0.0.1 self
```

## IPVLAN デバイス

親インターフェイスと MAC アドレスを共有したサブインターフェイスを作成する。

[BRIDGE デバイス](#bridge-デバイス) を作成済みとする。

### L2 モード

親インターフェイスがブリッジのように動作する。

名前空間 net0 に IPVLAN のデバイスを作成する。

```sh
sudo ip -n net0 link add link veth0n name ipvlan0 type ipvlan mode l2
sudo ip -d -n net0 link show
```

```text
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
2: veth0n@if9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 6e:da:ea:46:94:fa brd ff:ff:ff:ff:ff:ff link-netnsid 0 promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
5: ipvlan0@veth0n: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 6e:da:ea:46:94:fa brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    ipvlan  mode l2 bridge addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に IPVLAN のデバイスを作成する。

```sh
sudo ip -n net1 link add link veth1n name ipvlan1 type ipvlan mode l2
sudo ip -d -n net1 link show
```

```text
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
2: veth1n@if10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 22:1e:20:e3:69:12 brd ff:ff:ff:ff:ff:ff link-netnsid 0 promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
4: ipvlan1@veth1n: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 22:1e:20:e3:69:12 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    ipvlan  mode l2 bridge addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev ipvlan0
sudo ip -n net0 link set dev ipvlan0 up

sudo ip -n net1 addr add 10.0.1.2/24 dev ipvlan1
sudo ip -n net1 link set dev ipvlan1 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.099ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.099/0.099/0.099/0.000 ms
```

arp テーブルを確認する。

```sh
sudo ip -n net0 neigh show
```

```text
10.0.0.2 dev veth0n lladdr 22:1e:20:e3:69:12 STALE
10.0.1.2 dev ipvlan0 lladdr 22:1e:20:e3:69:12 STALE
```

### L3 モード

親インターフェイスがルータのように動作する。

### L3s モード

親インターフェイスがルータのように動作する。

```text
This is very similar to the L3 mode except that iptables (conn-tracking) works in this mode and hence it is L3-symmetric (L3s).
```

## MACVLAN デバイス

親インターフェイスと MAC アドレスが別のサブインターフェイスを作成する。

[BRIDGE デバイス](#bridge-デバイス) を作成済みとする。

### private モード

同じ親インターフェイスを共有するサブインターフェイスは通信できない。

### vepa モード

親インターフェイスが接続されたスイッチを介して、
同じ親インターフェイスを共有するサブインターフェイスは通信する。

### bridge モード

親インターフェイスがブリッジのように動作する。

名前空間 net0 に MACVLAN のデバイスを作成する。

```sh
sudo ip -n net0 link add link veth0n name macvlan0 type macvlan mode bridge
sudo ip -d -n net0 link show
```

```text
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
2: veth0n@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 92:49:9b:72:3d:52 brd ff:ff:ff:ff:ff:ff link-netnsid 0 promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
3: macvlan0@veth0n: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 32:59:f3:87:09:3d brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    macvlan mode bridge bcqueuelen 1000 usedbcqueuelen 1000 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に MACVLAN のデバイスを作成する。

```sh
sudo ip -n net1 link add link veth1n name macvlan1 type macvlan mode bridge
sudo ip -d -n net1 link show
```

```text
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
2: veth1n@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 66:ff:e3:db:5a:bd brd ff:ff:ff:ff:ff:ff link-netnsid 0 promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
3: macvlan1@veth1n: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 32:2b:36:aa:43:e7 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    macvlan mode bridge bcqueuelen 1000 usedbcqueuelen 1000 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev macvlan0
sudo ip -n net0 link set dev macvlan0 up

sudo ip -n net1 addr add 10.0.1.2/24 dev macvlan1
sudo ip -n net1 link set dev macvlan1 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.053ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.053/0.053/0.053/0.000 ms
```

### passthru モード

1つのサブインターフェイスが親インターフェイスと接続される。

### source モード

送信元 MAC アドレスのリストを使用して親インターフェイスで通信をフィルタリングする。MAC アドレスによる VLAN を作成する。

名前空間 net0 に MACVLAN のデバイスを作成する。

```sh
sudo ip -n net0 link add link veth0n name macvlan0 type macvlan mode source
sudo ip -d -n net0 link show
```

```text
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
2: veth0n@if6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 92:49:9b:72:3d:52 brd ff:ff:ff:ff:ff:ff link-netnsid 0 promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
4: macvlan0@veth0n: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 7e:d9:65:33:05:7d brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    macvlan mode source bcqueuelen 1000 usedbcqueuelen 1000 remotes (0) addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に MACVLAN のデバイスを作成する。

```sh
sudo ip -n net1 link add link veth1n name macvlan1 type macvlan mode source
sudo ip -d -n net1 link show
```

```text
1: lo: <LOOPBACK> mtu 65536 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
2: veth1n@if7: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 66:ff:e3:db:5a:bd brd ff:ff:ff:ff:ff:ff link-netnsid 0 promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
4: macvlan1@veth1n: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 9a:4b:11:18:00:5e brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    macvlan mode source bcqueuelen 1000 usedbcqueuelen 1000 remotes (0) addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev macvlan0
sudo ip -n net0 link set dev macvlan0 up

sudo ip -n net1 addr add 10.0.1.2/24 dev macvlan1
sudo ip -n net1 link set dev macvlan1 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ

(通らない)
```

名前空間 net0 に maclvan1 の MAC アドレスを登録する。

```sh
sudo ip -n net0 link set link dev macvlan0 type macvlan macaddr add 9a:4b:11:18:00:5e
sudo ip -d -n net0 link show macvlan0
```

```text
4: macvlan0@veth0n: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 7e:d9:65:33:05:7d brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    macvlan mode source bcqueuelen 1000 usedbcqueuelen 1000 remotes (1) 9a:4b:11:18:00:5e addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に maclvan0 の MAC アドレスを登録する。

```sh
sudo ip -n net1 link set link dev macvlan1 type macvlan macaddr add 7e:d9:65:33:05:7d
sudo ip -d -n net1 link show macvlan1
```

```text
4: macvlan1@veth1n: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 9a:4b:11:18:00:5e brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    macvlan mode source bcqueuelen 1000 usedbcqueuelen 1000 remotes (1) 7e:d9:65:33:05:7d addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.069ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.069/0.069/0.069/0.000 ms
```

## トンネル ipip / sit

IP over IPv4 トンネルを作成する。

`ipip` は IPv4 パケットをカプセル化する。
`sit` は `mode` で IPv4, IPv6, MPLS などカプセル化するパケットを選択する。

[BRIDGE デバイス](#bridge-デバイス) を作成済みとする。

名前空間 net0 に ipip デバイスを作成する。

```sh
sudo ip -n net0 link add name ipip0 type ipip local 10.0.0.1 remote 10.0.0.2
sudo ip -d -n net0 link show ipip0
```

```text
4: ipip0@NONE: <POINTOPOINT,NOARP> mtu 1480 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ipip 10.0.0.1 peer 10.0.0.2 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0
    ipip any remote 10.0.0.2 local 10.0.0.1 ttl inherit pmtudisc addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に ipip デバイスを作成する。

```sh
sudo ip -n net1 link add name ipip0 type ipip local 10.0.0.2 remote 10.0.0.1
sudo ip -d -n net1 link show ipip0
```

```text
4: ipip0@NONE: <POINTOPOINT,NOARP> mtu 1480 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ipip 10.0.0.2 peer 10.0.0.1 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0
    ipip any remote 10.0.0.1 local 10.0.0.2 ttl inherit pmtudisc addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev ipip0
sudo ip -n net0 link set dev ipip0 up

sudo ip -n net1 addr add 10.0.1.2/24 dev ipip0
sudo ip -n net1 link set dev ipip0 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.050ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.050/0.050/0.050/0.000 ms
```

`tcpdump` でパケットを確認する。`10.0.1.1 > 10.0.1.2` が設定されている。

```sh
sudo ip netns exec net1 tcpdump -c 1 -nel -i veth1n
```

```text
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on veth1n, link-type EN10MB (Ethernet), snapshot length 262144 bytes
10:40:08.269237 52:b1:44:b3:df:8d > 9a:4c:40:d0:1a:c7, ethertype IPv4 (0x0800), length 118: 10.0.0.1 > 10.0.0.2: 10.0.1.1 > 10.0.1.2: ICMP echo request, id 2039, seq 1, length 64
1 packet captured
2 packets received by filter
0 packets dropped by kernel
```

## トンネル ip6tnl

IP over IPv6 トンネルを作成する。

## トンネル VTI / VTI6

xfrm を利用して IPSec トンネルを作成する。

[BRIDGE デバイス](#bridge-デバイス) を作成済みとする。

名前空間 net0 に vti デバイスを作成する。

```sh
sudo ip -n net0 link add name vti0 type vti key 1 local 10.0.0.1 remote 10.0.0.2
sudo ip -d -n net0 link show vti0
```

```text
6: vti0@NONE: <POINTOPOINT,NOARP> mtu 1480 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ipip 10.0.0.1 peer 10.0.0.2 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0
    vti remote 10.0.0.2 local 10.0.0.1 ikey 0.0.0.1 okey 0.0.0.1 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に vti デバイスを作成する。

```sh
sudo ip -n net1 link add name vti0 type vti key 1 local 10.0.0.2 remote 10.0.0.1
sudo ip -d -n net1 link show vti0
```

```text
6: vti0@NONE: <POINTOPOINT,NOARP> mtu 1480 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ipip 10.0.0.2 peer 10.0.0.1 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0
    vti remote 10.0.0.1 local 10.0.0.2 ikey 0.0.0.1 okey 0.0.0.1 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev vti0
sudo ip -n net0 link set dev vti0 up

sudo ip -n net1 addr add 10.0.1.2/24 dev vti0
sudo ip -n net1 link set dev vti0 up
```

名前空間 net0 に IPSec のセキュリティアソシエーションを作成する。

```sh
SHA256_KEY=$(cat /dev/random | tr -dc 'a-f0-9' | head -c 64)
AES_KEY=$(cat /dev/random | tr -dc 'a-f0-9' | head -c 64)

sudo ip -n net0 xfrm state add src 10.0.0.1 dst 10.0.0.2 proto esp spi 1 enc aes "0x${AES_KEY}" auth sha256 "0x${SHA256_KEY}" mode tunnel
sudo ip -n net0 xfrm state add src 10.0.0.2 dst 10.0.0.1 proto esp spi 1 enc aes "0x${AES_KEY}" auth sha256 "0x${SHA256_KEY}" mode tunnel
sudo ip -n net0 xfrm state show
```

```text
src 10.0.0.2 dst 10.0.0.1
        proto esp spi 0x00000001 reqid 0 mode tunnel
        replay-window 0
        auth-trunc hmac(sha256) 0xa86503051e42234390fe1d8653e8e628c4ce7746a9f27d910e3d8c65115f5d24 96
        enc cbc(aes) 0xa5c8a9f7cd9890bf9b72a03216c6f906794c0daa6ad849b1a1a269f04bea6d65
        anti-replay context: seq 0x0, oseq 0x0, bitmap 0x00000000
        sel src 0.0.0.0/0 dst 0.0.0.0/0
src 10.0.0.1 dst 10.0.0.2
        proto esp spi 0x00000001 reqid 0 mode tunnel
        replay-window 0
        auth-trunc hmac(sha256) 0xa86503051e42234390fe1d8653e8e628c4ce7746a9f27d910e3d8c65115f5d24 96
        enc cbc(aes) 0xa5c8a9f7cd9890bf9b72a03216c6f906794c0daa6ad849b1a1a269f04bea6d65
        lastused 2025-12-20 11:41:09
        anti-replay context: seq 0x0, oseq 0x1, bitmap 0x00000000
        sel src 0.0.0.0/0 dst 0.0.0.0/0
```

名前空間 net1 に IPSec のセキュリティアソシエーションを作成する。

```sh
sudo ip -n net1 xfrm state add src 10.0.0.2 dst 10.0.0.1 proto esp spi 1 enc aes "0x${AES_KEY}" auth sha256 "0x${SHA256_KEY}" mode tunnel
sudo ip -n net1 xfrm state add src 10.0.0.1 dst 10.0.0.2 proto esp spi 1 enc aes "0x${AES_KEY}" auth sha256 "0x${SHA256_KEY}" mode tunnel
sudo ip -n net1 xfrm state show
```

```text
src 10.0.0.1 dst 10.0.0.2
        proto esp spi 0x00000001 reqid 0 mode tunnel
        replay-window 0
        auth-trunc hmac(sha256) 0xa86503051e42234390fe1d8653e8e628c4ce7746a9f27d910e3d8c65115f5d24 96
        enc cbc(aes) 0xa5c8a9f7cd9890bf9b72a03216c6f906794c0daa6ad849b1a1a269f04bea6d65
        anti-replay context: seq 0x0, oseq 0x0, bitmap 0x00000000
        sel src 0.0.0.0/0 dst 0.0.0.0/0
src 10.0.0.2 dst 10.0.0.1
        proto esp spi 0x00000001 reqid 0 mode tunnel
        replay-window 0
        auth-trunc hmac(sha256) 0xa86503051e42234390fe1d8653e8e628c4ce7746a9f27d910e3d8c65115f5d24 96
        enc cbc(aes) 0xa5c8a9f7cd9890bf9b72a03216c6f906794c0daa6ad849b1a1a269f04bea6d65
        anti-replay context: seq 0x0, oseq 0x0, bitmap 0x00000000
        sel src 0.0.0.0/0 dst 0.0.0.0/0
```

名前空間 net0 に IPSec のトンネリングポリシーを作成する。

```sh
sudo ip -n net0 xfrm policy add dir in tmpl src 10.0.0.2 dst 10.0.0.1 proto esp spi 1 mode tunnel mark 1
sudo ip -n net0 xfrm policy add dir out tmpl src 10.0.0.1 dst 10.0.0.2 proto esp spi 1 mode tunnel mark 1
sudo ip -n net0 xfrm policy show
```

```text
src 0.0.0.0/0 dst 0.0.0.0/0
        dir out priority 0 ptype main
        mark 0x1/0xffffffff
        tmpl src 10.0.0.1 dst 10.0.0.2
                proto esp spi 0x00000001 reqid 0 mode tunnel
src 0.0.0.0/0 dst 0.0.0.0/0
        dir in priority 0 ptype main
        mark 0x1/0xffffffff
        tmpl src 10.0.0.2 dst 10.0.0.1
                proto esp spi 0x00000001 reqid 0 mode tunnel
```

名前空間 net1 に IPSec のトンネリングポリシーを作成する。

```sh
sudo ip -n net1 xfrm policy add dir in tmpl src 10.0.0.1 dst 10.0.0.2 proto esp spi 1 mode tunnel mark 1
sudo ip -n net1 xfrm policy add dir out tmpl src 10.0.0.2 dst 10.0.0.1 proto esp spi 1 mode tunnel mark 1
sudo ip -n net1 xfrm policy show
```

```text
src 0.0.0.0/0 dst 0.0.0.0/0
        dir out priority 0 ptype main
        mark 0x1/0xffffffff
        tmpl src 10.0.0.2 dst 10.0.0.1
                proto esp spi 0x00000001 reqid 0 mode tunnel
src 0.0.0.0/0 dst 0.0.0.0/0
        dir in priority 0 ptype main
        mark 0x1/0xffffffff
        tmpl src 10.0.0.1 dst 10.0.0.2
                proto esp spi 0x00000001 reqid 0 mode tunnel
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.078ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.078/0.078/0.078/0.000 ms
```

`tcpdump` でパケットを確認する。ESP が設定されている。

```sh
sudo ip netns exec net1 tcpdump -c 1 -nel -i veth1n
```

```text
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on veth1n, link-type EN10MB (Ethernet), snapshot length 262144 bytes
11:51:51.286928 52:b1:44:b3:df:8d > 9a:4c:40:d0:1a:c7, ethertype IPv4 (0x0800), length 166: 10.0.0.1 > 10.0.0.2: ESP(spi=0x00000001,seq=0x3), length 132
1 packet captured
2 packets received by filter
0 packets dropped by kernel
```

## トンネル GRE

Layer3 over IPv4 トンネルを作成する。

[BRIDGE デバイス](#bridge-デバイス) を作成済みとする。

名前空間 net0 に gre デバイスを作成する。`gre0` は Linux カーネルが使用する。

```sh
sudo ip -n net0 link add gre1 type gre key 1 local 10.0.0.1 remote 10.0.0.2
sudo ip -d -n net0 link show gre1
```

```text
6: gre1@NONE: <POINTOPOINT,NOARP> mtu 1472 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/gre 10.0.0.1 peer 10.0.0.2 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0
    gre remote 10.0.0.2 local 10.0.0.1 ttl inherit ikey 0.0.0.1 okey 0.0.0.1 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に gre デバイスを作成する。`gre0` は Linux カーネルが使用する。

```sh
sudo ip -n net1 link add gre1 type gre key 1 local 10.0.0.2 remote 10.0.0.1
sudo ip -d -n net1 link show gre1
```

```text
6: gre1@NONE: <POINTOPOINT,NOARP> mtu 1472 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/gre 10.0.0.2 peer 10.0.0.1 promiscuity 0 allmulti 0 minmtu 0 maxmtu 0
    gre remote 10.0.0.1 local 10.0.0.2 ttl inherit ikey 0.0.0.1 okey 0.0.0.1 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev gre1
sudo ip -n net0 link set dev gre1 up

sudo ip -n net1 addr add 10.0.1.2/24 dev gre1
sudo ip -n net1 link set dev gre1 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.046ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.046/0.046/0.046/0.000 ms
```

`tcpdump` でパケットを確認する。GREv0 が設定されている。

```sh
sudo ip netns exec net1 tcpdump -c 1 -nel -i veth1n
```

```text
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on veth1n, link-type EN10MB (Ethernet), snapshot length 262144 bytes
13:00:13.014951 c2:27:a3:98:7b:40 > aa:56:91:a5:31:2a, ethertype IPv4 (0x0800), length 126: 10.0.0.1 > 10.0.0.2: GREv0, key=0x1, proto IPv4 (0x0800), length 92: 10.0.1.1 > 10.0.1.2: ICMP echo request, id 2035, seq 1, length 64
1 packet captured
2 packets received by filter
0 packets dropped by kernel
```

## トンネル GRETAP

Layer2 over IPv4 トンネルを作成する。

[BRIDGE デバイス](#bridge-デバイス) を作成済みとする。

名前空間 net0 に gretap デバイスを作成する。`gre0` は Linux カーネルが使用する。

```sh
sudo ip -n net0 link add gre1 type gretap key 1 local 10.0.0.1 remote 10.0.0.2
sudo ip -d -n net0 link show gre1
```

```text
7: gre1@NONE: <BROADCAST,MULTICAST> mtu 1458 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether c2:a9:8e:12:a9:d2 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 0
    gretap remote 10.0.0.2 local 10.0.0.1 ttl inherit ikey 0.0.0.1 okey 0.0.0.1 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に gretap デバイスを作成する。`gre0` は Linux カーネルが使用する。

```sh
sudo ip -n net1 link add gre1 type gretap key 1 local 10.0.0.2 remote 10.0.0.1
sudo ip -d -n net1 link show gre1
```

```text
7: gre1@NONE: <BROADCAST,MULTICAST> mtu 1458 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 9a:ff:1e:ed:56:10 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 0
    gretap remote 10.0.0.1 local 10.0.0.2 ttl inherit ikey 0.0.0.1 okey 0.0.0.1 addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev gre1
sudo ip -n net0 link set dev gre1 up

sudo ip -n net1 addr add 10.0.1.2/24 dev gre1
sudo ip -n net1 link set dev gre1 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.044ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.044/0.044/0.044/0.000 ms
```

`tcpdump` でパケットを確認する。GREv0 が設定されている。

```sh
sudo ip netns exec net1 tcpdump -c 1 -nel -i veth1n
```

```text
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on veth1n, link-type EN10MB (Ethernet), snapshot length 262144 bytes
13:08:01.688847 c2:27:a3:98:7b:40 > aa:56:91:a5:31:2a, ethertype IPv4 (0x0800), length 140: 10.0.0.1 > 10.0.0.2: GREv0, key=0x1, proto TEB (0x6558), length 106: c2:a9:8e:12:a9:d2 > 9a:ff:1e:ed:56:10, ethertype IPv4 (0x0800), length 98: 10.0.1.1 > 10.0.1.2: ICMP echo request, id 2257, seq 1, length 64
1 packet captured
2 packets received by filter
0 packets dropped by kernel
```

## トンネル IP6GRE

Layer3 over IPv6 トンネルを作成する。

## トンネル IP6GRETAP

Layer2 over IPv6 トンネルを作成する。

## トンネル GENEVE

Layer2 over UDP トンネルを作成する。

[BRIDGE デバイス](#bridge-デバイス) を作成済みとする。

名前空間 net0 に geneve デバイスを作成する。

```sh
sudo ip -n net0 link add geneve0 type geneve id 1 remote 10.0.0.2
sudo ip -d -n net0 link show geneve0
```

```text
8: geneve0: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 92:5c:e1:b9:27:75 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65485
    geneve id 1 remote 10.0.0.2 ttl auto dstport 6081 noudpcsum udp6zerocsumrx addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

名前空間 net1 に geneve デバイスを作成する。

```sh
sudo ip -n net1 link add geneve0 type geneve id 1 remote 10.0.0.1
sudo ip -d -n net1 link show geneve0
```

```text
8: geneve0: <BROADCAST,MULTICAST> mtu 1450 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 0a:11:aa:11:f0:92 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65485
    geneve id 1 remote 10.0.0.1 ttl auto dstport 6081 noudpcsum udp6zerocsumrx addrgenmode eui64 numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

アドレスを割り当てて有効化する。

```sh
sudo ip -n net0 addr add 10.0.1.1/24 dev geneve0
sudo ip -n net0 link set dev geneve0 up

sudo ip -n net1 addr add 10.0.1.2/24 dev geneve0
sudo ip -n net1 link set dev geneve0 up
```

疎通を確認する。

```sh
sudo ip netns exec net0 ping -c 1 10.0.1.2
```

```text
PING 10.0.1.2 (10.0.1.2) 56(84) バイトのデータ
64 バイト応答 送信元 10.0.1.2: icmp_seq=1 ttl=64 時間=0.062ミリ秒

--- 10.0.1.2 ping 統計 ---
送信パケット数 1, 受信パケット数 1, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.062/0.062/0.062/0.000 ms
```

`tcpdump` でパケットを確認する。Geneve が設定されている。

```sh
sudo ip netns exec net1 tcpdump -c 1 -nel -i veth1n
```

```text
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on veth1n, link-type EN10MB (Ethernet), snapshot length 262144 bytes
13:22:28.727692 c2:27:a3:98:7b:40 > aa:56:91:a5:31:2a, ethertype IPv4 (0x0800), length 92: 10.0.0.1.47204 > 10.0.0.2.geneve: Geneve, Flags [none], vni 0x1, proto TEB (0x6558): 92:5c:e1:b9:27:75 > Broadcast, ethertype ARP (0x0806), length 42: Request who-has 10.0.1.2 tell 10.0.1.1, length 28
1 packet captured
4 packets received by filter
0 packets dropped by kernel
```

## トンネル ERSPAN

ミラーリングしたパケットを GRE でカプセル化するトンネルを作成する。

## 参考

- [iproute2](https://github.com/iproute2)
- [Predictable Network Interface Names](https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/)
- [Introduction to Linux interfaces for virtual networking](https://developers.redhat.com/blog/2018/10/22/introduction-to-linux-interfaces-for-virtual-networking)
- [IPVLAN Driver HOWTO](https://docs.kernel.org/networking/ipvlan.html)
- [An introduction to Linux virtual interfaces: Tunnels](https://developers.redhat.com/blog/2019/05/17/an-introduction-to-linux-virtual-interfaces-tunnels)
