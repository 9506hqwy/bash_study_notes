# `parted` コマンド

ディスクパーティションを管理する。

## 表示

パーティションを表示する。

```sh
sudo parted /dev/sda print
```

```text
モデル: Msft Virtual Disk (scsi)
ディスク /dev/sda: 68.7GB
セクタサイズ (論理/物理): 512B/4096B
パーティションテーブル: gpt
ディスクフラグ:

番号  開始    終了    サイズ  ファイルシステム  名前                  フラグ
 1    1049kB  630MB   629MB   fat32             EFI System Partition  boot, esp
 2    630MB   1704MB  1074MB  xfs                                     bls_boot
 3    1704MB  8576MB  6872MB  linux-swap(v1)                          swap
 4    8576MB  49.0GB  40.4GB  xfs
 5    49.0GB  68.7GB  19.7GB  xfs                                     linux-home

```

JSON 形式で表示する。

```sh
sudo parted -j /dev/sda print
```

```json
{
   "disk": {
      "path": "/dev/sda",
      "size": "68.7GB",
      "model": "Msft Virtual Disk",
      "transport": "scsi",
      "logical-sector-size": 512,
      "physical-sector-size": 4096,
      "label": "gpt",
      "uuid": "a4fe9792-0a72-4899-addf-bb310334a162",
      "max-partitions": 128,
      "partitions": [
         {
            "number": 1,
            "start": "1049kB",
            "end": "630MB",
            "size": "629MB",
            "type": "primary",
            "type-uuid": "c12a7328-f81f-11d2-ba4b-00a0c93ec93b",
            "uuid": "90a994bd-5771-409d-956a-18bf0068f80a",
            "name": "EFI System Partition",
            "filesystem": "fat32",
            "flags": [
                "boot", "esp"
            ]
         },{
            "number": 2,
            "start": "630MB",
            "end": "1704MB",
            "size": "1074MB",
            "type": "primary",
            "type-uuid": "bc13c2ff-59e6-4262-a352-b275fd6f7172",
            "uuid": "19182f4c-2c56-41aa-8a4b-a78709fde12e",
            "filesystem": "xfs",
            "flags": [
                "bls_boot"
            ]
         },{
            "number": 3,
            "start": "1704MB",
            "end": "8576MB",
            "size": "6872MB",
            "type": "primary",
            "type-uuid": "0657fd6d-a4ab-43c4-84e5-0933c84b4f4f",
            "uuid": "f18361c5-fadd-4dc3-a4b9-a1a1a043d54c",
            "filesystem": "linux-swap(v1)",
            "flags": [
                "swap"
            ]
         },{
            "number": 4,
            "start": "8576MB",
            "end": "49.0GB",
            "size": "40.4GB",
            "type": "primary",
            "type-uuid": "4f68bce3-e8cd-4db1-96e7-fbcaf984b709",
            "uuid": "9407b2b8-d414-454e-beed-4392f3f97c4b",
            "filesystem": "xfs"
         },{
            "number": 5,
            "start": "49.0GB",
            "end": "68.7GB",
            "size": "19.7GB",
            "type": "primary",
            "type-uuid": "933ac7e1-2eb4-4f13-b844-0e14e2aef915",
            "uuid": "bf714f10-43f2-4d87-8fdf-0b27df5f7b65",
            "filesystem": "xfs",
            "flags": [
                "linux-home"
            ]
         }
      ]
   }
}
```

マシンリーダブルな形式で表示する。

```sh
sudo parted -m /dev/sda print
```

```text
BYT;
/dev/sda:68.7GB:scsi:512:4096:gpt:Msft Virtual Disk:;
1:1049kB:630MB:629MB:fat32:EFI System Partition:boot, esp;
2:630MB:1704MB:1074MB:xfs::bls_boot;
3:1704MB:8576MB:6872MB:linux-swap(v1)::swap;
4:8576MB:49.0GB:40.4GB:xfs::;
5:49.0GB:68.7GB:19.7GB:xfs::linux-home;
```

## 制御

### パーティションの作成

ディスクを選択する。

```sh
(parted) select /dev/sdb
```

```text
/dev/sdb を使用
```

GPT パーティションテーブルを作成する。

```sh
(parted) mklabel gpt
(parted) p
```

```text
モデル: Msft Virtual Disk (scsi)
ディスク /dev/sdb: 34.4GB
セクタサイズ (論理/物理): 512B/4096B
パーティションテーブル: gpt
ディスクフラグ:

番号  開始  終了  サイズ  ファイルシステム  名前  フラグ

```

名前 data01 を指定してパーティションを作成する。
MBR パーティションテーブルの場合は `name` ではなく `part-type` を指定する。

```sh
(parted) mkpart data01 xfs 1MiB 50%
(parted) p
```

```text
モデル: Msft Virtual Disk (scsi)
ディスク /dev/sdb: 34.4GB
セクタサイズ (論理/物理): 512B/4096B
パーティションテーブル: gpt
ディスクフラグ:

番号  開始    終了    サイズ  ファイルシステム  名前    フラグ
 1    1049kB  17.2GB  17.2GB  xfs               data01

```

### パーティションの拡張

パーティション 1 (/dev/sdb1) を拡張する。

```sh
(parted) resizepart 1 100%
(parted) p
```

```text
モデル: Msft Virtual Disk (scsi)
ディスク /dev/sdb: 34.4GB
セクタサイズ (論理/物理): 512B/4096B
パーティションテーブル: gpt
ディスクフラグ:

番号  開始    終了    サイズ  ファイルシステム  名前    フラグ
 1    1049kB  34.4GB  34.4GB                    data01

```

## 参考

- [永続ディスクのサイズを増やす](https://docs.cloud.google.com/compute/docs/disks/resize-persistent-disk)
- [Linux VM の仮想ハード ディスクを拡張する](https://learn.microsoft.com/ja-jp/azure/virtual-machines/linux/expand-disks)
- [Amazon EBS ボリュームのサイズ変更後にファイルシステムを拡張](https://docs.aws.amazon.com/ja_jp/ebs/latest/userguide/recognize-expanded-volume-linux.html)
