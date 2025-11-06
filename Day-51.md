Day 51: Troubleshooting marathon (boot, storage, services)
---
Namaste Future Linux Wizards!
Main aapka instructor, aur aaj hum ek bahut hi crucial aur exciting topic par baat karenge: **"Linux Troubleshooting Marathon: Boot, Storage, Services"**. Ye sirf ek topic nahi, balki ek skill hai, ek art hai jo aapko ek good sysadmin se great sysadmin banayegi. RHCSA aur RHCE exams mein iski bahut importance hai, aur real-world mein toh aapka daily bread-butter hi yahi hai!

Chaliye, shuru karte hain iss troubleshooting adventure ko!

---

## 1. Concept Explanation (Hinglish Mein)

**Troubleshooting kya hai?**
Imagine aap ek Sherlock Holmes ho, aur aapka Linux system ek crime scene. Jab system mein kuch galat hota hai – jaise boot nahi ho raha, disk full ho gayi, ya koi service kaam nahi kar rahi – toh aapko clue dhoondne hain, hypothesis banani hai, aur problem ko solve karna hai. Yahi troubleshooting hai! Ye sirf commands run karna nahi, balki **dimaag lagana, system ko samajhna, aur ek logical tareeke se problem ko isolate karna** hai.

RHCSA aur RHCE mein, aapko kai baar aise scenarios milenge jahan system partially broken hoga, aur aapko use theek karna hoga.

Hum iss troubleshooting marathon ko teen main hisson mein baatenge, kyunki mostly problems inhi areas mein aati hain:

### A. Boot Troubleshooting (System Start Kyun Nahi Ho Raha?)
**Yeh kya hai?** Jab aap apna computer on karte ho, toh bahut saare steps hote hain jab tak aapko login prompt na dikh jaye. Is poore process ko "Boot Sequence" kehte hain. Agar ismein kahin bhi gadbad hui, toh system ya toh atak jayega, ya error dega, ya kernel panic ho jayega.
**Kahan problem ho sakti hai?**
*   **GRUB (Grand Unified Bootloader) issues:** GRUB corrupt ho gaya, ya uski config file galat hai. Isse system GRUB prompt pe hi atak jata hai.
*   **Kernel issues:** Kernel file missing hai, corrupt hai, ya usmein koi compatibility issue hai.
*   **Initramfs/initrd issues:** Ye ek initial RAM filesystem hai jo kernel ko hardware detect karne aur root filesystem mount karne mein help karta hai. Agar ye corrupt ho gaya, toh kernel root partition nahi dhoond payega.
*   **FSTAB errors:** Agar `/etc/fstab` file mein koi entry galat hai aur system us partition ko boot time pe mount nahi kar pa raha, toh system emergency mode mein chala jayega.
*   **Missing/Corrupt Systemd units:** Agar critical services start nahi ho pa rahi boot time pe.

### B. Storage Troubleshooting (Disk Space Ya Data Access Mein Problem)
**Yeh kya hai?** Aapka data jahan rehta hai – hard drives, SSDs, partitions, filesystems – ismein jab bhi koi issue aata hai. Ye ek bahut common area hai jahan problems hoti hain.
**Kahan problem ho sakti hai?**
*   **Disk full (No space left on device):** Sabse common. Aapka partition bhar gaya hai, aur naya data store nahi ho pa raha.
*   **Filesystem corruption:** XFS ya EXT4 filesystem corrupt ho gaya hai, jisse data read/write nahi ho pa raha.
*   **Partition issues:** Partition table corrupt ho gayi, ya partitions galat tarike se created hain.
*   **LVM (Logical Volume Management) issues:** PV (Physical Volume), VG (Volume Group), ya LV (Logical Volume) mein koi problem. Jaise VG active nahi hai ya LV corrupt hai.
*   **Mount point issues:** Filesystem mount nahi ho raha, ya galat mount point par mount ho gaya hai.
*   **FSTAB errors:** Boot time pe koi filesystem mount nahi ho pa raha (jaise upar discuss kiya).

