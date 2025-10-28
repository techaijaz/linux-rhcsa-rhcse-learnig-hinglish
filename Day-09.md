Day 9: Ownership & groups (chown, chgrp, groups)
---
Namaste RHCSA aur RHCE Aspirants!

Aaj hum Linux ke ek bahut hi fundamental aur security point of view se extremely important topic par discuss karenge: **Ownership & Groups**. Yeh topic aapke RHCSA aur RHCE exam dono ke liye crucial hai, kyunki files aur directories ka access control is par hi based hota hai.

Chaliye, shuru karte hain!

---

## RHCSA + RHCE Training: Ownership & Groups (chown, chgrp, groups) – The Foundation of Linux Security!

**Lesson Goals (Iss Lesson se aap kya seekhenge):**

*   Files aur Directories ke Owner aur Group Owner ko samajhna.
*   `ls -l` command ka use karke ownership details dekhna.
*   `chown` command ka use karke file/directory ka user owner change karna.
*   `chgrp` command ka use karke file/directory ka group owner change karna.
*   `groups` command ka use karke user ki group membership dekhna.
*   `usermod` command se user ko groups mein add karna.
*   Recursive ownership changes (`-R`) aur advanced `chown` syntax ko samajhna.
*   Numeric UIDs aur GIDs ka ownership commands mein use.
*   SGID (Set Group ID) ka ownership par kya asar hota hai, yeh samajhna (RHCE level).

---

### 1. Introduction: Ownership & Groups Kya Hain?

Imagine kijiye aapka ek office hai. Office mein alag-alag departments hain (Sales, Marketing, IT, HR). Har file ya document ka ek "owner" hota hai (jisne banaya ya jisko assign kiya gaya hai), aur ek "group owner" hota hai (jis department se woh file related hai).

Linux mein bhi files aur directories ka ek:

1.  **User Owner (File Owner):** Jis user ne file banayi hai, ya jise us file ka owner assign kiya gaya hai. Sirf root user hi kisi bhi file ka owner change kar sakta hai.
2.  **Group Owner:** Jis group ko woh file assign ki gayi hai. Us group ke saare members ko us file par specific permissions mil sakte hain.

Yeh dono milkar file permissions (read, write, execute) ke saath decide karte hain ki kaun user ya kaunsa group file ko access kar sakta hai aur kaise. **Yeh Linux security ka ek bahut bada pillar hai!**

### 2. Seeing Ownership: `ls -l`

Sabse pehle, hum files ki ownership dekhte kaise hain? Iske liye hum `ls -l` command ka use karte hain.

**Command:**

```bash
ls -l [file/directory_name]
```

**Explanation:**

`ls -l` output mein aapko kayi columns milenge. Jin do columns par hum focus karenge, woh hain:

*   **Third Column:** Yeh **User Owner** batata hai.
*   **Fourth Column:** Yeh **Group Owner** batata hai.

**Example:**

```bash
ls -l /etc/passwd
```

**Output:**

```
-rw-r--r--. 1 root root 2235 Feb 28 10:30 /etc/passwd
```

Yahan:

*   **`root`** (third column) is the **User Owner**.
*   **`root`** (fourth column) is the **Group Owner**.

**Practice Task 1.1:**

1.  Apne home directory mein ek file banao: `touch my_test_file.txt`
2.  `ls -l` command se uski ownership dekho. Aap dekhoge ki user owner aur group owner dono aapka username hoga.
3.  `/var/log/messages` file ki ownership dekho: `ls -l /var/log/messages`

---

### 3. Changing User Owner: `chown`

`chown` command ka matlab hai "change owner". Iska use karke hum kisi file ya directory ka **user owner** change kar sakte hain.

**Syntax:**

```bash
chown [OPTIONS] USER[:GROUP] FILE/DIRECTORY...
```

**Important Notes:**

*   **Sirf `root` user hi kisi aur user ko file ka owner bana sakta hai.** Ek normal user sirf un files ka owner change kar sakta hai jo uske paas already owned hain, aur woh bhi sirf *apne aap ko* owner assign kar sakta hai. Kisi aur user ko nahin. **Yeh bahut important security concept hai!**
*   `USER` mein aap username ya uska numeric User ID (UID) de sakte ho.

**Examples:**

1.  **Change user owner of a file:**
    (Aapko `root` user banna padega ya `sudo` use karna padega)
    ```bash
    # Create a dummy file as a normal user
    touch file_for_john.txt
    ls -l file_for_john.txt
    # Output: -rw-rw-r--. 1 youruser youruser ... file_for_john.txt

    # Now, as root or using sudo, change the owner to 'john' (assuming 'john' user exists)
    sudo chown john file_for_john.txt
    ls -l file_for_john.txt
    # Output: -rw-rw-r--. 1 john youruser ... file_for_john.txt
    ```

