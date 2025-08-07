# `tshark` コマンド

パケットを解析する。wireshark の CLI 版となる。

[tcpdump](./tcpdump.md) でキャプチャしたパケットも解析できる。

## インストール

tshark をインストールする。

```sh
> dnf install wireshark-cli
```

## キャプチャ

TODO

### キャプチャフィルタ

[tcpdump](./tcpdump.md) と同じ式を `-f` オプションに指定してキャプチャするパケットを選択できる。

## 参照

### ファイル

ファイルからパケットを読む。[curl](./curl.md) で HTTP GET した結果を表示する。

```sh
> tshark -r http-get.pcap
    1   0.000000   172.16.1.2 → 10.88.0.2    TCP 74 36330 → 80 [SYN] Seq=0 Win=65495 Len=0 MSS=65495 SACK_PERM=1 TSval=1371160889 TSecr=0 WS=128
    2   0.000023    10.88.0.2 → 172.16.1.2   TCP 74 80 → 36330 [SYN, ACK] Seq=0 Ack=1 Win=65160 Len=0 MSS=1460 SACK_PERM=1 TSval=3304495463 TSecr=1371160889 WS=128
    3   0.000033   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [ACK] Seq=1 Ack=1 Win=65536 Len=0 TSval=1371160889 TSecr=3304495463
    4   0.000308   172.16.1.2 → 10.88.0.2    HTTP 143 GET /get HTTP/1.1
    5   0.000318    10.88.0.2 → 172.16.1.2   TCP 66 80 → 36330 [ACK] Seq=1 Ack=78 Win=65152 Len=0 TSval=3304495463 TSecr=1371160889
    6   0.001068    10.88.0.2 → 172.16.1.2   TCP 296 HTTP/1.1 200 OK  [TCP segment of a reassembled PDU]
    7   0.001078   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [ACK] Seq=78 Ack=231 Win=65408 Len=0 TSval=1371160890 TSecr=3304495464
    8   0.001099    10.88.0.2 → 172.16.1.2   HTTP/JSON 247 HTTP/1.1 200 OK , JavaScript Object Notation (application/json)
    9   0.001104   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [ACK] Seq=78 Ack=412 Win=65280 Len=0 TSval=1371160890 TSecr=3304495464
   10   0.001295   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [FIN, ACK] Seq=78 Ack=412 Win=65536 Len=0 TSval=1371160890 TSecr=3304495464
   11   0.001552    10.88.0.2 → 172.16.1.2   TCP 66 80 → 36330 [FIN, ACK] Seq=412 Ack=79 Win=65152 Len=0 TSval=3304495465 TSecr=1371160890
   12   0.001565   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [ACK] Seq=79 Ack=413 Win=65536 Len=0 TSval=1371160891 TSecr=3304495465
```

### 表示フィルタ

GUI 版と同じフィルタを `-Y` オプションに指定して表示するパケットを選択できる。

送信元 IP アドレスが 172.16.1.1 のパケットを表示する。

```sh
> tshark -r http-get.pcap -Y 'ip.src == 172.16.1.2'
    1   0.000000   172.16.1.2 → 10.88.0.2    TCP 74 36330 → 80 [SYN] Seq=0 Win=65495 Len=0 MSS=65495 SACK_PERM=1 TSval=1371160889 TSecr=0 WS=128
    3   0.000033   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [ACK] Seq=1 Ack=1 Win=65536 Len=0 TSval=1371160889 TSecr=3304495463
    4   0.000308   172.16.1.2 → 10.88.0.2    HTTP 143 GET /get HTTP/1.1
    7   0.001078   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [ACK] Seq=78 Ack=231 Win=65408 Len=0 TSval=1371160890 TSecr=3304495464
    9   0.001104   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [ACK] Seq=78 Ack=412 Win=65280 Len=0 TSval=1371160890 TSecr=3304495464
   10   0.001295   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [FIN, ACK] Seq=78 Ack=412 Win=65536 Len=0 TSval=1371160890 TSecr=3304495464
   12   0.001565   172.16.1.2 → 10.88.0.2    TCP 66 36330 → 80 [ACK] Seq=79 Ack=413 Win=65536 Len=0 TSval=1371160891 TSecr=3304495465
```

HTTP プロトコルのパケットを表示する。

```sh
> tshark -r http-get.pcap -Y 'http'
    4   0.000308   172.16.1.2 → 10.88.0.2    HTTP 143 GET /get HTTP/1.1
    8   0.001099    10.88.0.2 → 172.16.1.2   HTTP/JSON 247 HTTP/1.1 200 OK , JavaScript Object Notation (application/json)
```

### 出力形式

出力形式を `-T` オプションに指定できる。
text, json, xml 形式などがある。

