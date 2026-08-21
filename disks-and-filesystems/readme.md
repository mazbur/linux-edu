
## Disk And File Systems

A linux disk consists of multiple partitions. Partitions are subdivisions of whole disk.
```bash
\dev\sda1
\dev\sda3
```
Kernel presents each partition as block device, just like an entire disk.
Partiions are defined on partition tables.

FileSystem is layer up from partition. Database of files and directories that user interacts with.

#### Partitioning Disk Devices

Partitioning table is nothing but just data that says how the block is divided.
Below are some partitioning tools listed.
1. parted("partitions editior") A text based tool
2. gparted
3. fdisk

> fdisk is more commonly used nowadays

* To view a partition
```bash
parted -l
```
```
Warning: Unable to open /dev/sda read-write (Read-only file system).  /dev/sda
has been opened read-only.
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 374MB
Sector size (logical/physical): 512B/512B
Partition Table: loop
Disk Flags:

Number  Start  End    Size   File system  Flags
 1      0.00B  374MB  374MB  ext2


Warning: Unable to open /dev/sdb read-write (Read-only file system).  /dev/sdb
has been opened read-only.
Model: Msft Virtual Disk (scsi)
Disk /dev/sdb: 167MB
Sector size (logical/physical): 512B/512B
Partition Table: loop
Disk Flags:

Number  Start  End    Size   File system  Flags
 1      0.00B  167MB  167MB  ext2


Model: Msft Virtual Disk (scsi)
Disk /dev/sdc: 4295MB
Sector size (logical/physical): 512B/4096B
Partition Table: loop
Disk Flags:

Number  Start  End     Size    File system     Flags
 1      0.00B  4295MB  4295MB  linux-swap(v1)
```

Here loop stands for no partioning table used and entire disk being used a single/raw filesystem directly.
Usually it can be msdos, gpt or mbr.

Viewing or altering partitioning tables is quiet easy, but is also very risky. Ensure the partition is disconnected
and you have it backed up.

*fdisk* or parted both can be used to perform operations related to partitioning. *fdisk* gives you the option to design
the partition before applying it. *fdisk* only applies changes after you exit the program. On the other hand *parted* applies changes as you execute commands. *fdisk* makes a single system call at the end so that the kernel knows and can use the partions as block device, *parted* doesn't make system calls like that. It makes individual calls to kernel to let it know about partition calls.

Lets create a Partition Table

1. *fdisk* is a interative command, so first enter 
```bash
fdisk \dev\ssd
```
2. Use *p* for displaying already existing partitions.
```bash
Command (m for help): p
```
3. Use *d* if you want to delete existing partitions.
```bash
Command (m for help): d
```
4. Use *n* for new partition. Then *p* or *s* for primary or secondary respectively.
```bash
Command (m for help): n
Partition type
 p primary (0 primary, 0 extended, 4 free)
 e extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-8368127, default 2048): 2048
Last sector, +sectors or +size{K,M,G,T,P} (2048-8368127, default 8368127): +200M
``` 
1 is the partition number, rest are self explanatory.
5. Presss *p* to once again review your design or *q* if you want to quit and not go ahead.
6. After reviewing if you're satisfied, press *w* and that's it.

*fdisk* will not prompt you a confirmation. If you want to view the debugging logs, you can use *journalctl -k*.


#### FileSystems

Filesystems are link between kernel disks and user-space. It is database like structure which converts simple block device to hierachial file structure consisting of files and directories.

Below is the list of most common file system types.
1. The Fourth Extended filesystem (ext4)
2. b-tree Filesystem(btfs)
3. FAT Filesystems(msdos, vfat, exfat)
4. XFS
5. HFS+(hfsplus)
6. ISO 9660 (iso9660)

Once you're done with partitioning, you can create a filesystem.
Use *mkfs* command to create a filesystem. *mkfs* autmatically determines the number of blocks on the device and sets 
some reasonable defaults.

```bash
mkfs -t ext4 /dev/sdf2
```

This will create a ext4 partition on /dev/sdf2

After this you mount your filesystem to the device, which makes it available for the user space to use. **/** is also
mounted by kernel everytime you start your system. 
In order to mount a filesystem you must know:
* filesystem's device, location, or identifier
* filesystem type
* mount point

Run *mount* to learn the current filesystem status
```bash
mount
```
```
/dev/sda1 on / type ext4 (rw,errors=remount-ro)
proc on /proc type proc (rw,noexec,nosuid,nodev)
sysfs on /sys type sysfs (rw,noexec,nosuid,nodev)
fusectl on /sys/fs/fuse/connections type fusectl (rw)
debugfs on /sys/kernel/debug type debugfs (rw)
securityfs on /sys/kernel/security type securityfs (rw)
udev on /dev type devtmpfs (rw,mode=0755)
devpts on /dev/pts type devpts (rw,noexec,nosuid,gid=5,mode=0620)
tmpfs on /run type tmpfs (rw,noexec,nosuid,size=10%,mode=0755)
--snip-
```

Each line corresponds to a mounted system. Such as /dev/sda1.

To mount a filesystem manually
```bash
mount -t type device mountpoint
```
For Example
```bash
mount -t ext4 /dev/sdf2 /home/extra
```
To unmount
```bash
unmount mountpoint
```

Mounting can also be done using UUID, which stands for Universally Unique Identifier. 
To view UUID
```bash
blkid
```
```
/dev/sdf2: UUID="b600fe63-d2e9-461c-a5cd-d3b373a5e1d2" TYPE="ext4"
/dev/sda1: UUID="17f12d53-c3d7-4ab3-943e-a0a72366c9fa" TYPE="ext4"
PARTUUID="c9a5ebb0-01"
/dev/sda5: UUID="b600fe63-d2e9-461c-a5cd-d3b373a5e1d2" TYPE="swap"
PARTUUID="c9a5ebb0-05"
/dev/sde1: UUID="4859-EFEA" TYPE="vfat"
```

```bash
mount UUID=b600fe63-d2e9-461c-a5cd-d3b373a5e1d2 /home/extra
```

To mount filesystems at boot time and take the drudgery out of the mount command, Linux systems keep a permanent list offilesystems and options in /etc/fstab.

To view size and utilization of currently mounted filesystems
```bash
df
```
```
Filesystem 1K-blocks Used Available Use% Mounted on
/dev/sda1 214234312 127989560 75339204 63% /
/dev/sdd2 3043836 4632 2864872 1% /media/user/uuid
```