2.  **Change user owner and group owner simultaneously (Very common and efficient!):**
    Aap `USER:GROUP` syntax ka use karke ek hi command mein user owner aur group owner dono change kar sakte ho.
    (Again, `root` ya `sudo` access required)
    ```bash
    sudo chown mary:developers file_for_john.txt
    ls -l file_for_john.txt
    # Output: -rw-rw-r--. 1 mary developers ... file_for_john.txt
    ```

3.  **Recursive change (`-R`):**
    Agar aapko ek directory aur uske andar ki saari files aur subdirectories ka owner change karna hai, toh `-R` (recursive) option use karte hain.
    ```bash
    sudo mkdir /opt/project_alpha
    sudo touch /opt/project_alpha/index.html
    sudo touch /opt/project_alpha/data.txt
    sudo chown -R webadmin:webteam /opt/project_alpha/
    ls -l /opt/project_alpha/
    ls -l /opt/project_alpha/index.html
    ```
    (Yahan `webadmin` user aur `webteam` group create kiye hue hone chahiye)

4.  **Using Numeric UID:**
    Linux internally users ko UIDs se pehchanta hai. Aap `id -u john` se john ka UID dekh sakte hain.
    ```bash
    # Suppose john's UID is 1001
    sudo chown 1001 file_for_john.txt
    ls -l file_for_john.txt
    # Output: -rw-rw-r--. 1 john developers ... file_for_john.txt (Linux automatically maps 1001 to 'john')
    ```
    **RHCE Tip:** Agar koi user ID exist nahin karta, toh `chown` command numeric ID ko hi owner name ke roop mein dikhayega. Example: `sudo chown 9999 file.txt` will show `9999` as the owner if UID 9999 has no corresponding username.

**Practice Task 3.1:**

1.  `sudo useradd devuser` aur `sudo groupadd devteam` commands se ek user `devuser` aur ek group `devteam` banao. (Password set karna mat bhoolna: `sudo passwd devuser`)
2.  Apne home directory mein ek directory banao: `mkdir my_project`
3.  `my_project` ke andar do files banao: `touch my_project/task1.txt` aur `touch my_project/report.docx`
4.  `sudo chown devuser my_project/task1.txt` command se `task1.txt` ka user owner `devuser` mein change karo. `ls -l` se verify karo.
5.  `sudo chown -R devuser:devteam my_project/` command se `my_project` directory aur uske saare contents ka user owner `devuser` aur group owner `devteam` mein change karo. `ls -l` aur `ls -l my_project/` se verify karo.

---

### 4. Changing Group Owner: `chgrp`

`chgrp` command ka matlab hai "change group". Iska use karke hum kisi file ya directory ka **group owner** change kar sakte hain.

**Syntax:**

```bash
chgrp [OPTIONS] GROUP FILE/DIRECTORY...
```

**Important Notes:**

*   **Ek normal user sirf un files ka group owner change kar sakta hai jo uske paas owned hain, aur woh bhi sirf un groups mein jinka woh khud member ho.**
*   `root` user kisi bhi file ka group owner kisi bhi group mein change kar sakta hai.
*   `GROUP` mein aap group name ya uska numeric Group ID (GID) de sakte ho.

**Examples:**

1.  **Change group owner of a file:**
    ```bash
    # Create a file
    touch sales_report.pdf
    ls -l sales_report.pdf
    # Output: -rw-rw-r--. 1 youruser youruser ... sales_report.pdf

    # As root or using sudo, change group owner to 'sales' (assuming 'sales' group exists)
    sudo chgrp sales sales_report.pdf
    ls -l sales_report.pdf
    # Output: -rw-rw-r--. 1 youruser sales ... sales_report.pdf
    ```

2.  **Recursive change (`-R`):**
    Same as `chown`, `-R` option recursively changes group ownership.
    ```bash
    sudo chgrp -R marketing /var/www/html/campaigns/
    ```

**RHCE Tip: `chown --reference=RFILE FILE...`**
Yeh ek bahut handy command hai. Agar aapko ek file ki ownership (user aur group dono) kisi doosri file (RFILE) ke jaisi banani hai, toh aap `--reference` option use kar sakte ho.
```bash
sudo chown --reference=/etc/passwd my_test_file.txt
ls -l my_test_file.txt
# Output: -rw-rw-r--. 1 root root ... my_test_file.txt (because /etc/passwd is owned by root:root)
```

**Practice Task 4.1:**