### C. Service Troubleshooting (Application/Background Process Kaam Nahi Kar Raha)
**Yeh kya hai?** Linux mein saare applications, background processes, network daemons, ye sab "services" ke roop mein chalte hain (mostly `systemd` dwara managed). Agar koi web server (httpd), database (mariadb), ya network service (sshd) kaam nahi kar raha, toh yahan troubleshooting ki zaroorat padegi.
**Kahan problem ho sak सकती है?**
*   **Service failed to start:** Service start hi nahi ho pa rahi. Reason config error ho sakta hai, missing dependency ho sakti hai, ya port already in use ho sakta hai.
*   **Service running but not accessible:** Service chal rahi hai, par aap usko access nahi kar pa rahe. Iska matlab firewall block kar raha hai, ya SELinux policy allow nahi kar rahi, ya service galat port par listen kar rahi hai.
*   **Resource issues:** Service chal rahi hai, par bahut slow hai ya hang ho rahi hai kyunki CPU, RAM ya disk I/O use zyada ho raha hai.
*   **Dependency issues:** Service ko start hone ke liye kisi aur service ki zaroorat hai, aur woh start nahi hui.

**Troubleshooting ka Golden Rule:**
"Start with the symptoms, check the logs, form a hypothesis, test it, fix it, and *verify the fix*."
**Logs are your best friend!**

---

## 2. Key Linux Commands (Hinglish Explanations Ke Saath)

Troubleshooting ke liye ye commands aapke tools hain. Inko acche se samajhna bahut zaroori hai.

### General Troubleshooting Commands (Commonly Used)

*   `journalctl`: System logs ko dekhne ka master command. Boot messages se leke service specific logs tak sab yahan milta hai.
    *   `journalctl -xb`: Pichle boot ke logs, emergency mode mein bahut useful.
    *   `journalctl -u `: Kisi specific service ke logs dekhne ke liye (e.g., `journalctl -u httpd`).
    *   `journalctl -f`: Real-time logs follow karne ke liye (tail -f ki tarah).
*   `dmesg`: Kernel ring buffer messages dikhata hai. Boot ke starting phases mein hardware detection, kernel errors, drivers se related info yahan milti hai.
*   `cat`, `less`, `more`: Files ki content dekhne ke liye. `less` aur `more` bade files ke liye better hain.
*   `tail -f `: File ke end ki content real-time mein follow karne ke liye. Logs live monitor karne mein helpful.
*   `grep  `: Files mein specific text pattern search karne ke liye.
*   `find  -name `: Files aur directories ko search karne ke liye.
*   `ps aux`: Running processes ki detailed list. Kounsi process chal rahi hai, kitna CPU/RAM le rahi hai, kis user se chal rahi hai.
*   `top` / `htop`: Real-time mein system resource usage (CPU, Memory, processes) monitor karne ke liye.
*   `lsof -i`: Open network files aur ports ko list karta hai. Kaunsi process kaunsa port use kar rahi hai, pata chalega.
*   `strace `: Ek program ko trace karta hai aur uske system calls ko dikhata hai. Advanced debugging ke liye.
*   `yum install --reinstall `: Kisi package ko reinstall karne ke liye, agar wo corrupt ho gaya hai.
*   `rpm -V `: RPM package ki integrity check karne ke liye.

### Boot Troubleshooting Commands

*   `grubby --default-kernel`: Default boot hone wale kernel ka path batata hai.
*   `ls -l /boot`: Boot directory ki content dekhne ke liye (kernel files, initramfs images, GRUB config).
*   `grub2-mkconfig -o /boot/grub2/grub.cfg`: GRUB config file ko regenerate karne ke liye.
*   `dracut -f -v  `: Initramfs image ko rebuild karne ke liye. Example: `dracut -f -v /boot/initramfs-$(uname -r).img $(uname -r)`.
*   `chroot /mnt/sysimage`: Rescue mode mein root filesystem change karne ke liye. Bohut zaroori command hai.

### Storage Troubleshooting Commands

