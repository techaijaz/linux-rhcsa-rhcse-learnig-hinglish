Day 27: Logs & troubleshooting (journalctl, dmesg)
---
Namaste students, swagat hai aap sabhi ka is naye aur bahut hi important Linux training session mein! Aaj hum ek aise topic par baat karenge jo har Linux Administrator ya Engineer ke liye **backbone** hai – **Logs & Troubleshooting**! Jab bhi system mein koi problem aati hai, ya aap kuch analyze karna chahte hain, toh logs hi aapke **best friend** hote hain.

Hum focus karenge `journalctl` aur `dmesg` par, jo modern Linux systems (especially Red Hat-based systems like RHEL, CentOS, Fedora) mein logging aur kernel message management ke liye **fundamental** tools hain.

Chaliye, shuru karte hain!

---

## RHCSA + RHCE Training: Logs & Troubleshooting (journalctl, dmesg)

### 1. Concept Explanation (अवधारणा स्पष्टीकरण)

Linux systems mein logs **dil ki dhadkan** ki tarah hote hain. Har chhota-bada event, har action, har error – sab kuch ek record ban jaata hai. Yeh ek **detailed diary** hai jo aapko batati hai ki system mein kab, kya aur kaise hua.

Troubleshooting ke liye logs **gold mine** hain. Maano aapka web server down ho gaya, ya koi application crash ho rahi hai – logs aapko **clues** denge ki problem kahan hai. Security analysis, performance monitoring aur auditing ke liye bhi logs bahut zaroori hain.

Pehle ke Linux systems mein `syslog` based logs (`/var/log/messages`, `/var/log/secure` etc.) plain text files mein store hote the. Lekin modern Systemd-based systems mein, **journald** ek centralized binary logging system introduce kiya gaya hai.

**a. `journalctl` (Systemd Journal)**

*   **Kya Hai?** `journalctl` command Systemd ke journaling daemon `systemd-journald` dwara collect kiye gaye logs ko view karne ka tool hai. Yeh poore system ke logs ko manage karta hai – kernel se lekar applications tak, sab kuch.
*   **Kaisa Data?** Yeh logs binary format mein hote hain, text files ki tarah nahi. Iska fayda yeh hai ki yeh structured hote hain, unhe query karna aur filter karna bahut aasan ho jaata hai.
*   **Kahan Store Hote Hain?** Default roop se, `journalctl` ke logs non-persistent hote hain aur `/run/log/journal/` mein store hote hain. Jaise hi system reboot hota hai, yeh logs **gayab** ho jaate hain. Lekin, hum ise persistent bhi bana sakte hain (`/var/log/journal/` mein store hone ke liye), jo RHCE level par bahut important hai.
*   **Fayde:**
    *   **Centralized:** Ek hi jagah sab logs.
    *   **Structured:** Filtering aur searching bahut powerful.
    *   **Indexing:** Tez searching.
    *   **Boot-specific:** Har boot ke logs alag se dekhe ja sakte hain.
    *   **Metadata:** Har log entry ke saath bahut sari metadata (process ID, user ID, unit name, etc.) hoti hai.

**b. `dmesg` (Display Message Buffer)**

*   **Kya Hai?** `dmesg` command sirf Linux kernel ki taraf se aane wale messages ko display karta hai. Jab system boot hota hai, toh kernel saare detected hardware devices, driver initializations aur anya low-level boot information ko ek special **kernel ring buffer** mein store karta hai. `dmesg` isi buffer se messages retrieve karta hai.
*   **Kaisa Data?** Yeh bhi text-based messages hote hain, lekin yeh sirf kernel ke domain mein rehte hain.
*   **Kab Istemaal Karein?** `dmesg` tab useful hota hai jab aapko hardware errors, driver issues, ya boot process ke **initial stages** mein koi problem identify karni ho, jahan par `journald` shayad poori tarah se initialized na hua ho.
*   **Limitations:** Iska buffer size limited hota hai. Purane messages overwrite ho sakte hain agar naye messages bahut jyada hon.

**Key Difference (मुख्य अंतर):**
*   `dmesg` **sirf kernel** ke messages dikhata hai.
*   `journalctl` **poore system** (kernel + services + applications) ke messages dikhata hai.
*   `dmesg` ka data boot ke baad bhi available rehta hai jab tak buffer overwrite na ho. `journalctl` ka data default mein boot ke baad clear ho jaata hai (non-persistent).

