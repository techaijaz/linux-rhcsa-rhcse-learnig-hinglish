Day 14: Revision & lab practice (Hard/soft links, directory structure, permissions (chmod, umask) Ownership & groups (chown, chgrp, groups) Access Control Lists (ACLs) User & group management (useradd, passwd, /etc/passwd) SSH setup, key authentication Sudo, su, privilege escalation)
---
यह RHCSA + RHCE Revision & Lab Practice session आपको Linux filesystem, permissions, user management और secure access के fundamental concepts को revise और strengthen करने में मदद करेगा। हम Hinglish का उपयोग करेंगे ताकि concepts easy to understand हों।

---

## RHCSA + RHCE: Revision & Lab Practice (File Management, Permissions & Security)

**Target Audience:** RHCSA & RHCE Aspirants
**Duration:** 3-4 Hours (Self-paced, depending on practice)

---

### **Introduction: Kyun Zaruri Hai Yeh Topics?**

Linux files, directories, aur unki permissions, ownership aur access control certifications ka base hain. Yeh sab understanding aapko system administration, security, aur troubleshooting mein master banati hai. Agar yeh concepts strong nahi hain, toh advanced tasks mein problems aa sakti hain. Chaliye, shuru karte hain!

---

### **Section 1: File & Directory Basics (Links & Structure)**

#### **1.1 Hard Links aur Soft Links (Symbolic Links)**

**Explanation:**
*   **Hard Link:** Imagine karo ek hi file ke do alag-alag naam hain. Dono names ek hi inode number (filesystem par file ki actual location) ko point karte hain. Jab aap ek hard link create karte hain, toh file ka content duplicate nahi hota, bas ek naya entry ban jaati hai jo original file ke data ko refer karti hai. Agar original file delete bhi ho jaye, toh hard link se aap data access kar sakte hain, jab tak saare links delete na ho jayein. Hard links sirf same filesystem partition par create ho sakte hain aur directories ke liye nahi ban sakte.
*   **Soft Link (Symbolic Link):** Yeh ek shortcut jaisa hai, jaise Windows mein hota hai. Yeh ek naya file banata hai jiska content original file/directory ka path hota hai. Iska apna alag inode number hota hai. Agar original file delete ho jaye, toh soft link "broken" ho jaata hai aur kisi kaam ka nahi rehta (dangling link). Soft links different filesystems par bhi ban sakte hain aur directories ke liye bhi.

**Commands:**
*   `ln [source] [hard_link_name]` : Hard link banane ke liye.
*   `ln -s [source] [soft_link_name]` : Soft link banane ke liye.
*   `ls -li` : Files ke inode numbers dekhne ke liye (Hard links ka inode same hoga).
*   `rm` : Links delete karne ke liye (original file delete nahi hogi jab tak koi aur link exist karta ho, hard link ke case mein).

**Example:**
```bash
# Ek test file banayein
echo "Hello, this is original data." > original.txt

# Hard Link banayein
ln original.txt hardlink.txt
echo "original.txt ka hard link ban gaya."

# Soft Link banayein
ln -s original.txt softlink.txt
echo "original.txt ka soft link ban gaya."

# Check karein files aur unke inodes
ls -li original.txt hardlink.txt softlink.txt
# Output mein dekhein original.txt aur hardlink.txt ka inode same hoga. softlink.txt ka alag.

# Original file ko edit karein
echo "New data added." >> original.txt
cat hardlink.txt # Hard link se bhi updated data dikhega
cat softlink.txt # Soft link se bhi updated data dikhega

# Original file ko delete karein
rm original.txt
echo "Original file delete kar di."

# Ab hardlink aur softlink check karein
cat hardlink.txt # Data abhi bhi access hoga!
cat softlink.txt # Error! Soft link broken ho gaya.

# Directory ke liye soft link
mkdir mydir
ln -s mydir mydir_shortcut
ls -l mydir_shortcut
```

**Practice Task 1.1: Links ke Saath Khelna**
1.  Apni `/tmp` directory mein ek naya directory banayein `my_lab_dir`.
2.  `my_lab_dir` ke andar `report.txt` naam ki ek file banayein aur usmein kuch content likhein.
3.  `report.txt` ka ek hard link `final_report.txt` usi directory mein banayein.
4.  `report.txt` ka ek soft link `daily_report.txt` usi directory mein banayein.
5.  `ls -li` command ka use karke verify karein ki hard link aur soft link ke inode numbers kya hain.
6.  `report.txt` file ka content change karein. Check karein ki `final_report.txt` aur `daily_report.txt` mein changes reflect hue ya nahi.
7.  `report.txt` file ko delete karein.
8.  Ab `final_report.txt` aur `daily_report.txt` ko `cat` karke dekhein. Kya result milta hai? Explain karein.
9.  `my_lab_dir` directory ka ek soft link banayein `/home/youruser/my_lab_shortcut` (ya kisi bhi user ke home directory mein). Us shortcut se directory ko access karein.

