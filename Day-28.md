Day 28: Revision & lab practice (Partitions, mounting, /etc/fstab LVM basics (pvcreate, vgcreate, lvcreate) Swap, filesystem growth & shrink (xfs_growfs) SELinux management (enforcing, permissive, booleans) Boot process, GRUB, kernel options Logs & troubleshooting (journalctl, dmesg)) & mock RHCSA test
---
Namaste students! Welcome to this crucial "Revision & Lab Practice" session for your RHCSA + RHCE journey. Aaj hum kuch bahut important topics ko revise aur practice karenge, jo aapke exams aur real-world Linux administration mein daily use honge. So, apni virtual machines ready kar lo, aur chalo shuru karte hain!

---

## RHCSA + RHCE Revision & Lab Practice: Partitions, LVM, SELinux, Boot & Logs

### 1. Concept Explanation (Hinglish)

**Bhaiyon aur Behnon, dhyan se suno! Ye concepts aapke Linux career ki neev hain.**

*   **Partitions, Mounting, /etc/fstab:**
    *   **Partitions (विभाजन):** Socho aapke paas ek badi zameen (hard disk) hai. Aap use alag-alag hisson mein baantte ho – jaise ek hissa ghar banane ke liye, ek khetibadi ke liye. Linux mein, hard disk ko logical units mein divide karna hi "partitioning" hai. Ye primary, extended, aur logical partitions ho sakte hain. Har partition par hum ek "filesystem" banate hain (jaise ext4 ya XFS), jo data organize karne ka tareeka hai.
    *   **Mounting (जोड़ना):** Ab aapne zameen ke tukde (partitions) bana liye aur un par kheti ke liye mitti taiyaar kar di (filesystem bana diya). Lekin, un zameen ke tukdon ko use karne ke liye aapko unhe apne ghar (file system hierarchy, jaise `/`, `/home`, `/var`) se connect karna padega. Is connection ko "mounting" kehte hain. Jab tak koi partition mount nahi hota, aap uske andar ka data access nahi kar sakte. Example: `mount /dev/sdb1 /mnt/data`.
    *   **/etc/fstab:** Yeh ek special file hai jismein aapki machine boot hote waqt kaun se partitions kahan mount hone chahiye, iski information likhi hoti hai. Agar aapko koi partition permanently mount karna hai, toh aapko uski entry `/etc/fstab` mein karni padegi. Ismein UUID (Universal Unique Identifier) ya device name (jaise `/dev/sdb1`) ka use hota hai, aur saath hi mount options (jaise `defaults`, `noatime`, `nofail`) bhi specify kiye jaate hain.

*   **LVM Basics (Logical Volume Management):**
    *   **Kyon zaroori hai?** Normal partitions mein kya hota hai? Agar aapne ek partition 100GB ka banaya aur woh bhar gaya, toh use expand karna mushkil hota hai. LVM is mushkil ko solve karta hai! LVM aapko storage ko zyada flexible tareeke se manage karne deta hai. Aap partitions (Logical Volumes) ko on-the-fly extend, aur kuch cases mein shrink bhi kar sakte ho.
    *   **Components:**
        *   **Physical Volumes (PVs):** Ye aapke actual hard disk partitions hain, jinhe LVM use karne ke liye taiyaar kiya jata hai. Socho, ye raw blocks hain.
        *   **Volume Groups (VGs):** Ek ya ek se zyada PVs ko combine karke ek bada storage pool banaya jata hai. Ye pool hi VG hai. Aap is VG se space allocate kar sakte ho. Jaise alag-alag khet (PVs) ko jodkar ek bada farm (VG) banana.
        *   **Logical Volumes (LVs):** VG se jo space allocate karke hum apne partitions banate hain, unhe LVs kehte hain. Ye aapke regular partitions ki tarah hi kaam karte hain, bus difference ye hai ki ye flexible hote hain.

