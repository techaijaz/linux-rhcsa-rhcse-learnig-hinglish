Day 26: Boot process, GRUB, kernel options
---
Namaste aur swagat hai aap sabhi ka is comprehensive RHCSA + RHCE training lesson mein! Aaj hum ek bahut hi critical aur foundational topic cover karne wale hain – **"Boot process, GRUB, and Kernel options."**

Agar aap ek Linux system administrator banna chahte hain, toh is topic ki deep understanding hona bahut zaroori hai. System boot nahi ho raha, ya kernel crash ho gaya – aisi situations mein aapki troubleshooting skills isi knowledge par depend karti hain. Chaliye shuru karte hain!

---

## RHCSA + RHCE Training: Boot Process, GRUB, & Kernel Options

### 1. Concept Explanation (Hinglish)

Sabse pehle, hum boot process ko step-by-step samajhte hain. Imagine kijiye aapne computer ka power button press kiya – wahan se lekar login prompt tak kya kya hota hai?

#### A. The Linux Boot Process (Ek Overview)

1.  **BIOS/UEFI Initialization:**
    *   Jab aap system on karte hain, toh sabse pehle **BIOS (Basic Input/Output System)** ya newer systems mein **UEFI (Unified Extensible Firmware Interface)** activate hota hai.
    *   Yeh ek self-test karta hai jise **POST (Power-On Self-Test)** kehte hain. Ismein CPU, RAM, aur basic hardware check hote hain.
    *   POST successful hone ke baad, BIOS/UEFI boot order ke hisaab se bootable device (jaise hard drive, SSD, USB) dhundhta hai.

