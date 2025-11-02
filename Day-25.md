Day 25: SELinux management (enforcing, permissive, booleans)
---
Namaste students aur future Linux Gurus! Aaj hum ek bahut hi crucial topic par discuss karenge jo aapke system ki security ko ek naya level deta hai – **SELinux management**. Yeh topic RHCSA aur RHCE dono exams ke liye bahut important hai, aur real-world scenarios mein aapki problem-solving skills ko bhi boost karega.

Chaliye, shuru karte hain!

---

## RHCSA + RHCE Training Lesson: SELinux Management (Enforcing, Permissive, Booleans)

### Introduction

SELinux ya **Security-Enhanced Linux** Red Hat-based systems (jaise CentOS, Fedora, RHEL) mein ek additional layer of security provide karta hai. Jab hum Linux mein permissions (rwx) ki baat karte hain, toh woh **DAC (Discretionary Access Control)** hota hai, jismein user ya owner decide karta hai ki kaun kis file ko access karega. Lekin SELinux isse ek step aage jaakar **MAC (Mandatory Access Control)** implement karta hai, jahan kernel-level policy enforce ki jaati hai. Iska matlab hai ki even if root user try kare kisi unauthorized kaam ko karne ka, SELinux use rok sakta hai agar woh defined policy ke against ho.

Is lesson mein, hum SELinux ko kaise manage karte hain, uske different modes, aur booleans ko kaise use karte hain, yeh sab sikhenge.

---

### 1. Concept Explanation (Hinglish)

#### A. What is SELinux?

SELinux ek security architecture hai jo Linux kernel mein integrate kiya gaya hai. Iska primary goal hai processes aur files ke access control ko aur zyada granular aur secure banana. Yeh policies define karta hai jo system resources (files, directories, ports, processes, etc.) ko kaise access kiya ja sakta hai, yeh govern karti hain.

*   **Socho aise:** Aapka ghar (Linux system) hai. Usmein darwaze, khidkiyan hain (standard permissions – DAC). Lekin SELinux ek additional security guard hai (MAC) jo har cheez par nazar rakhta hai, aur policy follow na hone par rok deta hai, irrespective of who is trying to access (even root).

#### B. Why SELinux is Important? (DAC vs MAC)

Standard Linux permissions (rwx) DAC hain. Iska matlab hai ki ek user apni files par control rakhta hai. Agar ek program `root` privilege se run ho raha hai aur usmein koi vulnerability hai, toh woh poore system ko compromise kar sakta hai.

SELinux is problem ko solve karta hai. Yeh har process aur file ko ek **security context** assign karta hai. Policies define karti hain ki ek specific context wala process kis specific context wali file ko access kar sakta hai.

*   **Example:** Agar aapka web server (Apache/Nginx) compromise ho jata hai, toh DAC ke hisaab se attacker web server ki permissions ka use karke system mein spread ho sakta hai. Lekin SELinux ke saath, web server process (jiska context `httpd_t` hoga) sirf `httpd_sys_content_t` context wali files ko access kar sakta hai. Woh `root` user ki home directory ya system configuration files ko access nahi kar payega, bhale hi un par `rwx` permissions hon.

#### C. SELinux Components

1.  **Security Context:** Har file, directory, process, aur network port ko ek SELinux context milta hai. Yeh ek label hota hai jo us resource ki identity batata hai SELinux ko. Context ka format generally aisa hota hai: `user:role:type:level`.
    *   `user`: SELinux user (e.g., `system_u`)
    *   `role`: SELinux role (e.g., `object_r` for files, `system_r` for processes)
    *   `type`: Sabse important part. Yeh define karta hai resource ka purpose (e.g., `httpd_sys_content_t` for web content, `sshd_t` for SSH daemon process). Policies isi type par based hoti hain.
    *   `level`: Multi-Level Security (MLS) ya Multi-Category Security (MCS) ke liye hota hai (generally `s0` ya `s0:c0.c1023` RHCSA/RHCE scope mein).

2.  **Policy:** Yeh rules ka ek set hota hai jo define karta hai ki kaun sa context kis context ke saath interact kar sakta hai aur kaise (read, write, execute, etc.). Red Hat-based systems mein generally `targeted` policy use hoti hai, jo common services ko protect karti hai.