*   **Swap, Filesystem Growth & Shrink (xfs_growfs):**
    *   **Swap (स्वैप):** Jab aapki physical RAM bhar jaati hai aur system ko aur memory ki zaroorat padti hai, toh woh kuch inactive data ko hard disk par ek special area (swap space) mein move kar deta hai. Isse system slow ho sakta hai, lekin crash hone se bach jata hai. Ye ek virtual memory ki tarah kaam karta hai. Aap swap partition ya swap file bana sakte ho.
    *   **Filesystem Growth & Shrink:**
        *   **Growth:** LVM ka sabse bada fayda ye hai ki aap Logical Volumes (LVs) ka size badha sakte ho. Jaise `lvextend` se LV ka size badhaya, phir uske upar bane filesystem (jaise XFS ya ext4) ko bhi inform karna padta hai ki space badh gaya hai.
        *   **`xfs_growfs`:** Yeh command ખાસ taur par XFS filesystems ko extend karne ke liye use hota hai. `ext4` ke liye `resize2fs` use hota hai. **Important:** XFS filesystems ko "shrink" karna default tools se possible nahi hai.

*   **SELinux Management (Security Enhanced Linux):**
    *   **Kya hai ye?** SELinux ek "Security Enhanced Linux" hai jo Mandatory Access Control (MAC) provide karta hai. Normal Linux mein Discretionary Access Control (DAC) hota hai, jahan owner decides karta hai. SELinux mein, system policy decide karti hai ki kaun si process kaun si file ko access kar sakti hai, even if permissions `rw-rw-rw-` ho. Ye ek extra layer of security hai.
    *   **Modes:**
        *   `enforcing`: SELinux policies ko enforce karta hai aur violations ko prevent karta hai (default).
        *   `permissive`: Violations ko allow karta hai, lekin unhe log karta hai. Troubleshooting ke liye useful.
        *   `disabled`: SELinux completely band (not recommended).
    *   **Contexts:** Har file, directory aur process ka ek SELinux context hota hai (e.g., `system_u:object_r:httpd_sys_content_t:s0`). Ye context decide karta hai ki kaun kya access kar sakta hai.
    *   **Booleans:** Ye on/off switches hote hain jo SELinux policy ke specific parts ko adjust karte hain, without completely disabling SELinux. Jaise `httpd_can_network_connect` boolean decide karta hai ki Apache network se connect kar sakta hai ya nahi.

*   **Boot Process, GRUB, Kernel Options:**
    *   **Boot Process (बूट प्रक्रिया):** Jab aap computer on karte ho, toh kya hota hai?
        1.  **BIOS/UEFI:** Hardware initialize hota hai.
        2.  **MBR/GPT:** Bootloader (GRUB) ka pehla part load hota hai.
        3.  **GRUB (Grand Unified Bootloader):** Ye boot menu dikhata hai aur aapko OS select karne deta hai. Kernel ko load karta hai.
        4.  **Kernel:** Linux ka dil (core). Hardware aur software ke beech communication manage karta hai.
        5.  **initramfs:** Ek temporary root filesystem jismein zaroori drivers hote hain taaki real root filesystem mount ho sake.
        6.  **systemd:** Pehli process (PID 1) jo baaki sab services ko start karti hai aur system ko ready karti hai.
    *   **GRUB:** Aap GRUB configuration files (usually `/boot/grub2/grub.cfg`) ko edit karke boot options modify kar sakte ho, jaise kernel parameters add karna (e.g., `single` for single-user mode, `rd.break` for root shell access).

*   **Logs & Troubleshooting (journalctl, dmesg):**
    *   **Logs (लॉग्स):** Computer mein jo bhi events hote hain – services start/stop, errors, warnings, user logins – sab record kiye jaate hain. Ye logs troubleshooting ke liye bahut zaroori hain.
    *   **`journalctl`:** Ye `systemd-journald` daemon द्वारा manage किए गए logs ko dekhne ka main command hai. Ye binary logs ko manage karta hai aur bahut powerful filtering options provide karta hai. Aap isse system boot messages, service failures, user activities, sab dekh sakte ho.
    *   **`dmesg`:** Ye command kernel ring buffer ke messages ko show karta hai. Ye messages kernel boot hone ke waqt aur hardware events (jaise naya USB device lagana) ke baare mein hote hain. Hardware related issues troubleshoot karne ke liye `dmesg` bahut useful hai.

---

### 2. Key Linux Commands (Hinglish)

**In commands ko apne dimag mein aur ungliyon par bitha lo!**

