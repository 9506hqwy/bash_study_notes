# `tcpdump` コマンド

パケットをキャプチャする。

## インターフェイス

キャプチャ可能なインターフェイスを表示する。

```sh
>  tcpdump --list-interfaces
1.enp1s0 [Up, Running, Connected]
2.enp7s0 [Up, Running, Connected]
3.any (Pseudo-device that captures on all interfaces) [Up, Running]
4.lo [Up, Running, Loopback]
5.docker0 [Up, Disconnected]
6.bluetooth-monitor (Bluetooth Linux Monitor) [Wireless]
7.usbmon2 (Raw USB traffic, bus number 2)
8.usbmon1 (Raw USB traffic, bus number 1)
9.usbmon0 (Raw USB traffic, all USB buses) [none]
10.nflog (Linux netfilter log (NFLOG) interface) [none]
11.nfqueue (Linux netfilter queue (NFQUEUE) interface) [none]
```

インターフェイスを指定してキャプチャする。

```sh
> tcpdump --interface=enp1s0
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp1s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
```

## パケット数

キャプチャするパケット数を指定する。

```sh
> tcpdump -nn -c 1
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp1s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
19:51:00.317453 IP 192.168.122.73.22 > 192.168.122.1.38948: Flags [P.], seq 4087170371:4087170439, ack 4026476216, win 501, options [nop,nop,TS val 756554742 ecr 1823114601], length 68
1 packet captured
17 packets received by filter
0 packets dropped by kernel
```

## ファイル

キャプチャしたパケットをファイルに保存する。

バッファリングを無効化するには `-U` オプションを追加する。

ファイルをサイズでローテーションするには `-C` オプションでサイズを指定する。
ファイルを時間でローテーションするには `-G` オプションで秒数を指定する。
`-C` と `-G` オプションは併用できる。

```sh
> tcpdump -w test.cap
dropped privs to tcpdump
tcpdump: listening on enp1s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
^C
6752 packets captured
6883 packets received by filter
0 packets dropped by kernel

> ls -l test.cap
-rw-r--r--. 1 tcpdump tcpdump 6623111  8月  5 19:55 test.cap

> file test.cap
test.cap: pcap capture file, microsecond ts (little-endian) - version 2.4 (Ethernet, capture length 262144)
```

ファイルに保存したパケットを読む。

```sh
> tcpdump -nn -r test.cap | head -n 1
reading from file test.cap, link-type EN10MB (Ethernet), snapshot length 262144
dropped privs to tcpdump
19:57:27.471588 IP 192.168.122.73.22 > 192.168.122.1.38948: Flags [P.], seq 4088695155:4088695223, ack 4026496476, win 501, options [nop,nop,TS val 756941896 ecr 1823501756], length 68
```

## 式 (pcap-filter)

式はコマンドの引数に式を指定するか `-F` オプションで式を保存したファイルを指定する。

式は下記の構成となる。

```sh
[[qualifier]+ [id]]+
```

qualifier は type, dir, proto の 3 種類ある。

type は `id` の種別を指定する。

* host (既定値):
* net
* port
* portrange

dir は通信の方向を指定する。

* src
* dst
* src or dst
* src and dst
* 他多数

proto は通信プロトコルを指定する。

* ether
* ip
* tcp
* arp
* 他多数

式を and(&&), or(||), not(!) でつないで複数の条件を指定できる。

### 例

宛先ホストを指定する。

```sh
> tcpdump -nn -c 1 dst host 192.168.122.52
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp1s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
20:52:06.333067 IP 192.168.122.73 > 192.168.122.52: ICMP echo reply, id 2, seq 1, length 64
1 packet captured
2 packets received by filter
0 packets dropped by kernel
```

送信元ネットワークを指定する。

```sh
> tcpdump -nn -c 1 src net 192.168.122
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp1s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
20:58:17.859279 IP 192.168.122.52.22 > 192.168.122.1.51144: Flags [P.], seq 3742056651:3742056719, ack 2709413343, win 561, options [nop,nop,TS val 2383518513 ecr 1517642381], length 68
1 packet captured
4 packets received by filter
0 packets dropped by kernel
```

両端ポートを指定する。

```sh
> tcpdump -nn -c 1 dst or src port 22
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp1s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
20:59:58.473998 IP 192.168.122.52.22 > 192.168.122.1.51144: Flags [P.], seq 3742060371:3742060567, ack 2709414967, win 561, options [nop,nop,TS val 2383619128 ecr 1517743092], length 196
1 packet captured
3 packets received by filter
0 packets dropped by kernel
```

ICMP echo を指定する。

```sh
> tcpdump -nn -c 1 icmp[icmptype] == icmp-echo
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp1s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
21:05:55.171347 IP 192.168.122.73 > 192.168.122.52: ICMP echo request, id 2, seq 1, length 64
1 packet captured
1 packet received by filter
0 packets dropped by kernel
```

宛先ホストと TCP ポートを指定する。

```sh
> tcpdump -nn -c 1 dst host 192.168.122.52 and tcp dst port 22
dropped privs to tcpdump
tcpdump: verbose output suppressed, use -v[v]... for full protocol decode
listening on enp1s0, link-type EN10MB (Ethernet), snapshot length 262144 bytes
21:07:34.047971 IP 192.168.122.1.51144 > 192.168.122.52.22: Flags [.], ack 3742063583, win 667, options [nop,nop,TS val 1518198677 ecr 2384074702], length 0
1 packet captured
1 packet received by filter
0 packets dropped by kernel
```
