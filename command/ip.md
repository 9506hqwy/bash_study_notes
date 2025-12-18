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

## 参考

- [iproute2](https://github.com/iproute2)
- [Predictable Network Interface Names](https://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/)