3.  **Access Vector Cache (AVC):** Jab koi process kisi resource ko access karna chahta hai, toh kernel SELinux policy ko query karta hai. Result AVC mein cache ho jata hai taaki future requests faster hon. Agar access deny hota hai, toh woh "AVC denial" kehlata hai aur `audit.log` mein record hota hai.

#### D. SELinux Modes (Enforcing, Permissive, Disabled)

SELinux teen main modes mein operate karta hai:

1.  **Enforcing Mode:**
    *   **Yeh hai default aur sabse secure mode.** Is mode mein, SELinux policy puri tarah se enforce hoti hai. Agar koi operation policy ke against hai, toh SELinux use **block** kar deta hai aur ek "AVC denial" entry `audit.log` mein likhta hai.
    *   **Jaise:** Agar aapka web server aise files ko access karna chahe jo uske context ke hisaab se allowed nahi hain, toh woh operation fail ho jayega.

2.  **Permissive Mode:**
    *   Is mode mein, SELinux policy enforce nahi hoti, but violations ko still **log** kiya jata hai `audit.log` mein. Operations block nahi hote, sirf warnings generate hote hain.
    *   **Bahut useful for troubleshooting!** Agar aapko lagta hai ki koi application SELinux ki wajah se work nahi kar rahi, toh aap use permissive mode mein daalkar check kar sakte hain. Agar application chal jaati hai aur `audit.log` mein denials dikhte hain, toh problem SELinux hi hai.

3.  **Disabled Mode:**
    *   Is mode mein, SELinux poori tarah se off ho jata hai. Koi policy enforce nahi hoti, aur koi violations log bhi nahi hote.
    *   **Not recommended!** Yeh aapke system ki security ko significantly reduce kar deta hai. Sirf extreme cases mein ya tab use karein jab aapko confirmed ho ki SELinux aapki system boot hone mein problem kar raha hai.

#### E. SELinux Booleans

SELinux policy bahut rigid hoti hai. Kabhi-kabhi, humein system ki default policy mein thode changes karne hote hain without writing complex custom policies. Iske liye **SELinux Booleans** use hote hain.

*   Booleans essentially on/off switches hain jo policy ke certain parts ko modify karte hain.
*   **Example:** Default policy mein Apache web server ko home directories (`/home/user/public_html`) se files serve karne ki permission nahi hoti. Lekin agar aapko aisa karna hai, toh aap `httpd_enable_home_dirs` boolean ko `on` kar sakte hain. Isse Apache ko home directories access karne ki permission mil jaati hai without modifying the core policy.
*   Yeh bahut convenient hain common use cases ko handle karne ke liye.

---

### 2. Key Linux Commands (Hinglish)

Yahan important commands hain jo aapko SELinux management mein help karenge:

#### a. `sestatus`
*   **Kaam:** Current SELinux status, mode, policy, aur policy type check karta hai.
*   **Hinglish:** Yeh command aapko batayega ki SELinux on hai ya off, kis mode mein hai (enforcing/permissive), aur kaun si policy use ho rahi hai (targeted). Aapki pehli troubleshooting step yahi honi chahiye.

#### b. `getenforce`
*   **Kaam:** Sirf current enforcing mode ko display karta hai (Enforcing, Permissive, Disabled).
*   **Hinglish:** Jaldi se dekhna hai ki system Enforcing mein hai ya Permissive, toh yeh command use karo.

#### c. `setenforce [0|1]`
*   **Kaam:** SELinux mode ko temporarily change karta hai.
    *   `setenforce 0`: Permissive mode mein change karta hai.
    *   `setenforce 1`: Enforcing mode mein change karta hai.
*   **Hinglish:** Yeh change sirf current boot ke liye hai. System reboot karne par `/etc/selinux/config` file mein jo mode set hai, wahi apply hoga. Troubleshooting ke liye bahut useful hai.

#### d. `/etc/selinux/config`
*   **Kaam:** SELinux mode ko permanently configure karta hai.
*   **Hinglish:** Yeh configuration file hai jahan aap `SELINUX=` line mein `enforcing`, `permissive`, ya `disabled` set karte hain. System reboot karne par yeh setting apply hoti hai.

    ```
    # This file controls the state of SELinux on the system.
    # SELINUX= can take one of these three values:
    #     enforcing - SELinux security policy is enforced.
    #     permissive - SELinux prints warnings instead of enforcing.
    #     disabled - No SELinux policy is loaded.
    SELINUX=enforcing  # <-- Change this for permanent mode
    # SELINUXTYPE= can take one of these two values:
    #     targeted - Only targeted network daemons are protected.
    #     mls - Multi Level Security protection.
    SELINUXTYPE=targeted
    ```

