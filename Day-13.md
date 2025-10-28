Day 13: Sudo, su, privilege escalation
---
Namaste RHCSA/RHCE Training me aap ka swagat hai!

aaj hamara session bahot hi crucial topic per hai: **Sudo, su, and Privilege Escalation**. ye Concepts na sirf aap k exam exam k liye zaroori hai balki Real-world System Administration and Security ke liye bhi fundamental hai।

---

## RHCSA/RHCE Training: Sudo, su, and Privilege Escalation - Master Your System's Security
**(Sudo, su and Privilege Escalation: apni System Security me mahir bane)**

### Table of Contents:
1.  **Introduction**
2.  **Prerequisites**
3.  **Understanding Privilege**
4.  **The `su` Command**
    *   `su` Examplis
    *   Login Shell vs. Non-Login Shell
    *   Security Considerations
5.  **The `sudo` Command **
    *   `sudo` use
    *   `/etc/sudoers` File and its structure.
    *   `visudo` Utility
    *   `sudoers` Entry Breakdown
    *   User Aliases, Host Aliases, Runas Aliases, Command Aliases
    *   `NOPASSWD` Option
    *   Group-based Sudo Access
    *   `sudo` के साथ Options
6.  **Privilege Escalation**
    *   Legitimate vs. Illegitimate Escalation
    *   Security Best Practices
7.  **Practice Tasks**
8.  **Troubleshooting Common Issues**
9.  **Summary and Conclusion**

---

### 1. Introduction 

Linux systems me, security ek top priority hai. Normal users ke paas system-wide changes karne k adikar (privileges) nahi hote। Root user (jise superuser bhi kehte hain) ke paas system per complete control होता है।

**Privilege Escalation** का मतलब है, किसी user के permissions को अस्थायी (temporarily) या स्थायी (permanently) रूप से बढ़ाना, ताकि वह ऐसे tasks कर सके जो उसके normal permissions में नहीं हैं। हम legitimate privilege escalation के लिए `su` और `sudo` का उपयोग करते हैं।

*   **`su` (Substitute User / Switch User):** यह आपको किसी दूसरे user, अक्सर root, की identity adopt करने की अनुमति देता है। इसके लिए आपको target user का password जानना होगा।
*   **`sudo` (Substitute User DO / Super User DO):** यह आपको root या किसी अन्य user के रूप में specific commands run करने की अनुमति देता है, बिना उस user के password को जाने। इसके बजाय, यह आपके अपने password का उपयोग करता है (यदि configure किया गया हो)।

इन दोनों commands को समझना RHCSA/RHCE exams के लिए बहुत ज़रूरी है और real-world scenarios में आपकी system administration skills को boost करेगा।

### 2. Prerequisites (पूर्व-आवश्यकताएं)

इस lesson को समझने के लिए, आपको निम्नलिखित चीज़ों की basic जानकारी होनी चाहिए:
*   Basic Linux commands (जैसे `ls`, `cd`, `pwd`, `cat`, `vi`/`nano`)
*   User और Group Management (users बनाना, groups में जोड़ना)
*   File Permissions (chmod, chown)

### 3. Understanding Privilege (अधिकार को समझना)

Linux में, हर process एक user और group ID के साथ run होता है। ये IDs determine करते हैं कि process किस files, directories, और system resources को access कर सकता है।
*   **Root User (UID 0):** The most powerful user. Can do anything on the system.
*   **Regular Users (UID 1000+):** Limited access, cannot modify core system files or install software without elevated privileges.

जब हम privilege escalate करते हैं, तो हम temporarily root user के अधिकार प्राप्त करते हैं ताकि system-level tasks जैसे software installation, service management, user creation, etc. कर सकें।

### 4. The `su` Command (su कमांड)

`su` command आपको एक user account से दूसरे user account में switch करने की अनुमति देता है।

**`su` का उपयोग:**

**Syntax:** `su [options] [username]`