*   **Partitions & Filesystems:**
    *   `lsblk`: Connected block devices (disks, partitions, LVs) ko tree format mein dikhata hai. (Block devices ki jaankari)
    *   `fdisk /dev/sdX` (for MBR): Disk par partitions banane, modify karne ke liye. (Purane partitions)
    *   `gdisk /dev/sdX` (for GPT): GPT-based disks par partitions ke liye. (Naye, bade partitions)
    *   `parted /dev/sdX`: Modern partitioning tool, both MBR/GPT. Non-interactive & interactive mode mein use hota hai. (Advanced partitioning)
    *   `mkfs -t xfs /dev/device`: XFS filesystem banane ke liye. (`mkfs.xfs` bhi use kar sakte ho).
    *   `mkfs -t ext4 /dev/device`: ext4 filesystem banane ke liye. (`mkfs.ext4` bhi).
    *   `blkid`: Block device ke UUIDs aur filesystem types dikhata hai. (Device IDs pata karne ke liye)
    *   `mount /dev/device /mountpoint`: Ek device ko ek directory par mount karne ke liye.
    *   `umount /mountpoint`: Mounted device ko unmount karne ke liye.
    *   `df -h`: Mounted filesystems ki disk space usage dikhata hai (human-readable format).
    *   `cat /etc/fstab`: Permanent mount entries ko dekhne ke liye.
    *   `mount -a`: `/etc/fstab` mein listed sabhi entries ko mount karne ki koshish karta hai jo abhi mounted nahi hain.

*   **LVM Commands:**
    *   `pvcreate /dev/sdX1`: Physical Volume banata hai. (Raw partition ko LVM-ready banana)
    *   `pvdisplay`: Existing Physical Volumes ki jaankari.
    *   `vgcreate myvg /dev/sdX1 /dev/sdY1`: Volume Group banata hai. (PVs ko jodkar pool banana)
    *   `vgdisplay`: Existing Volume Groups ki jaankari.
    *   `lvcreate -L 5G -n mylv myvg`: 5GB ka Logical Volume banata hai `myvg` VG mein, naam `mylv`. (VG se partition banana)
    *   `lvdisplay`: Existing Logical Volumes ki jaankari.
    *   `lvextend -L +2G /dev/myvg/mylv`: Logical Volume ka size 2GB badhata hai. (LV ka size badhana)
    *   `lvreduce -L -1G /dev/myvg/mylv`: Logical Volume ka size 1GB kam karta hai (DANGER! Data loss ho sakta hai, pehle filesystem shrink karna padta hai).
    *   `xfs_growfs /mountpoint`: XFS filesystem ko extend karta hai. (Filesystem ko extended space use karne dena)
    *   `resize2fs /dev/myvg/mylv`: ext2/3/4 filesystem ko extend ya shrink karta hai.

*   **Swap Management:**
    *   `mkswap /dev/sdX2`: Swap partition banata hai.
    *   `swapon /dev/sdX2` (or `/path/to/swapfile`): Swap enable karta hai.
    *   `swapoff /dev/sdX2`: Swap disable karta hai.
    *   `free -h`: Memory aur swap usage dikhata hai (human-readable).

*   **SELinux Management:**
    *   `sestatus`: SELinux ka current status (enabled/disabled, enforcing/permissive).
    *   `getenforce`: Current SELinux mode (Enforcing/Permissive/Disabled).
    *   `setenforce 0`: SELinux ko Permissive mode mein set karta hai (temporarily).
    *   `setenforce 1`: SELinux ko Enforcing mode mein set karta hai (temporarily).
    *   `getsebool -a`: Sabhi SELinux booleans aur unki current state dikhata hai.
    *   `setsebool -P httpd_can_network_connect on`: Boolean ko permanently `on` karta hai (`-P` for permanent).
    *   `semanage fcontext -a -t httpd_sys_content_t "/webdata(/.*)?"`: Ek directory (`/webdata`) aur uske andar ki files ke liye SELinux context set karta hai. (Permanent context change)
    *   `restorecon -Rv /webdata`: Filesystem par files ke context ko unki policy ke hisaab se restore karta hai. (`semanage` ke baad ya files move hone par use hota hai).
    *   `chcon -t httpd_sys_content_t /webdata`: Temporarily ek file/directory ka context change karta hai. (Boot ke baad reset ho jayega agar policy mein change nahi kiya).
    *   `audit2allow -a -M mywebserver_policy`: SELinux audit logs se custom policy module banane mein help karta hai.

