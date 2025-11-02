Day 23: LVM basics (pvcreate, vgcreate, lvcreate)
---
Namaste, aur swagat hai aap sabhi ka is comprehensive RHCSA + RHCE training lesson mein! Aaj hum ek bahut hi important topic cover karenge: **LVM basics – Physical Volumes (PVs), Volume Groups (VGs), aur Logical Volumes (LVs) banana.**

Yeh topic sirf exam ke liye hi nahi, balki real-world server administration mein bhi bahut zaroori hai. LVM aapko storage ko bahut flexible tareeke se manage karne ki power deta hai.

---

## 1. Concept Explanation

Chaliye pehle samajhte hain ki LVM आखिर है क्या और yeh traditional partitioning se better kyun hai.

### What is LVM (Logical Volume Manager)?

LVM ek powerful storage management system hai jo aapko physical storage devices (jaise hard drives ya unke partitions) ko zyada flexible tareeke se manage karne ki suvidha deta hai. Imagine karo ki aapke paas ghar banane ke liye alag-alag size ki bricks hain. Traditional partitioning mein, aap har brick se directly ek kamra bana dete the. Agar baad mein kamra chhota pad gaya, toh usko enlarge karna ya uski size change karna mushkil ho jata tha.

LVM mein, hum bricks ko pehle ek bade "material store" mein ikattha karte hain, aur phir us "material store" se apni zaroorat ke hisaab se chote-bade kamre (partitions) banate hain. Agar baad mein kamre ki size badhani ho, toh hum "material store" se aur material le sakte hain, ya dusre kamre se material adjust kar sakte hain, bina sab kuch tode.

### Why LVM is Important? (LVM kyun zaroori hai?)

1.  **Flexibility**:
    *   **Resize LVs on the fly**: Aap Logical Volumes (LVs) ki size ko runtime mein bada (extend) ya chhota (shrink) kar sakte hain, data loss ki tension ke bina (mostly for extend, shrink needs more care). Traditional partitions mein yeh bahut mushkil ya impossible hota hai.
    *   **Span multiple disks**: Aap multiple physical disks ko ek single storage pool mein combine kar sakte hain, aur uss pool se LVs create kar sakte hain. Aapka data ab ek single disk tak seemit nahi rehta.

2.  **Abstraction**:
    *   Applications aur users LVs ko hi as normal partitions dekhte hain. Unhe is baat se koi matlab nahi hota ki yeh LV kis physical disk par hai ya kitne disks se milkar bana hai. Physical hardware detail se aapka logical storage alag ho jata hai.

3.  **Snapshots**:
    *   Aap running LVs ke point-in-time snapshots bana sakte hain. Yeh backup aur data recovery ke liye bahut useful hai.

### LVM Architecture & Components (LVM की वास्तुकला और घटक)

LVM mein teen main building blocks hote hain:

1.  **Physical Volume (PV)**:
    *   Yeh aapki actual hard drive ya uske partitions hote hain jinhe LVM use kar sakta hai.
    *   LVM ko use karne ke liye, pehle aapko apne physical disks ya partitions ko `pvcreate` command se LVM-compatible "Physical Volume" banana padta hai.
    *   **Analogy**: Yeh aapki raw building bricks hain.

2.  **Volume Group (VG)**:
    *   Ek ya zyada PVs ko combine karke hum ek bada storage pool banate hain jise "Volume Group" kehte hain.
    *   `vgcreate` command se aap multiple PVs ko ek VG mein jod sakte hain.
    *   **Analogy**: Yeh aapka "material store" hai jismein aapki saari bricks ikatthi hain.

3.  **Logical Volume (LV)**:
    *   VG mein available space se hum apni zaroorat ke hisaab se "Logical Partitions" ya LVs banate hain.
    *   Yeh LVs bilkul normal partitions ki tarah work karte hain. Aap inhein format kar sakte hain (`mkfs`), mount kar sakte hain (`mount`), aur data store kar sakte hain.
    *   `lvcreate` command se hum VG se LVs carve out karte hain.
    *   **Analogy**: Yeh aapke actual rooms hain jo aapne "material store" se material lekar banaye hain.

