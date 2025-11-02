Day 22: Partitions, mounting, /etc/fstab
---
Namaste, future Linux Gurus!

Welcome to this comprehensive RHCSA + RHCE training session on one of the most fundamental and crucial topics in Linux system administration: **"Partitions, Mounting, aur /etc/fstab"**.

Yeh topic bohot important hai kyuki iske bina aap storage ko effectively manage nahi kar sakte, aur data access bhi impossible hai. Chahe aap RHCSA ke liye prepare kar rahe ho ya RHCE ke advanced concepts seekh rahe ho, yeh topic aapko master karna hi hoga!

---

## **Partitions, Mounting, aur /etc/fstab: Ek Gehraai se Samjho!**

### **1. Concept Explanation (Hinglish)**

Chalo, shuru karte hain is topic ko bilkul basic se:

#### **Partitioning Kya Hai? (What is Partitioning?)**
Socho aapke paas ek naya hard drive hai, 1TB ka. Ab aap chahte ho ki aap isko different purposes ke liye use karo. Jaise, ek hissa Operating System ke liye, ek hissa users ke data ke liye, aur ek hissa backups ke liye. Is hard drive ke bade se space ko chhote-chhote logical sections mein divide karna hi **Partitioning** kehlata hai.
*   **Kyun Zaruri Hai? (Why is it necessary?)**:
    *   **Organization**: Data ko alag-alag rakhna easier ho jaata hai.
    *   **Security/Stability**: Agar ek partition corrupt ho jaye, toh doosre partitions ka data safe rehta hai.
    *   **Multi-OS**: Agar aap ek hi drive par do ya usse zyada operating systems (jaise Linux aur Windows) install karna chahte ho.
    *   **Filesystem Flexibility**: Har partition par aap alag type ka filesystem bana sakte ho (jaise XFS, ext4).

*   **Types of Partition Tables (MBR vs GPT)**:
    *   **MBR (Master Boot Record)**: Yeh old style hai. Ismein aap max 4 **Primary Partitions** bana sakte ho. Agar aapko 4 se zyada chahiye, toh aapko ek Primary Partition ko **Extended Partition** banana padega, aur uske andar aap **Logical Partitions** bana sakte ho. MBR ki limit 2TB tak hoti hai.
    *   **GPT (GUID Partition Table)**: Yeh modern standard hai. Ismein aap 128 Primary Partitions tak bana sakte ho (by default, can be more). Iski koi 2TB ki limit nahi hai, ye terabytes aur petabytes tak support karta hai. Modern systems aur bade disks ke liye GPT preferred hai.

#### **Filesystem Kya Hai? (What is a Filesystem?)**
Ek baar aapne disk ko partitions mein divide kar diya, toh agla step hai un partitions ko "format" karna. Is formatting process ko **Filesystem creation** kehte hain. Filesystem ek structure provide karta hai jahan data store hota hai, jaise files ke names, sizes, permissions, aur physical location track ki jaati hai.
*   **Common Linux Filesystems**:
    *   **XFS**: Red Hat based systems mein default aur preferred filesystem hai. High performance, large files/partitions ko handle kar sakta hai.
    *   **ext4**: Older but still very popular, good for general-purpose use.
    *   **SWAP**: Yeh koi regular filesystem nahi hai. Yeh disk ka woh area hai jo RAM ki tarah use hota hai jab physical RAM kam pad jaye. Jab RAM full ho jaati hai, toh least used data ko swap space mein move kar diya jaata hai.

#### **Mounting Kya Hai? (What is Mounting?)**
Imagine aapke ghar mein alag-alag rooms hain (partitions). Ab aapko kitchen mein jaana hai, toh aapko kitchen ke darwaze se enter karna padega. Linux mein, partitions ko use karne ke liye, unhe ek directory (darwaza) se "attach" karna padta hai. Is process ko **Mounting** kehte hain. Jis directory se attach karte hain, use **Mount Point** kehte hain.
*   **Example**: Agar aapne ek partition `/dev/sdb1` banaya aur usko `/mnt/data` par mount kiya, toh `/dev/sdb1` ka saara content `/mnt/data` directory mein dikhega.
*   **Temporary vs. Permanent Mounts**:
    *   **Temporary**: `mount` command se kiya gaya mount sirf current boot session ke liye hota hai. System reboot hone par woh mount hatt jaata hai.
    *   **Permanent**: Persistent mounts ke liye, hum `/etc/fstab` file use karte hain.