*   **Boot Process & GRUB:**
    *   `grub2-mkconfig -o /boot/grub2/grub.cfg`: GRUB configuration file ko regenerate karta hai.
    *   `grubby --update-kernel=ALL --args="parameter"`: Kernel boot parameters ko update karta hai. (Recommended way on RHEL/CentOS).
    *   `reboot`: System ko restart karta hai.
    *   `systemctl isolate rescue.target`: System ko rescue mode mein le jata hai.
    *   `systemctl isolate emergency.target`: System ko emergency mode mein le jata hai.

*   **Logs & Troubleshooting:**
    *   `journalctl`: Sabhi systemd journal entries dikhata hai.
        *   `journalctl -f`: Real-time logs follow karta hai.
        *   `journalctl -u httpd`: Specific service ke logs dikhata hai.
        *   `journalctl -k`: Kernel messages dikhata hai (similar to `dmesg`).
        *   `journalctl -b`: Current boot ke logs dikhata hai.
        *   `journalctl --since "1 hour ago"`: Last 1 hour ke logs.
    *   `dmesg`: Kernel ring buffer messages dikhata hai (hardware detection, driver issues).
        *   `dmesg -H`: Paged output with colors.
    *   `tail -f /var/log/messages`: Traditional way to follow system-wide log messages (older systems or specific apps).
    *   `cat /var/log/secure`: Authentication related logs.

---

### 3. Step-by-Step Examples

**Chalo ab in commands ko practically use karna seekhte hain! Apni VM mein root user se logon karo ya `sudo -i` kar lo.**

**Setup: Ek additional disk add karein apni VM mein.**
VirtualBox/VMware mein jao aur apni VM mein ek naya 10-20GB ka hard disk add karo. `reboot` karo ya `echo "- - -" > /sys/class/scsi_host/host0/scan` run karke disk ko scan karao (sometimes needed for hot-add).
`lsblk` se verify karo ki naya disk (`/dev/sdb` ya `/dev/sdc`) dikh raha hai.

---

#### Example 1: New Partition -> LVM -> Filesystem -> Mount -> fstab Entry

**Goal:** Ek naye disk `/dev/sdb` (ya jo bhi aapka naya disk hai) par LVM setup karna, ek Logical Volume banana, us par XFS filesystem banana aur use `/mydata` par permanently mount karna.

1.  **Disk identification:**
    ```bash
    lsblk
    # Output mein naya disk dekho, jaise /dev/sdb
    ```

2.  **Partition banayein (LVM type):**
    ```bash
    # `parted` ka use karenge kyuki ye flexible hai
    sudo parted /dev/sdb mklabel gpt # GPT label create karein (modern approach)
    sudo parted -a opt /dev/sdb mkpart primary 0% 100% # Poori disk ka ek partition
    sudo parted /dev/sdb set 1 lvm on # Partition 1 ko LVM flag dein
    lsblk # Verify partition /dev/sdb1
    ```
    *Agar aap MBR use kar rahe hote toh `fdisk /dev/sdb` -> `n` (new) -> `p` (primary) -> `Enter` (default sectors) -> `t` (change type) -> `8e` (Linux LVM) -> `w` (write changes) use karte.*

3.  **Physical Volume (PV) banayein:**
    ```bash
    sudo pvcreate /dev/sdb1
    sudo pvdisplay # Verify PV
    ```

4.  **Volume Group (VG) banayein:**
    ```bash
    sudo vgcreate vg_myproject /dev/sdb1
    sudo vgdisplay # Verify VG
    ```

5.  **Logical Volume (LV) banayein:**
    Hum ek 5GB ka LV banayenge.
    ```bash
    sudo lvcreate -L 5G -n lv_appdata vg_myproject
    sudo lvdisplay # Verify LV
    ```
    *LV ka full path hoga `/dev/vg_myproject/lv_appdata`*

6.  **Filesystem banayein (XFS):**
    ```bash
    sudo mkfs.xfs /dev/vg_myproject/lv_appdata
    ```

7.  **Mount point banayein aur mount karein:**
    ```bash
    sudo mkdir /mydata
    sudo mount /dev/vg_myproject/lv_appdata /mydata
    df -h /mydata # Verify mount
    ```