1.  `sudo groupadd it_support` aur `sudo groupadd hr_team` commands se do naye groups banao.
2.  Apne home directory mein `hr_docs` naam ki ek directory banao, aur uske andar `policy.txt` naam ki ek file banao.
3.  `sudo chgrp hr_team hr_docs/policy.txt` command se `policy.txt` ka group owner `hr_team` mein change karo. Verify with `ls -l`.
4.  `sudo chown youruser:it_support hr_docs/` command se `hr_docs` directory ka user owner aapka username aur group owner `it_support` mein change karo. Verify with `ls -l`.

---

### 5. Seeing User's Group Membership: `groups`

Jab hum group ownership ki baat karte hain, toh yeh bhi pata hona chahiye ki kaunsa user kis-kis group ka member hai. Iske liye `groups` command ka use hota hai.

**Syntax:**

```bash
groups [USERNAME]
```

**Examples:**

1.  **Current user ke groups dekhne ke liye:**
    ```bash
    groups
    # Output: youruser adm cdrom sudo dip plugdev lpadmin sambashare
    ```

2.  **Kisi specific user ke groups dekhne ke liye:**
    ```bash
    groups john
    # Output: john : john developers
    ```

**RHCE Tip:** `id` command bhi user information (UID, GID, groups) dikhati hai. `id john` ya `id -Gn john` (only group names) bahut useful hai.

---

### 6. Managing Group Membership: `usermod -aG`

Ownership aur groups ka topic tab tak poora nahin hota jab tak hum users ko groups mein add karna na seekhen. `usermod` command iske liye use hoti hai.

**Syntax:**

```bash
sudo usermod -aG GROUPNAME USERNAME
```

**Explanation:**

*   `usermod`: User modify karne ki command.
*   `-a`: "Append" ka matlab, existing groups ko hataye bina naya group add karo.
*   `-G GROUPNAME`: Specify the **supplementary group** to add the user to.

**Example:**

```bash
# Add user 'john' to the 'developers' group
sudo usermod -aG developers john
```

**Important:** Group membership change hone ke baad, user ko logout karke login karna padta hai, tabhi changes apply hote hain.

**RHCE Tip: `newgrp`**
`newgrp` command user ko temporary roop se kisi aur group ki permissions acquire karne mein help karti hai, bina logout kiye.
Agar user `john` `developers` group ka member hai, lekin uski primary group `john` hai. Woh developers group ki shared directory mein files create karna chahta hai.
```bash
# Login as 'john'
groups
# Output: john developers (john's primary group is 'john')

newgrp developers
# Ab 'john' ki effective primary group 'developers' ho gayi hai.
# Jo bhi nayi files ab 'john' banayega, unka group owner 'developers' hoga (agar directory par SGID set ho).
```
`newgrp` session khatam hone par ya jab user exit karta hai, toh primary group wapas original ho jaati hai.

**Practice Task 6.1:**

1.  User `devuser` ko `it_support` group ka member banao: `sudo usermod -aG it_support devuser`
2.  `groups devuser` se verify karo ki `devuser` ab `it_support` group ka member hai.
3.  `devuser` ke roop mein login karo (ya `su - devuser` se switch karo).
4.  `groups` command chalao aur dekho ki `it_support` list mein hai ya nahin.
5.  `newgrp it_support` command chalao.
6.  `touch /tmp/new_file_as_it.txt` banao.
7.  Apni main user ID par wapas aao aur `ls -l /tmp/new_file_as_it.txt` karke dekho ki is file ka group owner kya hai. (Agar SGID set nahin hai to `devuser` hi dikhega, but the *effective* group was `it_support` when creating).

---

### 7. Advanced RHCE Considerations: SGID & Ownership

Jab hum directories par **SGID (Set Group ID)** permission set karte hain, toh group ownership ka behavior change ho jaata hai.

*   Agar ek directory par SGID (represented by `s` in the group permissions, like `drwxrwsr-x`) set hai, toh us directory mein banayi gayi har nayi file ya subdirectory ka group owner automatically parent directory ka group owner ban jaata hai, irrespective of the user's primary group.

**Example:**

1.  Ek shared directory banao:
    ```bash
    sudo mkdir /shared_dev_data
    sudo chown root:devteam /shared_dev_data
    sudo chmod 2775 /shared_dev_data # The '2' sets the SGID bit
    ls -ld /shared_dev_data
    # Output: drwxrwsr-x. 2 root devteam ... /shared_dev_data
    ```
    (Notice the `s` in `rws` for the group permissions)