#### **/etc/fstab Kya Hai?**
`/etc/fstab` (filesystem table) ek configuration file hai jo Linux system ko batata hai ki boot time par kaun se filesystems ko kahan mount karna hai. Yeh file system ke automatic mounting aur unke options ko define karti hai.
*   **Har Entry ka Structure (6 Fields)**:
    1.  **Device**: Partition ka naam (e.g., `/dev/sdb1`), ya zyada stable tarika hai **UUID (Universally Unique Identifier)** use karna. UUID disk ke saath permanently attached hota hai, jabki `/dev/sdb1` jaisa naam boot ke baad change ho sakta hai.
    2.  **Mount Point**: Jis directory par filesystem mount hoga (e.g., `/mnt/data`, `/home`).
    3.  **Filesystem Type**: Partition ka filesystem type (e.g., `xfs`, `ext4`, `swap`).
    4.  **Options**: Mounting ke options (e.g., `defaults`, `noauto`, `rw`, `ro`, `usrquota`).
        *   `defaults`: rw, suid, dev, exec, auto, nouser, async. Yeh common aur safe options ka set hai.
        *   `noauto`: Yeh option system ko boot time par automatically mount karne se rokta hai. Manual mount karna padta hai (`mount /mount/point` ya `mount /dev/device`).
        *   `_netdev`: Network filesystems ke liye, batata hai ki network available hone ke baad hi mount kare.
    5.  **Dump (0 or 1)**: `dump` utility ke liye. `0` matlab backup na kare, `1` matlab backup kare. Modern systems mein usually `0` set hota hai.
    6.  **Pass (0, 1, or 2)**: `fsck` (filesystem check) order.
        *   `0`: fsck na kare.
        *   `1`: Root filesystem (`/`) ke liye. Priority high hoti hai.
        *   `2`: Doosre filesystems ke liye. Root ke baad check hote hain.

---

### **2. Key Linux Commands (Hinglish)**

Yahan kuch important commands hain jo aapko is topic mein bohot kaam aayenge:

*   `lsblk` (list block devices)
    *   **Kya Karta Hai**: Saare block devices (hard drives, partitions, loop devices) ko tree-like format mein show karta hai. Aapko pata chalega kaun sa disk hai, uske partitions kya hain, aur kaun sa partition kahan mount hai.
    *   **Syntax**: `lsblk` ya `lsblk -f` (filesystem info ke saath).
    *   **Example**: `lsblk -f`

*   `fdisk` (partition table manipulator)
    *   **Kya Karta Hai**: MBR-style disks par partitions create, delete, modify karta hai. GPT disks ke liye `gdisk` better hai.
    *   **Syntax**: `fdisk /dev/sdb` (yahan `/dev/sdb` aapke disk ka naam hai).
    *   **Interactive Mode Commands**: `n` (new partition), `d` (delete), `p` (print partition table), `w` (write changes to disk), `q` (quit without saving).
    *   **Note**: Changes `w` karne ke baad hi apply hote hain.

*   `gdisk` (GPT partition table manipulator)
    *   **Kya Karta Hai**: GPT-style disks par partitions create, delete, modify karta hai. `fdisk` ka modern equivalent hai GPT disks ke liye.
    *   **Syntax**: `gdisk /dev/sdb`
    *   **Interactive Mode Commands**: `n` (new partition), `d` (delete), `p` (print partition table), `w` (write changes to disk), `q` (quit without saving).

*   `parted` (disk partition table utility)
    *   **Kya Karta Hai**: `fdisk` aur `gdisk` se zyada powerful hai. MBR aur GPT dono ko support karta hai, aur partitions ko resize bhi kar sakta hai.
    *   **Syntax**: `parted /dev/sdb`
    *   **Interactive Mode Commands**: `print` (display partition table), `mklabel gpt` (create GPT label), `mkpart primary xfs 1MiB 100%` (create partition), `quit`.