#### e. `ls -Z ` / `ps -Z`
*   **Kaam:** Files, directories aur running processes ke SELinux security contexts ko display karta hai.
*   **Hinglish:** `-Z` option use karke aap dekh sakte hain ki kis file ya process ko kaun sa context assign kiya gaya hai. Context troubleshooting mein bahut crucial hai.

#### f. `chcon [OPTIONS]  `
*   **Kaam:** Files ya directories ke SELinux security context ko temporarily change karta hai.
    *   `-t `: Sirf type ko change karta hai.
    *   `--reference=`: Reference file ka context use karta hai.
    *   `-R`: Recursive change (directories ke liye).
*   **Hinglish:** Agar aapko kisi file ka context ek baar ke liye change karna hai, toh `chcon` use karo. Lekin yeh change persistent nahi hota – next `restorecon` ya file system relabel ke baad default context wapas aa jayega. **RHCSA level par `chcon` temporary testing ke liye theek hai, but for persistent changes, `semanage fcontext` is preferred.**

#### g. `restorecon [OPTIONS] `
*   **Kaam:** Files aur directories ke SELinux security contexts ko unki default policy value par restore karta hai.
    *   `-R`: Recursive restore (directories ke liye).
    *   `-v`: Verbose output (kya change hua, woh dikhayega).
*   **Hinglish:** Jab aap files copy ya move karte hain, toh unka context original location se inherit ho sakta hai, jo galat ho sakta hai. `restorecon` command un files ke context ko SELinux policy ke hisaab se correct kar deta hai. Nayi files ya directories banane ke baad bhi ise use karna helpful hai.

#### h. `semanage fcontext -a -t  ` (RHCE)
*   **Kaam:** SELinux policy mein permanent file context mappings add ya modify karta hai.
    *   `-a`: Add a new mapping.
    *   `-m`: Modify an existing mapping.
    *   `-d`: Delete a mapping.
    *   `-t `: Target SELinux type.
    *   ``: Regular expression jo files aur directories ko match karta hai.
*   **Hinglish:** Yeh `chcon` se zyada powerful aur persistent hai. Agar aapko ek custom directory (jaise `/srv/mywebapp`) ko web server content ke liye use karna hai, toh aap `semanage fcontext` se us path ke liye `httpd_sys_content_t` context permanently define kar sakte hain. Phir `restorecon -R /srv/mywebapp` chalane par woh context apply ho jayega aur reboots ke baad bhi maintain rahega.

    *   **RHCSA Note:** RHCSA level par `chcon` aur `restorecon` ka use zyaada poocha jata hai, lekin `semanage fcontext` RHCE ke liye crucial hai aur practical scenario mein best practice hai.

#### i. `semanage boolean -l`
*   **Kaam:** Saare SELinux booleans aur unki current (aur default persistent) values ko list karta hai.
    *   `-l -C`: Sirf changed booleans dikhayega.
*   **Hinglish:** Kaun-kaun se booleans available hain aur unki current setting kya hai, yeh janne ke liye yeh command use karo.

#### j. `getsebool `
*   **Kaam:** Ek specific boolean ki current value display karta hai.
*   **Hinglish:** Agar aapko sirf ek boolean ka status check karna hai, toh yeh command fast tareeka hai.

#### k. `setsebool [-P]  [on|off]`
*   **Kaam:** SELinux boolean ki value ko set karta hai.
    *   `-P`: Persistent change karta hai (i.e., reboot ke baad bhi value maintain rahegi).
*   **Hinglish:** Agar aapko kisi boolean ko `on` ya `off` karna hai, toh `setsebool` use karo. `-P` option lagana mat bhoolna agar aapko change permanent chahiye. Agar `-P` nahi lagaya toh change sirf temporary hoga.

#### l. `audit.log` (`/var/log/audit/audit.log`) / `journalctl -xe | grep avc`
*   **Kaam:** SELinux AVC denials aur other security related events ko log karta hai.
*   **Hinglish:** Jab bhi SELinux koi operation block karta hai ya permissive mode mein koi violation detect karta hai, toh woh `audit.log` mein ek entry banata hai. `grep avc` use karke aap specific SELinux denials ko filter kar sakte hain. Yeh troubleshooting ka pehla step hai.