---

### 2. Key Linux Commands (महत्वपूर्ण Linux कमांड्स)

Chaliye, ab dekhte hain woh commands jo aapko in logs ke saath kaam karne mein madad karenge:

#### `journalctl` Commands:

1.  **`journalctl`**
    *   **Explanation:** Saare collected logs display karta hai, sabse purane se sabse naye tak. Output paginated hota hai (`less` ki tarah).
    *   **Hinglish:** Poore system ke saare logs dikhayega, jo bhi journald ne collect kiye hain. Oldest se newest tak. Output ek-ek page mein dikhega.

2.  **`journalctl -b`**
    *   **Explanation:** Current boot ke saare logs display karta hai.
    *   **Hinglish:** Isse aapko **current boot session** ke saare logs milenge. Pichle boots ke logs nahi dikhenge (unless persistent logging enabled hai).

3.  **`journalctl -b -1`** (ya `-b -2`, etc.)
    *   **Explanation:** Pichle boot session ke logs display karta hai. `-1` last boot, `-2` second last boot, and so on.
    *   **Hinglish:** Agar system crash hua tha aur aap pichle boot ke logs dekhna chahte hain, toh yeh command bahut kaam aayegi. `-1` ka matlab hai **last boot**.

4.  **`journalctl -u `**
    *   **Explanation:** Specific systemd unit (service, socket, etc.) ke logs display karta hai. Jaise `journalctl -u httpd.service` ya `journalctl -u sshd`.
    *   **Hinglish:** Agar aapko sirf ek particular service (jaise Apache web server ya SSH daemon) ke logs dekhne hain, toh yeh command use karo. `UNIT_NAME` mein service ka naam aayega.

5.  **`journalctl --since "YYYY-MM-DD HH:MM:SS" --until "YYYY-MM-DD HH:MM:SS"`**
    *   **Explanation:** Logs ko specific time range ke beech filter karta hai. `now`, `today`, `yesterday` jaise relative terms bhi use kar sakte hain.
    *   **Hinglish:** Agar aapko ek specific time period ke logs dekhne hain, toh yeh command use karo. `since` kab se aur `until` kab tak ke logs. Bohot flexible hai time specify karne mein.

6.  **`journalctl -f`**
    *   **Explanation:** Real-time mein naye logs display karta hai, `tail -f` ki tarah.
    *   **Hinglish:** Agar aapko koi service start karke ya koi action perform karke live logs monitor karne hain, toh yeh command chalakar terminal ko open rakho. Naye logs apne aap update hote rahenge.

7.  **`journalctl -p `**
    *   **Explanation:** Logs ko unki priority (urgency level) ke hisaab se filter karta hai. Priorities hain:
        *   `0`: emerg (system unusable)
        *   `1`: alert (action must be taken immediately)
        *   `2`: crit (critical conditions)
        *   `3`: err (error conditions)
        *   `4`: warning (warning conditions)
        *   `5`: notice (normal but significant condition)
        *   `6`: info (informational messages)
        *   `7`: debug (debug-level messages)
    *   **Hinglish:** Agar aap sirf errors ya warnings dekhna chahte hain, toh priority filter ka use karo. Jaise `journalctl -p err` sirf error messages dikhayega.

8.  **`journalctl --disk-usage`**
    *   **Explanation:** Journal files kitni disk space le rahe hain, yeh batata hai.
    *   **Hinglish:** Jab aap persistent logging enable karte ho, toh logs disk space consume karte hain. Yeh command aapko batayegi ki kitni space use ho rahi hai.

9.  **`journalctl --vacuum-size=`** (RHCE level)
    *   **Explanation:** Oldest journal files delete karta hai jab tak total size specified `` se kam na ho jaye. Example: `journalctl --vacuum-size=1G`.
    *   **Hinglish:** Agar logs bahut zyada disk space consume kar rahe hain, toh unhe cleanup karne ke liye yeh command use karo. `1G` ka matlab 1 Gigabyte.

10. **`journalctl --vacuum-time=`** (RHCE level)
    *   **Explanation:** Specified `` se purane saare journal files delete karta hai. Example: `journalctl --vacuum-time=2weeks`.
    *   **Hinglish:** Agar aapko sirf latest logs chahiye aur purane logs ki zaroorat nahi hai, toh time-based cleanup kar sakte ho. `2weeks` ka matlab 2 hafte se purane logs delete kar do.