*   `mkfs.` (make filesystem)
    *   **Kya Karta Hai**: Partitions par filesystem banata hai. `mkfs` ke baad aapko filesystem type specify karna hota hai.
    *   **Syntax**:
        *   `mkfs.xfs /dev/sdb1`
        *   `mkfs.ext4 /dev/sdb2`
    *   **Note**: Yeh command partition par saara data erase kar deta hai. Careful!

*   `mount` (mount a filesystem)
    *   **Kya Karta Hai**: Filesystem ko ek directory (mount point) par attach karta hai. Temporary mount ke liye use hota hai.
    *   **Syntax**:
        *   `mount /dev/sdb1 /mnt/data` (device ko mount point par mount kare)
        *   `mount -a` (sabhi `/etc/fstab` entries ko mount kare jo `noauto` nahi hain)
        *   `mount` (currently mounted filesystems ko list kare)

*   `umount` (unmount filesystems)
    *   **Kya Karta Hai**: Mounted filesystem ko unmount karta hai.
    *   **Syntax**: `umount /mnt/data` (mount point ko unmount kare) ya `umount /dev/sdb1` (device ko unmount kare).
    *   **Note**: Agar filesystem busy hai (koi process usko use kar raha hai), toh `umount` fail hoga. Uss case mein `fuser -m /mnt/data` ya `lsof | grep /mnt/data` se process find karke kill karna pad sakta hai.

*   `df -hT` (disk free space)
    *   **Kya Karta Hai**: Mounted filesystems par disk space usage ko human-readable format (`-h`) aur filesystem type (`-T`) ke saath show karta hai.
    *   **Syntax**: `df -hT`

*   `blkid` (locate/print block device attributes)
    *   **Kya Karta Hai**: Block devices (partitions) ke UUID, LABEL, TYPE jaise attributes display karta hai. `/etc/fstab` mein UUID use karne ke liye yeh command bohot useful hai.
    *   **Syntax**: `blkid` ya `blkid /dev/sdb1`

*   `xfs_info` / `dumpe2fs`
    *   **Kya Karta Hai**: Specific filesystem ki detailed information provide karta hai. `xfs_info` XFS ke liye aur `dumpe2fs` ext2/3/4 ke liye.
    *   **Syntax**: `xfs_info /dev/sdb1` ya `dumpe2fs /dev/sdb1`

*   `mkswap` (set up a Linux swap area)
    *   **Kya Karta Hai**: Partition ya file ko swap area ke liye initialize karta hai.
    *   **Syntax**: `mkswap /dev/sdb3`

*   `swapon` / `swapoff` (enable/disable swap device/file)
    *   **Kya Karta Hai**: Swap area ko activate ya deactivate karta hai.
    *   **Syntax**: `swapon /dev/sdb3` ya `swapon -a` (sabhi `/etc/fstab` mein defined swap entries ko activate kare).
    *   **Syntax**: `swapoff /dev/sdb3`

*   `free -h` (display amount of free and used memory)
    *   **Kya Karta Hai**: RAM aur SWAP memory usage ko human-readable format mein dikhata hai.
    *   **Syntax**: `free -h`

---

### **3. Step-by-Step Examples**

Let's do some hands-on! Iske liye aapke paas ek virtual machine mein ek additional, unformatted disk (jaise `/dev/sdb`) hona chahiye.

#### **Scenario 1: Ek naya XFS partition banana aur use temporarily mount karna.**

**Goal**: Ek naya partition `/dev/sdb1` banayein, use XFS format karein, aur `/mnt/project_data` par mount karein.

1.  **Identify the new disk**:
    ```bash
    lsblk
    # Output mein dekho kaunsa disk unmounted hai (e.g., sdb)
    ```