---

#### **1.2 Standard Linux Directory Structure (FHS - Filesystem Hierarchy Standard)**

**Explanation:**
Linux mein sab kuch `/` (root directory) se shuru hota hai. Ek well-defined directory structure hota hai jisse Filesystem Hierarchy Standard (FHS) kehte hain. Yeh important hai ki aapko pata ho kaun si cheez kahan milti hai.

**Key Directories:**
*   `/`: Root directory. Har cheez ka parent.
*   `/bin`: **B**inaries (essential user commands like `ls`, `cp`, `mv`).
*   `/sbin`: **S**ystem **b**inaries (commands for system administration like `fdisk`, `reboot`).
*   `/etc`: **Etc**etera (system-wide configuration files like `passwd`, `fstab`).
*   `/home`: User home directories (`/home/john`, `/home/mary`).
*   `/root`: Root user ka home directory.
*   `/var`: **Var**iable data (files that change frequently like logs `/var/log`, mail `/var/mail`, spool files).
*   `/tmp`: **T**e**mp**orary files (delete ho jaate hain reboot par).
*   `/usr`: **U**nix **S**ystem **R**esources (user programs, libraries, documentation - usually non-essential for booting).
    *   `/usr/bin`: Most user-executable programs.
    *   `/usr/sbin`: Non-essential system administration binaries.
    *   `/usr/local`: Locally installed software.
*   `/opt`: **Opt**ional (third-party applications, like a separate web server stack).
*   `/dev`: **Dev**ices (hardware devices represented as files, like `/dev/sda`, `/dev/null`).
*   `/proc`: **Proc**esses (virtual filesystem providing process and kernel information).
*   `/mnt`: **M**ou**nt** point for temporary mounted filesystems (like USB drives).
*   `/media`: Mount point for removable media (like CDs/DVDs, USBs).
*   `/boot`: Boot loader files (kernel, initrd).

**Commands:**
*   `ls -F /`: Root directory ke contents dekhne ke liye.
*   `pwd`: Print Working Directory (aap current mein kahan hain).
*   `cd /etc`: Directory change karne ke liye.
*   `tree -L 2 /usr/local` (agar `tree` install ho): Directory structure ko visually dekhne ke liye.

**Practice Task 1.2: Directory Structure Exploration**
1.  Apni root directory (`/`) mein jaayein. `ls -F` command chala ke dekhein.
2.  `/etc` directory mein jaakar `passwd` aur `shadow` files ko `ls -l` karke dekhein. Kya difference dikhta hai permissions mein?
3.  `/var/log` directory mein jaakar kuch log files ke naam dekhein (e.g., `messages`, `boot.log`, `secure`). `tail -f /var/log/messages` chala ke real-time updates dekhein.
4.  `/usr/bin` mein jaakar `ls` command ko `ls -l` karein. Uska size aur ownership dekhein.
5.  `root` user ki home directory (`/root`) mein jaakar `ls -a` karein (agar aapko access hai, `sudo su -` se switch kar sakte hain). Dekhein hidden files kya hain.
6.  Apni user home directory mein wapas aayein.

---

### **Section 2: Permissions, Ownership & ACLs**

#### **2.1 Permissions (chmod, umask)**

**Explanation:**
Linux mein har file aur directory ke liye permissions set hoti hain, jo control karti hain ki kaun user kya kar sakta hai.
*   `r`: Read (file ko padh sakte hain, directory ke contents dekh sakte hain)
*   `w`: Write (file ko modify kar sakte hain, directory mein files create/delete kar sakte hain)
*   `x`: Execute (file ko run kar sakte hain (agar executable script hai), directory mein enter kar sakte hain)

Permissions teen categories ke liye hoti hain:
1.  **U**ser (owner)
2.  **G**roup
3.  **O**thers (everyone else)

**Numeric (Octal) Mode:**
*   `r = 4`
*   `w = 2`
*   `x = 1`
*   `no permission = 0`

Example: `rwx = 4+2+1 = 7`, `rw- = 4+2+0 = 6`, `r-x = 4+0+1 = 5`

