Day 43: Error handling, blocks, rescue
---
नमस्ते aur swagat hai aap sabhi ka is comprehensive RHCSA + RHCE training lesson mein! Aaj hum ek bahut hi crucial topic cover karenge: **"Error handling, blocks, and rescue"**. Yeh woh skills hain jo aapko ek experienced Linux admin banati hain, kyunki systems hamesha perfect nahi hote aur problems aati rehti hain. Toh chaliye shuru karte hain!

---

## **Error Handling, Blocks, Rescue: Ek Comprehensive Guide**

Ek Linux system admin ki life mein sabse common aur stressful situations tab aati hain jab system crash ho jaaye, boot na ho, ya koi critical service kaam karna band kar de. Aise mein, aapko pata hona chahiye ki problem ko kaise diagnose karein, data blocks ke issues ko kaise handle karein, aur system ko kaise rescue karein. Yeh lesson aapko inhi sab cheezon ke liye prepare karega.

---

### **1. Concept Explanation (Hinglish)**

Chaliye pehle in concepts ko detail mein samajhte hain:

#### **Error Handling (गलती को संभालना)**

Error handling ka matlab hai system mein hone wali galtiyon (errors) ko identify karna, unko samajhna, aur unhe theek karne ke liye necessary steps lena. Linux systems bahut detailed logs maintain karte hain jo aapko bataate hain ki kya galat ho raha hai.

*   **System Errors:** Ye software ya hardware se related ho sakte hain. Jaise, ek disk sector corrupt ho gaya, memory error aa gaya, ya koi process crash ho gaya.
*   **Application Errors:** Jab koi specific application theek se kaam nahi kar rahi. Uske apne logs hote hain.
*   **Filesystem Errors:** Yeh sabse common aur critical errors mein se ek hain. Jab filesystem ka structure corrupt ho jata hai, toh files read/write nahi ho paati. Iski wajah power failure, improper shutdown, ya bad blocks ho sakte hain.

Errors ko samajhna pehli seedhi hai solution tak pahunchne ki. Iske liye hum system logs aur messages par depend karte hain.

#### **Blocks (ब्लॉक्स)**

Storage devices, jaise ki hard drives ya SSDs, data ko fixed-size chunks mein store karte hain jinhe **blocks** kehte hain.

*   **Logical Blocks vs. Physical Blocks:** Operating system logical blocks ke terms mein data manage karta hai, jabki physical blocks actual hardware par hote hain. Ek filesystem, jaise EXT4 ya XFS, in blocks ko organize karta hai files aur directories ke liye.
*   **Inodes:** Har file aur directory ka ek inode hota hai jo uske metadata store karta hai (ownership, permissions, timestamps, aur woh kaun se data blocks use kar raha hai).
*   **Superblock:** Yeh filesystem ki sabse important metadata store karta hai, jaise filesystem ka type, size, block size, inode count, free blocks ki information, etc. Agar superblock corrupt ho jaaye, toh poora filesystem unreadable ho sakta hai.
*   **Bad Blocks:** Jab ek storage device ka koi physical block damage ho jata hai aur usse data reliably read ya write nahi ho pata, toh use **bad block** kehte hain. Bad blocks hone se data corruption ho sakta hai aur system instability bhi. Inhe identify karke mark karna zaroori hai taki OS unhe use na kare.

#### **Rescue (बचाव)**

Rescue ka matlab hai ek damaged, unbootable, ya inaccessible Linux system ko recover karna. Yeh ek critical skill hai. System ko rescue karne ke liye hum usually ek special environment mein boot karte hain.

*   **Single-User Mode (Runlevel 1 / Rescue Target):** Yeh ek minimal environment hai jismein system boot hota hai, networking aur multi-user services start nahi hoti. Iska primary use system files ko repair karna, root password reset karna, ya configuration errors theek karna hota hai.
*   **Emergency Mode (Emergency Target):** Jab system critical filesystems ko mount nahi kar paata, toh woh emergency mode mein chala jaata hai. Yahan par aapko ek shell milta hai jahan aap basic troubleshooting kar sakte ho. Filesystems read-only mount ho sakte hain, ya bilkul mount hi na ho.
*   **Boot Parameters (`rd.break`, `init=/bin/bash`):** Yeh GRUB menu se directly kernel ko extra instructions dene ka tareeka hai. Inka use karke aap boot process ko break kar sakte ho aur ek shell prompt par pahunch sakte ho, jisse aap system ko recover kar sako, jaise root password reset karna.
*   **Live CD/USB:** Extreme cases mein, jab system bilkul boot na ho, toh aap ek Linux Live CD ya USB se boot karke damaged system ke filesystems ko mount aur repair kar sakte ho.