*   **Root user में स्विच करना (Non-login shell):**
    ```bash
    su
    ```
    *   यह आपको root user में स्विच कर देगा, लेकिन आपका **current environment variables** (जैसे PATH) वही रहेंगे जो आपके original user के थे।
    *   यह आपसे root का password मांगेगा।

*   **Root user में स्विच करना (Login shell - Preferred):**
    ```bash
    su -
    # या
    su - root
    ```
    *   **यह सबसे ज़्यादा recommended तरीका है।** `-` (hyphen) option एक **login shell** simulate करता है। इसका मतलब है कि आप root के environment variables, working directory (`/root`), और PATH के साथ login करेंगे। यह unpredictable behavior से बचाता है।
    *   यह आपसे root का password मांगेगा।

*   **किसी अन्य user में स्विच करना:**
    ```bash
    su - newuser
    ```
    *   `newuser` के password की आवश्यकता होगी।
    *   यह `newuser` के login shell में स्विच कर देगा।

**उदाहरण:**
1.  **आप एक normal user 'devuser' के रूप में हैं।**
    ```bash
    devuser@localhost:~$ whoami
    devuser
    devuser@localhost:~$ pwd
    /home/devuser
    ```
2.  **Root में स्विच करें (non-login shell):**
    ```bash
    devuser@localhost:~$ su
    Password: 
    [root@localhost devuser]# whoami
    root
    [root@localhost devuser]# pwd
    /home/devuser  # ध्यान दें: Working directory नहीं बदला!
    [root@localhost devuser]# exit
    exit
    devuser@localhost:~$
    ```
3.  **Root में स्विच करें (login shell):**
    ```bash
    devuser@localhost:~$ su -
    Password: 
    Last login: Fri Sep 15 10:30:00 2023 on pts/0
    [root@localhost ~]# whoami
    root
    [root@localhost ~]# pwd
    /root  # ध्यान दें: Working directory बदल गया!
    [root@localhost ~]# exit
    logout
    devuser@localhost:~$
    ```

**Security Considerations:**
*   `su` का उपयोग करने के लिए आपको target user का password पता होना चाहिए। Root का password share करना एक security risk है।
*   `sudo` को ज़्यादा secure माना जाता है क्योंकि यह users को अपने ही password से authenticated होने के बाद specific commands चलाने की अनुमति देता है, बिना root password को reveal किए।

### 5. The `sudo` Command (sudo कमांड)

`sudo` आपको root या किसी अन्य user के रूप में specific commands run करने की अनुमति देता है। यह privilege escalation का preferred method है क्योंकि यह granular control और better auditing प्रदान करता है।

**`sudo` का उपयोग:**

**Syntax:** `sudo [options] command`

*   **एक command को root के रूप में execute करना:**
    ```bash
    sudo dnf update
    ```
    *   यह आपसे आपका **अपना password** मांगेगा (न कि root का)।
    *   `dnf update` command root privileges के साथ execute होगा।

*   **एक interactive shell को root के रूप में खोलना:**
    ```bash
    sudo -i
    ```
    *   यह एक **login shell** खोलेगा, जैसे आपने `su -` किया था, लेकिन आपके अपने password से authentication के बाद।
    *   **RHCSA/RHCE में यह बहुत ज़रूरी है!** आपको कई बार `sudo -i` का उपयोग करके root privileges के साथ tasks करने होंगे।

*   **एक non-login shell को root के रूप में खोलना:**
    ```bash
    sudo -s
    ```
    *   यह एक non-login shell खोलेगा, जिसमें root privileges होंगे।

*   **अपनी `sudo` permissions list करना:**
    ```bash
    sudo -l
    ```
    *   यह आपको दिखाएगा कि आप `sudo` का उपयोग करके कौन से commands run कर सकते हैं।