2.  **Create a new partition using `gdisk` (assuming GPT disk)**:
    ```bash
    sudo gdisk /dev/sdb
    ```
    *   `o` (Create a new empty GPT partition table) -> `Y`
    *   `n` (Create new partition)
        *   Partition number: `1` (default)
        *   First sector: `2048` (default)
        *   Last sector: `+2G` (for a 2GB partition, adjust as per your disk size)
        *   Hex code: `8300` (Linux filesystem default)
    *   `p` (Print partition table to verify)
    *   `w` (Write changes to disk) -> `Y`
    *   Ab `lsblk` se verify karo, aapko `/dev/sdb1` dikhega.

3.  **Format the new partition with XFS filesystem**:
    ```bash
    sudo mkfs.xfs /dev/sdb1
    ```
    *   **Caution**: Yeh command existing data ko erase kar dega!

4.  **Create a mount point directory**:
    ```bash
    sudo mkdir -p /mnt/project_data
    ```

5.  **Mount the partition temporarily**:
    ```bash
    sudo mount /dev/sdb1 /mnt/project_data
    ```

6.  **Verify the mount**:
    ```bash
    df -hT /mnt/project_data
    # Ya
    lsblk -f
    ```
    *   Aapko `/mnt/project_data` par `/dev/sdb1` mounted dikhega.

7.  **Test writing data**:
    ```bash
    sudo touch /mnt/project_data/test_file.txt
    ls /mnt/project_data/
    ```

8.  **Unmount the partition**:
    ```bash
    sudo umount /mnt/project_data
    # Ya sudo umount /dev/sdb1
    ```
    *   Verify again with `df -hT`. `/mnt/project_data` ab empty hoga.

#### **Scenario 2: Filesystem ko `/etc/fstab` use karke permanently mount karna (UUID ke saath).**

**Goal**: Upar banaye gaye `/dev/sdb1` partition ko `/mnt/project_data` par permanently mount karein.

1.  **Get the UUID of the partition**:
    ```bash
    blkid /dev/sdb1
    # Output kuch aisa hoga: /dev/sdb1: UUID="ab12cd34-ef56-7890-1234-abcdef567890" TYPE="xfs"
    # Is UUID ko copy kar lo.
    ```