---

### **2. Key Linux Commands (Hinglish)**

Yahan kuch important commands hain jo error handling, blocks, aur rescue mein kaam aate hain:

*   **`dmesg`**
    *   **Explanation:** `dmesg` command kernel ring buffer ke messages display karta hai. Jab system boot hota hai, ya koi hardware event (jaise disk error, USB connect/disconnect) hota hai, toh kernel un messages ko is buffer mein store karta hai. Yeh hardware-related errors aur kernel-level warnings ko check karne ke liye bahut useful hai.
    *   **Usage:** `dmesg`, `dmesg -T` (timestamp ke liye), `dmesg | grep -i error`
    *   **Hinglish:** Yeh command boot ke time ke saare kernel messages dikhata hai. Agar aapka system boot hone mein problem kar raha hai ya koi hardware issue hai, toh isse aapko hints milenge.

*   **`journalctl`**
    *   **Explanation:** `systemd` ke aane ke baad, `journalctl` systemd journal ke logs ko manage aur query karta hai. Yeh `dmesg` se zyada comprehensive hai aur kernel messages ke alawa system services, applications, aur user activity ke logs bhi dikhata hai.
    *   **Usage:** `journalctl`, `journalctl -f` (follow logs), `journalctl -p err` (sirf errors dikhao), `journalctl -u httpd` (specific service ke logs)
    *   **Hinglish:** Modern Linux systems mein, yeh aapka primary tool hai logs check karne ke liye. Dmesg se bhi zyada detailed aur organized logs yahan milte hain, services ke errors wagera sab.

*   **`fsck` (filesystem check)**
    *   **Explanation:** `fsck` command filesystems ko check aur repair karta hai. Yeh various filesystem types (ext2/3/4, XFS, etc.) ke liye specific checkers (`e2fsck`, `xfs_repair`) ko call karta hai. **Bahut important:** `fsck` ko hamesha unmounted filesystems par run karein, warna data corruption ho sakta hai! Agar root filesystem par `fsck` karna hai, toh system ko single-user/rescue mode mein boot karna padta hai.
    *   **Usage:** `fsck /dev/sda1`, `fsck -y /dev/sda1` (automatically yes for all questions), `e2fsck -f /dev/sda1` (force check, even if clean)
    *   **Hinglish:** Jab aapka filesystem corrupt ho jaaye (jaise power cut se), toh yeh command use karke aap usko repair kar sakte ho. Yaad rakho, mount kiye hue filesystem par ise kabhi mat chalao!

*   **`badblocks`**
    *   **Explanation:** `badblocks` utility disk par bad sectors ya blocks ko scan karta hai. Yeh typically `fsck` ya `e2fsck` ke saath use hota hai taki bad blocks ko filesystem ki bad block list mein add kiya ja sake. Isse OS un blocks ko future mein use nahi karega.
    *   **Usage:** `badblocks -v /dev/sda1` (verbose), `badblocks -wsv /dev/sda1` (destructive write test - **caution!** data erase ho jayega), `badblocks -o bad_blocks.txt /dev/sda1` (output to a file)
    *   **Hinglish:** Agar aapko lagta hai ki aapki hard drive mein physical damage hai, toh yeh command bad blocks ko dhundhta hai. Isko use karne se pehle backup zaroor lein, especially `-w` option ke saath.

*   **`debugfs`**
    *   **Explanation:** `debugfs` ek interactive filesystem debugger hai. Isse aap EXT2/3/4 filesystems ke internal structure ko investigate kar sakte hain, jaise inodes, superblocks, directories. Advanced troubleshooting ke liye useful hai.
    *   **Usage:** `debugfs /dev/sda1`, phir prompts par commands (e.g., `stat `, `ls -l`, `dump  `)
    *   **Hinglish:** Yeh ek gehra tool hai filesystem ko analyze karne ke liye. Jab `fsck` bhi kaam na kare aur aapko filesystem ke internals mein jhaankna ho, tab yeh kaam aata hai.

