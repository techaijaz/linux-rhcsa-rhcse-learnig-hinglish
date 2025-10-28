Day 8: Hard/soft links, directory structure, permissions (chmod, umask)
---
नमस्ते Linux Enthusiasts! Welcome to this detailed RHCSA + RHCE training lesson. Aaj hum Linux ke kuch sabse important aur foundational concepts ko explore karenge: **Hard/Soft Links, Directory Structure, aur Permissions (chmod, umask, ACLs).**

Yeh topics na sirf RHCSA exam ke liye essential hain, balki real-world server administration mein bhi daily kaam aate hain. Chaliye shuru karte hain!

---

## RHCSA + RHCE Training: Links, Directory Structure & Permissions

**Learning Objectives:**
Iss lesson ke end tak, aapko yeh sab samajh aa jayega aur aap perform kar payenge:
*   Linux Filesystem Hierarchy Standard (FHS) ko samajhna aur important directories ko pehchanna.
*   Hard aur Soft Links ke beech ka antar samajhna aur unhe create/manage karna.
*   File aur Directory Permissions (read, write, execute) ko samajhna.
*   `chmod` command ko symbolic aur octal modes mein use karke permissions manage karna.
*   Special Permissions (SUID, SGID, Sticky Bit) ko samajhna aur apply karna. (RHCE Focus)
*   `umask` value ka calculation aur use samajhna.
*   `chown` aur `chgrp` commands ka upyog karna.
*   Access Control Lists (ACLs) ko samajhna aur `setfacl`/`getfacl` commands se manage karna. (RHCE Focus)

---

### Module 1: Linux Directory Structure (Filesystem Hierarchy Standard - FHS)

Linux mein sab kuch ek file hai, aur files ek well-defined structure mein organize hoti hain. Iss structure ko **Filesystem Hierarchy Standard (FHS)** kehte hain. Yeh ek convention hai jo define karta hai ki kaunsi file kis directory mein honi chahiye.

**Kyun Important Hai?**
Server administration mein, aapko pata hona chahiye ki logs kahan milenge (`/var/log`), configuration files kahan hote hain (`/etc`), aur user data kahan store hota hai (`/home`).

**Important Directories aur Unka Maksad:**

| Directory | Purpose (English)                                       | Explanation (Hinglish)                                      |
| :-------- | :------------------------------------------------------ | :---------------------------------------------------------- |
| `/`       | **Root Directory:** The base of the entire filesystem.  | Sabse upar wali directory. Har cheez yahi se shuru hoti hai. |
| `/bin`    | **Binaries:** Essential user command binaries.          | Basic user commands (e.g., `ls`, `cat`, `mv`, `rm`). |
| `/sbin`   | **System Binaries:** Essential system administration binaries. | System commands jo root user ya sudo se run hote hain (e.g., `fdisk`, `reboot`, `ip`). |
| `/etc`    | **Configuration Files:** Host-specific configuration files. | System aur applications ki settings files (e.g., `/etc/passwd`, `/etc/fstab`). |
| `/home`   | **Home Directories:** User's personal directories.      | Har user ki apni personal directory (e.g., `/home/john`). |
| `/root`   | **Root User's Home Directory:** Home directory for the root user. | Root user ka personal folder. |
| `/dev`    | **Devices:** Device files.                              | Hardware devices ko represent karte hain (e.g., `/dev/sda1` for a partition, `/dev/null`). |
| `/proc`   | **Process Information:** Virtual filesystem providing process and kernel information. | Running processes aur kernel ki live information yahan files ki form mein milti hai. |
| `/sys`    | **System Information:** Virtual filesystem for system hardware. | Hardware devices ki live information. |
| `/tmp`    | **Temporary Files:** Temporary files.                   | Files jo temporary hain aur reboot par delete ho sakte hain. Koi bhi user yahan likh sakta hai. |
| `/var`    | **Variable Data:** Variable data files.                 | Files jin ka content change hota rehta hai, jaise logs (`/var/log`), mail (`/var/mail`), web server data (`/var/www`). |
| `/usr`    | **Unix System Resources:** Secondary hierarchy for read-only user data. | User programs aur libraries jo system ke liye essential nahi hain (e.g., applications, documentation). `/usr/bin`, `/usr/lib` jaisi sub-directories hoti hain. |
| `/opt`    | **Optional Software:** Optional application software packages. | Third-party software packages install karne ke liye (e.g., Oracle, commercial apps). |
| `/mnt`    | **Mount Point:** Temporarily mounted filesystems.       | Filesystems (jaise USB drives, network shares) ko mount karne ke liye temporary location. |
| `/media`  | **Removable Media:** Mount point for removable media (CD-ROMs, USB drives). | CDs, DVDs, USB drives ko automatically mount karne ke liye. |
| `/run`    | **Runtime Data:** Volatile runtime data.               | System boot hone ke baad generate hone wala volatile data (e.g., PID files). |