*   **`sudo` authentication timestamp को reset करना:**
    ```bash
    sudo -k
    ```
    *   `sudo` by default 5 मिनट के लिए आपके password को cache करता है। `sudo -k` उस cache को invalidate करता है, ताकि अगली बार `sudo` command चलाने पर आपसे password मांगा जाए।

**`/etc/sudoers` File और इसकी संरचना:**

यह `sudo` का दिल है। यह file define करती है कि कौन से users, कौन सी machines पर, किन commands को, किस user के रूप में execute कर सकते हैं।

**Important:**
*   **कभी भी `/etc/sudoers` को सीधे `vi` या `nano` से edit न करें!**
*   **हमेशा `visudo` utility का उपयोग करें।**

**`visudo` Utility (ज़रूरी!)**
`visudo` एक special editor है जो `/etc/sudoers` file को edit करने के लिए डिज़ाइन किया गया है। इसका सबसे बड़ा फायदा यह है कि यह file को save करने से पहले **syntax check** करता है। यदि कोई syntax error है, तो यह आपको file save करने से रोकेगा, जिससे system lock होने से बच जाएगा।

**`visudo` का उपयोग करना:**
```bash
sudo visudo
```
यह `sudoers` file को आपके default editor (जो आमतौर पर `vi` होता है) में खोलेगा।

**`sudoers` Entry Breakdown:**

`sudoers` file में entries का basic format ऐसा होता है:
```
User_Name   Machine_Name=(Runas_User)   Command
```
*   **`User_Name`:** वह user जिसे permissions दी जा रही हैं। (या `%Group_Name` एक group के लिए)
*   **`Machine_Name`:** वह host जिस पर ये permissions apply होती हैं। आमतौर पर `ALL` होता है, जिसका मतलब है किसी भी host पर।
*   **`(Runas_User)`:** वह user जिसके रूप में command execute किया जाएगा। (सबसे ज़्यादा `(ALL)` होता है, जिसका मतलब है किसी भी user के रूप में, लेकिन अक्सर `(root)` या `(operator)` जैसे specific users भी हो सकते हैं।)
*   **`Command`:** वह command जिसे user execute कर सकता है। (सबसे ज़्यादा `ALL` होता है, जिसका मतलब है कोई भी command)

**उदाहरण:**
```
john    ALL=(ALL)    ALL
```
*   User `john` किसी भी host (`ALL`) पर किसी भी user (`ALL`) के रूप में कोई भी command (`ALL`) चला सकता है। (यह बहुत broad permission है और आमतौर पर avoid किया जाता है।)

**User Aliases, Host Aliases, Runas Aliases, Command Aliases:**

RHCSA/RHCE में आपको `sudoers` file को organize करने के लिए aliases का उपयोग करना आना चाहिए। ये बड़ी `sudoers` files को readable और maintainable बनाते हैं।

*   **`User_Alias`:** Multiple users को एक single alias में group करता है।
    ```
    User_Alias    ADMINS = john, mary, peter
    ADMINS        ALL=(ALL) ALL
    ```
*   **`Host_Alias`:** Multiple hosts को एक alias में group करता है।
    ```
    Host_Alias    WEBHOSTS = webserver1, webserver2
    john          WEBHOSTS=(ALL) ALL
    ```
*   **`Runas_Alias`:** Multiple `Runas` users को एक alias में group करता है।
    ```
    Runas_Alias   OPERATORS = root, operator
    mary          ALL=(OPERATORS) /usr/bin/systemctl restart httpd
    ```
*   **`Cmnd_Alias`:** Multiple commands को एक alias में group करता है। **यह सबसे ज़्यादा उपयोग होता है!**
    ```
    Cmnd_Alias    PKGMGR = /usr/bin/dnf, /usr/bin/yum
    Cmnd_Alias    SRVCTRL = /usr/bin/systemctl start, /usr/bin/systemctl stop, /usr/bin/systemctl restart
    john          ALL=(ALL) PKGMGR, SRVCTRL
    ```
    *   यहां `john` सिर्फ `dnf`, `yum`, और `systemctl` के `start`, `stop`, `restart` commands को `sudo` कर सकता है।

