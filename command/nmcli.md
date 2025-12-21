# `nmcli` コマンド

## 表示オプション

### `overview`

概要を表示する。

```sh
nmcli -o c s
```

```text
NAME     UUID                                  TYPE      DEVICE
eth0     a9066116-7e30-3c1b-9e14-09e34ced33e3  ethernet  eth0
eth1     f8f128a1-1024-3933-9ad5-d303d4872926  ethernet  eth1
lo       642c6e7c-fce6-4bc9-bf1c-824c975c6344  loopback  lo
docker0  a089d989-aaa1-4691-aa3c-ccb631d6b574  bridge    docker0
```

フィールドを指定する。

```sh
nmcli -f connection.id,ipv4.method,ipv4.addresses c s eth0
```

```text
connection.id:                          eth0
ipv4.method:                            auto
ipv4.addresses:                         --
```

### `pretty`

装飾された形式で表示する。

```sh
nmcli -p c s
```

```text
===================================
  NetworkManager 接続プロファイル
===================================
NAME     UUID                                  TYPE      DEVICE
-----------------------------------------------------------------------------------------------------
eth0     a9066116-7e30-3c1b-9e14-09e34ced33e3  ethernet  eth0
eth1     f8f128a1-1024-3933-9ad5-d303d4872926  ethernet  eth1
lo       642c6e7c-fce6-4bc9-bf1c-824c975c6344  loopback  lo
docker0  a089d989-aaa1-4691-aa3c-ccb631d6b574  bridge    docker0
```

フィールドを指定する。

```sh
nmcli -p -f connection.id,ipv4.method,ipv4.addresses c s eth0
```

```text
===============================================================================
                         接続プロファイルの詳細 (eth0)
===============================================================================
connection.id:                          eth0
-------------------------------------------------------------------------------
ipv4.method:                            auto
-------------------------------------------------------------------------------
ipv4.addresses:                         --
-------------------------------------------------------------------------------
```

### `terse`

簡素な形式で表示する。

```sh
nmcli -t c s
```

```text
eth0:a9066116-7e30-3c1b-9e14-09e34ced33e3:802-3-ethernet:eth0
eth1:f8f128a1-1024-3933-9ad5-d303d4872926:802-3-ethernet:eth1
lo:642c6e7c-fce6-4bc9-bf1c-824c975c6344:loopback:lo
docker0:a089d989-aaa1-4691-aa3c-ccb631d6b574:bridge:docker0
```

フィールドを指定する。

```sh
nmcli -t -f connection.id,ipv4.method,ipv4.addresses c s eth0
```

```text
connection.id:eth0
ipv4.method:auto
ipv4.addresses:
```

### `multiline` モード

複数行で表示する。

```sh
nmcli -m multiline c s
```

```text
NAME:                                   eth0
UUID:                                   a9066116-7e30-3c1b-9e14-09e34ced33e3
TYPE:                                   ethernet
DEVICE:                                 eth0
NAME:                                   eth1
UUID:                                   f8f128a1-1024-3933-9ad5-d303d4872926
TYPE:                                   ethernet
DEVICE:                                 eth1
NAME:                                   lo
UUID:                                   642c6e7c-fce6-4bc9-bf1c-824c975c6344
TYPE:                                   loopback
DEVICE:                                 lo
NAME:                                   docker0
UUID:                                   a089d989-aaa1-4691-aa3c-ccb631d6b574
TYPE:                                   bridge
DEVICE:                                 docker0
```

フィールドを指定する。

```sh
nmcli -m multiline -f connection.id,ipv4.method,ipv4.addresses c s eth0
```

```text
connection.id:                          eth0
ipv4.method:                            auto
ipv4.addresses:                         --
```

### `tabular` モード

表形式で表示する。

```sh
nmcli -m tabular c s
```

```text
NAME     UUID                                  TYPE      DEVICE
eth0     a9066116-7e30-3c1b-9e14-09e34ced33e3  ethernet  eth0
eth1     f8f128a1-1024-3933-9ad5-d303d4872926  ethernet  eth1
lo       642c6e7c-fce6-4bc9-bf1c-824c975c6344  loopback  lo
docker0  a089d989-aaa1-4691-aa3c-ccb631d6b574  bridge    docker0
```

