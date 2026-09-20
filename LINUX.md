Content
1. [Linux](#linux)
2. [Disk Management](#2-disk-management-and-run-levels)
3. [User Management](#3-user-management)
4. [Permission Management](#4-linux-permission-management)

# 1. LINUX
Linux is a family of open-source Unix-like operating systems inspired by UNIX., based on the Linux kernel, a computer program that manages the system's hardware and software.

kernel that is used in a distribution, but most people just refer to it as an OS.
> 90% of application runs on **Linux**.

**Unix** is a proprietary (requiring a license for use), multitasking operating system, e.g. macOS, Debian. While **Linux** is a free and open-source, Unix-like operating system. e.g. Ubuntu, Kali Linux

### How to Use Linux
1.  **WSL (Windows Subsystem for Linux)** – Enable it on Windows to run Linux distributions natively.
2.  **Virtual Machine** – Use applications like [VirtualBox](https://www.virtualbox.org/wiki/Downloads)  to run Linux in a virtual environment.
3.  **Cloud Platforms** – Use Linux-based instances on AWS, GCP, or Azure.
4.  **Vagrant** – Try Linux environments locally using [Vagrant](https://developer.hashicorp.com/vagrant)

## Linux - OS
The kernel is the **heart**  of the operating system. It is written primarily in the **C programming language**  and acts as the bridge between the system hardware and software.
-   It manages system resources such as CPU, memory, and I/O devices.
-   Users interact with the kernel via a **shell** (command-line interface).    
-   Common shell commands include `mkdir`, `ls`, `cd`, etc.
- **root** is the most powerful account that can create, modify, delete accounts and make changes to system configuration files.
> **GRUB** (Grand Unified Bootloader) is the program responsible for loading and managing the boot process. It loads the **kernel**  of the OS into memory at startup.

Linux itself is not one single operating system. It has many versions called distributions (distros).

Examples:

- Ubuntu
- CentOS
- Debian
- Fedora
- Red Hat Enterprise Linux

Linux has hundreds of distributions (Linux OS versions). They are all based on the Linux kernel but are made for different purposes.

**Here are the most popular Linux OS families:**


**1. Debian Family** One of the oldest, free and most stable Linux families. e.x **ubuntu, kali linux, linux Mint**

**2. Red Hat Family** Popular in companies and enterprise servers. **Red Hat Enterprise Linux (RHEL), Fedora Linux, CentOS**

| |Debian| Red Hat | 
|--|--| -- |
| Package Management | apt + .deb packages | yum/dnf + .rpm packages |
|Where they are mostly used|Web servers, Cloud servers, Personal computers | Large companies, Banks, Government systems, Corporate servers |
|Cost Difference|Free|Paid|
|Package Commands|`apt`,`apt update`, `apt install`, `.deb`|`dnf/yum`, `dnf update`, `dnf install`, `.rpm`|
|Real-world example|A startup might use `AWS server -> Ubuntu -> Website`| A bank might use `Data Center -> RHEL -> Banking application`|


# 2. DISK MANAGEMENT AND RUN LEVELS
Unlike Windows linux doesn't have C:, D: or E:. Instead linux has a single starting point called root directory. It is represented by one character (`/`).

OS stores files and directories in an organized and structured way. There are many different types of filesystems. 

Windows:-
- FAT32 : Pendrive (if files <4GB)
- NTFS : Windows system drive

Linux:-
- ext2
- ext3
- ext4 : General Linux desktop
- xfs : High-performance server / large files (RHEL/Fedora)
```bash
cd /
ls -l    # list all the files structure
/home    # directory for user. Everything related to account user. 
/        # is the root of entire file system
/root    # root user home directory. It is not same as /
/boot    # contains file that is used by the boot loader (grub.cfg)
/bin --> /usr/bin # everyday users commands store here like, ls, mv, cat, etc.
/sbin    # system bin this contain admistrative tools used for managing OS. commands like networking, disk management booting system, etc.
/etc     # all configuration files. to change networks settings edit file inside it.
/dev     # systems devices (e.x. disk, speakers, keyboards, etc)
/opt     # optional add-on applications (not part of OS apps)
/proc    # virtual file system created by linux kernel. contain live infor about running processes 
/tmp     # directory for temporary files
/var     # system logs, application logs, email queues, databases and cache files.
/run     # system daemons that start very early to store temporary runtime file like PID files
/mnt     # to mount external filesystem (e.x NFS)
```
## Types of Disk
1. SATA Disk :  Common interface for HDD/SSD.
`/dev/sda`  : disk1
`/dev/sdb` : disk2
After partition disk1
`/dev/sda1` , `/dev/sda2`, and so on. 
- Use `lsblk` to see in terminal.

2. PATA Disk : Older disk interface. `/dev/hda`
3. Virtual Disk : Software-based disk used by virtual machines

**Storage device types:**
-   HDD
-   SSD

**Interfaces / technologies:**
-   SATA
-   NVMe/PCIe
-   SAS
-   USB, etc.


```bash
lsblk
# OUTPUT
sda # main disk
|__ sda1   part /boot    # partition 1
|__ sda2   part          # partition 2
    |__ cs-root   lvm  /

sdb # in future if we add new disk
```
## Partition Types
1. Primary Partition 
2. Extended Partition
3. Logical Partition

**1. Primary Partition:** 

-   A partition that can directly contain a filesystem and be used for storing data.
-   In **MBR**, you can have a maximum of **4 primary partitions**.
-   A primary partition can be made **bootable/active**.
-   Example:
    ```
    /dev/sda1
    /dev/sda2
    ```
### 2. Extended Partition

-   An **MBR-only** partition type.
-   It is used to overcome the 4-primary-partition limitation.
-   An extended partition acts as a **container** for logical partitions.
-   You can have **only one extended partition** on an MBR disk.
Example:
```
/dev/sda
├── /dev/sda1     → Primary
├── /dev/sda2     → Primary
├── /dev/sda3     → Primary
└── /dev/sda4     → Extended
     ├── /dev/sda5 → Logical
     ├── /dev/sda6 → Logical
     └── /dev/sda7 → Logical
```

### 3. Logical Partition
-   A logical partition is created **inside an extended partition**.
-   It allows you to create more partitions when using MBR.
-   Examples:
    ```
    /dev/sda5
    /dev/sda6
    /dev/sda7
    ```
### MBR vs GPT
**MBR and GPT are partitioning schemes**. They define **how a disk is organized into partitions**.
### 1. MBR — Master Boot Record
**MBR** is an older partitioning scheme.
It has some important limitations:
-   Maximum **4 primary partitions** and Older.
-   To create more partitions, you use **Extended + Logical partitions**
-   Supports disks up to about **2 TB** with the traditional 512-byte sector setup
-   Common on older BIOS-based systems
```
Disk (/dev/sda) (100GB)
│
├── Primary Partition : dev/sda1  (20GB)
├── Primary Partition : dev/sda2 (20GB)
├── Primary Partition : dev/sda3 (20GB)
└── Extended Partition : dev/sda4 (40GB)
     ├── Logical Partition : /dev/sda5 (20GB)
     └── Logical Partition : /dev/sda6 (20GB)
```
### 2. GPT — GUID Partition Table
**GPT** is the modern partitioning scheme and is commonly used today.
Advantages:
-   Supports **many partitions** and Modern
-   No Primary/Extended/Logical partition limitation like MBR
-   Supports **very large disks**
-   Works naturally with **UEFI**
-   Keeps backup partition-table information, making it more robust

```
Disk (/dev/nvme0n1)
│
├── EFI System Partition
├── Linux Partition
├── Linux Partition
└── Linux Partition
```
```
Physical storage
      ↓
   HDD / SSD
      ↓
Interface
   SATA / NVMe / SAS
      ↓
Partitioning scheme
     MBR / GPT
      ↓
Partitions
      ↓
Filesystem
   ext4 / XFS / etc.
```

## How data is stored
| Component    | What it is                                      | Analogy                        | Example                          |
| :----------- | :---------------------------------------------- | :----------------------------- | :------------------------------- |
| **Disk**     | Physical hardware                               | The empty warehouse            | HDD, SSD, NVMe                   |
| **Partition**| Logical slice of the disk                       | Rooms inside the warehouse     | C:\, D:\, `/dev/sda1`            |
| **Sector**   | Physical hardware unit (512 bytes)              | A single box on a shelf        | Fixed by hardware                |
| **Block**    | Logical OS unit (4 KB = 8 sectors)              | The entire shelf               | 4KB, 8KB, 16KB                   |
| **File System** | Software mapping files → blocks              | Library catalog                | NTFS, ext4, FAT32, XFS           |
| **Database** | Software managing structured data               | Smart filing cabinet           | MySQL, PostgreSQL, MongoDB       |

### Data Flow (SQL Query → Physical Disk)

1. **Database** → Checks indexes → finds internal page inside its file
2. **File System** → Maps file → finds which **blocks** hold that data
3. **Block Layer** → OS reads **4KB blocks** (made of 512-byte **sectors**)
4. **Partition** → OS knows blocks belong to Partition #2 (`/dev/sda2`)
5. **Disk** → Hardware physically retrieves data → sends it back up




## 1. Adding a New Disk (VMware)
After attaching a new virtual disk in VMware, use these commands to check the current disk/partition list:
```bash
lsblk
fdisk -l
lsscsi
```
#### Step 1: Detect the new disk

The new disk won't show up automatically. You have two options:

**Option A — Reboot the server** (simplest, but causes downtime)

**Option B — Rescan the bus without rebooting:**
```bash
# For new SCSI disks
echo "- - -" > /sys/class/scsi_host/host0/scan

# For NVMe / newly added PCI devices
echo 1 > /sys/bus/pci/rescan
```
This **triggers a bus rescan** — it tells the kernel to detect newly attached storage devices without a reboot. Run this against each host adapter if you have more than one (`host0`, `host1`, etc.). Once done, confirm the new disk appears with `lsblk`.

#### Step 2: Create a new partition
Verify the new disk is detected:
```bash
lsblk
fdisk -l
```
Create the partition:
```bash
fdisk /dev/sdb    # sdb is the new disk

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-20971519, default 2048): [Enter]
Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519): [Enter]
Command (m for help): w
```

Verify with `lsblk` — you'll now see **1 new partition** (`sdb1`) under the `sdb` disk.

Then tell the kernel about the new partition table without rebooting:
```bash
partprobe /dev/sdb
```

#### Step 3: Format and mount
```bash
mkfs.ext4 /dev/sdb1        # format the partition as ext4
mkdir /postgres             # create the mount point directory
mount /dev/sdb1 /postgres   # mount the partition
lsblk                       # verify the mount
df -h                       # see size and available space on /postgres
```

#### Step 4: Make the mount permanent

A manual `mount` won't survive a reboot, so add an entry to `/etc/fstab`:
```bash
vim /etc/fstab
```
Add this line:
```
/dev/sdb1   /postgres   ext4   defaults   0   0
```

**Field meanings (left to right):** device → mount point → filesystem type → mount options → dump → fsck order

-   **dump field (5th column):** `0` = the `dump` backup utility will skip this filesystem, `1` = include it. (This legacy tool is rarely used today — most systems leave this at `0`.)
-   **fsck order (6th column):** `0` = don't check this filesystem at boot, `1` = check first (root filesystem only), `2` = check after root. Non-root filesystems are usually set to `2`, not `0`.

Save and exit, then validate the entry:
```bash
mount -a
```
If there's a syntax error in `/etc/fstab`, `mount -a` will report it — fix the file and re-run until it's clean.

```bash
mount     # lists all currently mounted filesystems, so you can confirm /postgres mounted with the right options
```

### Create Partition
```bash
lsblk  # list the available disk
fdisk -l  # list available disk


fdisk /dev/sdb 		# '/dev/sdb' is a new disk after adding.
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-20971519, default 2048): [Enter]
Last sector, +sectors or +size{K,M,G} (2048-20971519, default 20971519): +10G
Command (m for help): p  # to view the newly created partition
Device     Boot Start     End Sectors Size Id Type
/dev/sdb1        2048 4196351 4194304   10G 83 Linux
Command (m for help): w  # to save it


lsblk   # to verify the newly created partition

# To Delete any Partition
fdisk /dev/sdb
Command (m for help): d  # to delete
Selected partition 1
Partition 1 has been deleted.
Command (m for help): w   # to save and quit
```


## 2. LVM (Logical Volume Manager)
LVM is a storage virtualization layer that sits between physical disks and the file system, giving you flexibility that traditional partitions cannot provide.

### The Three Layers of LVM
![](./images/linux/lvm.png)

### Why LVM? (vs Traditional Partitions)

![](./images/linux/LVM-vs-TraditionalPartitions.png)



### Creating LVM Partition
```bash
fdisk /dev/sdb

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-16777215, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-16777215, default 16777215): +2G

Created a new partition 1 of type 'Linux' and of size 2 GiB.

Command (m for help): t    # change partition type to LVM
Selected partition 1
Hex code or alias (type L to list all): L  # to list all types
Aliases:
   linux          - 83
   swap           - 82
   extended       - 05
   uefi           - EF
   raid           - FD
   lvm            - 8E
   linuxex        - 85
Hex code or alias (type L to list all): 8E   # LVM
Changed type of partition 'Linux' to 'Linux LVM'.

Command (m for help): w			# to save and exit
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

# NOW Create Physical Volume (VG)
pvcreate /dev/sdb1
pvdisplay  or pvs		# to verify

# Create Voulme Group (VG)
vgcreate vg_database /dev/sdb1
vgdisplay or vgs  # to verify

# Create Logical Volume (1GB for each apps)
lvcreate -L 1000M -n lv-app1 vg_database
lvcreate -L 1000M -n lv-app2 vg_database
lvdisply or lvs  # to verify

# Create an ext4 filesystem on the Logical Volume `lv-app1`, which belongs to the Volume Group `vg_database`
mkfs.ext4 /dev/vg_database/lv-app1
mkfs.ext4 /dev/vg_database/lv-app2

# create dir to mount
mkdir app1
mkdir app2

# Create mount point and mount
mount /dev/vg_database/lv-app1 /root/app1/
mount /dev/vg_database/lv-app2 /root/app2/

df -Th # to verify

# to see the change done in mount
cat /etc/mtab  # copy the last and paste below

# Add to /etc/fstab for persistence Mount
echo "/dev/mapper/vg_database-lv--app1 /root/app1 ext4 defaults  0 0" >> /etc/fstab
echo "/dev/mapper/vg_database-lv--app2 /root/app2 ext4 defaults  0 0" >> /etc/fstab

# 13. Validate fstab entries
mount -a
mount -av

# 14. Verify mount
df -h /mnt/apps
lsblk
```
```
Physical Disk
     ↓
Physical Volume (PV)
     ↓
Volume Group
vg_database
     ↓
Logical Volume
lv-app1
     ↓
ext4 filesystem
     ↓
Mounted at
/root/app1
```
### Extending a Disk Using LVM


```
                 STORAGE
                    │
                  DISK
                    │
                PARTITION
                    │
              ┌─────┴─────┐
              │            │
          Filesystem      LVM
              │            │
              │           PV
              │            │
              │           VG
              │            │
              │           LV
              │            │
              └─────┬──────┘
                    │
                MOUNT POINT
                    │
             /data /home /var
                    │
              FILES & DIRECTORIES
                    │
          Owner / Group / Permissions
```

## Creating entire raw disk directly as an LVM Physical Volume (PV) without creating a partition
Let's assume:

-   New disk: `/dev/sdb`
-   Disk size: **2 GB**
-   Volume Group: `vg-database`
-   Logical Volume: `lv-postgres`
-   Mount point: `/postgres`
-   Filesystem: **XFS**
```bash
lsblk
# Lists disks, partitions and mount points.
# Confirm that /dev/sdb is the new 2GB disk.

sudo pvcreate /dev/sdb
# Creates an LVM Physical Volume directly on /dev/sdb.
# No partition such as /dev/sdb1 is required.
sudo pvs
# Shows the available Physical Volumes.

sudo vgcreate vg-database /dev/sdb
# Creates VG named vg-database using /dev/sdb.
sudo vgs
# Shows the Volume Group and available free space.

sudo lvcreate -l 100%FREE -n lv-postgres vg-database
# Creates an LV named lv-postgres.
# -l 100%FREE means use all available space in the VG.
sudo lvs
# Displays the Logical Volume details.

sudo mkfs.xfs /dev/vg-database/lv-postgres
# Creates an XFS filesystem on the Logical Volume.

sudo mkdir /postgres
# Creates the directory where the filesystem will be mounted.

sudo mount /dev/vg-database/lv-postgres /postgres
# Mounts the LV to /postgres.

df -h /postgres
# Confirms that the filesystem is mounted and shows its size.

sudo vi /etc/fstab
#Make it permanent after reboot. Make change in the file

sudo mount -a
# Reads /etc/fstab and mounts filesystems.
# If there is no error, the fstab entry is valid.

df -h /postgres
# Verify the mount is working.
```

## Adding Space in LVM File System
### 1. Use free space already available in the VG
```bash
df -h /postgres
# Check current filesystem usage

vgs
# Check free space available in the Volume Group

lvs
# Check the current LV size

# if `vg-database` has 5 GB free, then
lvextend -L +5G /dev/vg-database/lv-postgres
# Increase LV by 5 GB

xfs_growfs /postgres
# Grow XFS filesystem to use the additional space

df -h /postgres
# Verify
```

### 2. Add a new disk to the existing VG
Suppose `/dev/sdc` is a new 10 GB disk.
```bash
lsblk
# Confirm the new disk

pvcreate /dev/sdc
# Create PV directly on the new disk

vgextend vg-database /dev/sdc
# Add the new disk to existing VG
vgs
# Verify VG has additional free space

# Then extend `/postgres`:
lvextend -L +10G /dev/vg-database/lv-postgres
# Increase LV by 10 GB

xfs_growfs /postgres
# Increase XFS filesystem

df -h /postgres
# Verify
```

### 3. Extend the existing disk/LUN(Logical Unit Number)
Suppose `/dev/sdb` was originally **20 GB** and storage team increases it to **30 GB**.
```bash
lsblk
# Verify that the OS sees the increased disk size
pvresize /dev/sdb
# Makes the additional disk space available to LVM
pvs
vgs
# Verify additional free space

lvextend -L +10G /dev/vg-database/lv-postgres
# Extend the LV

xfs_growfs /postgres
# Extend XFS filesystem

df -h /postgres
# Verify
```
> If the storage team increases the size of an existing LUN, I use `pvresize` to make the additional space available to LVM, then extend the LV and filesystem.
### 4. Add a new LV and mount it separately
Sometimes we **don't extend `/postgres` itself**. Instead, we create a separate LV for a specific directory.
For example, `/postgres/data` needs more space.
```bash
lvcreate -L 10G -n lv-pgdata vg-database
# Create a new 10GB LV

mkfs.xfs /dev/vg-database/lv-pgdata
# Create filesystem

mkdir /postgres/data
# Create mount point

mount /dev/vg-database/lv-pgdata /postgres/data
# Mount it

#Then add it to `/etc/fstab` using UUID.
```
| Situation | What to do |
|--|--|
| VG has free space | `lvextend` → `xfs_growfs` |
|New Disk Added |`pvcreate` → `vgextend` → `lvextend` → `xfs_growfs`|
|Existing LUN increased |`pvresize` → `lvextend` → `xfs_growfs`|
|Separate Filesystem Needed |Create new LV → format → mount|

> I will check whether the Volume Group has 10 GB free. If yes, I extend the LV by 10 GB and then grow the filesystem. If the VG doesn't have free space, I will add a new disk or request the existing LUN to be expanded.





# 3. USER MANAGEMENT

1. **Local User Management** : This is the traditional method where user accounts are defined in local files on the specific RHEL system. This is ideal for small environments or for system-level service accounts.
```bash
# Managing local users is typically done through a suite of powerful command-line utilities. 
sudo useradd john 		#Creates a new user account.
sudo passwd john@123 	#Sets or changes a user's password.
sudo groupadd developers #Creates a new group.
sudo usermod -aG developers john # Modifies existing user attributes
sudo userdel -r john #Deletes a user account.
```

2. **Centralized User Management (SSSD, LDAP, AD):** As your infrastructure grows, managing users locally on every server becomes a nightmare. Centralized management stores user information in a central directory (like LDAP, Active Directory, or Red Hat's Identity Management - IdM). Clients use **SSSD** (System Security Services Daemon) to connect to this directory for authentication and identity lookups.
	- `LDAP` : **LDAP (Lightweight Directory Access Protocol)** is a protocol used to **store and retrieve user information from a central directory**.
	- It stores: Username, Password (hashed), UID, GID, Home directory, Login shell, Email, Group
	- **How LDAP Authentication Works:-**
		- 1.  User enters username and password.
		- 2. Linux sends the authentication request to the LDAP server.
		- 3.  LDAP checks the credentials.
		- 4.  If valid, authentication succeeds.
		- 5.  The user logs in.
	- `AD`: Microsoft Active Directory (commonly called **Active Directory** or **AD**) is Microsoft's directory service used to manage users, computers, groups, and policies in Windows environments.
	- It stores: User accounts, Passwords, Computer accounts, Groups, Printers, Organizational Units (OUs), Security policies, Domain information

### Local User Management
1. username:-
	- max 32 character
2. uid:
	- 0 -> root
	- 1-99 -> OS users, lib, bin, ...
	- 100-999 --> id are reserved for third party applications (tomcat, apache, nfs, jboss, postgres, mysql, jetty, weblogic, ...)
	- 1000-60000 --> local users or general users
3. GID (Group ID
	-  Primary Group GID: Stored in `/etc/passwd`
4. Home Directory
	- 	Default location:  `/home/`
5. Login Shell
	- Default:  `/bin/bash`
	- Other valid shells: Listed in `/etc/shells` (e.g., `/bin/sh`, `/bin/tcsh`, `/bin/zsh`).

```bash
# Adding User SIMPLE
useradd chandan # this will crate user abd primary group of same name
passwd chandan # then set password
sudo /etc/shadow # to see password encrypted
su - chandan	# login as chandan

groupadd linuxgroup # creating new group
gpasswd linuxgroup

useradd -G linuxgroup sunny 	# for new user
usermod -G linuxgroup chandan # for existing user
getent group linuxgroup # to list all user inside group
```

```bash
# Creating User with differnt UID
sudo groupadd devopsGrp #first create group
getent group devopsGrp # verify 
# Adding User Advance
sudo useradd -u 1003 -g devopsGrp -c "Sunny Kumar - DevOps" -s /bin/bash -m -d /home/sunny sunny
# `u 1003` : Set specific UID
# -g devopsGrp : primary Group
# -G : Supplementary Groups (One or More)
# -c : comment for the user
# -s /bin/bash : Set the default login shell.
# -m : create home directory
# sunny : username

sudo passwd sunny # set password
id sunny # verify
passwd -n 10 -x 180 -w 7 sunny
# -n 10 : wait 10 days before changing pass
# -x 180 : expire
# -w 7 : warning period for pass expire
chage -l sunny # to check

usermod -aG <GROUPNAME> sunny # adding to different group

cat /etc/passwd OR getent passwd# list of users
cut -d: -f1 /etc/passwd # only user
cat /etc/group # list the group
getent group devopsGrp # List Members of a Group
last # to check login history
```

```bash
# Deleting User
userdel -r chandan # it will delete user including home directory
```

```bash
usermod -L chandan # lock the user
passwd -S chandan # to verify 'L' means account is locked
usermod -U chandan # unlock user
```


# 4 Linux Permission Management

Linux permissions control **who can read, write, or execute** a file or directory.

## 1. Permission Categories

Linux permissions are mainly divided into three categories:

| Category | Symbol | Meaning |
|---|---|---|
| User | `u` | File owner |
| Group | `g` | Group associated with the file |
| Others | `o` | Everyone else |

There is also:

| Symbol | Meaning |
|---|---|
| `a` | All → user + group + others |

## 2. Basic Permissions

Linux has three basic permissions:

| Permission | Symbol | Meaning | Numeric |
|---|---|---|---:|
| Read | `r` | Read file contents | 4 |
| Write | `w` | Modify file contents | 2 |
| Execute | `x` | Execute a file / access directory | 1 |

### Permission Combinations

```text
rwx = 4 + 2 + 1 = 7
rw- = 4 + 2     = 6
r-x = 4 + 1     = 5
r-- = 4         = 4
-wx = 2 + 1     = 3
-w- = 2         = 2
--x = 1         = 1
--- = 0
```

## 3. Checking Permissions
```bash
ls -l
# Example:
-rwxr-xr-- 1 john developers 1024 Sep 20 10:30 script.sh

- rwx r-x r--
  │   │   │
  │   │   └── Others
  │   └────── Group
  └────────── Owner
```
## 4. `chmod` – Change Permissions
`chmod` is used to change file or directory permissions.
```bash
# Add execute permission to owner
chmod u+x script.sh
# Add write permission to group
chmod g+w file.txt
### Remove write permission from group
chmod g-w file.txt
### Add read permission to everyone
chmod a+r file.txt
### Remove execute permission from others
chmod o-x script.sh
### Give owner read/write
chmod u+rw file.txt
```

## 5. chmod Operators

`+` : Add permission
`-` : Remove permission
`=` : Set exact permission

```bash
chmod u+x script.sh
chmod g-w file.txt
chmod o=r file.txt
```

## 6. Numeric Permissions
```bash
chmod  755 script.sh
# Owner  → 7 → rwx
# Group  → 5 → r-x
# Others → 5 → r-x
# So, 755 = rwxr-xr-x

chmod 600 secret.txt  	# Owner → rw-
chmod 700 script.sh 	# Owner → rwx
chmod 777 script.text 	# Owner  → rwx , Group  → rwx , Others → rwx
```

## 7. File Ownership
Linux files have:

1.  Owner/User
2.  Group
```bash
ls -l 
# -rw-r--r-- 1 root developers 1024 Sep 20 10:30 file.txt
# Owner = root
# Group = developers
```
## 8. `chown`– Change File Owner
```bash
sudo chown root file.txt 			# Change owner
sudo chown nagios:nagios file.txt # Change owner and group
sudo chown :developers file.txt 	# Change only group using chown
```
## 9. Recursive chown - `-R`
```bash
sudo chown -R john:developers project/
```

## 10. `chgrp` - Change Group Ownership
`chgrp` changes the group ownership of a file or directory.
```bash
sudo chgrp developers project.txt
sudo chgrp -R developers project/
```
|Command|Purpose  |
|--|--|
| `chown` | Change owner |
| `chgrp` | Change group |
| `chmod` | Change permissions |

## 11. umask
`umask` defines which permissions are removed from the default permissions when new files and directories are created.
## 12. Viewing Detailed File Information
```bash
stat file.txt
```
## 13. sudo
`sudo` allows an authorized user to execute commands with elevated privileges, commonly as root.
```bash
sudo -l # Check whether sudo is available:
```
**`/etc/sudoers`**
Linux uses the sudoers configuration to determine:
-   Who can use sudo
-   Which commands they can run
-   Which users they can run commands as
-   Whether a password is required

Main configuration file:
```bash
/etc/sudoers

sudo visudo # editing sudoers
john ALL=(ALL) ALL # Jhon can use sudo on all hosts
```

## 14. Sticky Bit
Sticky Bit is commonly used on shared directories so users cannot delete or rename files owned by other users.

## QUESTION: Permission Troubleshooting 
### 1. When a user cannot access a file, check:
```bash
ls -l file.txt 				# Check permission, owner and group
id username  				# check user's groups
getfacl file.text 			# check ACL
namei -l /path/to/file.txt 	# Check parent directory permissions
sudo -l 					# Check sudo privileges
```
### 2. Common Permission Errors : Permission denied
```bash
-bash: ./script.sh: Permission denied

ls -l script.sh
chmod u+x script.sh  # If execute permission is missing:
./script.sh
```