2.  Ab `devuser` (jo `devteam` ka member hai) login kare:
    ```bash
    # As devuser
    cd /shared_dev_data
    touch new_dev_file.txt
    mkdir new_dev_dir
    ls -l
    # Output:
    # -rw-rw-r--. 1 devuser devteam  0 May 10 12:00 new_dev_file.txt
    # drwxrwsr-x. 2 devuser devteam 40 May 10 12:00 new_dev_dir
    ```
    Yahan `new_dev_file.txt` aur `new_dev_dir` dono ka group owner automatically `devteam` ho gaya hai, even if `devuser` ki primary group `devuser` hai. Yeh collaborative environments mein bahut useful hai.

---

### 8. Practice Tasks (Comprehensive)

**Scenario:** Aapko ek shared web project setup karna hai jahan `webadmin` naam ka user files ka owner hoga, aur `webdevs` group ke members us par kaam kar sakenge.

1.  **Users aur Groups Create karo:**
    *   `sudo useradd webadmin`
    *   `sudo passwd webadmin` (Set password for webadmin)
    *   `sudo groupadd webdevs`
    *   `sudo useradd developer1`
    *   `sudo passwd developer1`
    *   `sudo useradd developer2`
    *   `sudo passwd developer2`

2.  **Shared Project Directory Banao:**
    *   `sudo mkdir /var/www/my_webapp`
    *   `sudo chown webadmin:webdevs /var/www/my_webapp` (User owner `webadmin`, Group owner `webdevs`)
    *   `sudo chmod 2775 /var/www/my_webapp` (Permissions: `rwx` for owner, `rws` for group - `s` is SGID, `r-x` for others)
    *   `ls -ld /var/www/my_webapp` se verify karo ownership aur permissions.

3.  **Developers ko Group mein Add karo:**
    *   `sudo usermod -aG webdevs developer1`
    *   `sudo usermod -aG webdevs developer2`
    *   `groups developer1` aur `groups developer2` se verify karo.

4.  **Developer ke roop mein Files Create karo:**
    *   `su - developer1` (Switch to developer1 user)
    *   `cd /var/www/my_webapp`
    *   `touch index.html`
    *   `echo "Welcome to our webapp" > index.html`
    *   `mkdir css`
    *   `touch css/style.css`
    *   `ls -l /var/www/my_webapp` aur `ls -l /var/www/my_webapp/css` se files ki ownership dekho.
        *   Expected output: User owner `developer1`, Group owner `webdevs` (due to SGID).

5.  **Ownership Copy karo (`--reference`):**
    *   `exit` (developer1 se log out karo, wapas apne main user par aao)
    *   `sudo touch /var/www/my_webapp/config.php` (yeh file `root:root` se banegi)
    *   `ls -l /var/www/my_webapp/config.php`
    *   `sudo chown --reference=/var/www/my_webapp/index.html /var/www/my_webapp/config.php`
    *   `ls -l /var/www/my_webapp/config.php` se verify karo.
        *   Expected output: User owner `developer1`, Group owner `webdevs`.

---

### 9. Review aur Summary

*   **`ls -l`**: Files aur directories ki ownership aur permissions dekhne ke liye.
*   **`chown`**: User owner change karne ke liye. (Sirf root hi doosre users ko assign kar sakta hai).
*   **`chgrp`**: Group owner change karne ke liye. (Normal user sirf un groups mein assign kar sakta hai jinka woh member ho).
*   **`chown USER:GROUP`**: Ek hi command mein user aur group owner change karne ka shortcut.
*   **`-R` option**: Recursive changes ke liye (directories aur unke contents ke liye).
*   **`groups [USERNAME]`**: User ki group memberships dekhne ke liye.
*   **`sudo usermod -aG GROUPNAME USERNAME`**: User ko supplementary group mein add karne ke liye (login/logout required).
*   **`newgrp GROUPNAME`**: Temporarily effective primary group change karne ke liye (RHCE level).
*   **SGID (`chmod 2XXX`)**: Directory par set karne par, us directory mein banayi gayi nayi files/directories ka group owner parent directory ke group owner jaisa ho jaata hai. Yeh shared environments ke liye bahut powerful feature hai.

Ownership aur groups Linux file system security ka ek basic, lekin powerful hissa hain. In commands aur concepts ko master karna aapko RHCSA aur RHCE exams mein success dilayega, aur real-world scenario mein system administration mein help karega.

---

### Next Steps:

Ab jab aap ownership aur groups ko samajh gaye hain, toh next step hai permissions (rwx) ko detail mein padhna aur `chmod` command ka use karna. Phir hum in dono concepts ko combine karke effective file access control samajh payenge. Iske baad, ACLs (Access Control Lists) ka topic aata hai jo aur fine-grained permissions provide karta hai.

Happy Learning! Agar koi sawal ho, toh poochne mein jhijhak mat karna!