*   `lsblk`: Saare block devices (disks, partitions, LVM volumes) ko tree-like format mein list karta hai, unke mount points ke saath.
*   `fdisk -l`: Traditional partition tables (MBR) ko list karta hai.
*   `parted -l`: Modern partition tables (GPT) aur MBR ko bhi support karta hai. More powerful.
*   `df -h`: Mounted filesystems ka disk space usage human-readable format mein dikhata hai.
*   `du -sh `: Kisi directory ka total disk usage batata hai. Bade files dhoondhne mein useful.
*   `mount`: Currently mounted filesystems ko list karta hai. Bina arguments ke run karne par.
*   `mount -a`: `/etc/fstab` mein defined saare filesystems ko mount karne ki koshish karta hai.
*   `umount `: Filesystem ko unmount karne ke liye.
*   `cat /etc/fstab`: Filesystems jo boot time pe mount hote hain, unki configuration file.
*   `xfs_repair ` / `fsck `: Filesystem errors ko repair karne ke liye. `xfs_repair` XFS filesystems ke liye aur `fsck` EXT4/EXT3 ke liye. **CAUTION: Unmount the filesystem first!**
*   `pvdisplay`, `vgdisplay`, `lvdisplay`: LVM physical volumes, volume groups, aur logical volumes ki information dikhate hain.
*   `vgchange -a y `: Volume Group ko activate karne ke liye.
*   `pvscan`, `vgscan`, `lvscan`: LVM components ko scan karne ke liye.
*   `restorecon -Rv /path`: SELinux context ko restore karne ke liye, agar storage issue SELinux ki wajah se hai.

### Service Troubleshooting Commands

*   `systemctl status `: Kisi service ka current status (running, stopped, failed) aur latest logs dikhata hai.
*   `systemctl start `: Service ko start karne ke liye.
*   `systemctl stop `: Service ko stop karne ke liye.
*   `systemctl restart `: Service ko restart karne ke liye.
*   `systemctl enable `: Service ko boot time pe automatically start hone ke liye enable karta hai.
*   `systemctl disable `: Service ko boot time pe start hone se rokta hai.
*   `systemctl daemon-reload`: Systemd configuration files ko reload karta hai, agar aapne koi service unit file change ki hai.
*   `ss -tunlp`: Network sockets ko list karta hai (listening ports, connected clients). `netstat` ka modern replacement. (`-t` TCP, `-u` UDP, `-n` numeric, `-l` listening, `-p` process).
*   `firewall-cmd --list-all`: Firewall ki current configuration dikhata hai (allowed services/ports).
*   `firewall-cmd --add-service= --permanent` / `--add-port=/tcp --permanent`: Firewall mein service ya port add karne ke liye.
*   `semanage fcontext -a -t  "/path(/.*)?"`: SELinux file context define karne ke liye.
*   `setsebool -P  `: SELinux booleans ko change karne ke liye.
*   `getenforce`: SELinux status (Enforcing/Permissive/Disabled) check karne ke liye.
*   `setenforce 0`: SELinux ko Permissive mode mein set karne ke liye (temporary troubleshooting).

---

## 3. Step-by-Step Examples (Real-World Scenarios)

Chaliye kuch common scenarios dekhte hain aur unhe kaise troubleshoot karte hain.

### Scenario 1: System Boot Nahi Ho Raha - GRUB/Initramfs Issue

**Problem Description:**
Aapne ek naya kernel install kiya tha, ya kuch updates kiye the, aur ab system boot nahi ho raha. Power on karte hi aapko ya toh `grub>` prompt mil raha hai, ya "Kernel Panic: VFS: Unable to mount root fs on unknown-block(0,0)" jaisa error aa raha hai.

**Troubleshooting Steps:**

1.  **Identify the Problem:** "Kernel Panic" ya "Unable to mount root fs" ka matlab hai kernel ko root filesystem nahi mil raha. GRUB prompt ka matlab GRUB config corrupt hai.
2.  **Boot into Rescue Mode:**
    *   System ko reboot karo aur jab GRUB menu aaye, `e` press karo edit karne ke liye.
    *   `linux16` ya `linux` line dhoondho. Uske end mein `rd.break` add karo (ya `init=/bin/bash` agar `rd.break` kaam na kare).
    *   `Ctrl+x` ya `F10` press karke boot karo.
    *   Aap `switch_root:/#` prompt par aa jaoge, ye emergency shell hai.