2.  **Bootloader Stage 1 (MBR/GPT):**
    *   Bootable device milne par, BIOS/UEFI us device ke **Master Boot Record (MBR)** ya **GUID Partition Table (GPT)** ke pehle sector ko load karta hai.
    *   Is sector mein **Stage 1 Bootloader** hota hai (e.g., GRUB's first stage). Iska kaam bahut simple hai – disk par GRUB ka Stage 2 (core.img) kahan hai, bas itna pata lagana.

3.  **Bootloader Stage 2 (GRUB2):**
    *   Stage 1 bootloader locate karta hai aur load karta hai **GRUB2 (Grand Unified Bootloader version 2)** ka `core.img`.
    *   GRUB2 ek powerful bootloader hai jo filesystems ko samajh sakta hai. Yeh `/boot` partition mein available hota hai.
    *   GRUB2 apni configuration file, `/boot/grub2/grub.cfg` ko read karta hai. Yeh file batati hai ki system par kaun-kaun se kernels available hain, default boot entry kya hai, timeout kitna hai, aur kaun se kernel parameters use karne hain.
    *   Aapko boot time par ek menu dikhta hai jahan se aap operating system, ya alag alag kernel versions choose kar sakte hain.

4.  **Kernel Loading & `initramfs`:**
    *   GRUB2 menu se aap jo kernel choose karte hain, GRUB us kernel image (`vmlinuz-version`) aur uske corresponding **`initramfs` (initial RAM filesystem)** image (`initramfs-version.img`) ko RAM mein load karta hai.
    *   **Kernel:** Linux ka dil (heart) hai. Yeh hardware se interact karta hai, processes manage karta hai, memory control karta hai.
    *   **`initramfs`:** Yeh ek chhota, temporary root filesystem hai jo RAM mein load hota hai. Kernel ko apne actual root filesystem (jaise `/` partition) tak pahunchne ke liye kuch drivers ki zaroorat hoti hai (jaise storage controller drivers, LVM modules, encrypted disk modules). `initramfs` woh basic drivers provide karta hai. Jab kernel `initramfs` ki madad se actual root filesystem mount kar leta hai, tab `initramfs` ka kaam khatam ho jata hai.

5.  **Kernel Initialization & `systemd` (PID 1):**
    *   Kernel load hone aur root filesystem mount hone ke baad, kernel sabse pehle process **`systemd`** ko start karta hai. `systemd` ka **PID (Process ID) hamesha 1** hota hai.
    *   `systemd` Linux ka service manager hai. Yeh system ki saari services, daemons, aur processes ko manage karta hai.
    *   `systemd` apne target units (pehle ke 'runlevels' ki tarah) ko read karta hai (jaise `multi-user.target`, `graphical.target`) aur unke according services start karta hai.
    *   Finally, jab saari essential services start ho jaati hain, aapko login prompt milta hai (ya graphical desktop environment).

#### B. GRUB2 in Detail

**GRUB2** aapke system ka traffic controller hai jab baat booting ki aati hai.

*   **Configuration File:** GRUB2 ki main configuration file hai `/boot/grub2/grub.cfg`. **WARNING: Is file ko direct edit mat karna!** Yeh file `grub2-mkconfig` command se generate hoti hai. Iske liye primary source file hai `/etc/default/grub` aur `/etc/grub.d/` directory mein scripts.
*   **Boot Menu:** Jab system boot hota hai, GRUB aapko ek menu dikhata hai jahan se aap installed OSes aur alag alag kernel versions ke beech choose kar sakte hain.
*   **Kernel Parameters:** GRUB menu mein aap specific kernel entry ko select karke `e` press karke uske parameters ko edit kar sakte hain. Yeh changes temporary hote hain (sirf current boot ke liye). Common parameters hain:
    *   `ro` / `rw`: Root filesystem read-only / read-write mount karo. (Recovery ke liye `rw` zaroori hai.)
    *   `quiet`: Boot messages ko minimize karo.
    *   `rhgb` (Red Hat Graphical Boot): Splash screen dikhao.
    *   `single` / `emergency`: Single-user mode mein boot karo (admin tasks ke liye).
    *   `init=/bin/bash` (ya `init=/sysroot/bin/sh`): Directly bash shell par boot karo, `systemd` start mat karo. (Password reset ke liye helpful).
    *   `rd.break`: `initramfs` stage par hi shell prompt de deta hai. (Advanced troubleshooting).
    *   `console=ttyS0,115200`: Serial console par boot messages redirect karo.

#### C. Kernel Options / Parameters

Kernel parameters woh instructions hain jo hum kernel ko boot karte samay dete hain taaki woh particular tarike se behave kare.
*   **Troubleshooting:** Agar system boot nahi ho raha, ya koi driver problem hai, toh hum specific parameters use karke problem isolate ya fix kar sakte hain.
*   **Recovery:** Root password reset karna, ya filesystem check karna, in sabke liye recovery options (jaise `init=/bin/bash`, `rd.break`) lagaye jaate hain.
*   **Performance/Security:** Advanced users kuch parameters adjust karte hain performance tune karne ya specific security features enable/disable karne ke liye.

#### D. `initramfs` - The Pre-Kernel Environment

`initramfs` ka full form hai "initial RAM filesystem".
*   Yeh ek chhota sa, compressed filesystem image hota hai jismein basic utilities aur kernel modules hote hain.
*   Kernel boot hone se pehle, GRUB is `initramfs` ko RAM mein load karta hai.
*   `initramfs` ka main kaam hai kernel ko actual root filesystem (jahan aapka OS install hai) tak pahunchne mein help karna. Jaise, agar aapka root filesystem LVM par hai, ya encrypted hai (LUKS), ya koi unusual storage controller use kar raha hai, toh `initramfs` uske liye zaroori drivers load karta hai.
*   Ek baar root filesystem mount ho gaya, `initramfs` ka kaam khatam aur control kernel ko mil jata hai, jo phir `systemd` ko start karta hai.

---

### 2. Key Linux Commands (Hinglish)

Yahan kuch important commands hain jo boot process aur GRUB se related hain:

*   **`grub2-mkconfig -o /boot/grub2/grub.cfg`**:
    *   **Explanation:** Yeh command GRUB2 ki main configuration file (`/boot/grub2/grub.cfg`) ko generate karta hai. Jab bhi aap `/etc/default/grub` ya `/etc/grub.d/` mein koi change karte hain, toh is command ko run karna zaroori hai taaki changes apply hon.
    *   **Hinglish:** Is command se GRUB ki config file banti hai. Agar aapne GRUB ki settings badli hain, toh isko run karna mat bhoolna, nahi toh changes apply nahi honge.
    *   **RHCE Tip:** UEFI systems par path thoda different ho sakta hai, jaise `/boot/efi/EFI/redhat/grub.cfg` (system to system vary karta hai). Best practice hai `grub2-mkconfig > /boot/grub2/grub.cfg` (BIOS) or `grub2-mkconfig > /boot/efi/EFI//grub.cfg` (UEFI). Ya phir `grub2-mkconfig` without `-o` aur output redirect kar do.

*   **`grub2-install /dev/sdX`**:
    *   **Explanation:** Yeh command GRUB2 bootloader ko specify kiye gaye disk (`/dev/sdX` - poori disk, not a partition) ke MBR/GPT mein install karta hai. Agar aapka bootloader corrupt ho gaya hai, ya aapne naya disk install kiya hai, toh iska use hota hai.
    *   **Hinglish:** Agar aapka GRUB bootloader crash ho gaya hai ya naye disk par install karna hai, toh is command se GRUB ko disk par likha jata hai. `/dev/sdX` ki jagah apni bootable hard disk ka naam dena (jaise `/dev/sda`).
    *   **RHCE Tip:** UEFI systems par `grub2-install` ke liye EFI System Partition (ESP) mount hona chahiye, aur command `grub2-install --target=x86_64-efi --efi-directory=/boot/efi /dev/sdX` type ka ho sakta hai.

*   **`grubby --default-kernel`**:
    *   **Explanation:** `grubby` Red Hat-based systems par kernel entries ko manage karne ka ek high-level tool hai. `grubby --default-kernel` current default kernel ko dikhata hai.
    *   **Hinglish:** Red Hat systems par kernels ko manage karne ka yeh ek smart tool hai. Isse aap dekh sakte ho ki abhi kaunsa kernel default boot hoga.

*   **`grubby --set-default=/boot/vmlinuz-`**:
    *   **Explanation:** Specify kiye gaye kernel ko default boot entry set karta hai.
    *   **Hinglish:** Is command se aap bata sakte ho ki next boot mein kaunsa specific kernel version default start hona chahiye.

*   **`grubby --update-kernel=ALL --args=""`**:
    *   **Explanation:** Saare installed kernels ke liye persistent kernel parameters add karta hai. Parameters `GRUB_CMDLINE_LINUX` variable mein add ho jaate hain `/etc/default/grub` mein (internally).
    *   **Hinglish:** Agar aapko koi kernel parameter hamesha ke liye apply karna hai (sab kernels par), toh is command ka use karo. Jaise `grubby --update-kernel=ALL --args="console=ttyS0"` serial console ke liye.

*   **`dracut -f -v /boot/initramfs-.img `**:
    *   **Explanation:** Yeh command `initramfs` image ko rebuild karta hai. `-f` force karta hai overwrite, `-v` verbose output deta hai. `kernel_version` usually `uname -r` se milta hai.
    *   **Hinglish:** Jab aapko naye drivers add karne hain, ya `initramfs` corrupt ho gaya hai, toh is command se naya `initramfs` image banate hain.

*   **`cat /etc/default/grub`**:
    *   **Explanation:** GRUB ke default settings ko dekhta hai. Yahi woh file hai jahan aap `GRUB_TIMEOUT`, `GRUB_CMDLINE_LINUX`, `GRUB_DEFAULT` jaise variables edit karte hain.
    *   **Hinglish:** GRUB ki basic settings yahan hoti hain. Yahi file edit karke aap GRUB ka behavior change karte ho (lekin `grub2-mkconfig` run karna mat bhoolna).

*   **`cat /proc/cmdline`**:
    *   **Explanation:** Running kernel ke boot arguments (parameters) dikhata hai. Isse aap verify kar sakte hain ki aapne jo parameters set kiye the woh apply hue ya nahi.
    *   **Hinglish:** Current running kernel ko kya kya boot arguments mile hain, woh yahan dikh jayenge. Yeh verify karne ke liye achha hai ki aapke changes apply hue hain ya nahi.

*   **`systemctl get-default` / `systemctl set-default multi-user.target`**:
    *   **Explanation:** `systemd` ke default target (equivalent to old runlevels) ko dekhne aur set karne ke liye. `graphical.target` (GUI) aur `multi-user.target` (CLI) common hain.
    *   **Hinglish:** Isse aap system ka default boot mode set karte ho – CLI-based (`multi-user.target`) ya GUI-based (`graphical.target`).

---

### 3. Step-by-Step Examples

Chaliye kuch practical scenarios dekhte hain:

#### Scenario 1: Root Password Reset Karna (GRUB emergency mode se)

Yeh ek classic RHCSA question aur real-world problem hai. Agar aap root password bhool gaye hain, toh GRUB ki madad se use reset kar sakte hain.

1.  **System Reboot karein aur GRUB menu mein entry select karein:**
    *   System boot hote hi **GRUB menu** aayega.
    *   Default kernel entry ko select karein (highlight karein), aur **`e` key** press karein edit mode mein jaane ke liye.

2.  **Kernel parameters edit karein:**
    *   `linux` ya `linuxefi` se start hone wali line dhundhiye.
    *   Is line mein `ro` (read-only) ko dhundhiye aur use `rw` (read-write) mein badal dijiye.
    *   Isi line ke end mein add kijiye: `init=/sysroot/bin/sh` (ya `init=/bin/bash` agar `rd.break` use kar rahe hain).
        *   **Red Hat 7/8/9 ke liye preferred method hai `rd.break`**: `ro` ko `rw` change karne ki zaroorat nahi. Bas `linux` wali line mein `rd.break` add kar do.
        *   Example: `linux ... ro crashkernel=auto ... $vt_handoff` ke baad `rd.break` add karein.
    *   Updated line kuch aisi dikhegi:
        ```
        linux ... rw init=/sysroot/bin/sh  (old method)
        ```
        OR
        ```
        linux ... ro crashkernel=auto ... $vt_handoff rd.break (preferred method for RHEL7/8/9)
        ```

3.  **Boot karein aur root filesystem ko mount karein (if using `init=/sysroot/bin/sh`):**
    *   Changes karne ke baad, **`Ctrl+x`** ya **`F10`** press karein boot karne ke liye.
    *   Agar aapne `init=/sysroot/bin/sh` use kiya hai, toh aapko ek shell prompt milega. Yahan aapko root filesystem ko read-write mode mein mount karna padega:
        ```bash
        mount -o remount,rw /sysroot
        ```
    *   **`chroot`** karein root environment mein:
        ```bash
        chroot /sysroot
        ```
    *   **Agar `rd.break` use kiya hai (preferred method):**
        *   Aapko `switch_root:/#` prompt milega.
        *   Root filesystem read-write mount karein:
            ```bash
            mount -o remount,rw /sysroot
            ```
        *   `chroot` karein:
            ```bash
            chroot /sysroot
            ```

4.  **Password reset karein:**
    *   Ab aap root environment mein hain. Password change karein:
        ```bash
        passwd root
        ```
    *   Naya password enter karein aur confirm karein.

5.  **SELinux relabeling (Important!):**
    *   Agar SELinux enable hai, toh aapko `.autorelabel` file create karni hogi taaki next boot par SELinux context sahi ho. Agar yeh step bhool gaye toh system boot nahi hoga ya issues dega.
        ```bash
        touch /.autorelabel
        ```

6.  **Exit aur reboot karein:**
    *   `exit` command se `chroot` environment se bahar niklein.
    *   Ek aur `exit` command se ya `reboot -f` se system ko reboot karein.
    *   Ab aap naye root password se login kar sakte hain.

#### Scenario 2: Default Boot Kernel Change Karna

Maan lijiye aapne naya kernel install kiya, aur usmein koi problem hai. Aap wapas old stable kernel par boot karna chahte hain.

1.  **Available kernels list karein:**
    ```bash
    grubby --info=ALL
    ```
    Isse aapko saare installed kernels aur unke index milenge (e.g., `/boot/vmlinuz-4.18.0-193.el8.x86_64`).

2.  **Current default kernel dekhein:**
    ```bash
    grubby --default-kernel
    ```

3.  **New default kernel set karein:**
    Maan lijiye aapko `4.18.0-80.el8.x86_64` ko default set karna hai.
    ```bash
    grubby --set-default=/boot/vmlinuz-4.18.0-80.el8.x86_64
    ```

4.  **Verify karein:**
    ```bash
    grubby --default-kernel
    ```
    Output mein naya default kernel dikhna chahiye.

5.  **Reboot karein:** Next boot par system automatically choose kiye gaye kernel se boot hoga.

#### Scenario 3: Persistent Kernel Parameter Add Karna

Aap chahte hain ki aapka system hamesha verbose mode mein boot ho, ya koi specific hardware parameter pass karna hai.

1.  **Temporarily add karein (testing ke liye):**
    *   System boot par GRUB menu mein `e` press karein.
    *   `linux` line mein `rhgb quiet` parameters ko `console=tty0 loglevel=7` (ya jo bhi aapko chahiye) se replace kar dein, ya add kar dein.
    *   `Ctrl+x` ya `F10` se boot karein.
    *   Boot hone ke baad verify karein: `cat /proc/cmdline`

2.  **Permanently add karein:**
    *   **Method 1 (using `grubby` - Recommended for RHCSA/RHCE):**
        ```bash
        sudo grubby --update-kernel=ALL --args="console=tty0 loglevel=7"
        ```
        Isse `GRUB_CMDLINE_LINUX` variable `/etc/default/grub` mein update ho jayega aur `grub2-mkconfig` bhi run ho jayega internally.

    *   **Method 2 (Manual editing - for deep understanding):**
        ```bash
        sudo vi /etc/default/grub
        ```
        `GRUB_CMDLINE_LINUX` line dhundhiye:
        ```
        GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/rhel-swap rd.lvm.lv=rhel/root rd.lvm.lv=rhel/swap rhgb quiet"
        ```
        `rhgb quiet` ko hata kar `console=tty0 loglevel=7` add kar dein (ya jo bhi parameters aapko chahiye).
        ```
        GRUB_CMDLINE_LINUX="crashkernel=auto resume=/dev/mapper/rhel-swap rd.lvm.lv=rhel/root rd.lvm.lv=rhel/swap console=tty0 loglevel=7"
        ```
        Save aur exit karein.

        Ab changes apply karne ke liye `grub.cfg` ko rebuild karein:
        ```bash
        sudo grub2-mkconfig -o /boot/grub2/grub.cfg
        ```
        *   **UEFI systems par path change ho sakta hai!** Use `sudo grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg` or similar as per your system.

3.  **Reboot aur Verify karein:**
    ```bash
    sudo reboot
    ```
    Boot hone ke baad, check karein:
    ```bash
    cat /proc/cmdline
    ```
    Aapko `console=tty0 loglevel=7` dikhna chahiye.

#### Scenario 4: `initramfs` Image Rebuild Karna

Agar aapne koi naya hardware driver install kiya hai, ya `initramfs` corrupt ho gaya hai, toh aapko ise rebuild karna pad sakta hai.

1.  **Current kernel version pata karein:**
    ```bash
    uname -r
    ```
    Output: `4.18.0-193.el8.x86_64` (example)

2.  **`initramfs` image rebuild karein:**
    ```bash
    sudo dracut -f -v /boot/initramfs-$(uname -r).img $(uname -r)
    ```
    *   `-f`: Force overwrite karega existing image ko.
    *   `-v`: Verbose output.
    *   `$(uname -r)`: Current kernel version ko dynamically provide karega.
    *   **RHCE Tip:** Agar aapko koi specific module `initramfs` mein add karna hai (e.g., `iscsi`), toh `dracut --add "iscsi" ...` use kar sakte hain.

3.  **Reboot karein (agar zaroori ho):**
    Kuch changes ke liye reboot zaroori hota hai.

---

### 4. Practice Tasks (Lab Exercises)

Yeh exercises aapko hands-on experience denge. Ek Virtual Machine (VM) mein Red Hat Enterprise Linux (RHEL) ya CentOS/Fedora install karke in tasks ko perform karein.

1.  **Task 1: Root Password Reset Challenge**
    *   Apne VM mein root password set karein (jo aap bhoolne ka pretend kar sakte hain).
    *   System reboot karein. GRUB se `rd.break` method use karke root password reset karein.
    *   Ensure SELinux relabeling is done.
    *   Verify login with the new password.

2.  **Task 2: Default Kernel Swap**
    *   Ek naya kernel install karein (agar available hai). Ya phir, `yum install kernel-4.18.0-305.el8` (replace with an older version if you already have newer).
    *   `grubby --info=ALL` se available kernels dekhein.
    *   `grubby --set-default` command use karke older kernel ko default set karein.
    *   Reboot karein aur `uname -r` se verify karein ki system older kernel se boot hua hai.
    *   Wapas default kernel par set karein.

3.  **Task 3: Persistent Custom Kernel Parameter**
    *   Apne system mein `loglevel=7` aur `net.ifnames=0` (network interface names legacy style, e.g., `eth0` instead of `enp0s3`) kernel parameters ko permanently add karein.
    *   Iske liye `/etc/default/grub` ko edit karein (aur `grub2-mkconfig` run karein) **OR** `grubby` command ka use karein.
    *   Reboot karein aur `cat /proc/cmdline` se verify karein ki parameters apply ho gaye hain.
    *   Changes ko undo karein.

4.  **Task 4: `initramfs` Corruption Simulation (Advanced/RHCE)**
    *   Current `initramfs` file ka backup lein: `cp /boot/initramfs-$(uname -r).img /root/backup_initramfs.img`
    *   Current `initramfs` file ko rename kar dein (`mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r).img.bak`).
    *   System reboot karein. Kya hota hai? (System boot nahi hoga, GRUB prompt par ruk jayega ya kernel panic dega).
    *   GRUB menu se entry edit karke (`e` press karke), `init=/bin/bash` ya `rd.break` use karke emergency shell mein jayein.
    *   `dracut` command use karke `initramfs` image ko rebuild karein.
    *   Reboot karein aur verify karein ki system normally boot ho raha hai.

5.  **Task 5: GRUB2 Installation to a Secondary Disk (RHCE)**
    *   Apne VM mein ek secondary disk add karein (e.g., `/dev/sdb`). Partition na karein, bas disk add karein.
    *   `grub2-install /dev/sdb` command use karke GRUB2 bootloader ko secondary disk par install karein.
    *   Apne VM ki BIOS/UEFI settings mein jaakar boot order change karein taaki system secondary disk (`/dev/sdb`) se boot ho.
    *   Verify karein ki system successfully boot hota hai.
    *   Boot order wapas primary disk par set karein.

---

### 5. Pro Tips / Common Mistakes

#### Pro Tips:

*   **Backup is Your Best Friend:** Hamesha `/etc/default/grub` ki backup copy bana lein `cp /etc/default/grub /etc/default/grub.bak` before making any changes.
*   **Test Temporary Changes:** Koi bhi GRUB change permanently apply karne se pehle, use GRUB menu mein `e` press karke temporarily test karein. Agar woh change boot ko break karta hai, toh ek simple reboot se system wapas pehle jaisa ho jayega.
*   **Understand `initramfs`:** Encrypted disks, LVM, ya custom storage drivers use karte waqt `initramfs` ka role bahut critical hota hai. Agar boot issues aayein toh `dracut` ko troubleshoot karna seekhein.
*   **SELinux Context:** Password reset ya file system recovery ke baad `.autorelabel` file create karna na bhoolein agar SELinux enabled hai. Nahi toh system boot nahi hoga ya login issues dega.
*   **GRUB Rescue Mode:** Agar GRUB bootloader completely corrupt ho jaye, toh aap GRUB rescue mode mein ja sakte hain. Wahan `ls`, `set prefix`, `set root`, `insmod normal`, `normal` jaise commands se system ko temporary boot kar sakte hain aur phir `grub2-install` aur `grub2-mkconfig` se GRUB ko repair kar sakte hain. Yeh RHCE level par bahut important hai.
*   **`grubby` vs. Manual Editing:** RHCSA/RHCE exams mein `grubby` tool ko prefer kiya jata hai kernel arguments aur default kernels ko manage karne ke liye. Manual editing `/etc/default/grub` mein bhi kar sakte hain, but `grubby` makes it safer and simpler.

#### Common Mistakes:

*   **Forgetting `grub2-mkconfig`:** `/etc/default/grub` edit karne ke baad `sudo grub2-mkconfig -o /boot/grub2/grub.cfg` (ya relevant path for UEFI) command run karna bhool jaana. Isse aapke changes apply nahi honge.
*   **Directly Editing `grub.cfg`:** `/boot/grub2/grub.cfg` ko direct edit karna. Yeh file automatically generate hoti hai, aur next `grub2-mkconfig` run hone par aapke changes overwrite ho jayenge. Hamesha `/etc/default/grub` ya `/etc/grub.d/` scripts ko modify karein.
*   **Incorrect `grub2-install` Target:** `grub2-install /dev/sda1` (partition) ki jagah `grub2-install /dev/sda` (poori disk) karna. Bootloader disk ke MBR/GPT mein install hota hai, partition mein nahi.
*   **Typos in Kernel Parameters:** `init=/bin/bash` ki jagah `int=/bin/bash` likh dena. Chhoti si typo bhi boot ko break kar sakti hai. Double-check your parameters.
*   **Not Understanding `chroot`:** Recovery mode mein `chroot /sysroot` karne ka matlab hai aapka shell environment ab `/sysroot` ko apna root directory maan raha hai. Iske bina aap system files ko modify nahi kar payenge.
*   **Ignoring SELinux:** Password reset ya root filesystem files modify karne ke baad SELinux context ko relabel karna bhool jaana.

---

Umeed hai yeh comprehensive lesson aapko Linux boot process, GRUB, aur kernel options ki gehri samajh dega. Ismein diye gaye commands aur practice tasks aapko RHCSA aur RHCE exams ke liye acchi tarah prepare karenge. Happy learning!