*   **`tune2fs`**
    *   **Explanation:** `tune2fs` utility ext2/3/4 filesystems ke tunable parameters ko modify karta hai. Jaise, `fsck` interval set karna, error behaviour change karna, journal options set karna.
    *   **Usage:** `tune2fs -l /dev/sda1` (list superblock info), `tune2fs -c 1 /dev/sda1` (force `fsck` after 1 mount), `tune2fs -O has_journal /dev/sda1` (add journal)
    *   **Hinglish:** Filesystem ki settings adjust karne ke liye yeh command use hota hai, jaise kitne boots ke baad `fsck` automatically chale.

*   **`mount -o remount,rw /`**
    *   **Explanation:** Rescue ya emergency mode mein aksar root filesystem read-only mount hota hai (`ro`). Changes karne ke liye, aapko use read-write (`rw`) mount karna padta hai.
    *   **Usage:** `mount -o remount,rw /`
    *   **Hinglish:** Jab aap rescue mode mein hote ho aur koi file edit karni hoti hai, toh aapko pehle root filesystem ko read-write banayein, nahi toh "read-only filesystem" error aayega.

*   **`chroot` (change root)**
    *   **Explanation:** `chroot` command ek process ke liye root directory ko change karta hai. Jab aap live CD ya rescue environment se boot karte ho aur damaged system ke files ko modify karna chahte ho (jaise password reset, configuration fix), toh aap damaged system ke root directory ko `chroot` karte ho. Isse aap commands run kar sakte ho jaise ki aap directly us damaged system par logged in ho.
    *   **Usage:**
        ```bash
        # Live CD/Rescue environment mein
        mount /dev/sda2 /mnt    # Damaged root partition ko mount karo
        mount --bind /dev /mnt/dev
        mount --bind /proc /mnt/proc
        mount --bind /sys /mnt/sys
        chroot /mnt /bin/bash   # Root ko /mnt par switch karo
        # Ab aap damaged system ke andar ho, jaise password reset kar sakte ho
        passwd root
        exit                   # Chroot environment se bahar niklo
        umount /mnt/sys /mnt/proc /mnt/dev /mnt
        reboot
        ```
    *   **Hinglish:** Agar aapka system boot nahi ho raha, aur aapne rescue mode ya live CD se boot kiya hai, toh damaged system ke filesystem ko `chroot` karke aap uske files ko theek kar sakte ho, jaise root password badalna ya fstab theek karna.

*   **`systemctl isolate rescue.target` / `emergency.target`**
    *   **Explanation:** `systemd`-based systems mein, `rescue.target` single-user mode ka equivalent hai aur `emergency.target` emergency mode ka. Yeh aapko boot time par ya running system par switch karne ki permission dete hain.
    *   **Usage (from GRUB):** GRUB menu mein `e` press karke kernel line mein `rhgb quiet` ko `systemd.unit=rescue.target` ya `systemd.unit=emergency.target` se replace karein.
    *   **Hinglish:** Yeh naye tarike hain single-user ya emergency mode mein jaane ke. GRUB se boot karte waqt inko specify karke aap system ko repair mode mein la sakte ho.

*   **Boot parameters (`single`, `rd.break`, `init=/bin/bash`)**
    *   **Explanation:** Ye GRUB menu se kernel ko diye jaane wale additional parameters hain.
        *   `single` (ya `1`) पुराने init systems में single-user mode में boot करने के लिए था, अब `systemd.unit=rescue.target` preferred है।
        *   `rd.break`: Initial RAM disk (initramfs) environment mein break karta hai, boot process ko interrupt karta hai, jisse aap root filesystem mount hone se pehle changes kar sakte ho (e.g., password reset).
        *   `init=/bin/bash`: Kernel ko batata hai ki `/sbin/init` (ya `/usr/lib/systemd/systemd`) ki jagah `/bin/bash` ko execute kare as the first process. Yeh aur bhi raw access deta hai, par `rd.break` zyada common aur secure hai password reset ke liye.
    *   **Usage (from GRUB):** GRUB menu mein `e` press karke kernel line edit karein.
    *   **Hinglish:** GRUB menu mein `e` dabakar aap kernel boot options ko edit kar sakte ho. `rd.break` aur `init=/bin/bash` jaise options aapko boot process mein rukne aur commands run karne ka mauka dete hain, especially root password reset ke liye.