3.  **Mount Root Filesystem (Read-Write):**
    ```bash
    mount -o remount,rw /sysroot
    chroot /sysroot
    ```
    *   Ab aap apne actual root filesystem ke andar ho.
4.  **Diagnose Root Cause:**
    *   **Check Kernel/Initramfs:**
        ```bash
        ls -l /boot
        grubby --default-kernel # Dekho current default kernel sahi hai ya nahi
        ```
        Agar initramfs image missing hai ya galat hai, toh rebuild karo.
        *   `uname -r` se current kernel version check karo.
        *   `dracut -f -v /boot/initramfs-$(uname -r).img $(uname -r)`
            *   *Hinglish:* "Isse aapki current kernel version ke liye ek naya initramfs image ban jayega."
    *   **Check GRUB Configuration:**
        ```bash
        grub2-mkconfig -o /boot/grub2/grub.cfg
        ```
        *   *Hinglish:* "Ye command GRUB configuration ko regenerate karega, aur saare available kernels ko detect karke entries bana dega."
5.  **Exit and Reboot:**
    ```bash
    exit # chroot se bahar aane ke liye
    reboot
    ```
    *   *Hinglish:* "System ab normally boot hona chahiye."

### Scenario 2: Storage Issue - `/etc/fstab` Error (Partition Not Mounting)

**Problem Description:**
System boot ho gaya, par emergency mode mein chala gaya. Aapko `journalctl -xb` mein error dikh raha hai "Failed to mount /data" or "Dependency failed for /data". Ya `df -h` karne par aapko `/data` partition dikh hi nahi raha.

**Troubleshooting Steps:**

1.  **Identify the Problem:** "Failed to mount /data" clearly indicates an issue with the `/data` partition's entry in `/etc/fstab`.
2.  **Access Emergency Shell:**
    *   Agar system emergency mode mein boot hua hai, toh aapko `root@localhost:~#` prompt mil jayega. Agar nahi, toh boot process mein `e` dabake `rd.break` add karo aur fir `chroot` karo jaise upar wale example mein kiya tha.
3.  **Check Logs:**
    ```bash
    journalctl -xb | less
    ```
    *   Scroll down to find the specific error message related to `/data` mount. It will often show *why* it failed (e.g., "No such device or address", "Wrong filesystem type").
    *   *Hinglish:* "Logs mein error ka exact reason mil jayega. UUID galat hai, ya filesystem type mis-match hai."
4.  **Examine `/etc/fstab`:**
    ```bash
    vi /etc/fstab
    ```
    *   `/data` entry dhoondho. Ensure:
        *   **UUID/LABEL/Device name:** Sahi hai ya nahi. `lsblk -f` ya `blkid` se confirm karo.
        *   **Mount Point:** `/data` sahi hai.
        *   **Filesystem Type:** `xfs`, `ext4` jo bhi hai, sahi likha hai.
        *   **Mount Options:** `defaults`, `_netdev` (agar network share hai).
        *   **Pass number:** Last column mein `0` ya `1` ya `2` (0=no check, 1=root fs, 2=other fs). Agar filesystem corruption check nahi chahiye toh `0` rakho.
    *   *Hinglish:* "Sabse common mistakes hain galat UUID ya spelling mistake."
5.  **Correct the Error:**
    *   Agar UUID galat hai, `blkid` se sahi UUID leke update karo.
    *   Agar koi option galat hai, theek karo.
    *   Agar aapko sure nahi hai, toh option mein `nofail` add kar do ya entry ko comment (hash `#`) kar do temporary basis par.
6.  **Test the Fix & Reboot:**
    ```bash
    mount -a # Saare fstab entries ko mount karne ki koshish karega
    systemctl daemon-reload # Agar service units depend kar rahi hain
    exit # Emergency mode se bahar
    reboot
    ```
    *   *Hinglish:* "`mount -a` se aap immediate test kar sakte ho ki fix kaam kar raha hai ya nahi. Phir reboot karke permanent fix verify karo."

### Scenario 3: Service Issue - Apache (httpd) Server Not Starting

**Problem Description:**
Aapki website down hai. Jab aap check karte ho, `httpd` service "failed" dikha rahi hai.

