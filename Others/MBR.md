# Master Boot Record --- MBR

[](https://wiki.osdev.org/MBR_(x86))

> A Master Boot Record (MBR) is the bootsector of a hard disk. That is, it is what the BIOS loads and runs, when it boots a hard disk. The MBR is the very first sector of the hard disk; it contains an MBR Bootstrap program (described below), and a Partition Table. Devices that emulate a hard disk during system initialization must also contain an MBR, because they must also have Partition Tables -- even if they are not bootable. 
>
> The BIOS will only boot an MBR from a device if that device is in the "boot sequence" stored in CMOS, and if the MBR on the device is formatted correctly. On the other hand, if a device is not in the boot sequence (but has a "drive number"), it is still possible for a Real Mode program (such as another MBR or bootloader) to load and boot that device's MBR directly. 

MBR是硬盘上的bootsector（引导扇区），位于硬盘的第一个扇区。MBR包含一个引导程序（MBR Bootstrap program），一个分区表。当启动硬盘时，它会被BIOS加载并运行（运行其中的引导程序）。即便硬盘不用于引导系统，也需要有分区表。



## MBR Bootstrap

An MBR is loaded by the BIOS at physical address 0x7c00, with DL set to the "drive number" that the  MBR was loaded from. The BIOS then jumps to the very beginning of the loaded MBR (0x7c00),  because that part of the MBR contains the "bootstrap" executable code.

Typical MBR bootstrap code will do the following:

-  relocate itself away from the 0x7c00 physical address (using a memory copy, and usually a far jump)
-  determine which partition (or hard disk) to boot from (either  by looking for the active partition, or by presenting the user with a  selection of installed operating systems to choose from)
  -  if the user selected an "inactive" partition, then set the  selected partition entry to "active", and clear the "active" bits of  other partition entries
  -  use BIOS INT 13h commands to rewrite the MBR if the partition table entries were modified
-  use BIOS INT 13h commands to load the Volume Boot Record (VBR, the "bootsector" of the bootloader) from the beginning of the selected partition to physical address 0x7c00
-  set DS:SI pointing to the selected partition table entry
-  jump to 0x7c00 (with CS set to 0, and DL set to the "drive number")

Note: it is intended for the value of DL, and the DS:SI pointer to be passed all the way into the kernel, for the kernel's use. This also means that the relocated MBR should not be overwritten during the boot process -- because the DS:SI pointer is aimed at a partition table entry inside that MBR, and needs to remain valid.



## MBR Format 

| Offset     | Size (bytes) | Description                                                  |
| ---------- | ------------ | ------------------------------------------------------------ |
| 0x000      | 440          | MBR Bootstrap (flat binary executable code)   引导程序，纯机器语言代码 |
| 0x1B8      | 4            | Optional "Unique Disk ID / Signature                         |
| 0x1BC      | 2            | Optional, reserved 0x0000                                    |
| 0x1BE(446) | 16           | First partition table entry 第一分区表                       |
| 0x1CE(462) | 16           | Second partition table entry 第二分区表                      |
| 0x1DE(478) | 16           | Third partition table entry 第三分区表                       |
| 0x1EE(494) | 16           | Fourth partition table entry 第四分区表                      |
| 0x1FE      | 2            | (0x55, 0xAA) "Valid bootsector" signature bytes              |


### Partition table entry format

| Offset | Size (bytes) | Description                                       |
| ------ | ------------ | ------------------------------------------------- |
| 0x00   | 1            | Drive attributes (bit 7 set = active or bootable) |
| 0x01   | 3            | CHS Address of partition start                    |
| 0x04   | 1            | Partition type                                    |
| 0x05   | 3            | CHS address of last partition sector              |
| 0x08   | 4            | LBA of partition start                            |
| 0x0C   | 4            | Number of sectors in partition                    |

| Element (offset) | Size     | Description                                                  |
| ---------------- | -------- | ------------------------------------------------------------ |
| 0                | byte     | Boot indicator bit flag: 0 = no, 0x80 = bootable (or "active") |
| 1                | byte     | Starting head                                                |
| 2                | 6 bits   | Starting sector (Bits 6-7 are the upper two bits for the Starting Cylinder field.) |
| 3                | 10 bits  | Starting Cylinder                                            |
| 4                | byte     | System ID         对应Partition type                         |
| 5                | byte     | Ending Head                                                  |
| 6                | 6 bits   | Ending Sector (Bits 6-7 are the upper two bits for the ending cylinder field) |
| 7                | 10 bits  | Ending Cylinder                                              |
| 8                | uint32_t | Relative Sector (to start of partition -- also equals the partition's starting LBA value) |
| 12               | uint32_t | Total Sectors in partition                                   |

note：

1. 如果忽略0x1B8和0x1BC的6个字节，MBR Bootstrap可以扩展到446个字节。

2. 虽然分区表有1234，但那只是为了便利，顺序并不重要。
3. 任何一个分区都可能是active。应该有且仅有一个分区表是active