#### m. `sealert -a /var/log/audit/audit.log` (RHCE)
*   **Kaam:** `audit.log` mein SELinux denials ko analyze karta hai aur human-readable suggestions provide karta hai. Requires `setroubleshoot-server` package.
*   **Hinglish:** Yeh command `audit.log` ko padhkar batata hai ki kya galat hua aur use theek karne ke liye kya karna chahiye (suggests `setsebool` ya `chcon` commands). RHCE level par yeh time-saving tool hai.

#### n. `audit2allow -a -M ` (RHCE)
*   **Kaam:** `audit.log` entries se custom SELinux policy modules generate karta hai.
*   **Hinglish:** Agar aapko `setsebool` se kaam nahi ban raha ya koi bahut custom requirement hai, toh `audit2allow` use karke aap khud ki SELinux policy banate hain. Yeh RHCE ka advanced topic hai.

    ```bash
    # Step 1: Denials collect karna
    # Step 2: Policy generate karna
    audit2allow -a -M mycustompolicy
    # Step 3: Policy compile aur install karna
    semodule -i mycustompolicy.pp
    ```

---

### 3. Step-by-Step Examples

Chaliye, kuch real-world examples dekhte hain:

#### Example 1: SELinux Mode Check and Change

1.  **Check current status:**
    ```bash
    sestatus
    # Output might look like:
    # SELinux status:       enabled
    # SELinuxfs mount:      /sys/fs/selinux
    # SELinux root directory: /etc/selinux
    # Loaded policy name:   targeted
    # Current mode:         enforcing
    # Mode from config file: enforcing
    # Policy MLS status:    enabled
    # Policy deny_unknown status: allow
    # Memory protection checking: actual (secure)
    # Max kernel policy version: 33
    ```

    ```bash
    getenforce
    # Output: Enforcing
    ```

2.  **Change to Permissive mode (temporary):**
    ```bash
    sudo setenforce 0
    getenforce
    # Output: Permissive
    sestatus | grep "Current mode"
    # Output: Current mode:         permissive
    ```

3.  **Change back to Enforcing mode (temporary):**
    ```bash
    sudo setenforce 1
    getenforce
    # Output: Enforcing
    ```

4.  **Change to Permissive mode (permanent):**
    *   `sudo vi /etc/selinux/config`
    *   Change `SELINUX=enforcing` to `SELINUX=permissive`.
    *   Save and exit.
    *   **Requires reboot for permanent change.** (Ya phir `setenforce 0` kar do current session ke liye).

#### Example 2: Troubleshooting File Access for Apache (Web Server)

Maana aapne Apache web server install kiya hai aur aap chahte hain ki woh `/var/www/custom_html` directory se web content serve kare. Aapne content daal diya, lekin browser mein "403 Forbidden" error aa raha hai.

1.  **Check SELinux mode:**
    *   `getenforce` -> Agar `Enforcing` hai, toh SELinux problem ho sakti hai.

2.  **Move to Permissive mode for troubleshooting (temporary):**
    *   `sudo setenforce 0`
    *   Ab browser refresh karo. Agar page dikh gaya, toh confirm ho gaya ki SELinux hi culprit hai.

3.  **Check SELinux contexts of the directory:**
    *   `ls -Zd /var/www/custom_html`
    *   `ls -Z /var/www/custom_html/index.html`
    *   **Expected context for web content:** `system_u:object_r:httpd_sys_content_t:s0`
    *   Agar aapka output kuch aisa aata hai (e.g., `default_t` ya `unlabeled_t`), toh context galat hai.
        ```
        drwxr-xr-x. root root system_u:object_r:default_t:s0 /var/www/custom_html
        -rw-r--r--. root root system_u:object_r:default_t:s0 /var/www/custom_html/index.html
        ```

4.  **Fix the contexts (temporary fix using `chcon` for testing):**
    *   Aap `/var/www` directory ke context ko reference le sakte hain.
    *   `sudo chcon --reference=/var/www -R /var/www/custom_html`
    *   Ya phir directly type specify kar sakte hain:
        `sudo chcon -R -t httpd_sys_content_t /var/www/custom_html`
    *   Fir se check karo contexts: `ls -Zd /var/www/custom_html`
    *   Ab browser check karo (SELinux still in Permissive).