**Troubleshooting Steps:**

1.  **Identify the Problem:** `httpd` service is failed.
2.  **Check Service Status:**
    ```bash
    systemctl status httpd
    ```
    *   *Hinglish:* "Ye command service ka current status aur uske latest logs dikhayega. `Active: failed` dikhega. Error message par dhyaan do."
3.  **Check Service Logs:**
    ```bash
    journalctl -u httpd --since "10 minutes ago" | less
    ```
    *   Yahan aapko detailed error messages milenge. Common errors:
        *   "Cannot bind to address": Port already in use, ya koi aur process use kar rahi hai, ya insufficient permissions.
        *   "Syntax error on line X of /etc/httpd/conf/httpd.conf": Configuration file mein galti.
        *   "Permission denied": DocumentRoot ya log files pe permissions issue.
    *   *Hinglish:* "Journalctl mein aapko exact line number ya reason mil jayega ki kahan gadbad hai."
4.  **Check Configuration Files:**
    ```bash
    apachectl configtest # Apache config syntax check karne ke liye
    vi /etc/httpd/conf/httpd.conf
    ls /etc/httpd/conf.d/ # Agar koi custom config file hai
    ```
    *   `apachectl configtest` bahut useful hai, ye bata dega ki config syntaxically correct hai ya nahi.
    *   Agar "Cannot bind to address" hai, toh `/etc/httpd/conf/httpd.conf` mein `Listen` directive check karo. Kahin koi aur service toh nahi chal rahi us port pe? `ss -tunlp | grep 80` ya `ss -tunlp | grep 443`
5.  **Check Firewall and SELinux:**
    *   **Firewall:**
        ```bash
        firewall-cmd --list-all
        ```
        Kya `http` service ya port `80/tcp` (aur `https`/`443/tcp` agar SSL hai) allowed hain?
        Agar nahi, toh add karo:
        ```bash
        firewall-cmd --add-service=http --permanent
        firewall-cmd --reload
        ```
    *   **SELinux:**
        ```bash
        getenforce # Enforcing mode mein hai toh dikkat ho sakti hai
        sestatus
        tail -f /var/log/audit/audit.log # SELinux denials yahan milenge
        ```
        Agar SELinux blocks dikh rahe hain, toh unhe allow karne ke liye `semanage fcontext` aur `restorecon` ya `setsebool` use karna pad sakta hai. Testing ke liye aap `setenforce 0` (Permissive mode) karke try kar sakte ho (production mein aisa nahi karna chahiye permanent fix ke bina).
6.  **Start Service & Verify:**
    ```bash
    systemctl start httpd
    systemctl status httpd
    ```
    *   Web browser se website access karke verify karo.
    *   *Hinglish:* "Jab problem solve ho jaye, toh service ko enable karna mat bhoolna taaki next boot pe bhi start ho jaye."
    ```bash
    systemctl enable httpd
    ```

---

## 4. Practice Tasks (Hands-On Lab Exercises)

Yeh tasks aapko ek Virtual Machine (VM) par karne hain, jaise CentOS Stream 9 ya RHEL 9. Ekdum fresh VM lena, aur snapshots lena mat bhoolna takki agar kuch zyada gadbad ho jaye toh revert kar sako.

### Task 1: Corrupt `/etc/fstab` and Fix

1.  **Preparation:**
    *   Ek naya logical volume (LV) ya partition create karo (e.g., `/dev/vdb1` ya `/dev/mapper/vg01-lv01`).
    *   Us par XFS filesystem banao: `mkfs.xfs /dev/vdb1`
    *   Ek mount point banao: `mkdir /mnt/data`
    *   Us LV ko `/etc/fstab` mein add karo (use UUID for robustness): `UUID= /mnt/data xfs defaults 0 0`
    *   `mount -a` aur `df -h /mnt/data` se verify karo ki mount ho raha hai.
    *   `reboot` karke check karo ki boot pe bhi mount ho raha hai.
2.  **Introduce the Problem:**
    *   `sudo vi /etc/fstab`
    *   `UUID` ki value mein ek character change kar do (e.g., last digit badal do). Ya filesystem type `xfs` ki jagah `ext4` likh do.
    *   `reboot` karo.