**`NOPASSWD` Option:**

यह option user को `sudo` command चलाते समय password prompt को bypass करने की अनुमति देता है।
```
User_Name   ALL=(ALL)    NOPASSWD: /usr/bin/systemctl restart httpd
```
*   इस configuration के साथ, जब user `User_Name` `sudo /usr/bin/systemctl restart httpd` चलाएगा, तो उससे password नहीं मांगा जाएगा।
*   **Security Warning:** `NOPASSWD` का उपयोग सावधानी से करें। यदि कोई attacker ऐसे command को exploit कर सकता है जो `NOPASSWD` है, तो वह आसानी से root access प्राप्त कर सकता है। इसे केवल बहुत specific और low-risk commands के लिए उपयोग करना चाहिए।

**Group-based Sudo Access:**

आप entire groups को `sudo` privileges grant कर सकते हैं। यह `User_Alias` के समान है, लेकिन group members automatically privileges inherit करते हैं।
```
%wheel  ALL=(ALL)       ALL
```
*   यह line, जो अक्सर डिफ़ॉल्ट रूप से `sudoers` file में uncommented होती है, `wheel` group के सभी members को `sudo` privileges देती है। `wheel` group एक पारंपरिक group है जिसका उपयोग admin-like access के लिए किया जाता है।
*   यदि आप किसी user को `sudo` access देना चाहते हैं, तो आप उसे `wheel` group में जोड़ सकते हैं:
    ```bash
    sudo usermod -aG wheel username
    ```

**`sudo` के साथ Options:**

*   **`Defaults`:** `sudoers` file में आप global या user-specific defaults define कर सकते हैं।
    ```
    Defaults        log_input, log_output  # logs all input/output of sudo commands
    Defaults!wheel  !authenticate          # wheel group members don't need to authenticate
    Defaults        passwd_timeout=0       # password cache never expires (dangerous!)
    ```
    *   `Defaults targetpw` (या `targetpw`) user को target user (जैसे root) का password prompt करता है, न कि user का अपना password। यह `sudo` के default behavior (`!targetpw`) के विपरीत है। `targetpw` को आमतौर पर avoid किया जाता है क्योंकि यह `sudo` के main security advantage को कम कर देता है।

### 6. Privilege Escalation (अधिकार बढ़ाना - वैधानिक और अवैध)

*   **Legitimate Privilege Escalation:**
    *   `su` और `sudo` का उपयोग करके system administration tasks करना।
    *   ये methods control और auditing के साथ आते हैं।

*   **Illegitimate (Unauthorized) Privilege Escalation:**
    *   यह वह जगह है जहां एक attacker या malicious user system की कमजोरियों (vulnerabilities) या misconfigurations का फायदा उठाकर अनधिकृत (unauthorized) उच्च privileges प्राप्त करता है।
    *   **उदाहरण:**
        *   Misconfigured `sudoers` file (जैसे `NOPASSWD: ALL` किसी non-admin user को देना)।
        *   SUID/SGID binaries (एक executable file जिसकी owner user ID या group ID के साथ run होने की permission है, भले ही इसे किसी और user ने execute किया हो)।
        *   Kernel vulnerabilities, application exploits.
    *   **RHCSA/RHCE में, हमें unauthorized escalation से बचाव के तरीकों पर ध्यान देना है।**

**Security Best Practices:**