2.  **Edit `/etc/fstab` file**:
    ```bash
    sudo vi /etc/fstab
    ```
    *   File ke end mein yeh line add karein (apni UUID aur mount point ke hisaab se replace karein):
        ```
        UUID=ab12cd34-ef56-7890-1234-abcdef567890 /mnt/project_data xfs defaults 0 0
        ```
    *   **Explanation**:
        *   `UUID=...`: Partition ka UUID.
        *   `/mnt/project_data`: Mount point.
        *   `xfs`: Filesystem type.
        *   `defaults`: Common mount options (rw, suid, dev, exec, auto, nouser, async).
        *   `0`: Dump option (backup ke liye, 0 matlab no backup).
        *   `0`: Fsck pass order (0 matlab no check at boot, for non-root filesystems it's usually 0 or 2).

3.  **Test the `/etc/fstab` entry without rebooting**:
    *   Pehle ensure karein ki partition unmounted hai (`sudo umount /mnt/project_data`).
    ```bash
    sudo mount -a
    ```
    *   Yeh command `/etc/fstab` mein listed saare entries ko mount karne ki koshish karega jo already mounted nahi hain aur jin par `noauto` option nahi laga hai.
    *   Agar koi error nahi aaya, toh aapka entry theek hai.

4.  **Verify the permanent mount**:
    ```bash
    df -hT /mnt/project_data
    ```
    *   Ab aap system reboot karke bhi verify kar sakte ho ki partition automatically mount ho gaya hai.

#### **Scenario 3: Ek Swap partition create aur enable karna.**

**Goal**: Ek naya partition `/dev/sdb2` banayein, use swap ke liye configure karein, aur permanently enable karein.

1.  **Create a new partition using `gdisk`**:
    *   Assume aapke paas `/dev/sdb` mein aur space hai.
    ```bash
    sudo gdisk /dev/sdb
    ```
    *   `n` (Create new partition)
        *   Partition number: `2` (default)
        *   First sector: (default, previous partition ke baad)
        *   Last sector: `+1G` (for a 1GB swap partition, adjust as needed)
        *   Hex code: `8200` (Linux swap ke liye)
    *   `p` (Print to verify)
    *   `w` (Write changes) -> `Y`
    *   `lsblk` se verify karo, aapko `/dev/sdb2` dikhega.

2.  **Format the new partition as swap**:
    ```bash
    sudo mkswap /dev/sdb2
    ```

3.  **Enable the swap partition temporarily**:
    ```bash
    sudo swapon /dev/sdb2
    ```

4.  **Verify swap status**:
    ```bash
    free -h
    ```
    *   Output mein aapko Swap section mein `/dev/sdb2` ki entry dikhegi.

5.  **Get UUID for swap partition**:
    ```bash
    blkid /dev/sdb2
    # Copy iska UUID.
    ```

6.  **Add entry to `/etc/fstab` for permanent swap**:
    ```bash
    sudo vi /etc/fstab
    ```
    *   File ke end mein yeh line add karein:
        ```
        UUID=your_swap_uuid_here none swap defaults 0 0
        ```
    *   **Explanation**:
        *   `UUID=...`: Swap partition ka UUID.
        *   `none`: Swap partitions ka koi mount point nahi hota.
        *   `swap`: Filesystem type `swap` hai.
        *   `defaults`: Common options.
        *   `0 0`: Dump aur fsck ke liye.

7.  **Test the `/etc/fstab` entry**:
    *   Pehle current swap ko disable karein: `sudo swapoff /dev/sdb2`
    *   Phir sabhi swap entries ko `fstab` se activate karein:
    ```bash
    sudo swapon -a
    ```

8.  **Verify again**:
    ```bash
    free -h
    ```
    *   Reboot ke baad bhi swap automatically enable ho jayega.

---

### **4. Practice Tasks (Aapke liye Lab Tasks)**

Yeh tasks aap apni virtual machine mein try kar sakte ho. Har step ko carefully perform karna aur har command ke output ko observe karna.

1.  **Task 1: Ek naya `ext4` filesystem banayein.**
    *   Apne system mein ek naya virtual disk add karein (ya existing disk ka unallocated space use karein).
    *   Use `gdisk` ya `fdisk` use karke ek 500MB ka naya partition (`/dev/sdb3` ya koi bhi available) banayein.
    *   Partition ko `ext4` filesystem se format karein.
    *   Ek mount point `/data/finance` banayein.
    *   Is partition ko temporarily `/data/finance` par mount karein aur `df -hT` se verify karein.
    *   Ek chhoti file `/data/finance/report.txt` create karein.
    *   Partition ko unmount karein.

2.  **Task 2: Upar wale partition ko permanently mount karein.**
    *   `blkid` use karke `/dev/sdb3` ka UUID find karein.
    *   `/etc/fstab` mein entry add karein taki yeh partition boot time par `/data/finance` par automatically mount ho jaye.
    *   `mount -a` se fstab entry ko test karein.
    *   System reboot karein aur `df -hT` se verify karein ki `/data/finance` mounted hai aur `report.txt` file visible hai.

3.  **Task 3: Ek aur XFS partition create aur mount karein (with specific options).**
    *   Ek naya 1GB ka partition (`/dev/sdb4`) create karein.
    *   Use `xfs` filesystem se format karein.
    *   Ek mount point `/opt/apps` banayein.
    *   Is partition ko `/etc/fstab` mein add karein taki yeh `/opt/apps` par mount ho, but with `noauto` option.
    *   Verify karein ki `mount -a` se yeh partition mount nahi hota.
    *   Manually mount karein: `sudo mount /opt/apps`.
    *   `df -hT` se verify karein.
    *   Unmount karein.

4.  **Task 4: Ek swap file banayein.**
    *   `dd` command use karke root filesystem par `/swapfile` naam ki ek 512MB ki file banayein.
    *   Correct permissions set karein (`chmod 600 /swapfile`).
    *   `mkswap` command use karke is file ko swap area mein convert karein.
    *   `swapon` command use karke ise temporarily enable karein.
    *   `free -h` se verify karein.
    *   Is swap file ko permanently enable karne ke liye `/etc/fstab` mein entry add karein (remember `none` for mount point).
    *   `swapoff /swapfile` aur `swapon -a` se test karein.
    *   System reboot karein aur verify karein ki swap file enabled hai.

---

### **5. Pro Tips / Common Mistakes (Tips aur Galtiyan jinse bachein)**

#### **Pro Tips**:

*   **Always use UUIDs in `/etc/fstab`**: Yeh best practice hai. Device names (`/dev/sdb1`) boot ke baad change ho sakte hain, especially agar aapke paas multiple disks hain ya storage devices add/remove hote hain. UUIDs unique aur stable hote hain.
*   **Test `/etc/fstab` entries *before* rebooting**: `sudo mount -a` command chala kar check karein ki aapki entries sahi hain. Agar ismein koi error aata hai, toh system boot hone mein problem aa sakti hai.
*   **Backup `/etc/fstab`**: `sudo cp /etc/fstab /etc/fstab.bak` hamesha changes karne se pehle kar lein. Agar kuch gadbad hoti hai, toh aap backup se restore kar sakte hain.
*   **Understand `defaults`**: `defaults` ek shortcut hai `rw, suid, dev, exec, auto, nouser, async` options ke liye. Apni requirement ke hisaab se specific options use karna seekhein.
*   **Emergency Boot**: Agar `/etc/fstab` mein koi syntax error hai aur system boot nahi ho pa raha hai, toh emergency/rescue mode mein boot karna padega. Wahan root filesystem ko read-only mount karke `/etc/fstab` ko fix karna padega (`mount -o remount,rw /`).
*   **`man` pages hain aapke dost**: Har command (`man lsblk`, `man fdisk`, `man mount`, `man fstab`) ke man pages padho. Yeh detailed information aur options provide karte hain.
*   **Empty Mount Points**: Hamesha ek empty directory par mount karein. Agar mount point directory mein pehle se files hain, toh woh mount hone ke baad hide ho jayengi aur access nahi ho payengi. Unmount karne par wapas visible ho jayengi.

#### **Common Mistakes**:

*   **Typos in `/etc/fstab`**: Ek chhoti si spelling mistake (UUID, mount point, filesystem type, options) poore system ko boot hone se rok sakti hai. Carefully check karein.
*   **Forgetting to create Filesystem**: Partition bana diya, lekin `mkfs` command chalana bhool gaye. Bina filesystem ke partition mount nahi ho sakta.
*   **Not creating Mount Point**: Jis directory par mount karna hai, woh exist nahi karti. `mkdir -p` use karein.
*   **Trying to `umount` a busy filesystem**: Agar koi process filesystem ko use kar raha hai (jaise, aap us directory mein cd karke baithe ho), toh `umount` fail ho jayega. `fuser -m /mount/point` ya `lsof | grep /mount/point` se process find karein aur kill karein ya exit karein.
*   **Incorrect Permissions on Mount Point**: Mount point par read/write permissions ka issue ho sakta hai, jo data access ko affect karega. `chmod` aur `chown` se fix karein.
*   **Forgetting `swapon -a` or `mount -a`**: `/etc/fstab` edit karne ke baad changes immediate effect mein nahi aate. Aapko `mount -a` (for filesystems) ya `swapon -a` (for swap) chalaana padega ya reboot karna padega.
*   **Incorrect `fsck` pass order (`0` vs `1` vs `2`)**: Root filesystem (`/`) ke liye `1` hona chahiye. Doosre data partitions ke liye `0` ya `2` rakh sakte hain. Agar `1` se zyada filesystems ko `1` set kar diya toh boot mein issue aa sakta hai.
*   **Mixing MBR and GPT commands**: Agar disk MBR hai, toh `fdisk` use karein. Agar GPT hai, toh `gdisk` ya `parted` use karein. Galat tool use karne se data loss ho sakta hai.

---

Umeed karta hu ki yeh lesson aapko "Partitions, mounting, aur /etc/fstab" ko gehraai se samajhne mein help karega. Yeh concepts RHCSA aur RHCE exams ke liye building blocks hain, toh inko achhe se practice karein.

All the best for your Linux journey! Happy learning!