**Commands to Explore:**

*   `pwd`: Print Working Directory (Batata hai aap kis directory mein hain).
*   `cd `: Change Directory (Dusri directory mein jaane ke liye).
*   `ls -F`: List contents of a directory, with indicators (directories ke aage `/`, executables ke aage `*`, links ke aage `@` dikhata hai).
*   `tree`: Directory structure ko graphical view mein dikhata hai. (Agar install na ho toh `sudo dnf install tree` ya `sudo apt install tree`).

---

### Module 2: Hard Links aur Soft Links (Symbolic Links)

Links files ko access karne ke alternate tarike hain. Yeh shortcuts ki tarah hote hain, but thode alag.

**Kyun Important Hai?**
Software versioning, system libraries, configuration management, aur disk space optimization mein links bohot useful hote hain.

#### 2.1 Hard Links

**Concept:** Hard link ek file ka additional name hota hai. Socho ki ek hi file ke multiple darwaze hain. Agar aap kisi bhi darwaze se file ko access karte hain, toh aap usi original content tak pahunchte hain.

**Technical Explanation:**
Har file ka ek unique **inode** number hota hai. Inode mein file ka metadata hota hai (jaise owner, permissions, creation date, aur disk par data blocks kahan hain). Jab aap hard link banate hain, toh aap ek naya directory entry banate hain jo existing file ke *same inode* ko point karta hai.

*   **Properties:**
    *   Hard links usi filesystem par ban sakte hain.
    *   Aap directories ke hard links nahi bana sakte.
    *   Hard link banane par, file ke inode ka link count badh jata hai.
    *   Agar original file delete ho jaye, tab bhi hard link tab tak kaam karta rahega jab tak *saare* links delete nahi ho jate. Data tab delete hota hai jab link count `0` ho jata hai.
    *   Inode numbers same hote hain.

**Command:**
`ln  `

**Example:**

```bash
# 1. Ek test file banate hain
echo "This is the original content." > original_file.txt

# 2. Hard link banate hain
ln original_file.txt hardlink_to_original.txt

# 3. Dono files ko dekhte hain (inode numbers par dhyaan dena)
ls -li original_file.txt hardlink_to_original.txt
# Output mein same inode number dikhega.

# 4. Kisi bhi link se content change karo
echo "New content added." >> hardlink_to_original.txt

# 5. Original file ka content dekho
cat original_file.txt
# Naya content bhi original file mein dikhega.

# 6. Original file delete karo
rm original_file.txt

# 7. Kya hard link abhi bhi kaam kar raha hai?
cat hardlink_to_original.txt
# Haan, kaam kar raha hai kyunki data abhi bhi disk par hai.

# 8. Ab hard link bhi delete karo
rm hardlink_to_original.txt
# Ab data disk se delete ho jayega (jab link count 0 ho gaya).
```

#### 2.2 Soft Links (Symbolic Links)

**Concept:** Soft link (ya symbolic link, symlink) ek "shortcut" ki tarah hota hai jo kisi dusri file ya directory ke *path* ko store karta hai. Yeh ek naya, chhota file hota hai jismein target file ya directory ka path likha hota hai.

**Technical Explanation:**
Soft link ka apna alag inode number hota hai. Yeh file system par ek alag entry hoti hai jismein original file/directory ka *path* store hota hai. Jab aap soft link ko access karte hain, toh system us path ko read karta hai aur aapko target file tak redirect karta hai.

*   **Properties:**
    *   Soft links across different filesystems ban sakte hain.
    *   Aap directories ke soft links bana sakte hain.
    *   Soft link ka apna alag inode number hota hai.
    *   Agar original file/directory delete ho jaye ya move ho jaye, toh soft link "broken" ho jata hai (dangling link), matlab woh ab kisi ko point nahi karta.
    *   `ls -l` output mein `->` symbol ke baad target path dikhta hai. Permissions bhi original file se alag ho sakte hain (par effect original ke hote hain).

**Command:**
`ln -s  `

**Example:**