**Summary Flow:**
`Physical Disk/Partition` --(`pvcreate`)--> `Physical Volume (PV)` --(`vgcreate`)--> `Volume Group (VG)` --(`lvcreate`)--> `Logical Volume (LV)` --(`mkfs`)--> `Filesystem` --(`mount`)--> `Mount Point`

---

## 2. Key Linux Commands

Yahan LVM basics ke liye zaroori commands ki list hai:

### a. Disk Identification & Partitioning

*   `lsblk`: Connected storage devices aur unke partitions ko list karta hai.
    *   **Explanation**: Yeh command aapko ek tree-like structure mein devices (`/dev/sda`, `/dev/sdb`), unke partitions (`/dev/sda1`), aur LVM components (PVs, VGs, LVs) ki information deta hai. Bohut useful hai disk layout samajhne ke liye.
*   `fdisk -l`: Saare disk partitions ki detailed information dikhata hai.
    *   **Explanation**: Yeh command raw partition table details dikhata hai. Naye disks ko identify karne aur un par partitions banane se pehle use karte hain.
*   `fdisk /dev/sdX`: Ek specific disk par partitions create/modify karne ke liye interactive tool.
    *   **Explanation**: `n` se naya partition, `t` se partition type change karna (LVM ke liye `8e` type code hota hai), `w` se changes save karna.
*   `partprobe`: Kernel ko naye ya modified partition tables ke baare mein inform karta hai.
    *   **Explanation**: `fdisk` se changes karne ke baad, yeh command chalao takki kernel immediate effect mein laaye. Agar yeh na chale toh system reboot karna pad sakta hai.

### b. Physical Volume (PV) Commands

*   `pvcreate /dev/sdX[N]`: Specified disk ya partition ko LVM Physical Volume banata hai.
    *   **Explanation**: Yeh command LVM metadata uss disk ya partition par likhta hai, jisse woh LVM ke liye available ho jata hai. `[N]` partition number hai, agar aap partition use kar rahe hain.
    *   **Example**: `pvcreate /dev/sdb1` (partition) ya `pvcreate /dev/sdb` (whole disk)
*   `pvdisplay`: Saare Physical Volumes ki detailed information dikhata hai.
    *   **Explanation**: PV size, VG jismein woh hai, kitni free space hai, etc.
*   `pvs`: Physical Volumes ki ek concise (short) summary dikhata hai.
    *   **Explanation**: Quick overview ke liye useful.

### c. Volume Group (VG) Commands

*   `vgcreate [VG_Name] [PV1] [PV2 ...]`: Naya Volume Group banata hai, ek ya zyada Physical Volumes ko combine karke.
    *   **Explanation**: Yeh command specified PVs ko ek saath jodkar ek logical storage pool (`VG_Name`) create karta hai.
    *   **Example**: `vgcreate my_data_vg /dev/sdb1 /dev/sdc1`
*   `vgdisplay`: Saare Volume Groups ki detailed information dikhata hai.
    *   **Explanation**: VG size, kitne PVs hain, kitni free space hai LVs banane ke liye.
*   `vgs`: Volume Groups ki ek concise summary dikhata hai.
    *   **Explanation**: Quick overview ke liye useful.

### d. Logical Volume (LV) Commands

*   `lvcreate -L [Size] -n [LV_Name] [VG_Name]`: Specified size ka naya Logical Volume banata hai.
    *   **Explanation**: `[Size]` MB, GB, TB mein ho sakti hai (e.g., `2G`, `512M`). `[LV_Name]` aapke Logical Volume ka naam hoga. `[VG_Name]` uss Volume Group ka naam hai jismein se LV banaya jayega.
    *   **Example**: `lvcreate -L 10G -n web_lv web_vg`
*   `lvcreate -l [Percentage] -n [LV_Name] [VG_Name]`: VG ki free space ke percentage ke hisaab se LV banata hai.
    *   **Explanation**: `[Percentage]` typically `50%FREE` ya `100%FREE` ho sakta hai.
    *   **Example**: `lvcreate -l 100%FREE -n logs_lv app_vg`