11. **`journalctl --no-pager`**
    *   **Explanation:** Output ko `less` (pager) mein show karne ki bajaye seedha terminal par print karta hai. Useful jab aap output ko `grep` ya kisi aur command mein pipe kar rahe ho.
    *   **Hinglish:** Agar aapko logs ko directly `grep` karna hai ya kisi script mein use karna hai, toh pager ko disable karna behtar hai.

#### `dmesg` Commands:

1.  **`dmesg`**
    *   **Explanation:** Kernel ring buffer ke saare messages display karta hai.
    *   **Hinglish:** Sabse basic command, saare kernel messages dikhayegi.

2.  **`dmesg -H`**
    *   **Explanation:** Human-readable output deta hai, timestamps ke saath.
    *   **Hinglish:** Output thoda clean aur samajhne mein aasaan ho jaata hai.

3.  **`dmesg -T`**
    *   **Explanation:** Timestamps ko actual human-readable date aur time mein convert karta hai.
    *   **Hinglish:** Agar aapko exact date aur time ke saath messages dekhne hain, toh `-T` use karo.

4.  **`dmesg -w`**
    *   **Explanation:** New messages ka wait karta hai aur unhe real-time mein display karta hai, `journalctl -f` ki tarah, lekin sirf kernel ke liye.
    *   **Hinglish:** Agar aapko hardware event ya driver activity ko live monitor karna hai, toh yeh command kaam aayegi.

5.  **`dmesg -c`**
    *   **Explanation:** Buffer ko clear karta hai aur phir naye messages display karta hai. **Caution:** Purane messages erase ho jayenge.
    *   **Hinglish:** Yeh command **carefully use karna** chahiye. Agar aapko fresh buffer se start karna hai, tabhi use karein.

6.  **`dmesg | grep -i "error"`**
    *   **Explanation:** Kernel messages mein "error" keyword search karta hai (case-insensitive).
    *   **Hinglish:** Kernel level par koi error dhoondhne ka sabse quick तरीका.

#### Other Useful Commands:

*   **`systemctl status `**:
    *   **Explanation:** Service ka current status, saath mein uske recent logs bhi dikhata hai. Often yeh troubleshooting ka **first step** hota hai.
    *   **Hinglish:** Jab bhi koi service problem de, sabse pehle uski `status` check karo. Yeh command aapko immediate context degi.

---

### 3. Step-by-Step Examples (कदम-दर-कदम उदाहरण)

Chaliye, kuch real-world scenarios mein in commands ka use dekhte hain.

**Scenario 1: System Boot Issues Diagnose Karna**

Maano, aapka system boot hone mein problem kar raha hai, ya koi service boot ke waqt start nahi ho rahi.

1.  **Check current boot logs:**
    ```bash
    journalctl -b
    ```
    *   **Explanation:** Isse aapko current boot process ke saare events milenge. Agar koi service fail hui hai, toh uski entries `red` color mein highlight ho sakti hain. Output ko scroll karke (`Page Up`/`Page Down` ya arrow keys) dekho.

2.  **Check previous boot logs (if system crashed/rebooted):**
    ```bash
    journalctl -b -1
    ```
    *   **Explanation:** Agar system boot loop mein tha ya restart ho gaya, toh pichle boot ke logs aapko problem ki **root cause** bata sakte hain.

3.  **Filter kernel messages during boot:**
    ```bash
    dmesg -T | less
    ```
    *   **Explanation:** Kernel messages mein hardware detection, driver loading errors ya anya low-level boot problems dhoondhne ke liye yeh best hai. `less` se aap output ko browse kar sakte hain.

**Scenario 2: Specific Service Troubleshooting**

Aapka Apache web server (`httpd`) kaam nahi kar raha hai.

1.  **Check service status and recent logs:**
    ```bash
    systemctl status httpd
    ```
    *   **Explanation:** Yeh command aapko `httpd` service ka current state (`active`, `inactive`, `failed`) aur uske last few log entries dikhayegi. Aksar yahin par problem ka clue mil jaata hai.

2.  **View all logs for Apache service:**
    ```bash
    journalctl -u httpd.service
    ```
    *   **Explanation:** Agar `systemctl status` se poora context nahi mila, toh `journalctl -u httpd.service` se aapko Apache ke saare logs milenge, purane bhi.