3.  **Troubleshoot and Fix:**
    *   System emergency mode mein boot hona chahiye.
    *   `journalctl -xb` se error message dhoondo.
    *   `blkid` se sahi UUID dhoondo.
    *   `/etc/fstab` ko edit karke sahi UUID (ya FS type) daalo.
    *   `mount -a` aur `reboot` karke verify karo.

### Task 2: Simulate GRUB Corruption & Recovery

1.  **Preparation:**
    *   `ls -l /boot/grub2/grub.cfg` output note karo.
    *   `grubby --default-kernel` ka output note karo.
2.  **Introduce the Problem (Method 1: GRUB config corruption):**
    *   `sudo mv /boot/grub2/grub.cfg /boot/grub2/grub.cfg.bak` (move the config file)
    *   `reboot` karo.
    *   Aapko `grub>` prompt milna chahiye.
3.  **Troubleshoot and Fix Method 1:**
    *   Emergency mode mein boot karo (ya live CD/USB se boot karke chroot karo).
    *   `chroot /sysroot` (agar emergency mode mein ho)
    *   `grub2-mkconfig -o /boot/grub2/grub.cfg`
    *   `exit` then `reboot`.
4.  **Introduce the Problem (Method 2: Initramfs deletion - *More risky, take snapshot!*):**
    *   `sudo rm /boot/initramfs-$(uname -r).img` (delete the active initramfs)
    *   `reboot` karo.
    *   Aapko "Kernel Panic" ya "Unable to mount root fs" jaisa error milega.
5.  **Troubleshoot and Fix Method 2:**
    *   Emergency mode mein boot karo (jaise `rd.break` add karke).
    *   `mount -o remount,rw /sysroot`
    *   `chroot /sysroot`
    *   `dracut -f -v /boot/initramfs-$(uname -r).img $(uname -r)`
    *   `exit` then `reboot`.

### Task 3: Broken Service (HTTPD) Configuration & Fix

1.  **Preparation:**
    *   `sudo dnf install httpd -y`
    *   `sudo systemctl enable --now httpd`
    *   `sudo firewall-cmd --add-service=http --permanent; sudo firewall-cmd --reload`
    *   Verify `curl http://localhost` se ki Apache chal raha hai.
2.  **Introduce the Problem:**
    *   `sudo vi /etc/httpd/conf/httpd.conf`
    *   `Listen 80` line ko `Listen 8080` kar do (ya koi aisa port jo non-standard ho aur block ho).
    *   `sudo systemctl restart httpd`
3.  **Troubleshoot and Fix:**
    *   `curl http://localhost` ab kaam nahi karega.
    *   `sudo systemctl status httpd` check karo. Ye running dikha sakta hai, par problem still hai.
    *   `sudo journalctl -u httpd` dekho.
    *   `ss -tunlp | grep 80` karo, aapko `httpd` process port 80 pe listen karti nahi dikhegi.
    *   `apachectl configtest` run karo (ho sakta hai is case mein syntax error na de, par real life mein de sakta hai).
    *   Firewall check karo: `firewall-cmd --list-all`. Kya 8080 allowed hai? (Nahi hai toh add karo `firewall-cmd --add-port=8080/tcp --permanent --reload`).
    *   Configuration file ko theek karo (`Listen 80` wapas).
    *   `sudo systemctl restart httpd` aur `curl http://localhost` se verify karo.

### Task 4: Disk Space Crunch

1.  **Preparation:**
    *   `df -h /` se root partition ka available space note karo.
    *   `mkdir /tmp/large_files`
2.  **Introduce the Problem:**
    *   `dd if=/dev/zero of=/tmp/large_files/file1 bs=1M count=2000` (2GB file banao)
    *   `dd if=/dev/zero of=/tmp/large_files/file2 bs=1M count=3000` (3GB file banao)
    *   `df -h /` karo, aapka root partition bhara hua dikhega (ya `No space left on device` error aayega).