フィールドを指定する。

```sh
nmcli -m tabular -f connection.id,ipv4.method,ipv4.addresses c s eth0
```

```text
id
eth0

method
auto

addresses
--
```

`-g` は `-t -m tabular -f` のショートカットになる。

```sh
nmcli -g  connection.id,ipv4.method,ipv4.addresses c s eth0
```

```text
eth0
auto

```

## オブジェクト general

Network Manager の全般設定を管理する。

## オブジェクト networking

ネットワークの有効・無効を管理する。

## オブジェクト radio

無線を管理する。

## オブジェクト connection

接続を管理する。

接続状態の概要を表示する。

```sh
nmcli c show
```

```text
AME     UUID                                  TYPE      DEVICE
eth0     a9066116-7e30-3c1b-9e14-09e34ced33e3  ethernet  eth0
eth1     f8f128a1-1024-3933-9ad5-d303d4872926  ethernet  eth1
lo       642c6e7c-fce6-4bc9-bf1c-824c975c6344  loopback  lo
docker0  a089d989-aaa1-4691-aa3c-ccb631d6b574  bridge    docker0
```

接続の設定を確認する。

```sh
nmcli c show eth0
```

```text
connection.id:                          eth0
connection.uuid:                        a9066116-7e30-3c1b-9e14-09e34ced33e3
connection.stable-id:                   --
  :
  :
```

設定を変更する。

```sh
sudo nmcli c m eth0 connection.autoconnect no
nmcli -g connection.autoconnect c s eth0
```

```text
no
```

切断する。

```sh
sudo nmcli c d eth0
```

```text
接続 'eth0' が正常に非アクティブ化されました (D-Bus アクティブパス: /org/freedesktop/NetworkManager/ActiveConnection/6)
```

接続する。

```sh
sudo nmcli c u eth0
```

```text
接続が正常にアクティベートされました (D-Bus アクティブパス: /org/freedesktop/NetworkManager/ActiveConnection/7)
```

### TAP デバイス

TAP デバイスを作成する。

```sh
sudo nmcli c add type tun ifname tap0 con-name tap0 mode tap
```

```text
接続 'tap0' (2f3db582-6dc5-4fdf-80b3-4067b757cf7e) が正常に追加されました。
```

デバイスを確認する。

```sh
ip -d link show tap0
```

```text
6: tap0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 1000
    link/ether 2e:b2:42:6e:67:92 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65521
    tun type tap pi off vnet_hdr off persist on addrgenmode none numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

### TUN デバイス

TUN デバイスを作成する。

```sh
sudo nmcli c add type tun ifname tun0 con-name tun0 mode tun
```

```text
接続 'tun0' (59c66604-336c-42c6-ab87-af3c04c774b8) が正常に追加されました。
```

デバイスを確認する。

```sh
ip -d link show tun0
```

```text
9: tun0: <NO-CARRIER,POINTOPOINT,MULTICAST,NOARP,UP> mtu 1500 qdisc fq_codel state DOWN mode DEFAULT group default qlen 500
    link/none  promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    tun type tun pi off vnet_hdr off persist on addrgenmode none numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

### VETH デバイス

VETH デバイスを作成する。

```sh
sudo nmcli c add type veth ifname veth0 con-name veth0 peer veth0n
```

```text
接続 'veth0' (3af9c505-e526-4196-a18d-eca10542b12e) が正常に追加されました。
```

デバイスを確認する。

```sh
ip -d link show veth0
```

```text
30: veth0@veth0n: <NO-CARRIER,BROADCAST,MULTICAST,UP,M-DOWN> mtu 1500 qdisc noqueue state LOWERLAYERDOWN mode DEFAULT group default qlen 1000
    link/ether de:50:da:c8:02:f7 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode none numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

peer デバイスを確認する。

```sh
ip -d link show veth0n
```

```text
29: veth0n@veth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 52:3a:da:00:a6:f8 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    veth addrgenmode eui64 numtxqueues 2 numrxqueues 2 gso_max_size 65536 gso_max_segs 65535 tso_max_size 524280 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