3.  **Monitor Apache logs in real-time:**
    ```bash
    journalctl -u httpd.service -f
    ```
    *   **Explanation:** Ab aap try karo Apache ko start karne ki (`sudo systemctl start httpd`) ya us par koi request bhejo. `journalctl -f` window mein aapko live messages dikhenge, jo debugging ke liye bahut useful hain.

**Scenario 3: Time-Based Analysis**

Aapko pata chala ki server mein problem "aaj subah 10 baje" se start hui thi.

1.  **Check logs from a specific time:**
    ```bash
    journalctl --since "10:00:00"
    # Or for a specific date and time:
    journalctl --since "2023-10-27 10:00:00"
    ```
    *   **Explanation:** Isse aapko us time period ke saare logs milenge, jahan aapko problem ka source mil sakta hai.

2.  **Check logs between a time range:**
    ```bash
    journalctl --since "2023-10-27 10:00:00" --until "2023-10-27 10:30:00"
    ```
    *   **Explanation:** Agar problem ka exact time frame pata hai, toh isse aap unnecessary data ko filter out kar sakte hain.

**Scenario 4: Making `journalctl` Logs Persistent (RHCE Level)**

Default mein, `journalctl` ke logs reboot ke baad clear ho jaate hain. Production environments mein yeh acceptable nahi hai.

1.  **Create the persistent directory:**
    ```bash
    sudo mkdir -p /var/log/journal
    ```
    *   **Explanation:** `systemd-journald` daemon automatically is directory ko detect karega aur logs ko `/run/log/journal` ki jagah `/var/log/journal` mein store karna shuru kar dega. Is directory ke permissions `root:systemd-journal` hone chahiye aur sticky bit set hona chahiye. Usually `systemd-journald` daemon yeh automatically handle karta hai.

2.  **Verify persistence (optional, after reboot):**
    ```bash
    journalctl -b -1
    ```
    *   **Explanation:** System reboot karne ke baad, agar aapko pichle boot ke logs dikhte hain, toh persistence successfully enable ho gaya hai.

**Scenario 5: Managing Journal Disk Usage (RHCE Level)**

Persistent logs disk space consume karte hain. Unhe manage karna important hai.

1.  **Check current disk usage:**
    ```bash
    journalctl --disk-usage
    ```
    *   **Explanation:** Yeh aapko batayega ki journal files kitni space le rahe hain.

2.  **Limit log size to 1GB:**
    ```bash
    sudo journalctl --vacuum-size=1G
    ```
    *   **Explanation:** Yeh command purane logs ko delete karegi jab tak total size 1GB tak na aa jaye.

3.  **Delete logs older than 2 weeks:**
    ```bash
    sudo journalctl --vacuum-time=2weeks
    ```
    *   **Explanation:** Isse 2 hafte se purane saare logs delete ho jayenge.

---

### 4. Practice Tasks (अभ्यास कार्य)

Ab time hai aapke haath gande karne ka! Apne Linux environment mein in tasks ko try karein.

1.  **Task 1: Basic Log Exploration**
    *   Apne system ke saare logs dekhein.
    *   Current boot ke logs dekhein.
    *   Pichle boot ke logs dekhein (agar aapne system reboot kiya hai).
    *   **Command Tip:** `journalctl` aur `journalctl -b`.

2.  **Task 2: Service Specific Logs**
    *   `sshd.service` (SSH daemon) ke logs dekhein.
    *   `firewalld.service` ke logs dekhein.
    *   Kisi bhi ek service ke logs ko real-time mein monitor karein (jaise `sshd`). Phir ek naya terminal open karke apne hi system par SSH login attempt karein aur changes dekhein.
    *   **Command Tip:** `journalctl -u ` aur `journalctl -u  -f`.

3.  **Task 3: Time-Based Filtering**
    *   Aaj ke din subah 9 baje se 10 baje ke beech ke saare logs dekhein.
    *   Pichle 30 minutes ke saare logs dekhein.
    *   **Command Tip:** `--since`, `--until`, `now`, `today`, `yesterday`, relative terms.

4.  **Task 4: Priority-Based Filtering**
    *   System mein saare `error` messages dekhein (`journalctl -p err`).
    *   Saare `warning` aur `error` messages dekhein (`journalctl -p warning..err`).
    *   **Command Tip:** `-p `.

