# LVM

Logical Volume Manager を操作する。

## 物理ボリューム

### PV 表示

物理ボリュームを表示する。

```sh
sudo pvdisplay /dev/sdb1
```

```text
  "/dev/sdb1" is a new physical volume of "<16.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name
  PV Size               <16.00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               EcPL8B-Pvyw-rehW-9cDy-rfMD-Q10F-NUh1fz
```

コロン区切りで表示する。

```sh
sudo pvdisplay -c
```

```text
  "/dev/sdb1" is a new physical volume of "<16.00 GiB"
  /dev/sdb1::33552384:-1:0:0:-1:0:0:0:0:EcPL8B-Pvyw-rehW-9cDy-rfMD-Q10F-NUh1fz
```

列形式で表示する。

```sh
sudo pvdisplay -C
```

```text
  PV         VG Fmt  Attr PSize   PFree
  /dev/sdb1     lvm2 ---  <16.00g <16.00g
```

行形式で表示する。

```sh
sudo pvdisplay -C --rows
```

```text
  PV /dev/sdb1
  VG
  Fmt lvm2
  Attr ---
  PSize <16.00g
  PFree <16.00g
```

特定のフィールドのみ取得する。

```sh
sudo pvdisplay -C -o pv_name --noheading
```

```text
  /dev/sdb1
```

表示する条件を指定して取得する。
指定方法は `sudo pvdisplay --select help` を参照する。

```sh
sudo pvdisplay -S "pv_name=~sdb"
```

```text
  "/dev/sdb1" is a new physical volume of "<16.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name
  PV Size               <16.00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               EcPL8B-Pvyw-rehW-9cDy-rfMD-Q10F-NUh1fz

```

### PV 制御

物理ボリュームを作成する。

```sh
sudo pvcreate /dev/sdb1
```

```text
  Physical volume "/dev/sdb1" successfully created.
  Creating devices file /etc/lvm/devices/system.devices
```

物理ボリュームを拡張する。
[`parted` コマンド](./parted.md) でパーティションを拡張する。

物理ボリュームをパーティションのサイズに変更する。
ボリュームグループは自動的にリサイズされる。

```sh
sudo pvresize /dev/sdb1
```

```text
  Physical volume "/dev/sdb1" changed
  1 physical volume(s) resized or updated / 0 physical volume(s) not resized
```

## ボリュームグループ

### VG 表示

ボリュームグループを表示する。

```sh
sudo vgdisplay
```

```text
  --- Volume group ---
  VG Name               data01
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <16.00 GiB
  PE Size               4.00 MiB
  Total PE              4095
  Alloc PE / Size       0 / 0
  Free  PE / Size       4095 / <16.00 GiB
  VG UUID               URP7cQ-3foi-j9Wd-9GXR-271b-5VBH-qFdiqM

```

オプションは [PV 表示](#pv-表示) と同じ。

### VG 制御

ボリュームグループを作成する。

```sh
sudo vgcreate data01 /dev/sdb1
```

```text
  Volume group "data01" successfully created
```

## 論理ボリューム

### LV 表示

論理ボリュームを表示する。

```sh
sudo lvdisplay
```

```text
  --- Logical volume ---
  LV Path                /dev/data01/lun01
  LV Name                lun01
  VG Name                data01
  LV UUID                ttmOCM-yxaN-9UND-KhaZ-h6mn-p43o-fIEGn1
  LV Write Access        read/write
  LV Creation host, time docker.home.local, 2025-12-24 00:32:11 +0900
  LV Status              available
  # open                 0
  LV Size                1.00 GiB
  Current LE             256
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0

```

オプションは [PV 表示](#pv-表示) と同じ。

### LV 制御

論理ボリュームを作成する。

```sh
sudo lvcreate -L 1GiB --name lun01 data01
```

```text
  Logical volume "lun01" created.
```

論理ボリュームを +1GiB 拡張する。

```sh
sudo lvextend -L +1G /dev/data01/lun01
```

```text
  Size of logical volume data01/lun01 changed from 1.00 GiB (256 extents) to 2.00 GiB (512 extents).
  Logical volume data01/lun01 successfully resized.
```

論理ボリュームを空き VG の 50% 拡張する。

```sh
sudo lvextend -l 50%FREE /dev/data01/lun01
```

```text
  Size of logical volume data01/lun01 changed from 2.00 GiB (512 extents) to 15.00 GiB (3840 extents).
  Logical volume data01/lun01 successfully resized.
```