---

### **3. Step-by-Step Examples (Hinglish)**

Chaliye kuch practical scenarios dekhte hain.

#### **Scenario 1: Identifying a Disk Error from Logs**

Aapka system slow chal raha hai aur kabhi-kabhi applications hang ho jaati hain. Aapko lagta hai disk issue ho sakta hai.

1.  **Check `dmesg` for immediate kernel messages:**
    ```bash
    dmesg | grep -i "error\|fail\|warn"
    ```
    *(Output kuch aisa ho sakta hai: `[ 123.456789] sd 0:0:0:0: [sda] tag#0 FAILED Result: hostbyte=DID_OK driverbyte=DRV_SENSE`, jo disk errors ko point karta hai.)*

2.  **Check `journalctl` for more comprehensive logs:**
    ```bash
    journalctl -p err -xb
    ```
    *(`-p err` sirf error messages dikhata hai, `-xb` current boot ke messages dikhata hai.)*
    *(Output mein aapko filesystem-related errors mil sakte hain, jaise `XFS (sda1): Metadata corruption detected at xfs_dinode_verify`)*

3.  **Identify the affected device/filesystem:** Logs se aapko `/dev/sda1` jaise device ya partition ka pata chal jayega.

#### **Scenario 2: Repairing a Corrupted Filesystem using `fsck`**

Maan lijiye aapka system reboot hone ke baad `/home` partition mount nahi ho raha aur `journalctl` mein `filesystem is dirty` ya `superblock corruption` jaise errors dikh rahe hain.

1.  **Boot into Rescue Mode:** GRUB menu mein `e` press karein, kernel line mein `rhgb quiet` ke baad `systemd.unit=rescue.target` add karein, aur `Ctrl+x` ya `F10` press karke boot karein.
    *(Aapko root password enter karne ke liye prompt kiya ja sakta hai.)*

2.  **Verify Filesystem Status:**
    ```bash
    lsblk # To find your /home partition, e.g., /dev/sdb1
    mount | grep /dev/sdb1 # Check if it's already mounted (it shouldn't be)
    ```

3.  **Run `fsck` on the unmounted partition:**
    ```bash
    fsck /dev/sdb1
    ```
    *(`fsck` aapko questions puch sakta hai, `y` type karein ya `fsck -y /dev/sdb1` use karein automatically repair ke liye.)*

4.  **Reboot:** `reboot` command run karein. System ab theek se boot hona chahiye aur `/home` mount ho jana chahiye.

#### **Scenario 3: Dealing with Bad Blocks**

`fsck` chalate waqt aapko bad block errors mile ya `dmesg` mein permanent bad sector errors dikh rahe hain.

1.  **Boot into Rescue Mode** (same as Scenario 2).

2.  **Identify the partition:** `/dev/sda1` assume karte hain.

3.  **Scan for bad blocks and output to a file:**
    ```bash
    badblocks -v /dev/sda1 > bad_blocks.txt
    ```
    *(`-v` verbose output deta hai.)*

4.  **Inform `e2fsck` about the bad blocks:**
    ```bash
    e2fsck -l bad_blocks.txt /dev/sda1
    ```
    *(`-l` option `e2fsck` ko bad block list file deta hai, aur woh un blocks ko filesystem ki internal bad block list mein add kar deta hai taki unka use na ho.)*

5.  **Reboot:** `reboot`

#### **Scenario 4: Recovering a Forgotten Root Password**

Yeh bahut common scenario hai!

1.  **Reboot the system.** GRUB menu aate hi `e` press karein.

2.  **Edit GRUB entry:** Us line ko dhundo jo `linux` ya `linux16` se start hoti hai.
    *   Us line ke end mein ya `rhgb quiet` ke baad `rd.break` add karein.
    *   (Ya `init=/bin/bash` bhi use kar sakte hain, par `rd.break` preferred hai.)

3.  **Boot with changes:** `Ctrl+x` ya `F10` press karein.