5.  **Task 5: Kernel Logs & Troubleshooting**
    *   `dmesg` output ko human-readable timestamps ke saath dekhein.
    *   `dmesg` output mein "error" ya "fail" keywords search karein. Kya koi hardware ya driver issue dikhta hai?
    *   **Command Tip:** `dmesg -T`, `dmesg | grep -i "error"`.

6.  **Task 6: Simulate & Troubleshoot (RHCSA Focus)**
    *   Ek non-existent service ko start karne ki koshish karein: `sudo systemctl start non_existent_service`.
    *   Ab `journalctl` ka use karke is command ki wajah se aaye error message ko dhoondhein. Kis service ke logs mein milega? (`systemd` unit ke logs mein).
    *   **Command Tip:** `journalctl -u systemd.service` ya `journalctl -xe`.

7.  **Task 7: Persistent Logging Setup (RHCE Focus)**
    *   `journalctl` logs ko persistent banane ke liye directory create karein (`/var/log/journal`).
    *   System ko reboot karein.
    *   Reboot ke baad, verify karein ki aap pichle boot ke logs dekh pa rahe hain (`journalctl -b -1`).
    *   **Command Tip:** `sudo mkdir -p /var/log/journal`.

8.  **Task 8: Log Size Management (RHCE Focus)**
    *   Apne journal logs ki current disk usage check karein.
    *   Agar aapke system mein bahut purane logs hain, toh unhe 1GB tak limit karein ya 1 week se purane logs ko delete karein.
    *   **Command Tip:** `--disk-usage`, `--vacuum-size`, `--vacuum-time`.

---

### 5. Pro Tips / Common Mistakes

*   **Don't just `cat /var/log/messages` anymore:** Purane system par `cat /var/log/messages` ek common practice thi. Modern Systemd systems mein, `journalctl` hi **go-to tool** hai. Direct `/var/log` files ko browse karna miss-information de sakta hai ya incomplete ho sakta hai.
*   **Use `journalctl`'s Native Filters:** `journalctl` ke apne powerful filters (`-u`, `-p`, `--since`, `--until`) ka poora fayda uthao. Sirf `journalctl | grep "something"` karne se pehle socho, kya `journalctl` ka apna filter use kar sakta hun? Yeh zyada efficient aur accurate hota hai.
*   **Understand Persistence:** Hamesha yaad rakho ki default mein `journalctl` logs persistent nahi hote hain. Production servers par, **hamesha** `/var/log/journal` directory bana kar persistence enable karo. Varna, system reboot ke baad aapke troubleshooting clues gayab ho jayenge.
*   **`dmesg` for Hardware/Boot Issues:** Jab bhi hardware-related problem (NIC not working, disk error, USB device issue) ya system boot hone mein stuck ho, toh `dmesg` aapka pehla dost hona chahiye.
*   **Timezones Matter:** Logs mein timestamps server ke timezone ke hisaab se hote hain. Agar aap alag timezone se access kar rahe ho, toh time differences ka dhyan rakho. `timedatectl` command se server ka timezone check kar sakte ho.
*   **Piping to `less` or `more`:** `journalctl` output default mein paginated hota hai. Lekin agar aap `dmesg` ya `journalctl --no-pager` use kar rahe ho aur output bahut bada hai, toh use `less` mein pipe karna mat bhoolo (`dmesg -T | less`) taaki aap use scroll kar sako.
*   **Error vs. Warning vs. Info:** Logs mein alag-alag priority levels hote hain. Sabhi errors ko ignore mat karo, aur har warning ko ek emergency mat samjho. Context samajhna zaroori hai.
*   **`journalctl -xe` is your friend:** Agar koi service start nahi ho rahi ya fail ho gayi hai, toh `journalctl -xe` command bahut useful hai. Yeh recent errors ko detail mein dikhata hai aur aksar suggestions bhi deta hai.

---

Umeed hai ki yeh comprehensive lesson aapko `journalctl` aur `dmesg` ko samajhne aur unka effective tarike se use karne mein madad karega. Logs padhna aur unhe interpret karna ek **skill** hai jo practice se hi behtar hoti hai. Keep practicing, yeh skills aapko ek **solid Linux administrator** banayenge!

All the best! ������
