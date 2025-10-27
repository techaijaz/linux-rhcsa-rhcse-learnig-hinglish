Day 15: RPM, YUM, DNF, module streams
---
Namaste, future Linux Gurus!

Aaj hum ek bahut hi crucial topic cover karne wale hain jo aapko RHCSA aur RHCE exams clear karne mein aur real-world server management mein champion banayega: **"RPM, YUM, DNF, and Module Streams."**

Yeh topics Red Hat-based systems (jaise RHEL, CentOS, Fedora) mein software packages ko manage karne ka core hain. Agar aapko inki achhi pakad ho gayi, toh aapka server management ka experience bahut smooth ho jayega.

Chaliye shuru karte hain!

---

## 1. Concept Explanation (Hinglish)

### A. RPM (Red Hat Package Manager)

Socho, aapko apne system par koi software install karna hai. Pehle kya hota tha? Aap source code download karte, usse compile karte (`./configure`, `make`, `make install`), aur yeh process bahut time consuming aur error-prone hota tha.

**RPM** yahi problem solve karta hai. Yeh ek standard package format hai Red Hat-based distributions ke liye.

*   **Kya hai RPM?**
    *   RPM ek tarah ka "archive file" hai, bilkul jaise `.zip` ya `.tar.gz`. Lekin iske andar sirf files hi nahi hoti, isme software ke liye saari zaroori metadata (jaise software ka naam, version, kon banaya, kon si files hain, kis software pe depend karta hai) bhi hoti hai.
    *   Yeh `.rpm` extension ke saath aata hai.
*   **Kyon use karein RPM?**
    *   **Easy Installation:** Ek single command se software install ho jata hai.
    *   **Verification:** Aap verify kar sakte ho ki package files mein koi tampering toh nahi hui.
    *   **Uninstallation:** Clean tareeke se software ko remove kar sakte ho, bina koi junk file chode.
    *   **Upgrade/Downgrade:** Software ko easily update ya revert kar sakte ho.
*   **Mukhya samasya (Main Problem):** RPM khud dependency resolve nahi karta. Agar aapko `httpd` install karna hai, aur usse `apr` aur `apr-util` ki zaroorat hai, toh RPM aapko batayega ki dependencies missing hain, par unhe khud dhoond ke install nahi karega. Yeh kaam aapko manually karna padega. Yahi par entry hoti hai YUM/DNF ki.

### B. YUM (Yellowdog Updater, Modified)

RPM ki dependency problem ko solve karne ke liye **YUM** aaya. YUM ek high-level package management tool hai jo RPM ke upar kaam karta hai.

*   **Kya hai YUM?**
    *   YUM ek package manager hai jo **repositories** se packages fetch karta hai. Repositories essentially servers hote hain jahan saare RPM packages store hote hain aur unki metadata bhi.
    *   Yeh automatic **dependency resolution** karta hai. Aap `yum install httpd` likho, YUM khud hi `httpd` ki saari dependencies ko identify karega, unhe download karega, aur sahi order mein install karega.
*   **Kyon use karein YUM (RPM ke upar)?**
    *   **Automatic Dependency Resolution:** Sabse bada fayda. Aapko har dependency manually track nahi karni padegi.
    *   **Repository Management:** Aap multiple repositories configure kar sakte ho (jaise BaseOS, AppStream, EPEL).
    *   **System Updates:** Poore system ko single command se update kar sakte ho (`yum update`).
    *   **Package Search:** Packages ko search karna आसान ho jata hai.
*   **YUM ki limitations (aur DNF kyon aaya):** YUM Python 2 pe based tha, jo ab End-of-Life ho chuka hai. Iski performance bhi kabhi-kabhi slow hoti thi, khaaskar bade repositories ya complex dependency graphs ke saath.

### C. DNF (Dandified YUM)

**DNF** YUM ka successor hai. Red Hat Enterprise Linux 8 (RHEL 8) aur uske baad ke versions mein DNF default package manager hai. `yum` command ab DNF ka ek alias ban gaya hai, meaning `yum` type karne par internally DNF hi run hota hai.

*   **Kya hai DNF?**
    *   DNF ek next-generation package manager hai. Yeh YUM se bhi zyada robust, efficient aur modern hai.
    *   Yeh libsolv aur hawkey libraries ka use karta hai dependency resolution ke liye, jo isse kafi faster aur reliable banata hai.
    *   Python 3 par based hai.
*   **Kyon use karein DNF (YUM ke upar)?**
    *   **Improved Performance:** YUM se faster dependency resolution aur metadata processing.
    *   **Stronger API:** Developers ke liye better API provide karta hai.
    *   **Modular Design:** Modules aur streams ko support karta hai (iske baare mein hum aage padhenge).
    *   **Consistent Behavior:** Less prone to errors aur deadlocks.
    *   **Rich Features:** `dnf history`, `dnf provides`, `dnf group install` jaise aur bhi kayi advanced features.

### D. Module Streams

Yeh DNF ka ek bahut hi powerful feature hai jo RHEL 8 se introduce hua hai. Socho, aapko ek server par Python 3.6 chahiye aur dusre application ko Python 3.9. Pehle yeh mushkil hota tha, kyunki ek package ke multiple major versions ek saath install karna ya manage karna mushkil tha.

*   **Kya hain Module Streams?**
    *   **Module Streams** ek tarah se software ke different versions aur unki dependencies ka collection hain, jo ek saath manage kiye ja sakte hain.
    *   Yeh aapko ek hi OS version par alag-alag versions ke software (jaise Python, Node.js, PostgreSQL, Nginx) install aur use karne ki flexibility dete hain.
    *   Har "module" mein multiple "streams" ho sakti hain. Har stream ek specific major version (e.g., `python39` for Python 3.9) ko represent karti hai.
    *   Ek stream active hone par, us stream ke packages hi `dnf install` command ke through available hote hain.
*   **Kyon use karein Module Streams?**
    *   **Version Flexibility:** Ek OS par multiple major software versions run kar sakte ho.
    *   **Application Compatibility:** Different applications ki specific version requirements ko fullfill kar sakte ho.
    *   **Easier Management:** `dnf module` commands se in versions ko manage karna easy ho jata hai.
    *   **Reduced Conflicts:** Package conflicts ko kam karta hai, kyunki har stream ke packages ek dusre se alag hote hain.

**Summary:**

*   **RPM:** Low-level package format, dependency resolution nahi karta.
*   **YUM:** High-level package manager, RPM ke upar, dependency resolution karta hai, repositories se packages leta hai.
*   **DNF:** YUM ka modern successor, faster, more reliable, aur modularity support karta hai.
*   **Module Streams:** DNF ka ek feature jo single OS par multiple major software versions manage karne ki capability deta hai.

---

## 2. Key Linux Commands (Hinglish Explanations)

Chaliye, ab dekhte hain in sab se related important commands. Yaad rakhiye, RHEL 8/9 mein `yum` likhne par bhi `dnf` hi run hota hai, lekin hum `dnf` ko prefer karenge.

### A. RPM Commands

`rpm` command direct `.rpm` files ko manage karta hai.