Permissions string example: `-rwxr-xr--`
*   `-`: File type (d for directory, l for link, - for regular file)
*   `rwx`: Owner permissions (7)
*   `r-x`: Group permissions (5)
*   `r--`: Others permissions (4)
*   Total: `754`

**`umask`:** Default permissions set karta hai naye files aur directories ke liye.
*   File ke liye default `666` (rw-rw-rw-) hota hai. `umask` usmein se minus hota hai.
*   Directory ke liye default `777` (rwxrwxrwx) hota hai. `umask` usmein se minus hota hai.
*   `umask 022`:
    *   Files: `666 - 022 = 644` (rw-r--r--)
    *   Directories: `777 - 022 = 755` (rwxr-xr-x)

**Commands:**
*   `chmod [permissions] [file/directory]`: Permissions change karne ke liye.
    *   `chmod 755 myfile.sh` (numeric)
    *   `chmod u=rwx,g=rx,o=r myfile.txt` (symbolic)
    *   `chmod +x myscript.sh` (add execute permission for all)
    *   `chmod go-w myfile.txt` (group aur others se write permission remove)
*   `ls -l`: Permissions dekhne ke liye.
*   `umask`: Current umask value dekhne ke liye.
*   `umask -S`: Symbolic form mein umask dekhne ke liye.
*   `umask 027`: Temporarily umask set karne ke liye.

**Example:**
```bash
touch myfile.txt
mkdir mydir

# Default permissions check karein (umask ke hisaab se)
ls -l myfile.txt mydir

# umask change karein aur naye files banayein
umask 007
echo "New umask set to 007."
touch newfile.txt
mkdir newdir
ls -l newfile.txt newdir # Permissions 660 aur 770 honi chahiye

# Permissions change karein
chmod 740 myfile.txt
chmod u+w,g-x,o=r newdir
ls -l myfile.txt newdir
```

**Practice Task 2.1: Permissions Masterclass**
1.  Apni home directory mein `project_files` naam ki ek directory banayein.
2.  `project_files` ke andar `report.doc` aur `script.sh` naam ki do files banayein. `script.sh` mein ek simple `echo "Hello from script!"` command likhein.
3.  Verify karein ki `report.doc` aur `script.sh` ki default permissions kya hain.
4.  `report.doc` ko aise configure karein ki:
    *   Owner `rw` kar sake.
    *   Group `r` kar sake.
    *   Others `read` na kar sake (`---`).
    *   Numeric `chmod` use karein.
5.  `script.sh` ko aise configure karein ki:
    *   Owner `rwx` kar sake.
    *   Group `rx` kar sake.
    *   Others `x` kar sake.
    *   Symbolic `chmod` use karein.
6.  `script.sh` ko run karne ki koshish karein (`./script.sh`). Agar permission denied aaye, toh troubleshoot karein.
7.  Apna `umask` check karein.
8.  `umask` ko `077` set karein (temporary). Ek nayi file `secret.txt` banayein aur uski permissions `ls -l` se check karein. Explan karein `077` ka effect.

---

#### **2.2 Ownership & Groups (chown, chgrp, groups)**

**Explanation:**
Har file aur directory ka ek **owner** user aur ek **owner group** hota hai.
*   **Owner User:** Woh user jisne file banayi, ya jisko `chown` command se owner banaya gaya hai.
*   **Owner Group:** Woh primary group ya secondary group jiska file owner hissa hai, ya jisko `chgrp` command se assign kiya gaya hai.

**Commands:**
*   `chown [new_owner] [file/directory]`: Owner user change karne ke liye. (Requires root privileges or `sudo`).
    *   `chown john myfile.txt`
*   `chown [new_owner]:[new_group] [file/directory]`: Owner user aur group dono change karne ke liye.
    *   `chown john:devs myfile.txt`
*   `chgrp [new_group] [file/directory]`: Owner group change karne ke liye. (Requires root privileges or `sudo`, or if user is member of new_group).
    *   `chgrp devs myfile.txt`
*   `ls -l`: Ownership aur group details dekhne ke liye.
*   `id [username]`: Kisi specific user ki UIDs, GIDs, aur groups dekhne ke liye.
*   `groups [username]`: Kisi specific user ke groups list karne ke liye. (Agar `username` na dein, toh current user ke groups).

**Example:**
```bash
# Apni current user, group aur ID dekhein
id
groups

# Ek test file banayein
touch test_ownership.txt
ls -l test_ownership.txt # Apka user aur primary group dikhega

# Naya user aur group banane ki zaroorat padegi testing ke liye.
# (Requires sudo)
sudo useradd testuser1
sudo groupadd devteam
sudo usermod -aG devteam testuser1 # Add testuser1 to devteam group
sudo usermod -aG devteam $USER # Add your current user to devteam group

# Ab ownership change karein
sudo chown testuser1 test_ownership.txt
ls -l test_ownership.txt

sudo chgrp devteam test_ownership.txt
ls -l test_ownership.txt

sudo chown testuser1:devteam test_ownership.txt
ls -l test_ownership.txt
```

**Practice Task 2.2: Ownership & Group Assignments**
1.  Apni home directory mein `team_project` naam ki ek directory banayein.
2.  `sudo useradd project_lead` aur `sudo useradd team_member` users create karein.
3.  `sudo groupadd developers` aur `sudo groupadd testers` groups create karein.
4.  `project_lead` ko `developers` group ka member banayein.
5.  `team_member` ko `developers` aur `testers` dono groups ka member banayein.
6.  `team_project` directory ka owner `project_lead` aur group `developers` set karein.
7.  `team_project` ke andar `specs.txt` naam ki file banayein (by your current user).
8.  `specs.txt` ka owner `team_member` aur group `testers` set karein.
9.  `ls -l team_project` aur `ls -l team_project/specs.txt` se verify karein.
10. `id project_lead` aur `id team_member` se unke groups verify karein.

---

#### **2.3 Access Control Lists (ACLs) - RHCE Focus**

**Explanation:**
Standard permissions (owner, group, others) kabhi-kabhi enough nahi hoti. Agar aapko ek specific user ya group ko permissions deni hain, jo na toh file ka owner hai aur na hi primary group, toh ACLs kaam aate hain. ACLs granular control provide karte hain.

**ACL Commands:**
*   `getfacl [file/directory]`: ACLs dekhne ke liye.
*   `setfacl -m u:[user]:[permissions] [file/directory]`: User ke liye ACL set karne ke liye.
*   `setfacl -m g:[group]:[permissions] [file/directory]`: Group ke liye ACL set karne ke liye.
*   `setfacl -x u:[user] [file/directory]`: User ke liye ACL remove karne ke liye.
*   `setfacl -x g:[group] [file/directory]`: Group ke liye ACL remove karne ke liye.
*   `setfacl -b [file/directory]`: Saari ACLs remove karne ke liye.
*   `setfacl -m d:u:[user]:[permissions] [directory]`: Default ACL set karne ke liye (directory mein naye files automatically yeh ACL inherit karenge).

**ACL Flag in `ls -l`:** Jab kisi file/directory par ACL set hota hai, toh `ls -l` output mein permission string ke baad `+` symbol aata hai.

**Example:**
```bash
# Ek test file banayein
touch acl_test.txt
ls -l acl_test.txt # Dekhein permissions
getfacl acl_test.txt # No ACLs yet

# Naya user add karein (agar nahi hai)
sudo useradd visitor1

# visitor1 ko acl_test.txt par read permission dein
sudo setfacl -m u:visitor1:r acl_test.txt

# Ab phir se check karein
ls -l acl_test.txt # + symbol dikhega
getfacl acl_test.txt # visitor1 ki entry dikhegi

# test karein (switch to visitor1)
sudo su - visitor1
cat /path/to/acl_test.txt # Read access hona chahiye
echo "write test" >> /path/to/acl_test.txt # Permission Denied aana chahiye
exit # visitor1 se bahar aayein

# Visitor ko write permission dein
sudo setfacl -m u:visitor1:rw acl_test.txt
# test karein as visitor1 (agar aapko access hai)
# Ab write kar payenge

# ACL remove karein
sudo setfacl -x u:visitor1 acl_test.txt
getfacl acl_test.txt # visitor1 ki entry remove ho gayi
```

**Practice Task 2.3: ACL Implementation**
1.  Apni home directory mein `shared_data` naam ki ek directory banayein.
2.  `shared_data` ke andar `confidential.txt` aur `public.txt` naam ki do files banayein.
3.  `sudo useradd auditor` aur `sudo groupadd project_managers` users aur groups create karein (agar pehle se nahi hain).
4.  `confidential.txt` par default permissions rakhein, lekin `auditor` user ko `read` only access dein using ACL.
5.  `public.txt` par `project_managers` group ko `read` aur `write` access dein using ACL.
6.  `shared_data` directory par ek default ACL set karein ki koi bhi naya user (`new_user` maan lijiye, aapko create karna hoga) jo is directory mein file banaye, usko `read` aur `write` permission automatically mile (yaani directory ke contents ko `new_user` read/write kar sake).
    *   `sudo useradd new_user`
    *   `sudo setfacl -m d:u:new_user:rwx shared_data`
    *   `cd shared_data`
    *   `sudo -u new_user touch test_file_by_new_user.txt` (This requires `new_user` to have execute permission on `shared_data` parent directory)
    *   `ls -l` aur `getfacl test_file_by_new_user.txt` se verify karein.