8.  **`/etc/fstab` mein entry add karein (Permanent Mount):**
    Pehle LV ka UUID pata karein:
    ```bash
    blkid /dev/vg_myproject/lv_appdata
    # Output mein UUID copy kar lo, jaise: UUID="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
    Ab `/etc/fstab` mein entry add karo (using `nano` or `vim`):
    ```bash
    sudo nano /etc/fstab
    ```
    File ke end mein ye line add karo:
    ```
    UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /mydata xfs defaults 0 0
    ```
    *Replace `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` with your actual UUID.*
    Save and exit (`Ctrl+X`, `Y`, `Enter` in nano).

9.  **`/etc/fstab` entry test karein:**
    ```bash
    sudo umount /mydata # Pehle unmount karo
    sudo mount -a # fstab mein di gayi sabhi entries ko mount karne ki koshish karega
    df -h /mydata # Verify ki mount ho gaya
    ```
    Agar koi error nahi aaya, toh aapka mount successful hai. Reboot ke baad bhi ye mount rahega.

---

#### Example 2: Extend an XFS LVM Partition

**Goal:** Humare `/mydata` LV ko 2GB se extend karna.

1.  **Current status dekhein:**
    ```bash
    df -h /mydata
    sudo lvdisplay /dev/vg_myproject/lv_appdata
    ```
    Note down the current size.

2.  **Logical Volume (LV) extend karein:**
    ```bash
    sudo lvextend -L +2G /dev/vg_myproject/lv_appdata
    # Ya poora free space use karne ke liye:
    # sudo lvextend -l +100%FREE /dev/vg_myproject/lv_appdata
    ```
    `lvdisplay` se confirm karein ki LV ka size badh gaya hai. Lekin `df -h` abhi bhi purana size dikhayega, kyunki filesystem ko update nahi kiya gaya.

3.  **Filesystem (XFS) extend karein:**
    ```bash
    sudo xfs_growfs /mydata
    ```

4.  **Verify karein:**
    ```bash
    df -h /mydata
    # Ab aapko naya, badha hua size dikhna chahiye.
    ```

---

#### Example 3: SELinux Management (Apache Webserver Context)

**Goal:** Ek custom directory `/webdata` banayein, usmein ek file rakhein aur SELinux context set karein taaki Apache use serve kar sake.

1.  **Directory aur test file banayein:**
    ```bash
    sudo mkdir /webdata
    echo "
Welcome to my custom web page!
" | sudo tee /webdata/index.html
    sudo chown apache:apache /webdata -R # Apache ko ownership dein
    sudo chmod 755 /webdata
    sudo chmod 644 /webdata/index.html
    ```

2.  **Current SELinux context dekhein:**
    ```bash
    ls -Zd /webdata
    ls -Z /webdata/index.html
    # Output mein default context dikhega, jaise 'unconfined_u:object_r:default_t:s0' ya 'var_t'.
    ```

3.  **Apache install karein aur SELinux boolean check karein:**
    ```bash
    sudo dnf install httpd -y
    sudo systemctl enable --now httpd
    sudo systemctl status httpd
    
    # Check if Apache is allowed to access network (important for web servers)
    getsebool httpd_can_network_connect
    # Agar ye off hai, toh on kar do:
    sudo setsebool -P httpd_can_network_connect on
    ```

4.  **SELinux context policy mein add karein:**
    Apache web content ke liye `httpd_sys_content_t` context use hota hai. Hum apne `/webdata` ke liye yahi set karenge.
    ```bash
    # Ye command SELinux policy mein permanent rule add karega
    sudo semanage fcontext -a -t httpd_sys_content_t "/webdata(/.*)?"

    # Ab files/directories par context apply karein
    sudo restorecon -Rv /webdata
    ```
    `ls -Zd /webdata` aur `ls -Z /webdata/index.html` se phir se context check karein. Ab ye `httpd_sys_content_t` hona chahiye.

5.  **Apache config karein aur test karein:**
    `sudo nano /etc/httpd/conf.d/mywebsite.conf` mein ye content dalein:
    ```
    
        DocumentRoot /webdata
        
            Require all granted
        
    
    ```
    ```bash
    sudo systemctl restart httpd
    ```
    Apne browser se `http:///` par browse karein. Agar webpage dikh raha hai, toh SELinux configuration sahi hai.

    **Troubleshooting Tip:** Agar access denied ho, toh `sudo setenforce 0` karke permissive mode mein try karein. Agar permissive mein kaam karta hai, toh SELinux policy issue hai. `sudo tail -f /var/log/audit/audit.log` mein `AVC` errors search karein, aur `audit2allow` use karein.