peer デバイスの設定を確認する。

```sh
nmcli -g veth.peer c s veth0
```

```text
veth0n
```

### BRIDGE デバイス

BRIDGE デバイスを作成する。

```sh
sudo nmcli c add type bridge ifname br0 con-name br0
```

```text
接続 'br0' (8fd98053-3b07-42e6-982d-0a0a124c4f45) が正常に追加されました。
```

デバイスを確認する。

```sh
ip -d link show br0
```

```text
16: br0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default qlen 1000
    link/ether 2a:67:fa:2c:ef:1c brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    bridge forward_delay 1500 hello_time 200 max_age 2000 ageing_time 30000 stp_state 1 priority 32768 vlan_filtering 0 vlan_protocol 802.1Q bridge_id 8000.0:0:0:0:0:0 designated_root 8000.0:0:0:0:0:0 root_port 0 root_path_cost 0 topology_change 0 topology_change_detected 0 hello_timer    0.12 tcn_timer    0.00 topology_change_timer    0.00 gc_timer  289.73 fdb_n_learned 0 fdb_max_learned 0 vlan_default_pvid 1 vlan_stats_enabled 0 vlan_stats_per_port 0 group_fwd_mask 0 group_address 01:80:c2:00:00:00 mcast_snooping 1 no_linklocal_learn 0 mcast_vlan_snooping 0 mst_enabled 0 mdb_offload_fail_notification 0 mcast_router 1 mcast_query_use_ifaddr 0 mcast_querier 0 mcast_hash_elasticity 16 mcast_hash_max 4096 mcast_last_member_count 2 mcast_startup_query_count 2 mcast_last_member_interval 100 mcast_membership_interval 26000 mcast_querier_interval 25500 mcast_query_interval 12500 mcast_query_response_interval 1000 mcast_startup_query_interval 3125 mcast_stats_enabled 0 mcast_igmp_version 2 mcast_mld_version 1 nf_call_iptables 0 nf_call_ip6tables 0 nf_call_arptables 0 addrgenmode none numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

### VLAN デバイス

VLAN デバイスを作成する。

```sh
sudo nmcli c add type vlan con-name eth0.100 dev eth0 id 100
```

```text
接続 'eth0.100' (5260093d-49bc-4b47-b750-5925803c338c) が正常に追加されました。
```

デバイスを確認する。

```sh
ip -d link show eth0.100
```

```text
23: eth0.100@eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether 00:15:5d:00:26:06 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 0 maxmtu 65535
    vlan protocol 802.1Q id 100 <REORDER_HDR> addrgenmode none numtxqueues 1 numrxqueues 1 gso_max_size 62780 gso_max_segs 65535 tso_max_size 62780 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 62780 gro_ipv4_max_size 65536
```

### VXLAN デバイス

VXLAN デバイスを作成する。

```sh
sudo nmcli c add type vxlan ifname vxlan0 con-name vxlan0 id 100
```

```text
接続 'vxlan0' (a9824524-2259-4401-abd5-f497e9a3033f) が正常に追加されました。
```

デバイスを確認する。

```sh
ip -d link show vxlan0
```

```text
25: vxlan0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether b2:a4:2c:8d:fb:ce brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    vxlan id 100 srcport 0 0 dstport 8472 ttl auto ageing 300 addrgenmode none numtxqueues 1 numrxqueues 1 gso_max_size 65536 gso_max_segs 65535 tso_max_size 65536 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 65536 gro_ipv4_max_size 65536
```

### IPVLAN デバイス

IPVLAN デバイスを作成する。

```sh
sudo nmcli c add type ipvlan ifname ipvlan0 con-name ipvlan0 dev eth0 mode l2
```

```text
接続 'ipvlan0' (f5bff91a-52ed-40b4-a509-bf755d865270) が正常に追加されました。
```

デバイスを確認する。

```sh
ip -d link show ipvlan0
```

```text
27: ipvlan0@eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/ether 00:15:5d:00:26:06 brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65535
    ipvlan  mode l2 bridge addrgenmode none numtxqueues 1 numrxqueues 1 gso_max_size 62780 gso_max_segs 65535 tso_max_size 62780 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 62780 gro_ipv4_max_size 65536