```sh
> tshark -r http-get.pcap -Y 'http' -T json
[
  {
    "_index": "packets-2025-08-06",
    "_type": "doc",
    "_score": null,
    "_source": {
      "layers": {
        "frame": {
          "frame.encap_type": "1",
          "frame.time": "Aug  6, 2025 20:32:10.790043000 JST",
          "frame.offset_shift": "0.000000000",
          "frame.time_epoch": "1754479930.790043000",
          "frame.time_delta": "0.000275000",
...
```

出力する項目を `-T fields` オプションと `-E` オプションで指定する。

IP ヘッダの送信元 IP アドレスと宛先 IP アドレスを CSV 形式で取得する。

```sh
> tshark -r http-get.pcap -Y 'http' -T fields -e ip.src -e ip.dst -E separator=, -E quote=d
"172.16.1.2","10.88.0.2"
"10.88.0.2","172.16.1.2"
```

### パケット内容表示

`-V` オプションを指定してパケットの詳細情報を表示する。

HTTP GET リクエストを表示する。パケットごとに下記が表示される。

* イーサネットヘッダ (レイヤー2)
  1. プリアンブル (キャプチャされない。)
  2. 宛先 MAC アドレス
  3. 送信元 MAC アドレス
  4. タイプ (IPv4: 0x0800)