1.  **Least Privilege Principle:** Users को केवल वही privileges दें जिनकी उन्हें अपने काम के लिए ज़रूरत है, और ज़्यादा नहीं।
2.  **Use `sudo` over `su`:** जहाँ संभव हो `sudo` का उपयोग करें। यह बेहतर auditing और granular control प्रदान करता है। Root password को कम से कम लोगों के साथ share करें।
3.  **`visudo` का उपयोग करें:** हमेशा `visudo` का उपयोग करके `/etc/sudoers` file को edit करें।
4.  **Specific Commands Only:** `Cmnd_Alias` का उपयोग करके users को specific commands तक ही सीमित रखें, न कि `ALL` commands तक।
5.  **Audit Logs Check करें:** `/var/log/secure` (या syslog/journalctl) में `sudo` activity को monitor करें।
6.  **`NOPASSWD` से बचें:** यदि बहुत ज़रूरी न हो तो `NOPASSWD` option का उपयोग न करें, और यदि करें तो केवल बहुत specific और low-risk commands के लिए।
7.  **`sudoers` file Permissions:** `/etc/sudoers` और `/etc/sudoers.d/` directories की permissions 0440 (root:root) होनी चाहिए ताकि केवल root ही उन्हें पढ़ सके। `visudo` आमतौर पर इसे maintain करता है।

### 7. Practice Tasks (अभ्यास कार्य)

आइए इन concepts को practical करके देखें। अपनी Linux VM में (RHEL/CentOS/Fedora) इन steps को follow करें।

**Task 1: Setup - Users और Groups बनाना**
1.  **`traininguser` नामक एक नया user बनाएं:**
    ```bash
    sudo useradd traininguser
    sudo passwd traininguser
    ```
    (एक मज़बूत password set करें)
2.  **`sysadmins` नामक एक नया group बनाएं:**
    ```bash
    sudo groupadd sysadmins
    ```
3.  **`devops` नामक एक और group बनाएं:**
    ```bash
    sudo groupadd devops
    ```
4.  **`traininguser` को `sysadmins` group में जोड़ें:**
    ```bash
    sudo usermod -aG sysadmins traininguser
    ```
5.  **`traininguser` के लिए एक home directory बनाएं और permissions ठीक करें (यदि useradd ने automatic नहीं किया):**
    ```bash
    sudo mkdir -p /home/traininguser
    sudo chown -R traininguser:traininguser /home/traininguser
    sudo chmod 700 /home/traininguser
    ```

**Task 2: `su` Command का अभ्यास**
1.  **अपने current user से `traininguser` पर स्विच करें (login shell):**
    ```bash
    su - traininguser
    ```
    *   `traininguser` का password दर्ज करें।
    *   `whoami` और `pwd` चलाकर verify करें।
    *   `exit` करके वापस अपने original user पर आ जाएं।
2.  **अपने current user से `root` पर स्विच करें (login shell):**
    ```bash
    su -
    ```
    *   `root` का password दर्ज करें।
    *   `whoami` और `pwd` चलाकर verify करें।
    *   `exit` करके वापस अपने original user पर आ जाएं।

**Task 3: Basic `sudo` Access (Group-based)**
1.  **`traininguser` को `sudo` access दें `wheel` group के माध्यम से:**
    *   पहले verify करें कि `wheel` group को `sudoers` में access है (यह आमतौर पर डिफ़ॉल्ट होता है):
        ```bash
        sudo visudo
        ```
        `%wheel  ALL=(ALL)       ALL` line को देखें। यदि यह commented (`#`) है, तो इसे uncomment करें।
        (यदि आप `vi` editor से परिचित नहीं हैं, तो `i` दबाकर insert mode में जाएं, `#` हटाएं, `Esc` दबाकर command mode में जाएं, और `:wq` दबाकर save और exit करें।)
    *   **`traininguser` को `wheel` group में जोड़ें:**
        ```bash
        sudo usermod -aG wheel traininguser
        ```
    *   **Switch to `traininguser`:**
        ```bash
        su - traininguser
        ```
    *   **`traininguser` के रूप में एक `sudo` command चलाएं:**
        ```bash
        sudo dnf update
        ```
        *   आपसे `traininguser` का password मांगा जाएगा। Password दर्ज करने के बाद, `dnf update` command root privileges के साथ शुरू होना चाहिए।
        *   `sudo -l` चलाकर `traininguser` की `sudo` permissions list करें।
    *   `exit` करके वापस अपने original user पर आ जाएं।