5.  **Restore to Enforcing mode:**
    *   `sudo setenforce 1`
    *   Ab browser check karo. Chances hain ki ab bhi 403 error aayega agar aapne `semanage fcontext` use nahi kiya.

6.  **Correct way to fix (Persistent using `semanage fcontext` and `restorecon`):**
    *   **Step 1: Define persistent context mapping.**
        ```bash
        sudo semanage fcontext -a -t httpd_sys_content_t "/var/www/custom_html(/.*)?"
        # Explanation:
        # -a: Add a new entry
        # -t httpd_sys_content_t: Target type for web content
        # "/var/www/custom_html(/.*)?": This regex means /var/www/custom_html itself
        # and anything inside it.
        ```
    *   **Step 2: Apply the defined context to files.**
        ```bash
        sudo restorecon -Rv /var/www/custom_html
        # -R: Recursive
        # -v: Verbose (show changes)
        ```
    *   **Step 3: Verify the contexts.**
        ```bash
        ls -Zd /var/www/custom_html
        ls -Z /var/www/custom_html/index.html
        # Output should now show httpd_sys_content_t
        ```
    *   **Step 4: Ensure SELinux is Enforcing and test.**
        ```bash
        sudo setenforce 1
        # Now check your web page in the browser. It should work!
        ```

#### Example 3: Managing SELinux Booleans (Allowing FTP home directory access)

Maana aapne Pure-FTPd server set up kiya hai aur aap users ko unki home directories (`/home/user/`) mein FTP access dena chahte hain. Default SELinux policy isse allow nahi karti for security reasons.

1.  **Check relevant booleans:**
    *   Humein FTP aur home directories se related booleans chahiye. `semanage boolean -l | grep ftp` ya `grep home` karte hain.
    *   Aapko shayad `ftpd_full_access` ya `ftpd_anon_write` jaise booleans milenge. Humare case mein, `ftp_home_dir` ho sakta hai ya `allow_ftpd_full_access`. Let's assume `ftpd_full_access` is the one.
    *   `getsebool ftpd_full_access` (output will be `ftpd_full_access --> off`)

2.  **Enable the boolean (temporary):**
    *   `sudo setsebool ftpd_full_access on`
    *   `getsebool ftpd_full_access` (output will be `ftpd_full_access --> on`)
    *   Ab FTP access test karo. Agar chal gaya, toh boolean hi problem tha.

3.  **Make the change persistent:**
    *   `sudo setsebool -P ftpd_full_access on`
    *   `-P` option is important here for persistent change.
    *   Ab system reboot bhi kar doge toh bhi yeh boolean `on` rahega.

---

### 4. Practice Tasks

Yeh lo kuch exercises jo aap apne test environment mein try kar sakte ho:

1.  **SELinux Mode Management:**
    *   Apne system ka SELinux status check karo.
    *   SELinux ko `Permissive` mode mein change karo temporarily. Verify karo.
    *   Wapas `Enforcing` mode mein le aao. Verify karo.
    *   `/etc/selinux/config` file ko edit karke SELinux ko `Permissive` par set karo. System reboot karke verify karo ki woh `Permissive` mode mein boot hua hai. Fir wapas `Enforcing` kar do aur reboot.

2.  **Custom Web Content Directory:**
    *   Ek nayi directory banao: `/opt/mywebdata`.
    *   Usmein ek simple `index.html` file banao.
    *   Verify karo ki Apache (ya Nginx) is directory se files serve nahi kar pa raha hai (403 Forbidden). (Apache ko `/etc/httpd/conf/httpd.conf` mein `DocumentRoot` change karke ya `VirtualHost` banake configure karna padega).
    *   `semanage fcontext` aur `restorecon -Rv` ka use karke `/opt/mywebdata` aur uske contents ko correct web server context (`httpd_sys_content_t`) do.
    *   Verify karo ki ab web server files serve kar pa raha hai.

3.  **SELinux Boolean for FTP:**
    *   ProFTPD ya Pure-FTPd jaisa koi FTP server install karo (agar installed nahi hai).
    *   Check karo ki kya aap apne home directory (`/home/youruser/`) mein FTP access kar pa rahe ho. Chances hain ki SELinux allow nahi karega.
    *   `semanage boolean -l | grep ftp` se relevant boolean find karo (e.g., `ftpd_full_access` or `ftpd_anon_write`, or `ftp_home_dir`).
    *   Us boolean ko `on` karo using `setsebool -P`.
    *   Verify karo ki ab aap FTP se home directory access kar pa rahe ho.