---

#### Example 4: Boot Process & GRUB (Entering Emergency Mode)

**Goal:** System ko Emergency Mode mein boot karna. Ye aksar root password reset karne ya critical boot issues fix karne ke liye use hota hai.

1.  **System ko reboot karein:**
    ```bash
    sudo reboot
    ```

2.  **GRUB menu par rookna:**
    Jab VM boot ho rahi ho, toh GRUB menu aate hi **Spacebar** ya **Shift** key press karein taaki boot process ruk jaye.

3.  **Kernel parameters edit karein:**
    *   Jo default kernel entry selected hai, uspe **`e`** press karein (for edit).
    *   Niche scroll karein aur `linux` ya `linux16` se shuru hone wali line dhundhein.
    *   Us line ke end mein, `rhgb quiet` ko remove karein aur `systemd.unit=emergency.target` add karein.
    *   **`Ctrl+x`** or **`F10`** press karein to boot with these modified parameters.

4.  **Emergency mode mein:**
    Aapko ab ek emergency shell prompt (`sh-5.1#`) par hona chahiye. Yahan aap root filesystem ko read-write mode mein mount kar sakte ho:
    ```bash
    mount -o remount,rw /sysroot
    chroot /sysroot
    # Ab aap root ki tarah ho, password reset kar sakte ho, filesystems check kar sakte ho.
    # Upar ke changes temporary hain, system reboot karne par normal boot hoga.
    ```
    Jab kaam ho jaye, `exit` type karke `reboot` karein.

---

#### Example 5: Logs & Troubleshooting (journalctl aur dmesg)

**Goal:** System logs ko effectively use karna.

1.  **Recent boot ke kernel messages dekhein:**
    ```bash
    journalctl -k -b
    # Ya siraf kernel ring buffer:
    dmesg | less
    ```

2.  **Specific service ke logs dekhein (e.g., httpd):**
    ```bash
    journalctl -u httpd.service
    # Real-time updates ke liye:
    journalctl -f -u httpd.service
    ```

3.  **Errors aur warnings find karein:**
    ```bash
    journalctl -p err
    journalctl -p warning
    ```

4.  **Last 5 minutes ke logs dekhein:**
    ```bash
    journalctl --since "5 minutes ago"
    ```

5.  **Disk related errors dhoondhein:**
    ```bash
    dmesg | grep -i 'disk\|error\|fail'
    journalctl -p err | grep -i 'disk\|error\|fail'
    ```

---

### 4. Practice Tasks

**Ab aapki baari hai! In tasks ko try karo aur confidence build karo.**

1.  **Naya Storage Setup:**
    *   Apni VM mein ek naya 8GB ka hard disk add karein (ya sparse file create karein `dd if=/dev/zero of=/var/lib/newdisk.img bs=1M count=8000`).
    *   Is naye disk par LVM setup karein (PV, VG, LV `lv_salesdata`, size 6GB).
    *   LV ko XFS filesystem se format karein.
    *   `lv_salesdata` ko `/sales` directory par permanently mount karein.
    *   `/sales` directory mein `sales_report.txt` naam ki ek file banayein aur usmein kuch content dalein.
    *   Verify karein ki sab sahi hai (`df -h`, `mount`, `ls -l /sales`).

2.  **LVM Resizing:**
    *   Upar wale `lv_salesdata` ko 2GB se extend karein.
    *   Verify karein ki filesystem size bhi badh gaya hai.

3.  **Swap Management:**
    *   Apne system par 1GB ki ek swap file banayein (`/swapfile`).
    *   Use enable karein.
    *   Use permanently enable karein (`/etc/fstab` mein entry).
    *   Verify karein ki swap active hai (`free -h`, `swapon -s`).

4.  **SELinux Context Fix:**
    *   `mkdir /webapps` banao.
    *   Ek file banao `/webapps/test.html` (`echo "SELinux test" > /webapps/test.html`).
    *   Apache ko configure karein taaki woh `/webapps` ko serve kar sake (jaise Example 3 mein kiya).
    *   Agar Apache ko `/webapps` access karne mein dikkat aa rahi hai (browser mein "Forbidden" error), toh SELinux ko troubleshoot karo aur `test.html` ka context `httpd_sys_content_t` mein set karo permanently.
    *   Verify karein ki webpage access ho raha hai aur SELinux enforcing mode mein hai.

5.  **SELinux Boolean Toggle:**
    *   `ftpd_full_access` boolean ki current state check karein.
    *   Agar woh `off` hai, toh use `on` karein (permanently).
    *   Verify karein ki state change ho gayi hai.

6.  **Logs Analysis:**
    *   Apni VM ko reboot karein.
    *   Boot hone ke baad, `journalctl -b` command chala ke dekhein ki koi service `failed` status mein toh nahi hai.
    *   Kernel messages mein apne network interface ka naam dhoondhein (`dmesg | grep -i eth` ya `dmesg | grep -i ens`).
    *   `audit.log` mein `denied` entries dhoondhein (agar SELinux active hai).

7.  **GRUB Modification:**
    *   GRUB menu mein jaake, temporary taur par kernel options mein `loglevel=7` add karein.
    *   Boot karein aur `dmesg` ya `journalctl -k` se dekhein ki zyada verbose output mil raha hai ya nahi.
    *   Reboot karein aur confirm karein ki change permanent nahi hua.

---

### 5. Pro Tips / Common Mistakes

**In baton ka hamesha dhyan rakhna, ye aapko bahut si mushkilon se bachayengi!**

*   **`/etc/fstab` ki galti:**
    *   **Mistake:** Incorrect UUID ya device path dena.
    *   **Pro Tip:** Hamesha `blkid` se UUID copy karo. Device names jaise `/dev/sdb1` boot order change hone par badal sakte hain, isliye UUID preferred hai.
    *   **Mistake:** `mount -a` test kiye bina reboot karna.
    *   **Pro Tip:** `sudo mount -a` hamesha run karo `/etc/fstab` edit karne ke baad. Agar koi error aaye toh fix karo, phir reboot karo. Varna system boot nahi hoga!
    *   **Mistake:** Wrong mount options (jaise `noauto` for `/`).
    *   **Pro Tip:** Root filesystem (`/`) ke liye `defaults` use karo. NFS/SMB mounts ke liye `_netdev` aur `nofail` jaise options use karna useful hota hai.

*   **LVM ke saath kaam karte waqt:**
    *   **Mistake:** `xfs_growfs` ya `resize2fs` chalana bhool jana `lvextend` ke baad.
    *   **Pro Tip:** `lvextend` sirf LV ka size badhata hai; filesystem ko bhi inform karna padta hai ki space use kar le. XFS ke liye `xfs_growfs /mountpoint` aur ext4 ke liye `resize2fs /dev/vgname/lvname`.
    *   **Mistake:** `lvreduce` bina filesystem shrink kiye chalana.
    *   **Pro Tip:** **Never, ever** `lvreduce` without shrinking the filesystem first (for ext4). Aur XFS filesystems ko shrink nahi kiya ja sakta. `lvreduce` se data loss ho sakta hai!

*   **SELinux se ladte waqt:**
    *   **Mistake:** "SELinux is difficult, disable it!" mentality.
    *   **Pro Tip:** SELinux ek important security feature hai. Disable karne se pehle troubleshoot karna seekho.
    *   **Mistake:** `chcon` se context change karna aur sochna ki woh permanent hai.
    *   **Pro Tip:** `chcon` temporary hai, `relabel` ya `restorecon` karne par context reset ho jayega. Permanent changes ke liye `semanage fcontext` use karo, aur phir `restorecon -Rv` chalao.
    *   **Mistake:** `restorecon` bhool jana files copy/move karne ke baad.
    *   **Pro Tip:** Agar aapne kisi directory mein files copy ya move ki hain jiska context alag hai, toh naye files ka context galat ho sakta hai. `restorecon -Rv /path/to/directory` chalana na bhoolen.
    *   **Pro Tip:** `setenforce 0` (permissive mode) debugging ke liye useful hai. Agar permissive mein issue solve ho jaye, toh matlab SELinux policy issue hai. `audit.log` check karo aur `audit2allow` use karo.