3.  **Troubleshoot and Fix:**
    *   `df -h` se identify karo ki kaunsa partition full hai.
    *   `du -sh /*` aur `du -sh /var/*`, `du -sh /tmp/*` (ya jahan bhi aapko doubt hai) se badi directories dhoondo.
    *   Ek baar bade files/directories mil jayen, unko review karo. Kya ye needed hain?
    *   `rm -rf /tmp/large_files` se files ko delete karo.
    *   `df -h` se verify karo ki space free ho gaya hai.

---

## 5. Pro Tips / Common Mistakes (Gurumantra aur Bachne ke Tarike)

### Pro Tips (Aapke Troubleshooting Gurumantra)

1.  **Logs, Logs, Logs!** (Jahan tak possible ho, logs check karo. `journalctl` is your best friend.)
2.  **Stay Calm & Systematic:** Panic mat karo. Ek step-by-step approach follow karo: Observe -> Hypothesize -> Test -> Fix -> Verify.
3.  **One Change at a Time:** Jab troubleshooting kar rahe ho, ek baar mein ek hi change karo. Agar multiple changes karoge toh pata nahi chalega ki kaunsa change kaam kiya ya kisne naya problem create kiya.
4.  **Verify the Fix:** Fix karne ke baad, hamesha verify karo ki problem resolve ho gayi hai aur koi naya problem create nahi hua. Reboot karke bhi check karo agar boot-time issue tha.
5.  **Check SELinux & Firewall:** Network/Service related issues mein hamesha SELinux aur Firewall ko priority pe check karo. Ye do bade culprits hote hain jo log bhool jaate hain. `setenforce 0` (temporary permissive mode) karke try karo to rule out SELinux.
6.  **Backup Before Major Changes:** Khaas kar ke `/etc/fstab`, GRUB config, ya service config files edit karne se pehle backup le lo. `cp /etc/fstab /etc/fstab.bak`.
7.  **Understand Dependencies:** Services ki dependencies ko samjho. Ek service dusri pe depend kar sakti hai. `systemctl list-dependencies ` is useful.
8.  **Use `man` Pages:** Commands ke baare mein zyada janne ke liye `man ` ka use karo.
9.  **Google/Red Hat Documentation:** Agar stuck ho jao, toh error messages ko copy karke Google/Red Hat documentation mein search karo. Chances hain ki kisi aur ko bhi ye problem aayi hogi.
10. **Snapshot Your VMs:** Lab environments mein hamesha VMs ka snapshot lo critical changes se pehle. Ye aapko revert karne mein help karega agar kuch galat ho jaye.

### Common Mistakes (Jinse Bachna Hai)

1.  **Blind Copy-Pasting:** Internet se solutions ko bina samjhe copy-paste karna. Har system aur scenario different ho sakta hai. Hamesha samjho ki command kya kar raha hai.
2.  **Ignoring Error Messages:** Error messages ko dhyan se na padhna. Aksar error message mein hi solution ka hint chhupa hota hai.
3.  **Blaming Hardware First:** Problem hote hi hardware ko blame karna. Mostly problems software configuration mein hoti hain.
4.  **Not Checking Permissions/Ownership:** Filesystem ya service related issues mein galat permissions (`chmod`) ya ownership (`chown`) bhi culprit ho sakte hain.
5.  **Forgetting `systemctl daemon-reload`:** Jab bhi aap service unit files edit karte ho (`/etc/systemd/system/*.service`), toh `systemctl daemon-reload` run karna mat bhoolo, varna systemd ko naye changes ka pata nahi chalega.
6.  **Editing `fstab` without Verifying UUIDs/Labels:** Galat UUID ya LABEL se `fstab` entry banana jo system ko boot-time pe emergency mode mein le jaye. Hamesha `blkid` se verify karo.
7.  **Running `fsck`/`xfs_repair` on Mounted Filesystems:** Ye data loss ka karan ban sakta hai. Hamesha filesystem ko unmount karo pehle. Agar root filesystem hai, toh rescue mode se ya read-only mount karke hi repair karo.

---

Ye troubleshooting marathon ek continuous learning process hai. Jitna zyada aap practice karoge, utna behtar hote jaoge. RHCSA aur RHCE exam mein ye skills aapko bahut kaam aayengi. All the best!