**Task 4: Specific Command `sudo` Access (User-based)**
1.  **`traininguser` को केवल `dnf install` command के लिए `sudo` access दें, बिना `wheel` group के।**
    *   सबसे पहले, `traininguser` को `wheel` group से हटा दें ताकि केवल specific permission ही active रहे:
        ```bash
        sudo gpasswd -d traininguser wheel
        ```
    *   **`visudo` खोलें:**
        ```bash
        sudo visudo
        ```
    *   File के अंत में या `Cmnd_Alias` के नीचे यह line जोड़ें:
        ```
        traininguser ALL=(ALL) /usr/bin/dnf install
        ```
    *   Save और exit करें।
    *   **Switch to `traininguser`:**
        ```bash
        su - traininguser
        ```
    *   **`traininguser` के रूप में `dnf install` चलाएं:**
        ```bash
        sudo dnf install htop
        ```
        *   यह successfully काम करना चाहिए।
    *   **`traininguser` के रूप में एक unauthorized `sudo` command चलाएं:**
        ```bash
        sudo dnf remove htop
        # या
        sudo useradd testuser
        ```
        *   आपको "is not allowed to execute" जैसा एक error मिलना चाहिए।
    *   `sudo -l` चलाकर `traininguser` की `sudo` permissions list करें।
    *   `exit` करके वापस अपने original user पर आ जाएं।

**Task 5: `NOPASSWD` के साथ Specific Command Access**
1.  **`traininguser` को `/usr/sbin/service httpd status` command के लिए `NOPASSWD` access दें।**
    *   **`visudo` खोलें:**
        ```bash
        sudo visudo
        ```
    *   `traininguser` की पिछली entry को edit करें या एक नई line जोड़ें:
        ```
        traininguser ALL=(ALL) NOPASSWD: /usr/sbin/service httpd status
        ```
        (यदि `dnf install` वाली line भी रखनी है तो `traininguser ALL=(ALL) /usr/bin/dnf install, NOPASSWD: /usr/sbin/service httpd status` ऐसे comma-separated लिख सकते हैं)
    *   Save और exit करें।
    *   **Switch to `traininguser`:**
        ```bash
        su - traininguser
        ```
    *   **Command चलाएं:**
        ```bash
        sudo /usr/sbin/service httpd status
        ```
        *   आपसे password नहीं मांगा जाना चाहिए।
    *   `exit` करके वापस अपने original user पर आ जाएं।

**Task 6: `Cmnd_Alias` और Group-based `sudo` Access**
1.  **`devops` group के members को `PKGMGR` alias में define किए गए commands को चलाने की अनुमति दें।**
    *   **`visudo` खोलें:**
        ```bash
        sudo visudo
        ```
    *   `Cmnd_Alias` section में यह line जोड़ें (यदि पहले से नहीं है):
        ```
        Cmnd_Alias PKGMGR = /usr/bin/dnf, /usr/bin/yum
        ```
    *   फिर, `devops` group के लिए access grant करें:
        ```
        %devops ALL=(ALL) PKGMGR
        ```
    *   Save और exit करें।
    *   **`traininguser` को `devops` group में जोड़ें (और पिछली `sudoers` entry हटा दें या disable कर दें `#` से):**
        ```bash
        sudo usermod -aG devops traininguser
        sudo visudo  # Remove or comment out the 'traininguser ALL=(ALL)...' lines
        ```
    *   **Switch to `traininguser`:**
        ```bash
        su - traininguser
        ```
    *   **`dnf update` चलाएं:**
        ```bash
        sudo dnf update
        ```
        *   आपसे `traininguser` का password मांगा जाएगा और command run हो जाना चाहिए।
    *   **`sudo -l` चलाकर verify करें।**
    *   `exit` करके वापस अपने original user पर आ जाएं।