4.  **Analyze and Fix an AVC Denial (RHCE flavor):**
    *   Ek simple shell script banao `test.sh` jismein ek command ho jo `/tmp` mein ek file banati ho.
    *   Ab is script ko `/root/` directory mein rakho aur execute karne ki koshish karo. (`/root` user ke liye `ssh_home_t` context hota hai, jo arbitrary script execution ke liye nahi bana hai).
    *   Chances hain ki permission denied hoga ya SELinux block karega.
    *   `journalctl -xe | grep avc` ya `cat /var/log/audit/audit.log | grep avc` se denial entries dekho.
    *   Agar `setroubleshoot-server` installed hai, toh `sealert -a /var/log/audit/audit.log` chalao. Suggestions dekho.
    *   Try to fix the issue using the suggested `chcon` command (temporary) or by moving the script to a more appropriate location (`/usr/local/bin` with `bin_t` context) and then using `restorecon`.

---

### 5. Pro Tips / Common Mistakes

#### Pro Tips:

*   **SELinux ko Disable Mat Karo (Unless Absolutely Necessary):** Yeh sabse badi mistake hai. Troubleshooting ke liye `Permissive` mode best hai. Disable karne se aapke system ki security bahut kam ho jaati hai.
*   **Audit Log Is Your Best Friend:** Jab bhi koi issue ho aur SELinux enable ho, sabse pehle `audit.log` ya `journalctl -xe | grep avc` check karo. Wahan aapko exact reason milega ki SELinux ne kya block kiya.
*   **`restorecon` Is Crucial After File Operations:** Files copy ya move karne ke baad, ya backups restore karne ke baad, hamesha `restorecon -Rv /path/to/files` chalao taaki contexts default aur correct ho jayein.
*   **`semanage fcontext` for Permanent Contexts:** `chcon` sirf temporary change karta hai. Agar aapko ek directory ke context ko permanently change karna hai taaki woh reboot ke baad bhi maintain rahe, toh `semanage fcontext` use karo, aur uske baad `restorecon` chalana mat bhoolna.
*   **Booleans First:** Agar koi application default policy mein kaam nahi kar rahi, toh pehle relevant boolean search karo aur use enable karne ki koshish karo (`setsebool -P`). Custom policy (`audit2allow`) banakar policy ko extend karna last resort hona chahiye.
*   **Understand `-P` with `setsebool`:** Hamesha yaad rakho ki `setsebool` command ke saath `-P` flag lagana zaroori hai agar aapko boolean change persistent chahiye.
*   **Install `policycoreutils-python-utils`:** Is package mein `semanage` aur `restorecon` jaise powerful tools hote hain. `setroubleshoot-server` RHCE ke liye bahut helpful hai.
    `sudo yum install policycoreutils-python-utils setroubleshoot-server`

#### Common Mistakes:

*   **Ignoring SELinux:** Log aksar SELinux ko pehle disable kar dete hain, jab unhein koi problem aati hai, without proper investigation.
*   **Using `chcon` for Persistent Changes:** `chcon` changes are not permanent. Reboot, `restorecon` or file system relabel will revert them.
*   **Forgetting `restorecon`:** `semanage fcontext` sirf policy rule define karta hai; woh actual files par context apply nahi karta. Uske liye `restorecon` chalana zaroori hai.
*   **Misinterpreting `audit.log`:** `audit.log` mein bahut entries hoti hain. `grep avc` use karke specific SELinux denials ko filter karna सीखें.
*   **Not Rebooting After `/etc/selinux/config` changes:** Mode change in `/etc/selinux/config` takes effect only after a reboot (or `setenforce` for immediate, temporary change).

---

Aapko SELinux ek challenge lag sakta hai shuru mein, lekin practice se aur in commands aur concepts ko samajhne se, yeh aapke system security arsenal ka ek bahut powerful tool ban jayega. Yaad rakho, troubleshooting mein sabse important hai systematic approach – `sestatus`, `getenforce`, `audit.log`, aur phir action!

All the best, aur keep exploring! Jai Hind!