7.  Verify karein ki saari ACLs theek se set hain `getfacl` aur `ls -l` commands se.
8.  `auditor` user aur `project_managers` group ke liye ACL entries ko remove karein.

---

### **Section 3: User & Group Management**

#### **3.1 User & Group Creation, Modification, Deletion**

**Explanation:**
System par users aur groups create, modify aur delete karna ek admin ka essential task hai.
*   `/etc/passwd`: User account information (username, UID, GID, home directory, shell). No password stored here.
*   `/etc/shadow`: Encrypted password aur password expiry information. Highly restricted.
*   `/etc/group`: Group information (group name, GID, members).

**Commands:**
*   `useradd [options] [username]`: Naya user account banane ke liye.
    *   `useradd -m newuser` (create home directory automatically)
    *   `useradd -G sales,marketing newuser` (specific groups mein add karein)
    *   `useradd -s /sbin/nologin newuser` (shell disable karein)
*   `passwd [username]`: User ka password set/change karne ke liye.
*   `userdel [options] [username]`: User account delete karne ke liye.
    *   `userdel newuser` (account delete karega, home dir nahi)
    *   `userdel -r newuser` (account aur home dir dono delete karega)
*   `usermod [options] [username]`: User account modify karne ke liye.
    *   `usermod -l newname oldname` (username change)
    *   `usermod -aG newgroup username` (additional group mein add)
    *   `usermod -s /bin/bash username` (shell change)
*   `groupadd [groupname]`: Naya group banane ke liye.
*   `groupdel [groupname]`: Group delete karne ke liye.
*   `gpasswd -a [user] [group]`: User ko group mein add karein.
*   `gpasswd -d [user] [group]`: User ko group se remove karein.

**Example:**
```bash
# Naya user banayein
sudo useradd -m devuser1
echo "devuser1 created with home directory."

# Password set karein
sudo passwd devuser1
# Enter new password twice

# `/etc/passwd` entry check karein
grep devuser1 /etc/passwd

# Naya group banayein
sudo groupadd developers
echo "developers group created."

# devuser1 ko developers group mein add karein
sudo usermod -aG developers devuser1
echo "devuser1 added to developers group."

# Groups check karein
id devuser1

# User ka shell change karein
sudo usermod -s /bin/bash devuser1
grep devuser1 /etc/passwd

# User ko delete karein (with home directory)
sudo userdel -r devuser1
echo "devuser1 deleted with home directory."

# Group ko delete karein
sudo groupdel developers
echo "developers group deleted."
```

**Practice Task 3.1: User & Group Lifecycle**
1.  `it_dept` aur `hr_dept` naam ke do groups banayein.
2.  `john_doe` aur `jane_smith` naam ke do users banayein, unki home directories bhi create honi chahiye.
3.  `john_doe` ko `it_dept` ka primary group member banayein.
4.  `jane_smith` ko `hr_dept` ka primary group member banayein aur `it_dept` ka secondary group member.
5.  Dono users ke liye passwords set karein.
6.  `john_doe` ka default shell `/bin/bash` se `/sbin/nologin` mein change karein. Verify karein `/etc/passwd` se.
7.  `jane_smith` ka username `jane_s` mein change karein.
8.  `id john_doe`, `id jane_s`, aur `/etc/passwd`, `/etc/group` ko `grep` karke verify karein.
9.  `john_doe` user ko delete karein, uski home directory ke saath.
10. `it_dept` aur `hr_dept` groups ko delete karein.

---

### **Section 4: Secure Access (SSH & Sudo)**

#### **4.1 SSH Setup & Key Authentication**

**Explanation:**
SSH (Secure Shell) remote systems ko securely access karne ka standard method hai. Password authentication ke alava, key-based authentication ek zyada secure aur convenient option hai. Ismein aap ek public/private key pair generate karte hain. Public key remote server par store hoti hai, aur private key aapke client machine par. Login karte waqt, aapko password ki jagah private key use karni padti hai.

**Commands:**
*   `ssh [user]@[host]`: Remote system se connect karne ke liye.
*   `ssh-keygen`: Public/private key pair generate karne ke liye.
    *   Default location: `~/.ssh/id_rsa` (private), `~/.ssh/id_rsa.pub` (public).