```bash
# 1. Ek test file banate hain
echo "This is the original content." > original_file.txt

# 2. Soft link banate hain
ln -s original_file.txt softlink_to_original.txt

# 3. Dono files ko dekhte hain (inode numbers aur '->' par dhyaan dena)
ls -li original_file.txt softlink_to_original.txt
# Output mein alag inode numbers aur soft link ke aage '-> original_file.txt' dikhega.
# Soft link ki permissions 'lrwxrwxrwx' honge, 'l' ka matlab link hai.

# 4. Kisi bhi link se content change karo
echo "New content added." >> softlink_to_original.txt

# 5. Original file ka content dekho
cat original_file.txt
# Naya content bhi original file mein dikhega.

# 6. Original file delete karo
rm original_file.txt

# 7. Kya soft link abhi bhi kaam kar raha hai?
cat softlink_to_original.txt
# Nahi! Error: "No such file or directory" - kyunki original file gayab hai.

# 8. Broken soft link ko delete karo
rm softlink_to_original.txt

# 9. Directory ka soft link example
mkdir my_data
echo "Important file here." > my_data/document.txt
ln -s my_data linked_data
ls -l linked_data
ls linked_data/document.txt
```

#### 2.3 Hard Link vs. Soft Link - Key Differences

| Feature           | Hard Link                                         | Soft Link (Symbolic Link)                                |
| :---------------- | :------------------------------------------------ | :------------------------------------------------------- |
| **Type**          | Another name for the same inode (file data).      | A new file containing the path to another file/directory. |
| **Inode No.**     | Same as the original file.                        | Different from the original file/directory.             |
| **Filesystem**    | Must reside on the same filesystem.               | Can span across different filesystems.                  |
| **Directories**   | Cannot link directories.                          | Can link directories.                                    |
| **Original Deletion** | Link still works until all hard links are removed. | Becomes "broken" (dangling) if original is deleted/moved. |
| **Disk Space**    | Doesn't consume additional space (except for directory entry). | Consumes minimal space (to store the path).             |
| **Identification**| `ls -l` shows multiple hard links by increasing link count. | `ls -l` shows `l` at the start of permissions and `-> target`. |

---

### Module 3: File and Directory Permissions (chmod, umask, ACLs)

Linux mein security ka ek bahut important hissa hai file permissions. Yeh define karte hain ki kaun user kis file ya directory ke saath kya kar sakta hai.

**Kyun Important Hai?**
Data confidentiality, system integrity, aur user access control ke liye permissions ka sahi se set hona critical hai.

#### 3.1 Basic Permissions: Read, Write, Execute (rwx)

Har file aur directory ke liye teen tarah ki permissions hoti hain:

*   **Read (r):**
    *   **File ke liye:** Aap file ka content padh sakte hain (`cat`, `less`).
    *   **Directory ke liye:** Aap directory mein files ki list dekh sakte hain (`ls`).