*   `rpm -ivh `: **Install** karta hai ek RPM package.
    *   `-i`: install
    *   `-v`: verbose (details dikhayega)
    *   `-h`: hash marks (progress bar ke liye # dikhayega)
    *   **Caution:** Dependencies manually handle karni padengi.
*   `rpm -qa`: **Query All** installed RPM packages.
    *   `qa` means query all. Output mein saare installed packages ki list aayegi.
    *   Example: `rpm -qa | grep httpd` (httpd related packages dhundhne ke liye)
*   `rpm -qi `: Ek specific package ki **Query Info** dikhata hai.
    *   Details jaise Description, Version, Release, Install Date, Vendor, Size.
    *   Example: `rpm -qi httpd`
*   `rpm -ql `: Ek specific package ki **Query List** of files dikhata hai.
    *   Kon-kon si files is package ne install ki hain, unki list.
    *   Example: `rpm -ql httpd`
*   `rpm -qf `: Batata hai ki ek specific file kis package se belong karti hai.
    *   `f`: file.
    *   Example: `rpm -qf /etc/httpd/conf/httpd.conf`
*   `rpm -V `: Ek package ki **Verify** installation.
    *   Check karta hai ki package files mein koi changes toh nahi hue hain installation ke baad.
    *   Example: `rpm -V httpd`
*   `rpm -e `: Ek package ko **Erase** (uninstall) karta hai.
    *   `e`: erase.
    *   **Caution:** Agar is package par koi aur package depend karta hai, toh yeh fail ho jayega. Use `--nodeps` option se dependency checks ko bypass kar sakte ho, par yeh recommended nahi hai as it can break your system.
    *   Example: `rpm -e httpd`

### B. DNF (and YUM) Commands

DNF repositories se interact karta hai aur automatic dependency resolution karta hai.

*   `dnf install `: Ek package aur uski saari dependencies ko install karta hai.
    *   Example: `dnf install httpd`
*   `dnf remove `: Ek package aur uski jo dependencies ab kisi aur package ko zaroori nahi hain, unhe remove karta hai.
    *   Example: `dnf remove httpd`
*   `dnf update`: Saare installed packages ko latest version par update karta hai.
    *   **Critical for security and stability!**
    *   Example: `dnf update`
*   `dnf check-update`: Batata hai ki kon-kon se packages update hone ke liye available hain, par update nahi karega.
    *   Example: `dnf check-update`
*   `dnf upgrade`: `dnf update` ka alias hai, same kaam karta hai.
*   `dnf search `: Repositories mein packages ko search karta hai jinke naam ya description mein `keyword` ho.
    *   Example: `dnf search nginx`
*   `dnf list [installed|available|all] [glob_expression]`: Packages ko list karta hai.
    *   `dnf list installed`: Installed packages.
    *   `dnf list available`: Available packages in repositories.
    *   `dnf list all`: Sabhi packages (installed + available).
    *   `dnf list *httpd*`: Wildcard ke saath list karna.
*   `dnf info `: Ek package ki detailed information dikhata hai (similar to `rpm -qi`).
    *   Example: `dnf info httpd`
*   `dnf repolist [all|enabled|disabled]`: Configure kiye gaye repositories ki list dikhata hai.
    *   `dnf repolist`: Enabled repositories.
    *   `dnf repolist all`: Sabhi repositories (enabled + disabled).
*   `dnf history`: Package transactions (install, remove, update) ka record dikhata hai.
    *   `dnf history undo `: Ek specific transaction ko undo karta hai.
    *   `dnf history redo `: Ek specific transaction ko redo karta hai.
    *   `dnf history rollback `: System ko ek specific transaction state par wapas le jata hai.
*   `dnf provides `: Batata hai ki kon sa package ek specific file ya command provide karta hai.
    *   Example: `dnf provides /usr/bin/htpasswd` (httpd-tools package)
*   `dnf makecache`: Repository metadata cache ko rebuild karta hai. Fresh information ke liye use karte hain.
*   `dnf clean all`: Saare cached repository metadata aur packages ko delete karta hai. Disk space free karne ya corrupt cache issue fix karne ke liye.
*   `dnf downgrade `: Ek package ko uske previous version par downgrade karta hai.
    *   Example: `dnf downgrade httpd` (usually specific version bhi specify karna padta hai)
*   `dnf group install ""`: Related packages ke groups ko install karta hai.
    *   Example: `dnf group install "Development Tools"`
*   `dnf enable ` / `dnf disable `: Repositories ko enable/disable karta hai.
    *   Example: `dnf config-manager --set-enabled epel` (EPEL repo ko enable karne ke liye, `dnf-utils` package se aata hai)

### C. DNF Module Commands

Module streams ko manage karne ke liye specific DNF commands hain.

*   `dnf module list [module_name]`: Available modules aur unki streams ko list karta hai.
    *   `dnf module list`: Saare modules.
    *   `dnf module list python3`: Python 3 module ki streams.
*   `dnf module info [:stream]`: Ek specific module ya stream ki detailed information dikhata hai.
    *   Example: `dnf module info python3:3.9`
*   `dnf module enable :`: Ek specific module stream ko enable karta hai.
    *   Iske baad hi aap us stream se packages install kar paoge.
    *   Example: `dnf module enable python3:3.9`
*   `dnf module disable `: Ek module ko disable karta hai.
    *   Isse us module ki current active stream deactivate ho jati hai.
    *   Example: `dnf module disable python3`
*   `dnf module reset `: Ek module ko uski default stream par reset karta hai.
    *   Basically, active stream ko remove karke default stream ko activate karta hai.
    *   Example: `dnf module reset python3`
*   `dnf module install [:stream]`: Ek specific module stream se packages install karta hai. Agar stream specify nahi ki toh default ya enabled stream se install karega.
    *   Example: `dnf module install python3` (default stream se)
    *   Example: `dnf module install python3:3.9/devel` (3.9 stream ka `devel` profile install karega, profiles mein specific packages hote hain)

---

## 3. Step-by-Step Examples

Chaliye kuch practical scenarios mein in commands ko dekhte hain.

### Example 1: `httpd` Web Server Installation aur Basic Management

1.  **Check if `httpd` is already installed:**
    ```bash
    rpm -qa | grep httpd
    ```
    *(Agar kuch output nahi aaya, toh installed nahi hai.)*

2.  **Search for `httpd` package in repositories:**
    ```bash
    dnf search httpd
    ```
    *(Aapko `httpd.x86_64` aur `httpd-tools.x86_64` jaise packages dikhenge.)*

3.  **Install `httpd` package:**
    ```bash
    sudo dnf install httpd
    ```
    *(DNF automatically saari dependencies resolve karega aur install karega. 'y' press karke confirm karein.)*

4.  **Verify `httpd` installation and its files:**
    ```bash
    rpm -qi httpd
    rpm -ql httpd | head -n 10 # Pehle 10 files dekhein
    dnf info httpd # Similar to rpm -qi but DNF style
    ```

5.  **Check which package provides a specific file:**
    ```bash
    rpm -qf /etc/httpd/conf/httpd.conf
    # Ya DNF se:
    dnf provides /etc/httpd/conf/httpd.conf
    ```

6.  **Start and Enable `httpd` service:**
    ```bash
    sudo systemctl enable httpd --now
    sudo systemctl status httpd
    ```

7.  **Remove `httpd` package:**
    ```bash
    sudo dnf remove httpd
    ```
    *(DNF prompt karega ki kon-kon si dependencies bhi remove hongi.)*

### Example 2: Using DNF History to Undo/Redo

1.  **Install a package (e.g., `git`):**
    ```bash
    sudo dnf install git
    ```

2.  **View DNF history:**
    ```bash
    dnf history
    ```
    *(Aapko ek list dikhegi transactions ki, with IDs. Latest transaction top par hogi.)*

3.  **Identify the transaction ID for `git` installation.** Let's assume it's ID `5`.

4.  **Undo the `git` installation:**
    ```bash
    sudo dnf history undo 5
    ```
    *(DNF `git` ko uninstall kar dega aur uski un-needed dependencies ko bhi.)*

5.  **Verify `git` is gone:**
    ```bash
    dnf list installed git
    ```

6.  **Redo the `git` installation (agar zaroorat pade):**
    ```bash
    sudo dnf history redo 5
    ```
    *(DNF `git` ko phir se install kar dega.)*

### Example 3: Managing Module Streams (e.g., Python 3.9)

By default, RHEL 8/9 mein Python 3.6 ya 3.8 default stream hoti hai. Maan lijiye aapko Python 3.9 chahiye.

1.  **List available Python 3 module streams:**
    ```bash
    dnf module list python3
    ```
    *(Output mein aapko `python3.8`, `python3.9` jaise streams dikhenge, aur ek `[d]` (default) tag dikhega current default stream ke saamne.)*

    ```
    Name           Stream         Profiles                      Summary
    python3        3.6                                            Python 3.6
    python3        3.8 [d]        common [d], devel, build      Python 3.8
    python3        3.9            common [d], devel, build      Python 3.9
    ...
    ```
    Is example mein `python3.8` default (`[d]`) hai.

2.  **Enable Python 3.9 stream:**
    ```bash
    sudo dnf module enable python3:3.9
    ```
    *(Yeh aapko batayega ki pehle wali default stream (python3.8) disable ho jayegi. 'y' press karke confirm karein.)*

3.  **Verify that Python 3.9 is now the active stream:**
    ```bash
    dnf module list python3
    ```
    *(Ab `python3.9` ke saamne `[e]` (enabled) tag dikhega, aur `python3.8` ke saamne `[d]` hat jayega.)*

4.  **Install Python 3.9 packages:**
    ```bash
    sudo dnf install python3
    ```
    *(Ab `dnf install python3` command actual mein Python 3.9 packages install karega.)*
    ```bash
    python3 --version # Output should be Python 3.9.x
    ```

5.  **Reset to default stream (agar zaroorat pade):**
    Agar aapko wapas default stream (jo initial thi, `python3.8` in this example) par jana hai:
    ```bash
    sudo dnf module reset python3
    ```
    *(Yeh current active stream ko disable karega aur default stream ko activate karega.)*

6.  **Disable the module completely:**
    ```bash
    sudo dnf module disable python3
    ```
    *(Yeh module ki kisi bhi stream ko active nahi rakhega.)*

---

## 4. Practice Tasks (Lab Tasks)

Ab aapki baari hai! In tasks ko apne RHEL/CentOS 8/9 system par try karein.

1.  **Find & Install a Text Editor:**
    *   `dnf search nano` command se `nano` text editor ko search karein.
    *   `nano` package ko install karein.
    *   `rpm -qi nano` aur `rpm -ql nano` commands se iski details aur files dekhein.
    *   Ek file create karein `nano /tmp/test_file.txt`.
    *   `rpm -qf /usr/bin/nano` se confirm karein ki `nano` executable kis package se aata hai.
    *   `nano` package ko remove karein.

2.  **Explore Repository Information:**
    *   `dnf repolist` command se apne system par enabled repositories ki list dekhein.
    *   `dnf repolist all` se sabhi (enabled aur disabled) repositories dekhein.
    *   Apne system par EPEL (Extra Packages for Enterprise Linux) repository enable karein. (Hint: aapko `dnf-utils` package install karna pad sakta hai, phir `dnf config-manager --set-enabled epel` use karein). Agar EPEL install nahi hai toh pehle `sudo dnf install epel-release` karein.
    *   EPEL enable hone ke baad, `dnf repolist` se confirm karein ki `epel` repository visible hai.
    *   `dnf search htop` (htop EPEL mein available hota hai) command se `htop` package ko search karein.

3.  **DNF History Advanced Usage:**
    *   `sudo dnf install tmux` command se `tmux` package install karein.
    *   `dnf history` command se `tmux` installation ki transaction ID note karein.
    *   `sudo dnf history info ` (replace `` with actual ID) command se is transaction ki detailed info dekhein.
    *   Ek aur package install karein, jaise `sudo dnf install tree`.
    *   Ab `tmux` aur `tree` dono ke installation ko ek saath undo karein (hint: `dnf history rollback `). Verify karein ki dono packages remove ho gaye hain.

4.  **Module Stream Challenge (Node.js):**
    *   Check karein ki kon-kon se Node.js module streams available hain: `dnf module list nodejs`.
    *   Assume aapko Node.js version 18 install karna hai. Uske corresponding stream ko identify karein.
    *   `sudo dnf module enable nodejs:` command se us stream ko enable karein. (e.g., `nodejs:18`)
    *   `dnf install nodejs` command se Node.js install karein.
    *   `node --version` command se verify karein ki correct version install hua hai.
    *   Jab kaam ho jaye, toh `sudo dnf module reset nodejs` command se module ko default state par reset karein.

---

## 5. Pro Tips / Common Mistakes

Yeh kuch important tips hain aur kuch common mistakes jinhe aapko avoid karna chahiye:

*   **Always prefer `dnf` over `rpm` for installation/removal:** `rpm` direct files ko manage karta hai aur dependency resolution nahi karta. `dnf` automatic dependency resolution karta hai aur repositories se packages fetch karta hai. Direct `rpm` ka use tab hi karein jab aapko ek specific `.rpm` file install karni ho jo repository mein nahi hai, aur aapko uski dependencies manually resolve karne ka gyaan ho.
*   **Never use `rpm -e --nodeps` blindly:** `--nodeps` option dependency checks ko bypass karta hai. Agar aap kisi package ko uski dependencies ke bina remove karoge, toh dusre packages jo us par depend karte hain, woh break ho sakte hain. Only use this option if you absolutely know what you're doing.
*   **Keep your system updated:** `sudo dnf update` regularly run karna bahut zaroori hai. Yeh security patches aur bug fixes provide karta hai.
*   **Understand Repository Priority:** Agar multiple repositories mein ek hi package ke alag-alag versions hain, toh DNF `priority` settings ke hisab se package choose karta hai. Default RHEL repos ki priority high hoti hai. `dnf config-manager --set-enabled/disabled` ka use kar ke repos ko manage karein.
*   **`dnf clean all` vs `dnf makecache`:**
    *   `dnf clean all`: Saare cached repository metadata aur downloaded packages delete kar deta hai. Agar disk space kam ho ya cache corrupt ho, tab use karein. Next command par metadata phir se download hogi.
    *   `dnf makecache`: Metadata cache ko force full rebuild karta hai. Fresh metadata ki zaroorat hone par use karein.
*   **What if a package isn't found?**
    *   Check package name spelling: `dnf search ` se sahi naam dhoondein.
    *   Check enabled repositories: `dnf repolist` se dekhein ki aapka zaroori repository (jaise EPEL) enabled hai ya nahi.
    *   `dnf provides ` is your best friend: Agar aapko pata hai ki kaunsa command ya file chahiye, par package ka naam nahi pata, toh `dnf provides` use karein.
*   **Locking specific packages from updates:** Agar aap kisi package ko update nahi karna chahte, toh `dnf.conf` file mein `exclude=` directive add kar sakte ho.
    *   `sudo vi /etc/dnf/dnf.conf`
    *   `exclude=httpd*` (httpd aur usse related saare packages exclude kar dega)
*   **`dnf history rollback` is a lifesaver:** Agar kisi package installation/update ne system ko break kar diya hai, toh `dnf history rollback ` se previous stable state par wapas ja sakte ho. Always remember this!
*   **Module Stream Notation:** Jab bhi aap module commands use karein, `module_name:stream/profile` ka format yaad rakhein.
    *   `nodejs:18`: `nodejs` module ki `18` stream.
    *   `python3:3.9/devel`: `python3` module ki `3.9` stream ka `devel` profile. Profiles packages ke specific subsets hote hain.
*   **Never directly update or remove a running kernel using `rpm -Uvh` or `rpm -e`.** Always use `dnf update` for kernel updates. DNF properly handles kernel installations, keeping old kernels available for rollback if the new one causes issues.

---

Umeed hai yeh detailed lesson aapko RPM, YUM, DNF, aur Module Streams ki gehri samajh de paya hoga. Yeh concepts aapki Linux journey mein bahut kaam aayenge. Practice karte rahiye! All the best!