**Task 7: Cleaning Up**
1.  **`traininguser` को हटा दें:**
    ```bash
    sudo userdel -r traininguser
    ```
2.  **`sysadmins` और `devops` groups को हटा दें:**
    ```bash
    sudo groupdel sysadmins
    sudo groupdel devops
    ```
3.  **`visudo` खोलें और उन सभी lines को हटा दें जो आपने इस अभ्यास के लिए जोड़ी थीं। `/etc/sudoers` को उसकी original state में वापस ले आएं।**
    ```bash
    sudo visudo
    ```
    *   यह step बहुत ज़रूरी है ताकि आपके system पर कोई unwanted `sudo` configurations न रहें।

### 8. Troubleshooting Common Issues (सामान्य समस्याओं का निवारण)

*   **"User is not in the sudoers file. This incident will be reported."**
    *   मतलब user को `sudo` permissions नहीं दी गई हैं।
    *   **Solution:** `visudo` का उपयोग करके `/etc/sudoers` में user या उसके group के लिए एक entry जोड़ें।
*   **"Sorry, try again." (गलत password)**
    *   आप अपना खुद का password गलत दर्ज कर रहे हैं।
    *   **Solution:** सही password दर्ज करें। यदि आप अपना password भूल गए हैं, तो root के रूप में login करके `passwd your_username` से reset करें।
*   **Syntax errors in `/etc/sudoers` (system locked out)**
    *   यदि आपने `visudo` का उपयोग नहीं किया और `/etc/sudoers` को सीधे edit करके उसमें गलती कर दी, तो `sudo` काम करना बंद कर सकता है।
    *   **Solution:** Recovery mode में boot करें, या live CD/USB से boot करके `/etc/sudoers` file को ठीक करें। यही कारण है कि `visudo` इतना महत्वपूर्ण है!
*   **Permissions issues with `/etc/sudoers`:**
    *   `/etc/sudoers` की permissions 0440 (root:root) होनी चाहिए।
    *   `sudo chmod 0440 /etc/sudoers` और `sudo chown root:root /etc/sudoers` से ठीक करें। (लेकिन केवल तभी जब आप `sudo` के रूप में कुछ भी चला पा रहे हों, या recovery mode में।)

### 9. Summary and Conclusion (सारांश और निष्कर्ष)

इस lesson में, हमने `su` और `sudo` commands को detail में explore किया, जो Linux systems में privilege escalation के लिए essential हैं।

*   **`su`** आपको एक user से दूसरे user में (पासवर्ड जानने पर) स्विच करने की अनुमति देता है, `su -` के साथ एक clean login environment देता है।
*   **`sudo`** अधिक secure और flexible तरीका है specific commands को elevated privileges के साथ चलाने के लिए, बिना root password को reveal किए।
*   **`/etc/sudoers`** वह configuration file है जो `sudo` access को control करती है, और इसे **`visudo`** के माध्यम से edit करना अनिवार्य है।
*   हमने `sudoers` entries की structure, aliases, `NOPASSWD` option, और group-based permissions को समझा।
*   हमने legitimate privilege escalation के महत्व और security best practices पर भी चर्चा की।

RHCSA/RHCE exams और real-world scenarios दोनों के लिए इन concepts की मज़बूत समझ और practical experience बेहद ज़रूरी है।

**आगे क्या?**
*   `journalctl` का उपयोग करके `sudo` logs की जांच करना सीखें (`journalctl _COMM=sudo`)।
*   `sudoers` manual page (`man sudoers`) को पढ़ें ताकि आप और advanced options explore कर सकें।
*   अपनी VM में अलग-अलग `sudoers` configurations के साथ experiment करते रहें।

यह lesson आपकी RHCSA/RHCE journey का एक महत्वपूर्ण कदम है। Keep practicing!
शुभकामनाएं!