*   `ssh-copy-id [user]@[host]`: Apni public key remote server par copy karne ke liye (automatic `~/.ssh/authorized_keys` mein add ho jaati hai).
*   `cat ~/.ssh/authorized_keys`: Remote server par jahan public keys store hoti hain.
*   `sudo systemctl status sshd`: SSH service ka status dekhne ke liye.
*   `sudo systemctl restart sshd`: SSH service restart karne ke liye.
*   `/etc/ssh/sshd_config`: SSH server ki main configuration file. Important parameters: `PasswordAuthentication`, `PermitRootLogin`, `AllowUsers`, `PubkeyAuthentication`.

**Example:**
**(Requires 2 VMs or connecting to localhost for practice)**

**On Client Machine (Your Current VM):**
```bash
# SSH key pair generate karein (agar pehle se nahi hai)
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
# Enter path (default ~./ssh/id_rsa), passphrase (optional but recommended)

# Public key ko remote server par copy karein (replace user@remote_host)
ssh-copy-id user@remote_host
# First time password enter karna padega.

# Ab passwordless login try karein
ssh user@remote_host
# Agar passphrase set kiya hai, toh woh enter karna padega.
```

**On Server Machine (or if connecting to self, on same VM):**
```bash
# Check karein ki SSH service chal rahi hai
sudo systemctl status sshd

# Authorized keys file ka content dekhein
cat ~/.ssh/authorized_keys
# Apki public key yahan dikhni chahiye

# SSHD config file mein changes (Agar zaruri ho)
# sudo vi /etc/ssh/sshd_config
#   PasswordAuthentication no  (for strict key-only auth)
#   PubkeyAuthentication yes
# sudo systemctl restart sshd
```

**Practice Task 4.1: SSH Key Authentication Setup**
1.  **Scenario:** Aapko `server_vm` (ek doosra VM) ko `client_vm` (aapka current VM) se passwordless SSH access set karna hai. Agar aapke paas do VMs nahi hain, toh `client_vm` se `client_vm` ko hi access karne ki koshish karein (i.e., `user@localhost`).
2.  `client_vm` par `ssh-keygen` command chala kar ek naya SSH key pair generate karein. (Passphrase optional, but practice ke liye set karein).
3.  Apni public key ko `server_vm` (ya `localhost`) par copy karein using `ssh-copy-id`. Aapko `server_vm` ke user ka password dena padega first time.
4.  Copy hone ke baad, `client_vm` se `server_vm` ko `ssh` karein. Kya password maanga jaata hai? Agar haan, toh passphrase enter karein. Agar nahi, toh congratulations!
5.  `server_vm` par `~/.ssh/authorized_keys` file ka content dekhein. Kya aapki public key usmein hai?
6.  **(RHCE Level):** `server_vm` ki `/etc/ssh/sshd_config` file mein `PasswordAuthentication no` set karein aur `sshd` service restart karein.
7.  `client_vm` se `server_vm` ko phir se SSH karein. Agar passwordless key authentication fail ho jaye, toh kya error aata hai? (Expected: "Permission denied (publickey).")
8.  `sshd_config` mein change ko revert karein (`PasswordAuthentication yes`) ya apni public key ko `authorized_keys` mein manually add karein agar zaroorat pade.

---

#### **4.2 Sudo, Su, & Privilege Escalation**

**Explanation:**
`root` user ke paas system par unlimited powers hote hain. Lekin directly `root` account use karna risky hai. `sudo` aur `su` commands privilege escalation ke liye use hote hain.
*   **`su` (substitute user/switch user):** Aapko kisi doosre user ke shell mein switch karne ki allow karta hai. Agar koi username specify nahi kiya toh default `root` user hota hai. `su -` se complete login shell milta hai (environment variables bhi change hote hain).
*   **`sudo` (superuser do):** Ek non-root user ko root (ya kisi aur user) ki tarah commands run karne ki permission deta hai, provided woh user `/etc/sudoers` file mein listed ho. `sudo` `root` ka password nahi mangta, balki current user ka password mangta hai.

**`/etc/sudoers` File:**
Yeh file `sudo` ke rules define karti hai. Isko `visudo` command se hi edit karna chahiye, direct `vi` se nahi, kyunki `visudo` syntax check karta hai.
*   **Format:** `user_name ALL=(ALL) ALL`
    *   `user_name`: Woh user ya group (prefixed with `%`) jisko permission deni hai.
    *   `ALL=(ALL)`: Kis host se aur kis user ki tarah command run kar sakte hain. (`ALL` = kisi bhi host se, `(ALL)` = kisi bhi user ki tarah).
    *   `ALL`: Kaun se commands run kar sakte hain.