*   `lvdisplay`: Saare Logical Volumes ki detailed information dikhata hai.
    *   **Explanation**: LV path (`/dev/mapper/VG_Name-LV_Name`), size, VG jismein woh hai, type.
*   `lvs`: Logical Volumes ki ek concise summary dikhata hai.
    *   **Explanation**: Quick overview ke liye useful.

### e. Filesystem & Mount Commands

*   `mkfs.xfs /dev/mapper/[VG_Name]-[LV_Name]`: LV par XFS filesystem banata hai.
*   `mkfs.ext4 /dev/mapper/[VG_Name]-[LV_Name]`: LV par EXT4 filesystem banata hai.
    *   **Explanation**: LV create hone ke baad, usko use karne ke liye filesystem banana padta hai. RHCSA mein XFS preferred hai.
*   `mkdir /path/to/mountpoint`: Mount karne ke liye directory banata hai.
*   `mount /dev/mapper/[VG_Name]-[LV_Name] /path/to/mountpoint`: LV ko mount point par mount karta hai.
*   `df -h`: Mounted filesystems ki summary dikhata hai, useful for verification.
*   `/etc/fstab`: Filesystem table, system boot hone par automatically filesystems mount karne ke liye entries contain karta hai.
    *   **Explanation**: Permanent mount ke liye is file mein entry add karna zaroori hai. UUID use karna best practice hai.
*   `blkid /dev/mapper/[VG_Name]-[LV_Name]`: LV ka UUID (Universally Unique Identifier) dikhata hai.
    *   **Explanation**: `fstab` mein device paths ke bajaye UUIDs use karna zyada robust hota hai.
*   `mount -a`: `/etc/fstab` mein listed saare filesystems ko mount karne ki koshish karta hai, useful for testing `fstab` entries.

---

## 3. Step-by-Step Examples

Chaliye ek real-world scenario dekhte hain. Maan lijiye aapke paas ek server hai aur aapne do naye virtual disks add kiye hain: `/dev/sdb` (2GB) aur `/dev/sdc` (2GB). Humein in disks par LVM set up karna hai.

**Objective**:
1.  `/dev/sdb` aur `/dev/sdc` ko LVM ke liye taiyar karein.
2.  Ek Volume Group `data_vg` banayein jo in dono disks ko use kare.
3.  `data_vg` mein se do Logical Volumes banayein:
    *   `web_data_lv` (1.5GB) XFS filesystem ke saath, `/var/www/html` par mount hoga.
    *   `db_logs_lv` (1GB) EXT4 filesystem ke saath, `/var/log/mysql` par mount hoga.
4.  Dono LVs ko permanently mount karein.

**Prerequisites**:
*   Root access.
*   Do naye disks (`/dev/sdb`, `/dev/sdc`) attached hain. (Agar VM use kar rahe hain toh settings mein jaake disks add kar sakte hain).

---

### Step 1: Identify New Disks and Create LVM Partitions

Pehle check karte hain ki naye disks system mein detect hue hain ya nahi.

```bash
# Check available disks
lsblk

# Output might look something like this (your actual output may vary)
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda      8:0    0   20G  0 disk
├─sda1   8:1    0    1G  0 part /boot
└─sda2   8:2    0   19G  0 part
  ├─rhel-root 253:0    0 17.5G  0 lvm  /
  └─rhel-swap 253:1    0  1.5G  0 lvm  [SWAP]
sdb      8:16   0    2G  0 disk      <-- New disk 1
sdc      8:32   0    2G  0 disk      <-- New disk 2
```
Hum dekh sakte hain `/dev/sdb` aur `/dev/sdc` detect ho gaye hain. Ab hum in par LVM partitions banayenge. Ek hi disk par multiple partitions banane ke bajaye, yahan hum har disk ka ek hi partition banayenge aur use LVM type set karenge.