4.  **You are now in `initramfs` prompt (switch_root:/#):**
    *   Root filesystem read-only mount hota hai. Ise read-write mount karein:
        ```bash
        mount -o remount,rw /sysroot
        ```
    *   `chroot` into the mounted system:
        ```bash
        chroot /sysroot
        ```
    *   Password reset karein:
        ```bash
        passwd root
        ```
        *(Naya password type karein aur confirm karein.)*

    *   SELinux context update karein (Agar SELinux enforcing hai):
        ```bash
        touch /.autorelabel
        ```
        *(Isse next boot par SELinux poore filesystem ko relabel karega, jo thoda time consuming ho sakta hai, but zaruri hai warna SELinux errors aa sakte hain.)*

5.  **Exit and Reboot:**
    ```bash
    exit       # Chroot environment se bahar
    exit       # initramfs se bahar nikal kar system ko reboot karega
    ```
    Ab aap naye root password se login kar sakte ho.

#### **Scenario 5: Fixing a Corrupted `/etc/fstab` Entry**

Suppose aapne `/etc/fstab` mein galat entry kar di aur system boot nahi ho raha, "Dependency failed for /home" jaise error message de raha hai.

1.  **Reboot system.** GRUB menu mein `e` press karein.

2.  **Edit GRUB entry:** `linux` ya `linux16` line ke end mein `systemd.unit=emergency.target` add karein.

3.  **Boot:** `Ctrl+x` ya `F10`.

4.  **Emergency Mode:** Aapko ek prompt milega (`Press Enter for maintenance (or type Control-D to continue)`). `Enter` press karein.
    *   Root password puchega, enter karein.
    *   Aapko ek shell prompt (`sh-5.x#`) mil jayega.

5.  **Mount root filesystem as read-write:**
    ```bash
    mount -o remount,rw /sysroot
    ```
    *Agar `/sysroot` mount nahi hai toh pehle `mount /dev/mapper/centos-root /sysroot` karein (agar LVM use ho raha hai) ya jo bhi aapka root partition hai.*

6.  **Edit `/etc/fstab`:**
    ```bash
    vi /sysroot/etc/fstab
    ```
    *Galat entry ko comment out (`#`) ya correct karein.*

7.  **Save aur Exit:** `vi` se save karke exit karein (`:wq`).

8.  **Reboot:**
    ```bash
    exit
    reboot
    ```
    System ab theek se boot ho jana chahiye.

---

### **4. Practice Tasks (Hinglish)**

Yeh exercises aapko hands-on experience denge. Inhe ek Virtual Machine (VM) mein perform karein, real system par nahi!

**Task 1: Corrupt and Repair a Filesystem**

1.  **Ek naya partition banao:** `fdisk` ya `gdisk` use karke ek chota sa partition banao (e.g., `/dev/sdb1` of 1GB).
    ```bash
    sudo fdisk /dev/sdb
    # Naya partition banao, type Linux. Write changes.
    sudo partprobe # kernel ko naye partition ki info do
    ```
2.  **Filesystem create karo:** Us par EXT4 filesystem banao.
    ```bash
    sudo mkfs.ext4 /dev/sdb1
    ```
3.  **Temporarily mount karo:** Use ek directory par mount karo.
    ```bash
    sudo mkdir /mnt/tempfs
    sudo mount /dev/sdb1 /mnt/tempfs
    ```
4.  **Kuch data copy karo:** Thodi files copy karo ismein (e.g., `/etc` se kuch files).
    ```bash
    sudo cp -r /etc/* /mnt/tempfs/
    ```
5.  **Unmount karo:**
    ```bash
    sudo umount /mnt/tempfs
    ```
6.  **Filesystem ko corrupt karo (simulated power failure):** **CAUTION: DO THIS ONLY ON A TEST PARTITION!**
    ```bash
    sudo dd if=/dev/zero of=/dev/sdb1 bs=1k count=1 seek=100
    # Isse block 100 par zero write ho jayega, jo critical superblock ya inode area ho sakta hai.
    ```
7.  **Ab `fsck` run karo:** `fsck /dev/sdb1`. Dekho kya errors aate hain aur unhe theek karo. `fsck -y /dev/sdb1` bhi try kar sakte ho.
8.  **Verify:** `mount /dev/sdb1 /mnt/tempfs` karke dekho files access ho rahi hain ya nahi.

**Task 2: Simulate Bad Blocks and Mark Them**

1.  **Task 1 ka `/dev/sdb1` use karo.**
2.  **`badblocks` se scan karo:**
    ```bash
    sudo badblocks -v /dev/sdb1
    # Note down any reported bad blocks (ya file mein save karo)
    ```
3.  **Generate a bad block file:** Apne aap se ek `bad_blocks.txt` banao aur usme ek do block numbers (jo `badblocks` ne detect kiye ho ya random values) add karo. Jaise:
    ```
    1234
    5678
    ```
4.  **`e2fsck` ko use karke bad blocks ko mark karo:**
    ```bash
    sudo e2fsck -l bad_blocks.txt /dev/sdb1
    ```
5.  **Verify:** `tune2fs -l /dev/sdb1 | grep -i bad` chalake dekho bad block count update hua hai ya nahi.

**Task 3: Change Root Password using `rd.break`**

1.  Apni VM ko reboot karo.
2.  `rd.break` method use karke root password change karo (Scenario 4 follow karo).
3.  Verify ki naye password se login ho raha hai.

**Task 4: Introduce an `fstab` Error and Fix it**

1.  `fstab` mein ek galat entry add karo (e.g., `/etc/fstab` mein ek non-existent device ko mount karne ki entry daal do ya ek existing entry mein filesystem type galat kar do, jaise `/dev/sda1 /mnt/test ext4defaults 0 0` mein `ext4defaults` ki jagah `ext8defaults` likh do).
    ```bash
    # Example:
    echo "/dev/sdz1 /nonexistent/mountpoint ext4 defaults 0 0" | sudo tee -a /etc/fstab
    ```
2.  System ko reboot karo.
3.  Dekho ki system boot hone mein problem kar raha hai aur emergency mode mein ja raha hai ya nahi.
4.  Emergency mode mein `fstab` entry ko theek karo (Scenario 5 follow karo).
5.  Verify ki system theek se boot ho raha hai.

---

### **5. Pro Tips / Common Mistakes (Hinglish)**

Yahan kuch important tips aur aisi galtiyan jinse aapko bachna chahiye:

*   **Backup, Backup, Backup! (Backup Hamesha Rakho):** Kisi bhi critical operation se pehle, especially disk related, hamesha apne data ka backup lein. Rescue operations mein data loss ka risk hota hai.
*   **Don't Run `fsck` on a Mounted Filesystem (Mounted Filesystem Par `fsck` Mat Chalao):** Yeh sabse badi galti hai! Isse data corruption ho sakta hai. Hamesha filesystem ko unmount karo ya system ko rescue mode mein boot karo.
*   **Understand Different Rescue Modes (Rescue Modes Ko Samjho):** `rescue.target`, `emergency.target`, `rd.break`, `init=/bin/bash` – har ek ka alag use case hai. Practice karo ki kab kaunsa use karna hai.
*   **Read Logs Carefully (Logs Dhyan Se Padho):** Error messages aapke sabse achhe dost hain. Har message ko padho aur Google search karo agar samajh nahi aa raha. `journalctl` ke filters ko use karna seekho.
*   **Practice in a VM (VM Mein Practice Karo):** Real production system par direct experiments mat karo. Virtual Machines aapko ek safe sandbox provide karti hain jahan aap jitni chahe utni galtiyan kar sakte ho aur unhe theek karna seekh sakte ho.
*   **Know Your System's Boot Process (System Ke Boot Process Ko Samjho):** GRUB, Kernel, initramfs, systemd – in sabki understanding aapko troubleshooting mein bahut help karegi.
*   **SELinux Context (SELinux Context Ka Dhyan Rakho):** Password reset ya file system repair ke baad, agar SELinux enforcing mode mein hai, toh `/.autorelabel` file create karna mat bhoolna. Agar nahi karoge, toh system boot hone ke baad bhi services ya files access nahi ho payengi.
*   **Be Patient (Dheere Dheere Kaam Karo):** Ghabrao mat. Troubleshooting mein patience bahut zaroori hai. Har step ko carefully perform karo aur cross-check karo.
*   **Document Your Steps (Steps Ko Document Karo):** Jo solutions aapko milte hain, unhe note down karo. Future mein similar problems aane par yeh reference kaam aayega.

---

I hope yeh comprehensive lesson aapko "Error handling, blocks, and rescue" ke bare mein ek solid understanding deta hai. Yeh skills aapko ek confident aur capable Linux administrator banayenge. Keep practicing! All the best!