**Commands:**
*   `su - [username]`: Kisi doosre user ki tarah login shell prapt karein. (`su -` for root).
*   `sudo [command]`: Command ko root (ya specified user) ki tarah run karein.
*   `visudo`: `/etc/sudoers` file ko safely edit karne ke liye.
*   `whoami`: Current user ka naam dekhne ke liye.
*   `sudo -l`: Dekhne ke liye ki aapko `sudo` se kaun se commands run karne ki permission hai.

**Example:**
```bash
# Root ki tarah switch karein
su -
# Root password enter karein. Ab aap root hain.
whoami
exit # Root se bahar aayein

# Naya user banayein (agar nahi hai)
sudo useradd adminuser1
sudo passwd adminuser1

# adminuser1 ko sudo access dene ke liye
# METHOD 1: Add user to 'wheel' group (RHEL/CentOS/Fedora)
sudo usermod -aG wheel adminuser1
echo "adminuser1 added to wheel group."

# METHOD 2: Edit /etc/sudoers directly (using visudo)
sudo visudo
# Add this line at the end (for custom users, not generally recommended if 'wheel' group is available):
# adminuser1 ALL=(ALL) ALL
# Save and exit (:wq)

# Test as adminuser1
sudo su - adminuser1
sudo ls /root
# adminuser1 ka password enter karein.
exit # adminuser1 se bahar aayein

# Apne current user ko sudo access check karein
sudo -l

# Ek specific command ke liye sudo permission
# sudo visudo
# user_name ALL=/usr/bin/systemctl restart httpd
# (This allows user_name to only restart httpd service with sudo)
```

**Practice Task 4.2: Sudo & Su - Privilege Management**
1.  `devops_admin` naam ka ek naya user create karein aur uske liye password set karein.
2.  `devops_admin` ko `wheel` group ka member banayein (ya jo bhi group aapke OS mein `sudo` access ke liye configured hai, jaise `sudo` group Debian/Ubuntu mein).
3.  `devops_admin` user mein `su -` karein. Wahan se `sudo` command (e.g., `sudo systemctl status firewalld`) run karne ki koshish karein. Verify karein ki aapko access milta hai.
4.  `visudo` command ka use karke `/etc/sudoers` file ko open karein.
5.  `devops_admin` user ke liye ek custom entry add karein jo use sirf `systemctl restart network` (ya `NetworkManager` ya koi bhi non-critical service jo aapke system par hai) command run karne ki permission de. `wheel` group se usko remove na karein. (Hint: Line add karein `devops_admin ALL=(ALL) /usr/bin/systemctl restart network`). Make sure to use the full path to the command.
6.  `devops_admin` ki tarah `su -` karein aur `sudo systemctl restart network` chala ke dekhein. Kya yeh kaam karta hai?
7.  `sudo systemctl restart sshd` chalane ki koshish karein. Kya yeh kaam karta hai? Explan karein difference.
8.  `devops_admin` user ko `wheel` group se remove karein.
9.  Ab `devops_admin` ki tarah login karke sirf `sudo systemctl restart network` chalane ki koshish karein. Aur `sudo ls /root` chalane ki koshish karein. Kya result milta hai? Explain karein.
10. `devops_admin` user ko delete karein aur `/etc/sudoers` file se uski entry remove karein (using `visudo`).

---

### **Consolidated Practice Lab: Tech Solutions Pvt. Ltd. Setup**

**Scenario:** Aapko "Tech Solutions Pvt. Ltd." ke liye ek naya Linux server set karna hai. Is server par ek "Development" department aur ek "Testing" department kaam karega. Kuch specific access control rules apply karne hain.

**Tasks:**

1.  **Directory Structure & Initial Setup:**
    *   `/srv/tech_solutions` naam ki ek main directory banayein.
    *   Iske andar do sub-directories banayein: `development` aur `testing`.
    *   `development` directory mein `dev_plans.txt` naam ki file banayein aur usmein kuch sample data likhein.
    *   `testing` directory mein `test_cases.xlsx` (ek empty file) banayein.

2.  **User & Group Management:**
    *   Do groups banayein: `developers` aur `testers`.
    *   Do users banayein: `rajesh` (developer) aur `pooja` (tester).
        *   `rajesh` ka primary group `developers` hona chahiye.
        *   `pooja` ka primary group `testers` hona chahiye.
    *   Ek naya user `admin_ops` banayein aur usko `sudo` access dein (using `wheel` group). `admin_ops` ke liye password set karein.