```bash
# Create partition on /dev/sdb
sudo fdisk /dev/sdb

# Follow the fdisk prompts:
# n (for new partition)
# p (for primary)
# 1 (for partition number 1)
# Press Enter for default first sector
# Press Enter for default last sector (to use entire disk)
# t (for change partition type)
# 8e (for Linux LVM)
# w (to write changes and exit)

# Repeat for /dev/sdc
sudo fdisk /dev/sdc

# Follow the fdisk prompts:
# n
# p
# 1
# Enter
# Enter
# t
# 8e
# w

# Inform the kernel about the new partitions
sudo partprobe

# Verify partitions are created and recognized
lsblk
# Output should now show sdb1 and sdc1
# sdb      8:16   0    2G  0 disk
# └─sdb1   8:17   0    2G  0 part  <-- LVM partition
# sdc      8:32   0    2G  0 disk
# └─sdc1   8:33   0    2G  0 part  <-- LVM partition
```

---

### Step 2: Create Physical Volumes (PVs)

Ab hum naye partitions `/dev/sdb1` aur `/dev/sdc1` ko LVM Physical Volumes banayenge.

```bash
sudo pvcreate /dev/sdb1
sudo pvcreate /dev/sdc1

# Verify the PVs
sudo pvs
# Output:
#   PV         VG Fmt  Attr PSize PFree
#   /dev/sdb1      lvm2 ---  2.00g 2.00g
#   /dev/sdc1      lvm2 ---  2.00g 2.00g

sudo pvdisplay
# Detailed output of PVs
```

---

### Step 3: Create Volume Group (VG)

Ab hum in dono PVs `/dev/sdb1` aur `/dev/sdc1` ko use karke ek Volume Group `data_vg` banayenge.

```bash
sudo vgcreate data_vg /dev/sdb1 /dev/sdc1

# Verify the VG
sudo vgs
# Output:
#   VG      #PV #LV #SN Attr   VSize VFree
#   data_vg   2   0   0 wz--n- 3.99g 3.99g  <-- Total size is sum of PVs
#   rhel      1   2   0 wz--n- 19.00g    0

sudo vgdisplay data_vg
# Detailed output of data_vg
```

---

### Step 4: Create Logical Volumes (LVs)

Ab `data_vg` se hum do Logical Volumes banayenge: `web_data_lv` (1.5GB) aur `db_logs_lv` (1GB).

```bash
# Create web_data_lv of 1.5GB
sudo lvcreate -L 1.5G -n web_data_lv data_vg

# Create db_logs_lv of 1GB
sudo lvcreate -L 1G -n db_logs_lv data_vg

# Verify the LVs
sudo lvs
# Output:
#   LV          VG      Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
#   db_logs_lv  data_vg -wi-a-----   1.00g
#   web_data_lv data_vg -wi-a-----   1.50g
#   root        rhel    -wi-ao----  17.50g
#   swap        rhel    -wi-ao----   1.50g

sudo lvdisplay data_vg
# Detailed output of LVs within data_vg
```
**Note**: Aapko `Free PE / Size` dikhega `vgdisplay` mein jo remaining free space hai VG mein.

---

### Step 5: Format Logical Volumes with Filesystems

Ab LVs ko use karne ke liye un par filesystems banayenge. `web_data_lv` ke liye XFS aur `db_logs_lv` ke liye EXT4.

Logical Volumes ke device paths `/dev/mapper/VG_Name-LV_Name` format mein hote hain.

```bash
# Format web_data_lv with XFS
sudo mkfs.xfs /dev/mapper/data_vg-web_data_lv

# Format db_logs_lv with EXT4
sudo mkfs.ext4 /dev/mapper/data_vg-db_logs_lv

# Optional: Verify filesystem creation
sudo blkid /dev/mapper/data_vg-web_data_lv
sudo blkid /dev/mapper/data_vg-db_logs_lv
```

---

### Step 6: Create Mount Points and Mount Logical Volumes

Mount karne ke liye directories banayenge aur phir LVs ko un par mount karenge.

```bash
# Create mount points
sudo mkdir -p /var/www/html
sudo mkdir -p /var/log/mysql

# Mount the LVs
sudo mount /dev/mapper/data_vg-web_data_lv /var/www/html
sudo mount /dev/mapper/data_vg-db_logs_lv /var/log/mysql

# Verify mounts
df -h /var/www/html /var/log/mysql
# Output should show the mounted LVs with their sizes
# Filesystem                         Size  Used Avail Use% Mounted on
# /dev/mapper/data_vg-web_data_lv    1.5G  6.0M  1.5G   1% /var/www/html
# /dev/mapper/data_vg-db_logs_lv     976M  2.6M  909M   1% /var/log/mysql
```