```

### MACVLAN デバイス

MACVLAN デバイスを作成する。

```sh
sudo nmcli c add type macvlan ifname macvlan0 con-name macvlan0 dev eth0 mode bridge
```

```text
接続 'macvlan0' (210bb55f-6d05-4e39-af6e-afa715f9c3a3) が正常に追加されました。
```

デバイスを確認する。

```sh
ip -d link show macvlan0
```

```text
26: macvlan0@eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default qlen 1000
    link/ether e2:4f:a9:85:d1:df brd ff:ff:ff:ff:ff:ff promiscuity 0 allmulti 0 minmtu 68 maxmtu 65521
    macvlan mode bridge bcqueuelen 1000 usedbcqueuelen 1000 addrgenmode none numtxqueues 1 numrxqueues 1 gso_max_size 62780 gso_max_segs 65535 tso_max_size 62780 tso_max_segs 65535 gro_max_size 65536 gso_ipv4_max_size 62780 gro_ipv4_max_size 65536
```

## オブジェクト device

デバイスを管理する。

デバイス状態の概要を表示する。

```sh
nmcli d status
```

```text
DEVICE   TYPE      STATE            CONNECTION
eth0     ethernet  接続済み         eth0
eth1     ethernet  接続済み         eth1
lo       loopback  接続済み (外部)  lo
docker0  bridge    接続済み (外部)  docker0
```

デバイス状態を表示する。

```sh
nmcli d show eth0
```

```text
GENERAL.DEVICE:                         eth0
GENERAL.TYPE:                           ethernet
GENERAL.HWADDR:                         00:15:5D:00:26:06
GENERAL.MTU:                            1500
GENERAL.STATE:                          100 (接続済み)
GENERAL.CONNECTION:                     eth0
GENERAL.CON-PATH:                       /org/freedesktop/NetworkManager/ActiveConnection/2
WIRED-PROPERTIES.CARRIER:               オン
IP4.ADDRESS[1]:                         172.24.96.11/20
IP4.GATEWAY:                            172.24.96.1
IP4.ROUTE[1]:                           dst = 172.24.96.0/20, nh = 0.0.0.0, mt = 100
IP4.ROUTE[2]:                           dst = 0.0.0.0/0, nh = 172.24.96.1, mt = 100
IP4.DNS[1]:                             172.24.96.1
IP4.DOMAIN[1]:                          mshome.net
IP6.ADDRESS[1]:                         fe80::215:5dff:fe00:2606/64
IP6.GATEWAY:                            --
IP6.ROUTE[1]:                           dst = fe80::/64, nh = ::, mt = 1024
```

切断する。

```sh
sudo nmcli d disconnect eth0
```

```text
デバイス 'eth0' が正常に切断されました。
```

接続する。

```sh
sudo nmcli d connect eth0
```

```text
デバイス 'eth0' が 'a9066116-7e30-3c1b-9e14-09e34ced33e3' で正常にアクティベートされました。
```

デバイスを管理外にする。

```sh
sudo nmcli d set eth0 manage no
nmcli d show eth0
```

```text
GENERAL.DEVICE:                         eth0
GENERAL.TYPE:                           ethernet
GENERAL.HWADDR:                         00:15:5D:00:26:06
GENERAL.MTU:                            1500
GENERAL.STATE:                          10 (管理無し)
GENERAL.CONNECTION:                     --
GENERAL.CON-PATH:                       --
WIRED-PROPERTIES.CARRIER:               オン
IP4.GATEWAY:                            --
IP6.GATEWAY:                            --
```

## オブジェクト agent

シークレットと pokit エージェントを管理する。

## オブジェクト monitor

Network Manager の状態変更を監視する。

## 参考

- [NetworkManager](https://github.com/NetworkManager)
