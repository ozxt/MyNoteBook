# GUID Partition Table --- GPT

[](http://taggedwiki.zubiaga.org/new_content/656516abe3b3e9177b704d2a79db3d95)

[](https://wiki.osdev.org/GPT)

> In computer hardware, GUID Partition Table (GPT) is a standard for the layout of the partition table on a physical hard disk. It is **a part of the Extensible Firmware Interface (EFI) standard** proposed by Intel as a replacement for the PC BIOS, one of the few remaining parts of the original IBM PC. **EFI uses GPT whereas BIOS uses a Master Boot Record (MBR)**.

GPT是EFI的一部分；EFI使用GPT，BIOS（也就是Legacy）使用MBR。

## 特点

> Current PC BIOS schemes use a master boot record (MBR) to begin the process of initializing the disk. The MBR begins with an entry called the Master Boot Code, which contains an executable binary for the purpose of identifying and booting the active partition. EFI instead contains this capability itself, but to maintain backwards compatibility, **GPT retains the MBR entry as the first sector on the disk followed by a Primary Partition Table Header, the actual beginning of GPT**.

传统BIOS中，MBR在开头有个入口区域叫作Master Boot Code，里面包含一个程序用于识别和引导活动分区。EFI本身也有这种能力。但是为了向下兼容，GPT在硬盘第一个扇区保留着MBR入口。紧接着才是真正的GPT。

> GPT uses modern **logical block addressing (LBA)** in place of the cylinder-head-sector (CHS) addressing used with old MBRs (modern MBRs can use LBA). **Legacy MBR information is contained in LBA 0, the GPT header is in LBA 1, and the partition table itself follows. In 64-bit Windows operating systems, 16,384 bytes, or 32 sectors, are reserved for the GPT, leaving LBA 34 as the first usable sector on the disk.**

GPT使用逻辑块寻址（LBA），早期的MBR使用CHS寻址（柱面-磁头-扇区）（现代的MBR也用LBA了）。

对于GPT，LBA 0位置存储传统的MBR，GPT header存在LBA 1位置，接着是分区表项。在64bit windows里，16384个字节或者32个扇区用于放置GPT表项。所以第一个可用的扇区在LBA 34位置（1+1+32个逻辑块，0~33）

> According to Apple,[1] "Do not assume that the {LBA} size is always going to be 512 bytes." When booting an Intel-based Mac from a hard drive that is partitioned, the hard disk must be partitioned according to GPT, rather than Apple Partition Map.

不要总是认为LBA的大小是512个字节

> GPT also provides for redundancy. **The GPT header and partition table are written at both the beginning and the end of the disk.**

硬盘末尾有一份备份（GPT头和GPT表项）。

## 结构细节

### 使用GPT的硬盘结构：

| LBA编号            | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| LBA 0              | Protective Master Boot Record (PMBR). Holds a partition pointing to GPT to avoid accidental overwrite by old programs. |
| LBA 1              | partition header, can be identified by 8 bytes magic "EFI PART" (45h 46h 49h 20h 50h 41h 52h 54h) |
| LBA 2..33          | partition table entires                                      |
| ...data on disk... |                                                              |
| LBA -33..-2        | mirror of partition table                                    |
| LBA -1             | mirror of partition header on last addressable sector        |

### Legacy MBR (LBA 0)

> The primary purpose of the MBR at the beginning of the disk is **to  prevent MBR-based disk utilities from mis-recognizing, and possibly  over-writing, GPT disks**. A single partition type of 0xEE, encompassing  the entire GPT drive, is indicated and identifies it as GPT. Some 32-bit OSes which cannot read GPT disks nevertheless recognize this ID and  present the disk as an inaccessible GPT disk. Older OSes will generally  recognize the disk as containing one partition of unknown type and no  empty space, and will typically refuse to modify the disk unless the  user explicitly requests and confirms the deletion of this partition.  This minimizes accidental erasures.
>
> If the disk is larger than two terabytes (the maximum partition size  in the legacy MBR), the size of this partition is marked as 2 TB,  ignoring the rest of disk.

保留这个MBR主要目的是防止那些基于MBR的工具误识别甚至覆写了这个GPT硬盘，所以它也叫作Protective Master Boot Record（PMBR）。在PMBR的分区表第五个字节（0x1c2位置，该位置表示分区类型）是0xee。旧电脑无法识别这个分区类型（0xee），会认为这个硬盘包含一个未知类型的分区，且没有空闲的空间，除非用户显式指定，不然不会对这个分区执行删除修改之类的操作。

### Partition table header (LBA 1)

| **Offset** | **Length (bytes)** | **Description**                                              |
| ---------- | ------------------ | ------------------------------------------------------------ |
| 0x0        | 8                  | Signature, can be identified by 8 bytes magic "EFI PART" (45h 46h 49h 20h 50h 41h 52h 54h) |
| 0x8        | 4                  | GPT Revision                                                 |
| 0xC        | 4                  | Header size                                                  |
| 0x10       | 4                  | CRC32 checksum of the GPT header                             |
| 0x14       | 4                  | Reserved                                                     |
| 0x18       | 8                  | The LBA containing this header              GPT表头的LBA位置 |
| 0x20       | 8                  | The LBA of the alternate GPT header      备份GPT表头的LBA位置 |
| 0x28       | 8                  | The first usable block that can be contained in a GPT entry  可用作存数据的第一个块的LBA |
| 0x30       | 8                  | The last usable block that can be contained in a GPT entry  可用作存数据的最后一个块的LBA |
| 0x38       | 16                 | GUID of the disk                                             |
| 0x48       | 8                  | Starting LBA of the GUID Partition Entry array  第一个表项所在LBA位置 |
| 0x50       | 4                  | Number of Partition Entries    可用表项数 或 已用表项数      |
| 0x54       | 4                  | Size (in bytes) of each entry in the Partition Entry array - must be a value of 128×2ⁿ where n ≥ 0 (in the past, multiples of 8 were  acceptable)  每个表项的大小（128的2ⁿ倍） |
| 0x58       | 4                  | CRC32 of the Partition Entry array.                          |
| 0x5C       | **blocksize**-0x5C | Reserved (should be zeroed)                                  |

### Partition entries (LBA 2–33)

GUID partition entry format：

| **Offset** | **Length (bytes)** | **Description**                               |
| ---------- | ------------------ | --------------------------------------------- |
| 0x0        | 16                 | Partition Type GUID (zero means unused entry) |
| 0x10       | 16                 | Unique Partition GUID                         |
| 0x20       | 8                  | StartingLBA                                   |
| 0x28       | 8                  | EndingLBA                                     |
| 0x30       | 8                  | Attributes                                    |
| 0x38       | 72                 | Partition Name                                |

partition attributes：

| Bit  | Content                                                      |
| ---- | ------------------------------------------------------------ |
| 0    | system partition (disk partitioning utilities must reserve the partition as is) |
| 60   | read-only                                                    |
| 62   | hidden                                                       |
| 63   | do not automount (eg. do not assign drive letter)            |

Partition type GUIDs（类型UUID）：

| Partition type                                    | Globally-Unique Identifier (GUID)    |
| ------------------------------------------------- | ------------------------------------ |
| Unused entry                                      | 00000000-0000-0000-0000-000000000000 |
| EFI System Partition                              | C12A7328-F81F-11D2-BA4B-00A0C93EC93B |
| BIOS Boot Partition                               | 21686148-6449-6E6F-744E-656564454649 |
| MBR partition scheme                              | 024DEE41-33E7-11D3-9D69-0008C781F39F |
| Windows - Microsoft Reserved Partition            | E3C9E316-0B5C-4DB8-817D-F92DF00215AE |
| Windows - Basic Data Partition                    | EBD0A0A2-B9E5-4433-87C0-68B6B72699C7 |
| Windows - Logical Disk Manager metadata partition | 5808C8AA-7E8F-42E0-85D2-E1E90434CFB3 |
| Windows - Logical Disk Manager data partition     | AF9B60A0-1431-4F62-BC68-3311714A69AD |
| Linux - Data partition                            | EBD0A0A2-B9E5-4433-87C0-68B6B72699C7 |
| Linux - RAID partition                            | A19D880F-05FC-4D3B-A006-743F0F84911E |
| Linux - Swap partition                            | 0657FD6D-A4AB-43C4-84E5-0933C84B4F4F |
| Linux - Logical Volume Manager (LVM) partition    | E6D6D379-F507-44C2-A23C-238F2A3DF928 |
| Linux - Reserved                                  | 8DA63339-0007-60C0-C436-083AC8230908 |

The GUIDs in this table are written assuming a little-endian byte order. For example, the GUID for an EFI System partition is written as C12A7328-F81F-11D2-BA4B-00A0C93EC93B here, which corresponds to the 16 byte sequence 28 73 2A C1 1F F8 D2 11 BA 4B 00 A0 C9 3E C9 3B — only the first three blocks are byte-swapped.

Linux and Windows use the same GUID for their respective data partitions.



# UUID，GUID，PartitionGUID（PARTUUID）

 UUID全称：Universally Unique Identifier，即通用唯一识别码。是一个由4个连字号(-)将32个字节长的字符串分隔后生成的字符串，总共36个字节长。比如：550e8400-e29b-41d4-a716-446655440000。  UUID是由开放软件基金会（OSF）定义的。GUID（Globals Unique Identifier）是微软对UUID这个标准的实现。UUID还有其它各种实现，不止GUID一种。



## blkid命令的输出有UUID和PARTUUID：

**UUID is a filesystem-level UUID**, which is retrieved from the filesystem metadata inside the partition. It can only be read if the filesystem type is known and readable.

**PARTUUID is a partition-table-level UUID for the partition**, a standard feature for all partitions on GPT-partitioned disks. Since it is retrieved from the partition table, it is accessible without making any assumptions at all about the actual contents of the partition. If the partition is encrypted using some unknown encryption method, this might be the only accessible unique identifier for that particular partition.

**PTUUID is the UUID of the partition table itself**, a unique identifier for the entire disk assigned at the time the disk was partitioned. It is the equivalent of the disk signature on MBR-partitioned disks but with more bits and a standardized procedure for its generation.

On MBR-partitioned disks, there are no UUIDs in the partition table. The 32-bit disk signature is used in place of a PTUUID, and PARTUUIDs are created by adding a dash and a two-digit partition number to the end of the disk signature.