---

### Step 7: Make Mounts Permanent (fstab)

Reboot ke baad bhi LVs automatically mount ho jayein, iske liye `/etc/fstab` mein entries add karenge. **Hamesha UUIDs use karna best practice hai!**

Pehle LVs ke UUIDs pata karte hain:

```bash
sudo blkid /dev/mapper/data_vg-web_data_lv
# Output: /dev/mapper/data_vg-web_data_lv: UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" TYPE="xfs"

sudo blkid /dev/mapper/data_vg-db_logs_lv
# Output: /dev/mapper/data_vg-db_logs_lv: UUID="yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy" TYPE="ext4"
```

Ab `/etc/fstab` file ko edit karenge (use `vi` or `nano`):

```bash
sudo vi /etc/fstab
```

File ke end mein yeh lines add karein (UUIDs ko apne actual UUIDs se replace karein):

```
# LVM for web_data
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /var/www/html xfs defaults 0 0

# LVM for db_logs
UUID=yyyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyyy /var/log/mysql ext4 defaults 0 0
```
**Explanation of `fstab` fields**:
*   **UUID=...**: Device to mount (UUID is preferred).
*   **/var/www/html**: Mount point.
*   **xfs/ext4**: Filesystem type.
*   **defaults**: Mount options (rw, suid, dev, exec, auto, nouser, async).
*   **0**: Dump utility backup (0 means no backup).
*   **0**: fsck check order (0 means no check, for non-root filesystems this is common).

**Test `fstab` entries**:
```bash
# Unmount the LVs first (if already mounted)
sudo umount /var/www/html
sudo umount /var/log/mysql

# Mount all entries from fstab
sudo mount -a

# Verify again
df -h /var/www/html /var/log/mysql
```
Agar `mount -a` successfully run ho jata hai aur `df -h` mein LVs mounted dikhte hain, toh aapki `fstab` entries sahi hain.

---

## 4. Practice Tasks

Ab aapko kuch practical exercises karne hain taaki aap LVM concepts par apni pakad mazboot kar sakein. Apne virtual machine mein naye virtual disks add karke yeh tasks perform karein.

### Task 1: Basic LVM Setup

1.  **Add a new 1.5GB disk**: Apne VM mein ek naya 1.5GB virtual disk (`/dev/sdd`) add karein.
2.  **Create PV**: `sdd` disk ka use karke ek Physical Volume create karein (whole disk, no partition needed for this task).
3.  **Create VG**: Ek Volume Group `app_storage_vg` banayein jo iss PV ko use kare.
4.  **Create LV**: `app_storage_vg` se ek Logical Volume `app_data_lv` banayein of 1GB.
5.  **Format LV**: `app_data_lv` ko `ext4` filesystem se format karein.
6.  **Mount LV**: Ek mount point `/app_data` create karein aur `app_data_lv` ko wahan permanently mount karein.
7.  **Verify**: `df -h` aur `lvs` commands se verify karein.

### Task 2: Multi-Disk VG & Multiple LVs

1.  **Add two new 1GB disks**: Apne VM mein do naye virtual disks (`/dev/sde` aur `/dev/sdf`) each 1GB size ke add karein.
2.  **Create LVM Partitions**: `fdisk` ka use karke, `/dev/sde` par `/dev/sde1` (LVM type) aur `/dev/sdf` par `/dev/sdf1` (LVM type) partitions create karein.
3.  **Create PVs**: Dono partitions (`/dev/sde1`, `/dev/sdf1`) se Physical Volumes create karein.
4.  **Create VG**: Ek Volume Group `web_vg` banayein jo in dono PVs ko use kare.
5.  **Create LVs**:
    *   `web_vg` se ek LV `static_content_lv` banayein of 1.2GB.
    *   `web_vg` se ek LV `dynamic_logs_lv` banayein jo `web_vg` ki **remaining free space ka 80%** use kare.