* IP ヘッダ (レイヤー3)
  1. バージョン (Ipv4: 0b0100)
  2. ヘッダ長 (20バイト = 4バイト単位 x 5)
  3. ToS (DSCP)
  4. パケット長
  5. 識別子 (ランダムな一意な識別子)
  6. フラグ (フラグメント許可、ラストフラグメントか)
  7. フラグメントオフセット (フラグメント化したパケットのオフセット)
  8. TTL
  9. プロトコル番号 (TCP: 6) ※ [RFC 790](https://datatracker.ietf.org/doc/html/rfc790)
  10. チェックサム
  11. 送信元 IP アドレス
  12. 宛先 IP アドレス
* TCP ヘッダ (レイヤー4)
  1. 送信元ポート番号
  2. 宛先ポート番号
  3. シーケンス番号
  4. ACK 番号
  5. ヘッダ長 (32バイト = 4バイト単位 x 8)
  6. フラグ(ACK, SYN, FIN など)
  7. ウィンドウサイズ
  8. チェックサム
  9. 緊急ポインタ?
  10. オプション
* HTTP  (レイヤー7)
  1. HTTTP GET リクエスト

```sh
> tshark -r http-get.pcap -Y 'http and tcp.dstport == 80' -V
Frame 4: 143 bytes on wire (1144 bits), 143 bytes captured (1144 bits)
    Encapsulation type: Ethernet (1)
    Arrival Time: Aug  6, 2025 20:32:10.790043000 JST
    [Time shift for this packet: 0.000000000 seconds]
    Epoch Time: 1754479930.790043000 seconds
    [Time delta from previous captured frame: 0.000275000 seconds]
    [Time delta from previous displayed frame: 0.000000000 seconds]
    [Time since reference or first frame: 0.000308000 seconds]
    Frame Number: 4
    Frame Length: 143 bytes (1144 bits)
    Capture Length: 143 bytes (1144 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: eth:ethertype:ip:tcp:http]
Ethernet II, Src: 2a:f2:96:27:17:9b (2a:f2:96:27:17:9b), Dst: 0a:7b:db:22:e5:6f (0a:7b:db:22:e5:6f)
    Destination: 0a:7b:db:22:e5:6f (0a:7b:db:22:e5:6f)
        Address: 0a:7b:db:22:e5:6f (0a:7b:db:22:e5:6f)
        .... ..1. .... .... .... .... = LG bit: Locally administered address (this is NOT the factory default)
        .... ...0 .... .... .... .... = IG bit: Individual address (unicast)
    Source: 2a:f2:96:27:17:9b (2a:f2:96:27:17:9b)
        Address: 2a:f2:96:27:17:9b (2a:f2:96:27:17:9b)
        .... ..1. .... .... .... .... = LG bit: Locally administered address (this is NOT the factory default)
        .... ...0 .... .... .... .... = IG bit: Individual address (unicast)
    Type: IPv4 (0x0800)
Internet Protocol Version 4, Src: 172.16.1.2, Dst: 10.88.0.2
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 129
    Identification: 0xb57c (46460)
    Flags: 0x40, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0xcd8e [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 172.16.1.2
    Destination Address: 10.88.0.2
Transmission Control Protocol, Src Port: 36330, Dst Port: 80, Seq: 1, Ack: 1, Len: 77
    Source Port: 36330
    Destination Port: 80
    [Stream index: 0]
    [TCP Segment Len: 77]
    Sequence Number: 1    (relative sequence number)
    Sequence Number (raw): 1313192187
    [Next Sequence Number: 78    (relative sequence number)]
    Acknowledgment Number: 1    (relative ack number)
    Acknowledgment number (raw): 2785518333
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x018 (PSH, ACK)
        000. .... .... = Reserved: Not set
        ...0 .... .... = Nonce: Not set
        .... 0... .... = Congestion Window Reduced (CWR): Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 1... = Push: Set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...0 = Fin: Not set
        [TCP Flags: ·······AP···]
    Window: 512
    [Calculated window size: 65536]
    [Window size scaling factor: 128]
    Checksum: 0xb7df [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 1371160889, TSecr 3304495463
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 1371160889
            Timestamp echo reply: 3304495463
    [SEQ/ACK analysis]
        [iRTT: 0.000033000 seconds]
        [Bytes in flight: 77]
        [Bytes sent since last PSH flag: 77]
    [Timestamps]
        [Time since first frame in this TCP stream: 0.000308000 seconds]
        [Time since previous frame in this TCP stream: 0.000275000 seconds]
    TCP payload (77 bytes)
Hypertext Transfer Protocol
    GET /get HTTP/1.1\r\n
        [Expert Info (Chat/Sequence): GET /get HTTP/1.1\r\n]
            [GET /get HTTP/1.1\r\n]
            [Severity level: Chat]
            [Group: Sequence]
        Request Method: GET
        Request URI: /get
        Request Version: HTTP/1.1
    Host: 172.16.1.2\r\n
    User-Agent: curl/7.76.1\r\n
    Accept: */*\r\n
    \r\n
    [Full request URI: http://172.16.1.2/get]
    [HTTP request 1/1]
```

### TCP

統計を表示する。

```sh
> tshark -r http-get.pcap -z conv,tcp -q
================================================================================
TCP Conversations
Filter:<No Filter>
                                                           |       <-      | |       ->      | |     Total     |    Relative    |   Duration   |
                                                           | Frames  Bytes | | Frames  Bytes | | Frames  Bytes |      Start     |              |
172.16.1.2:36330           <-> 10.88.0.2:80                     5 749bytes        7 547bytes       12 1,296bytes     0.000000000         0.0016
================================================================================
```

TCP フローを表示する。

```sh
> tshark -r http-get.pcap -z flow,tcp,standard,"ip.addr==172.16.1.2 and tcp.stream==0" -q
|Time     | 172.16.1.2                            |
|         |                   | 10.88.0.2         |
|0.000000 |         SYN       |                   |Seq = 0
|         |(36330)  ------------------>  (80)     |
|0.000023 |         SYN, ACK  |                   |Seq = 0 Ack = 1
|         |(36330)  <------------------  (80)     |
|0.000033 |         ACK       |                   |Seq = 1 Ack = 1
|         |(36330)  ------------------>  (80)     |
|0.000308 |         PSH, ACK - Len: 77            |Seq = 1 Ack = 1
|         |(36330)  ------------------>  (80)     |
|0.000318 |         ACK       |                   |Seq = 1 Ack = 78
|         |(36330)  <------------------  (80)     |
|0.001068 |         PSH, ACK - Len: 230           |Seq = 1 Ack = 78
|         |(36330)  <------------------  (80)     |
|0.001078 |         ACK       |                   |Seq = 78 Ack = 231
|         |(36330)  ------------------>  (80)     |
|0.001099 |         PSH, ACK - Len: 181           |Seq = 231 Ack = 78
|         |(36330)  <------------------  (80)     |
|0.001104 |         ACK       |                   |Seq = 78 Ack = 412
|         |(36330)  ------------------>  (80)     |
|0.001295 |         FIN, ACK  |                   |Seq = 78 Ack = 412
|         |(36330)  ------------------>  (80)     |
|0.001552 |         FIN, ACK  |                   |Seq = 412 Ack = 79
|         |(36330)  <------------------  (80)     |
|0.001565 |         ACK       |                   |Seq = 79 Ack = 413
|         |(36330)  ------------------>  (80)     |
```

指定した TCP ストリームの内容を表示する。

```sh
# ストリームを確認
> tshark -r http-get.pcap -T fields -e tcp.stream | sort | uniq
0

> tshark -r http-get.pcap -z follow,tcp,ascii,0 -q

===================================================================
Follow: tcp,ascii
Filter: tcp.stream eq 0
Node 0: 172.16.1.2:36330
Node 1: 10.88.0.2:80
77
GET /get HTTP/1.1
Host: 172.16.1.2
User-Agent: curl/7.76.1
Accept: */*


        230
HTTP/1.1 200 OK
Server: gunicorn/19.9.0
Date: Wed, 06 Aug 2025 11:32:10 GMT
Connection: keep-alive
Content-Type: application/json
Content-Length: 181
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true


        181
{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "172.16.1.2",
    "User-Agent": "curl/7.76.1"
  },
  "origin": "172.16.1.2",
  "url": "http://172.16.1.2/get"
}

===================================================================
```