*   **Boot Process & GRUB:**
    *   **Mistake:** `grub.cfg` ko manually edit karna.
    *   **Pro Tip:** `/boot/grub2/grub.cfg` ko direct edit mat karo. Ye auto-generated file hai. Changes ke liye `/etc/default/grub` ya `grubby` command use karo aur phir `grub2-mkconfig -o /boot/grub2/grub.cfg` se regenerate karo.
    *   **Pro Tip:** `rd.break` ya `single` kernel parameter ka use karke root shell access karna sikh lo. Ye password reset aur boot issues fix karne ke liye life-saver hai.

*   **General Troubleshooting:**
    *   **Pro Tip:** Hamesha `man` pages refer karo kisi bhi command ki poori jaankari ke liye (`man journalctl`).
    *   **Pro Tip:** Virtual Machines mein practice karo, taaki real production system par galti na ho. Snapshots le lo practice karne se pehle!
    *   **Pro Tip:** Logs check karne ki aadat banao. Koi bhi issue ho, sabse pehle `journalctl`, `dmesg`, `var/log/messages` (agar hai toh), `var/log/secure` check karo.

---

### Mock RHCSA Test Questions

**Ab time hai aapki knowledge test karne ka. Ye questions RHCSA exam format ke similar hain.**

**Scenario 1: Storage Expansion**

Aap ek server par kaam kar rahe ho jismein ek naya 15GB ka disk (`/dev/sdd`) install kiya gaya hai. Aapko ye task perform karna hai:

1.  `/dev/sdd` par ek naya Physical Volume banayein.
2.  Ek naya Volume Group `vg_webcontent` banayein, aur usmein `/dev/sdd` ko add karein.
3.  `vg_webcontent` se ek 10GB ka Logical Volume `lv_website` banayein.
4.  `lv_website` par XFS filesystem create karein.
5.  `/var/www/html/mysite` directory banayein.
6.  `lv_website` ko `/var/www/html/mysite` par permanently mount karein.
7.  Verify karein ki filesystem mount ho gaya hai aur boot ke baad bhi available rahega.

**Scenario 2: SELinux Webserver Security**

Aapne ek Apache webserver setup kiya hai aur uski document root `/data/web` set ki hai.

1.  `ls -Zd /data/web` command ka output kya hoga agar `httpd_sys_content_t` context apply nahi kiya gaya hai?
2.  Aapko `/data/web` aur uske andar ki sabhi files/directories par `httpd_sys_content_t` context permanently assign karna hai. Required commands likhiye.
3.  `restorecon -Rv /data/web` chalane ke baad `ls -Zd /data/web` ka output kya hona chahiye?
4.  Agar aapka webserver network se connect nahi ho pa raha hai jabki network sahi hai, toh kaun sa SELinux boolean check karoge aur kaise enable karoge permanently?

**Scenario 3: Troubleshooting & Recovery**

Aapke system mein kuch boot issue aa gaya hai. Jab aap system boot karte ho, toh woh GRUB menu mein stuck ho jata hai ya emergency mode mein chala jata hai. Aapko GRUB configuration check karna hai.

1.  Aap Emergency Mode mein kaise boot karoge? Step-by-step procedure bataiye.
2.  Emergency Mode mein enter hone ke baad, aap apne root filesystem (`/`) ko read-write mode mein kaise mount karoge taaki aap files edit kar sako?
3.  GRUB configuration file ka default path kya hai? Is file ko directly edit karne ki bajaye, aap kis file ko edit karoge aur kaun sa command chalaoge changes ko apply karne ke liye?

**Scenario 4: Log Analysis**

Aapko system ki performance aur health check karni hai.

1.  Apne system ke last boot ke saare messages kaise dekhoge, jinmein kernel messages bhi shamil hon?
2.  Agar aapko sirf woh log entries dekhni hain jinmein `failed` keyword ho aur woh `httpd` service se related hon, toh aap kaun sa `journalctl` command use karoge?
3.  Aap hardware device detection messages kaise dekhoge?

---

All the best! In topics par command hasil karna aapko ek skilled Linux admin banayega. Practice, practice, aur practice!