6.  **Format LVs**:
    *   `static_content_lv` ko `xfs` se format karein.
    *   `dynamic_logs_lv` ko `ext4` se format karein.
7.  **Mount LVs**: Mount points `/var/www/static` aur `/var/log/dynamic_web` create karein, aur LVs ko wahan permanently mount karein.
8.  **Verify**: `lsblk`, `pvs`, `vgs`, `lvs`, `df -h` se saare steps ko verify karein.

### Task 3: (Challenge Task) LVM Cleanup

1.  Task 1 mein banaye gaye `app_storage_vg` aur uske `app_data_lv` ko system se remove karein.
    *   **Hint**: Pehle mount point ko umount karein, phir `/etc/fstab` se entry remove karein. LV ko remove karein, phir VG ko remove karein, aur finally PV ko remove karein. Steps reverse order mein hote hain.
2.  Task 2 mein banaye gaye `web_vg` aur uske LVs ko system se remove karein.

---

## 5. Pro Tips / Common Mistakes

### Pro Tips:

*   **Always Verify**: Har step ke baad (PV, VG, LV creation, mount), `pvs`, `vgs`, `lvs`, `df -h` commands se verify karein.
*   **Descriptive Naming**: LVM components (VG aur LV) ke liye meaningful aur descriptive names use karein (e.g., `db_vg`, `web_logs_lv`). Isse management easy ho jata hai.
*   **UUIDs in `/etc/fstab`**: Hamesha UUIDs use karein `/etc/fstab` mein instead of device paths (jaise `/dev/mapper/data_vg-web_data_lv`). Device paths boot order ya device discovery ke basis par change ho sakte hain, jisse boot issues ho sakte hain. UUIDs unique aur stable hote hain.
*   **`partprobe` after Partitioning**: `fdisk` ya `gdisk` se partitions banane ke baad `partprobe` command chalana na bhoolein, warna kernel naye partitions ko recognize nahi karega.
*   **LVM Metadata Backup**: LVM configuration ka backup lena useful ho sakta hai in case of a disaster. `vgcfgbackup` command se aap VG metadata ka backup le sakte hain. `vgcfgrestore` se restore kar sakte hain.
*   **Understanding PE/LE**: `pvdisplay` aur `vgdisplay` output mein Physical Extents (PE) aur Logical Extents (LE) terms hote hain. Yeh LVM ki smallest allocation units hoti hain. Default PE size 4MB hoti hai, lekin aap VG create karte waqt isko specify kar sakte hain.

### Common Mistakes:

1.  **Wrong Disk Selection**: `pvcreate` command galat disk par chala dena. Always double-check `lsblk` ya `fdisk -l` output before running any command that modifies disks. **Isse data loss ho sakta hai!**
2.  **Forgetting LVM Partition Type**: `fdisk` mein partition banane ke baad, `t` option se partition type `8e` (Linux LVM) set karna bhool jaana. LVM utility ko woh partition recognise nahi karega.
3.  **Not Formatting LV**: Logical Volume create karne ke baad us par `mkfs` command se filesystem banana bhool jaana. Bina filesystem ke aap LV par data store nahi kar sakte.
4.  **Not Making Mounts Persistent**: `/etc/fstab` mein entry add karna bhool jaana. System reboot hone par aapka LV automatically mount nahi hoga.
5.  **Typos in `/etc/fstab`**: `fstab` mein galat UUID, wrong mount point, ya incorrect filesystem type likh dena. Isse system boot hone mein problem aa sakti hai ya boot stuck ho sakta hai. Hamesha `mount -a` se test karein.
6.  **Confusing LVM with RAID**: LVM aapko flexibility deta hai, lekin data redundancy nahi. Agar aapko data redundancy chahiye toh LVM ko RAID ke saath combine karna hoga. LVM data ko distribute karta hai, copy nahi karta.
7.  **Not Creating Mount Point**: `mount` command chalane se pehle `mkdir` se mount point directory banana bhool jaana.

---

Umeed hai yeh lesson aapko LVM basics samajhne mein aur use karne mein helpful laga hoga. Practice karte rahiye, aur koi bhi doubt ho toh poochte rahiye! All the best!