*   **Write (w):**
    *   **File ke liye:** Aap file ka content modify, delete, ya overwrite kar sakte hain.
    *   **Directory ke liye:** Aap directory mein files create, delete, rename, ya move kar sakte hain (even if you don't own the file, as long as you have write on the *directory*).
*   **Execute (x):**
    *   **File ke liye:** Aap file ko ek program ya script ki tarah run kar sakte hain.
    *   **Directory ke liye:** Aap directory mein `cd` kar sakte hain, uski sub-directories ko access kar sakte hain. (Agar `x` nahi hai, toh `cd` bhi nahi kar payenge, bhale hi `r` ho).

#### 3.2 User, Group, Others (ugo)

Yeh permissions teen categories ke liye set ki jaati hain:

1.  **User (u):** File ka owner. Jis user ne file banayi hai, woh uska owner hota hai.
2.  **Group (g):** File ka group owner. Ek user multiple groups ka member ho sakta hai.
3.  **Others (o):** Baaki sab users jo na toh owner hain aur na hi group owner ke member hain.
4.  **All (a):** Sabhi (user, group, others).

`ls -l` command se aap permissions dekh sakte hain:

```bash
ls -l my_file.txt
# Output: -rwxrw-r-- 1 user group 123 Jan 1 10:00 my_file.txt
```

Is output ko decode karte hain:
*   Pehla character (`-`): File type. `-` for regular file, `d` for directory, `l` for symbolic link.
*   Next 9 characters: Permissions. Yeh teen-teen ke set mein hote hain:
    *   Pehle teen: User (owner) permissions (`rwx`)
    *   Dusre teen: Group permissions (`rw-`)
    *   Teesre teen: Others permissions (`r--`)

#### 3.3 Changing Permissions: `chmod`

`chmod` (change mode) command permissions change karne ke liye use hota hai. Iske do main tarike hain: Symbolic Mode aur Octal Mode.

##### 3.3.1 Symbolic Mode

Symbolic mode mein permissions ko characters se specify kiya jaata hai:

*   **Who:** `u` (user), `g` (group), `o` (others), `a` (all)
*   **Operator:** `+` (add permission), `-` (remove permission), `=` (set exact permission)
*   **Permission:** `r` (read), `w` (write), `x` (execute)

**Examples:**

*   `chmod u+x my_script.sh`: Owner ko execute permission do.
*   `chmod g-w my_file.txt`: Group se write permission hatao.
*   `chmod o=r my_data.txt`: Others ko sirf read permission do (baaki sab hata do).
*   `chmod a+rwx my_dir`: Sabhi ko read, write, execute permission do `my_dir` par.
*   `chmod ug+rw,o-rwx my_file.txt`: User aur group ko rw do, others se rwx hatao.
*   `chmod -R g+w shared_folder`: Recursively (`-R`) `shared_folder` aur uske andar ki sabhi files/directories ko group ke liye write permission do.

##### 3.3.2 Octal (Numeric) Mode

Octal mode mein har permission ko ek numeric value assign ki jaati hai:

*   `r` (read) = **4**
*   `w` (write) = **2**
*   `x` (execute) = **1**
*   No permission = **0**

Permissions ka total sum karke octal value banate hain. Har category (user, group, others) ke liye ek number hota hai.

| Permission | Octal Value |
| :--------- | :---------- |
| `---`      | `0`         |
| `--x`      | `1`         |
| `-w-`      | `2`         |
| `-wx`      | `3`         |
| `r--`      | `4`         |
| `r-x`      | `5`         |
| `rw-`      | `6`         |
| `rwx`      | `7`         |

**Examples:**

*   `chmod 755 my_script.sh`:
    *   User: `rwx` (4+2+1=7)
    *   Group: `r-x` (4+0+1=5)
    *   Others: `r-x` (4+0+1=5)
    *   Common for executable scripts/directories.
*   `chmod 644 my_file.txt`:
    *   User: `rw-` (4+2+0=6)
    *   Group: `r--` (4+0+0=4)
    *   Others: `r--` (4+0+0=4)
    *   Common for regular files.
*   `chmod 700 private_folder`: Only owner can access.
*   `chmod 660 shared_data.txt`: Owner aur group read/write kar sakte hain, others kuch nahi.

#### 3.4 Special Permissions (RHCE Focus)

Normal permissions ke alawa, teen special permissions bhi hote hain jo Linux security mein bahut important role play karte hain. Yeh permissions file modes ke aage ek extra digit se specify hote hain (4-digit octal value).

##### 3.4.1 SUID (Set User ID)

*   **Octal Value:** `4` (Pehli digit)
*   **Symbolic:** `u+s`
*   **Maksad:** Jab ek executable file par SUID bit set hota hai, toh woh file ko run karne wala user us file ke *owner ki permissions* ke saath run karta hai, na ki apni khud ki permissions ke saath.
*   **Dikhta kaise hai:** `ls -l` mein user ke execute permission (`x`) ki jagah `s` ya `S` dikhta hai. `s` agar `x` bhi set ho, `S` agar `x` set na ho.
*   **Example:** `passwd` command. Jab aap `passwd` run karte hain, toh woh system files (`/etc/shadow`) ko modify karta hai jinhe sirf `root` user access kar sakta hai. `passwd` command root user ka owner hota hai aur uspar SUID bit set hota hai, isliye non-root user bhi apna password change kar pate hain.
*   **Security Risk:** SUID bit carefully use karna chahiye, kyunki agar koi malicious script ya program par SUID set ho gaya, toh woh uske owner (ho sakta hai root) ke permissions ke saath run ho kar system ko harm kar sakta hai.

**Example:**

```bash
# 1. Ek simple script banao (jise aap root se run karna chahenge)
echo '#!/bin/bash' > my_suid_script.sh
echo 'echo "I am running as user: $(whoami)"' >> my_suid_script.sh
chmod +x my_suid_script.sh
sudo chown root:root my_suid_script.sh # Owner ko root banao

# 2. SUID bit set karo
sudo chmod u+s my_suid_script.sh
# Ya sudo chmod 4755 my_suid_script.sh

# 3. Permissions dekho
ls -l my_suid_script.sh
# Output: -rwsr-xr-x ... root root ... my_suid_script.sh

# 4. Non-root user se run karo
./my_suid_script.sh
# Output: "I am running as user: root" (even if you are a regular user)

# 5. SUID bit hatao
sudo chmod u-s my_suid_script.sh
```

##### 3.4.2 SGID (Set Group ID)

*   **Octal Value:** `2` (Pehli digit)
*   **Symbolic:** `g+s`
*   **Maksad:**
    *   **Executable Files par:** Jab SGID bit set hota hai, toh file ko run karne wala user us file ke *group owner ki permissions* ke saath run karta hai.
    *   **Directories par:** Yeh SGID ka sabse common use case hai. Jab SGID bit ek directory par set hota hai, toh us directory mein jo bhi nayi files aur sub-directories banengi, woh automatically parent directory ka *group owner inherit* kar lengi, na ki file banane wale user ka primary group. This is great for shared directories.
*   **Dikhta kaise hai:** `ls -l` mein group ke execute permission (`x`) ki jagah `s` ya `S` dikhta hai. `s` agar `x` bhi set ho, `S` agar `x` set na ho.

**Example (Directory SGID):**

```bash
# 1. Ek shared directory banao aur uska group change karo
sudo mkdir /shared_data
sudo chgrp analysts /shared_data # Assuming 'analysts' group exists, ya 'users' group use kar sakte hain
sudo chmod 770 /shared_data      # Owner aur Group ko full access, others ko kuch nahi

# 2. SGID bit set karo
sudo chmod g+s /shared_data
# Ya sudo chmod 2770 /shared_data

# 3. Permissions dekho
ls -ld /shared_data
# Output: drwxrws--- ... root analysts ... /shared_data ( 's' for group execute)

# 4. Ab kisi user (jo analysts group ka member hai) se login karo.
# Uss user se shared_data mein ek file banao.
cd /shared_data
touch new_report.txt
ls -l new_report.txt
# Output: -rw-rw-r-- ... user_name analysts ... new_report.txt
# Notice karo, naya file 'analysts' group ka owner hai, na ki user_name ka primary group.
```

##### 3.4.3 Sticky Bit

*   **Octal Value:** `1` (Pehli digit)
*   **Symbolic:** `+t`
*   **Maksad:** Sticky bit sirf directories par apply hota hai. Jab ek directory par sticky bit set hota hai, toh us directory mein users sirf un files aur directories ko delete ya rename kar sakte hain jinke woh *owner* hain. Even if they have write permission on the directory, they cannot delete others' files.
*   **Dikhta kaise hai:** `ls -l` mein others ke execute permission (`x`) ki jagah `t` ya `T` dikhta hai. `t` agar `x` bhi set ho, `T` agar `x` set na ho.
*   **Example:** `/tmp` directory. Yeh sabse common example hai. `/tmp` par sticky bit set hota hai, isliye koi bhi user wahan files bana sakta hai, lekin sirf apni hi files ko delete kar sakta hai.

**Example:**

```bash
# 1. Ek shared temporary directory banao
sudo mkdir /my_temp_dir
sudo chmod 777 /my_temp_dir # Sabhi ko full access do (temporarily)

# 2. Sticky bit set karo
sudo chmod +t /my_temp_dir
# Ya sudo chmod 1777 /my_temp_dir

# 3. Permissions dekho
ls -ld /my_temp_dir
# Output: drwxrwxrwt ... root root ... /my_temp_dir ( 't' for others execute)

# 4. UserA se login karo aur ek file banao
# userA$ cd /my_temp_dir
# userA$ touch userA_file.txt

# 5. UserB se login karo (same system)
# userB$ cd /my_temp_dir
# userB$ touch userB_file.txt

# 6. UserB se UserA ki file delete karne ki koshish karo
# userB$ rm userA_file.txt
# Output: rm: cannot remove 'userA_file.txt': Operation not permitted
# UserB cannot delete UserA's file, even though /my_temp_dir has write permission for 'others'.
```

**Summary of Special Permissions:**

| Octal Prefix | Symbolic | Name        | Apply To        | Effect                                                         | Example                                     |
| :----------- | :------- | :---------- | :-------------- | :--------------------------------------------------------------- | :------------------------------------------ |
| `4`          | `u+s`    | SUID        | Executable Files | Program runs with owner's permissions.                           | `passwd`                                    |
| `2`          | `g+s`    | SGID        | Executable Files | Program runs with group owner's permissions.                     | `wall`                                      |
| `2`          | `g+s`    | SGID        | Directories     | New files/dirs inherit parent's group owner.                     | Shared project directories (`/shared_data`) |
| `1`          | `+t`     | Sticky Bit  | Directories     | Only file owner (or root) can delete/rename files in that dir. | `/tmp`                                      |

#### 3.5 Default Permissions: `umask`

`umask` (user file-creation mode mask) ek value hai jo define karti hai ki jab naye files aur directories banaye jaate hain, toh unhe default roop se kaunsi permissions milengi. Umask `permission *hataane*` ke liye use hota hai.

**Calculation:**

*   **Files ke liye base permissions:** `666` (`rw-rw-rw-`)
*   **Directories ke liye base permissions:** `777` (`rwxrwxrwx`)

Nayi file/directory ki permissions = Base Permissions MINUS `umask` value.

**Example:**
Agar `umask` value `0022` (ya `022`) hai:

1.  **Files ke liye:**
    *   Base: `666`
    *   Umask: `022`
    *   Result: `644` (`rw-r--r--`)
    *   Calculation: `6-0=6`, `6-2=4`, `6-2=4`

2.  **Directories ke liye:**
    *   Base: `777`
    *   Umask: `022`
    *   Result: `755` (`rwxr-xr-x`)
    *   Calculation: `7-0=7`, `7-2=5`, `7-2=5`

**Commands:**
*   `umask`: Current umask value dekhne ke liye. (Output generally octal mein hota hai, `0022` ya `0002`).
*   `umask `: Umask value change karne ke liye. (Yeh session-specific hota hai).

**Practice:**

```bash
# 1. Current umask dekho
umask

# 2. Ek file aur directory banao aur permissions dekho
touch file_umask.txt
mkdir dir_umask
ls -l file_umask.txt dir_umask

# 3. Umask change karo
umask 007 # Others ko ab kuch nahi milega by default

# 4. Nayi file aur directory banao aur permissions dekho
touch file_umask_new.txt
mkdir dir_umask_new
ls -l file_umask_new.txt dir_umask_new
# File: rw-rw---- (660), Dir: rwxrwx--- (770) hona chahiye
```

#### 3.6 Changing Ownership: `chown` aur `chgrp`

*   `chown : `: File ka owner aur group dono change karne ke liye.
    *   `chown user1 file.txt`: Owner ko `user1` banao.
    *   `chown :group1 file.txt`: Group ko `group1` banao (owner same rahega).
    *   `chown user1:group1 file.txt`: Owner `user1` aur group `group1` banao.
    *   `-R` option recursive change ke liye.
*   `chgrp  `: Sirf file ka group change karne ke liye.
    *   `chgrp sales report.txt`
    *   `-R` option recursive change ke liye.

**Note:** `chown` aur `chgrp` commands ko generally `root` user ya `sudo` ke saath run kiya jaata hai, kyunki aap apni files ka ownership dusron ko de sakte hain, but dusron ki files ka ownership khud nahi le sakte (unless you are root).

#### 3.7 Access Control Lists (ACLs) - (RHCE Focus)

Standard ugo (user, group, others) permissions kai baar insufficient ho sakte hain. Socho ki aap chahte hain ki `userA` ek file ko read kare, `userB` usko read aur write kare, aur `groupC` bhi read kare. Standard permissions mein yeh manage karna mushkil hai. Yahan ACLs kaam aate hain.

ACLs file permissions ko more granular control provide karte hain.

**Commands:**

*   `getfacl `: Existing ACLs ko dekhne ke liye.
*   `setfacl  `: ACLs ko set ya modify karne ke liye.

**Important `setfacl` Options:**

*   `-m`: Modify (add/change) an ACL entry.
    *   `u::`: Specific user ke liye.
    *   `g::`: Specific group ke liye.
*   `-x`: Remove an ACL entry.
*   `-b`: Remove all extended ACL entries.
*   `-R`: Recursive (directories ke liye).

**ACL Mask:**
Jab aap ACL set karte hain, toh ek `mask` entry automatically generate hoti hai. Yeh mask define karta hai ki additional user aur group entries ko *maximum* kya permissions mil sakti hain. Agar mask `r--` hai, toh koi bhi ACL entry `rw-` ya `rwx` hone par bhi effectively `r--` se zyada permissions nahi degi. `setfacl -m m::rwx file.txt` se mask ko explicitly set kar sakte hain.

**Example:**

```bash
# 1. Ek test file banao
touch acl_test_file.txt
ls -l acl_test_file.txt # Standard permissions dekho (e.g., -rw-rw-r--)

# 2. ACLs dekho (abhi koi extended ACL nahi hoga)
getfacl acl_test_file.txt

# 3. Ek specific user ko extra read/write access do (e.g., 'user1' ko)
# (Make sure 'user1' exists: sudo useradd user1)
sudo setfacl -m u:user1:rw acl_test_file.txt

# 4. ACLs fir se dekho
getfacl acl_test_file.txt
# Output mein user1 ki entry dikhegi, aur ek 'mask' entry bhi dikhegi.
# Permissions output mein '+' sign bhi dikhega: -rw-rw-r--+

# 5. User1 se login karke file mein write karne ki koshish karo
# user1$ echo "User1 wrote this" >> acl_test_file.txt
# user1$ cat acl_test_file.txt # Successful

# 6. Ek group ko sirf read access do (e.g., 'developers' group ko)
# (Make sure 'developers' group exists: sudo groupadd developers)
sudo setfacl -m g:developers:r acl_test_file.txt
getfacl acl_test_file.txt

# 7. Ek specific ACL entry remove karo
sudo setfacl -x u:user1 acl_test_file.txt
getfacl acl_test_file.txt

# 8. Sabhi extended ACL entries remove karo
sudo setfacl -b acl_test_file.txt
getfacl acl_test_file.txt
```

---

### Practice Tasks (Hinglish)

Yeh tasks aapko RHCSA aur RHCE exam-style scenarios ke liye prepare karenge. Apne Linux VM (Virtual Machine) par practice karein.

**Task 1: Directory Structure aur Basic Navigation**
1.  Apni current working directory find karo.
    `pwd`
2.  Root directory (`/`) mein jaao. Uske contents list karo aur identify karo `/etc`, `/var`, `/home` directories.
    `cd /`
    `ls -F`
3.  `/var/log` directory mein jaao aur uske contents list karo. Koi bhi teen log files ke naam batao.
    `cd /var/log`
    `ls -l`
4.  Apni home directory mein wapas aao.
    `cd ~`

**Task 2: Hard aur Soft Links**
1.  Apni home directory mein `my_docs` naam ki ek directory banao.
    `mkdir ~/my_docs`
2.  `my_docs` ke andar `report.txt` naam ki ek file banao aur usmein kuch content likho.
    `echo "This is the quarterly report." > ~/my_docs/report.txt`
3.  `report.txt` ka ek hard link apni home directory mein `hard_report.txt` naam se banao.
    `ln ~/my_docs/report.txt ~/hard_report.txt`
4.  `report.txt` ka ek soft link apni home directory mein `sym_report.txt` naam se banao.
    `ln -s ~/my_docs/report.txt ~/sym_report.txt`
5.  `ls -li` command use karke teeno files ke inode numbers aur details dekho. Kya hard link aur original file ka inode same hai? Kya soft link ka inode alag hai?
    `ls -li ~/my_docs/report.txt ~/hard_report.txt ~/sym_report.txt`
6.  Original `report.txt` file ko delete kar do. `hard_report.txt` aur `sym_report.txt` ko access karke dekho. Kaunsa kaam karega aur kaunsa nahi, aur kyun?
    `rm ~/my_docs/report.txt`
    `cat ~/hard_report.txt`
    `cat ~/sym_report.txt`

**Task 3: Basic Permissions (`chmod`, `chown`, `chgrp`)**
1.  Apni home directory mein `secure_data` naam ki ek directory banao.
    `mkdir ~/secure_data`
2.  `secure_data` directory aur uske andar `private.txt` naam ki ek file banao (usmein kuch content bhi add karo).
    `echo "Top Secret" > ~/secure_data/private.txt`
3.  `private.txt` ke permissions `rw-r-----` set karo (owner ko read/write, group ko read only, others ko kuch nahi). Octal aur Symbolic, dono modes use karke karo.
    *   Octal: `chmod 640 ~/secure_data/private.txt`
    *   Symbolic: `chmod u=rw,g=r,o-rwx ~/secure_data/private.txt`
4.  `secure_data` directory ke permissions `rwxr-x---` set karo (owner ko full, group ko read/execute, others ko kuch nahi).
    `chmod 750 ~/secure_data`
5.  Check karo ki permissions sahi set hue hain.
    `ls -l ~/secure_data`
    `ls -l ~/secure_data/private.txt`
6.  Ek naya user (`testuser1`) aur ek naya group (`devteam`) banao.
    `sudo useradd testuser1`
    `sudo groupadd devteam`
7.  `private.txt` file ka owner `testuser1` aur group `devteam` set karo.
    `sudo chown testuser1:devteam ~/secure_data/private.txt`
8.  `secure_data` directory ka group `devteam` set karo.
    `sudo chgrp devteam ~/secure_data`
9.  Permissions aur ownership verify karo.
    `ls -l ~/secure_data`
    `ls -l ~/secure_data/private.txt`

**Task 4: Special Permissions (`SUID`, `SGID`, `Sticky Bit`) - RHCE Focus**
1.  Apni home directory mein `shared_project` naam ki ek directory banao.
    `mkdir ~/shared_project`
2.  `shared_project` directory ka group `devteam` set karo (jo Task 3 mein banaya tha).
    `sudo chgrp devteam ~/shared_project`
3.  `shared_project` par SGID bit set karo (taki usmein banne wali files `devteam` group ko inherit karein). Permissions `rwxrws---` hone chahiye.
    `sudo chmod 2770 ~/shared_project`
4.  Verify karo SGID bit set hua hai.
    `ls -ld ~/shared_project`
5.  `testuser1` se login karo (agar aap root/sudo user hain). `shared_project` mein jaao aur ek file banao (`testuser1_file.txt`). Is file ki ownership aur group check karo. Kya group `devteam` hai?
    *   (Ek naye terminal mein `su - testuser1` karo)
    *   `cd ~/shared_project`
    *   `touch testuser1_file.txt`
    *   `ls -l testuser1_file.txt`
    *   (`exit` karke wapas original user par aa jao)
6.  Apni home directory mein `secret_app.sh` naam ki ek script banao jo `whoami` command run karti hai. Iska owner `root` set karo aur ispar SUID bit set karo. Permissions `rwsr-xr-x` hone chahiye.
    `echo '#!/bin/bash' > ~/secret_app.sh`
    `echo 'echo "Running as: $(whoami)"' >> ~/secret_app.sh`
    `chmod +x ~/secret_app.sh`
    `sudo chown root:root ~/secret_app.sh`
    `sudo chmod 4755 ~/secret_app.sh`
7.  Verify karo SUID set hua hai. `ls -l ~/secret_app.sh`
8.  `secret_app.sh` ko run karo. Output mein kaunsa user dikhta hai?
    `~/secret_app.sh`
9.  Apni home directory mein `public_area` naam ki ek directory banao. Iske permissions `rwxrwxrwt` set karo (sticky bit ke saath).
    `mkdir ~/public_area`
    `chmod 1777 ~/public_area`
10. Verify karo sticky bit set hua hai. `ls -ld ~/public_area`
11. `testuser1` se login karo. `public_area` mein jaao aur `testuser1_temp.txt` naam ki file banao.
    *   `su - testuser1`
    *   `cd ~/public_area`
    *   `touch testuser1_temp.txt`
    *   `exit`
12. Ab apne original user se `testuser1_temp.txt` ko delete karne ki koshish karo. Kya aap kar payenge? Kyun?
    `rm ~/public_area/testuser1_temp.txt`

**Task 5: `umask` aur `ACLs` - RHCE Focus**
1.  Current `umask` value check karo.
    `umask`
2.  `umask` ko `007` set karo.
    `umask 007`
3.  Ek `umask_test_file.txt` aur `umask_test_dir` banao. Inki permissions check karo. Kya aapki expected permissions (`660` for file, `770` for dir) dikh rahi hain?
    `touch umask_test_file.txt`
    `mkdir umask_test_dir`
    `ls -l umask_test_file.txt umask_test_dir`
4.  Original `umask` value par wapas aao ya terminal session close karke open karo.
5.  Apni home directory mein `acl_data` naam ki ek file banao.
    `echo "Sensitive Data" > ~/acl_data`
6.  `testuser1` ko `acl_data` par `read, write` access do ACLs use karke.
    `sudo setfacl -m u:testuser1:rw ~/acl_data`
7.  `testuser1` se login karke `acl_data` ko read aur write karne ki koshish karo.
    *   `su - testuser1`
    *   `cat ~/acl_data`
    *   `echo "More data from user1" >> ~/acl_data`
    *   `cat ~/acl_data`
    *   `exit`
8.  `acl_data` ke ACLs dekho (`getfacl`). Output mein `mask` entry par dhyaan do.
    `getfacl ~/acl_data`
9.  `testuser1` ke liye ACL entry remove karo.
    `sudo setfacl -x u:testuser1 ~/acl_data`
10. Fir se ACLs check karo. `testuser1` ki entry remove ho gayi hogi.
    `getfacl ~/acl_data`

---

**Summary:**
Aaj humne Linux ke fundamental building blocks, yani **Directory Structure**, **Hard aur Soft Links**, aur **Permissions** (basic, special, umask, ACLs) ko detail mein samjha. RHCSA aur RHCE exams ke liye yeh topics bahut crucial hain, aur real-world administration mein bhi daily use hote hain.

**Further Learning:**
*   `man chmod`, `man chown`, `man setfacl`, `man ln`: Commands ki official documentation padho.
*   Online RHCSA/RHCE practice labs search karo.
*   Different scenarios mein permissions aur links ko experiment karte raho.

Keep practicing, aur aap zaroor in concepts mein maharat hasil kar lenge! Good luck!