3.  **Ownership & Permissions:**
    *   `/srv/tech_solutions/development` directory ka owner `rajesh` aur group `developers` set karein. Permissions `770` honi chahiye.
    *   `/srv/tech_solutions/testing` directory ka owner `pooja` aur group `testers` set karein. Permissions `770` honi chahiye.
    *   `dev_plans.txt` file ka owner `rajesh` aur group `developers` set karein. Permissions `660` honi chahiye.
    *   `test_cases.xlsx` file ka owner `pooja` aur group `testers` set karein. Permissions `660` honi chahiye.

4.  **ACL Implementation:**
    *   `developers` group ke members ke alava, `pooja` (tester) ko `dev_plans.txt` file par `read` access dein using ACL.
    *   `testing` directory mein, ek default ACL set karein ki koi bhi file jo `testing` directory mein banayi jaye, usko `admin_ops` user automatically `read` aur `write` access ho.
    *   Verify karein ki `admin_ops` user `testing` directory mein ek file bana sakta hai aur us par `rw` permission mil jaati hai automatically. (`sudo -u admin_ops touch /srv/tech_solutions/testing/admin_log.txt` use kar sakte hain).

5.  **Hard & Soft Links:**
    *   `dev_plans.txt` file ka ek hard link `backup_dev_plans.txt` `/srv/tech_solutions/development` directory mein banayein.
    *   `/srv/tech_solutions/testing` directory ka ek soft link `/home/rajesh/my_test_shortcut` banayein, jisse `rajesh` apni home directory se `testing` directory ko access kar sake.

6.  **SSH Key Authentication (Self-Connect):**
    *   `admin_ops` user ke liye ek SSH key pair generate karein.
    *   `admin_ops` user ki public key ko uske apne `authorized_keys` file mein add karein (`ssh-copy-id` ya manually).
    *   `admin_ops` user ki tarah passwordless SSH login test karein (e.g., `ssh admin_ops@localhost`).

**Verification Steps:**
*   `ls -lR /srv/tech_solutions`
*   `id rajesh`, `id pooja`, `id admin_ops`
*   `getfacl -R /srv/tech_solutions`
*   `sudo -u rajesh cat /srv/tech_solutions/development/dev_plans.txt` (success)
*   `sudo -u pooja cat /srv/tech_solutions/development/dev_plans.txt` (success due to ACL)
*   `sudo -u pooja echo "test" >> /srv/tech_solutions/development/dev_plans.txt` (fail)
*   `sudo -u admin_ops touch /srv/tech_solutions/testing/admin_log.txt` aur fir `getfacl /srv/tech_solutions/testing/admin_log.txt`
*   `ls -li /srv/tech_solutions/development/dev_plans.txt /srv/tech_solutions/development/backup_dev_plans.txt`
*   `ls -l /home/rajesh/my_test_shortcut`
*   `ssh admin_ops@localhost` (should not ask for password)

---

### **Key Takeaways (Important Points):**

*   **Hard vs. Soft Links:** Inode number, original file deletion effect, directory support, filesystem boundaries.
*   **FHS:** Har directory ka purpose pata hona chahiye (`/etc`, `/var`, `/usr`, `/opt`).
*   **Permissions:** `rwx` ka matlab files aur directories ke liye alag hota hai. Numeric aur symbolic `chmod` use karna aana chahiye. `umask` default permissions ke liye hai.
*   **Ownership:** `chown` aur `chgrp` `sudo` se hi chalte hain. Primary aur secondary groups ka concept clear hona chahiye.
*   **ACLs:** Standard permissions ke limits ko overcome karta hai. `getfacl` aur `setfacl` crucial hain. Default ACLs for directories (RHCE).
*   **User/Group Management:** `useradd`, `passwd`, `usermod`, `userdel`, `groupadd`, `groupdel` ke options (especially `-m`, `-r`, `-aG`). `/etc/passwd`, `/etc/shadow`, `/etc/group` ka structure.
*   **SSH Keys:** Secure aur automated login ke liye essential. `ssh-keygen`, `ssh-copy-id`, `authorized_keys`.
*   **Sudo:** `root` privileges safely manage karne ke liye. `visudo` aur `/etc/sudoers` ka syntax important hai. `su -` aur `sudo` mein difference.

---

**Next Steps:**
*   Yeh lab baar-baar practice karein. Scenarios change karke naye problems create karein.
*   Har command ke `man` page ko padhein (e.g., `man chmod`, `man useradd`).
*   Certification exams mein time management bhi important hai, toh commands ko fast type karne ki practice karein.

All the best for your RHCSA + RHCE journey!
