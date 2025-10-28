Day 18: Cron & at job scheduling
---
Namaste, future Linux champions! ������ Main aapka instructor hoon aur aaj hum ek bahut hi important topic cover karenge jo aapke RHCSA aur RHCE exams ke liye fundamental hai: **Cron & At job scheduling**.

Yeh topic aapko sikhayega ki kaise aap apne Linux systems par repetitive (बार-बार होने वाले) aur one-time (एक बार होने वाले) tasks ko automate kar sakte hain. Chaliye shuru karte hain!

---

## ������ RHCSA + RHCE Training Lesson: Cron & At Job Scheduling

### 1. Concept Explanation (अवधारणा स्पष्टीकरण)

Imagine karo aap ek alarm clock set karte ho, jo har subah 6 baje bajta hai, ya phir aap kisi ko bolte ho ki "kal subah 10 baje mujhe yaad dilana yeh kaam karna hai". Linux mein bhi hum aise hi kaam schedule kar sakte hain. Yahi hai job scheduling!

**Kyun Zaroori Hai Yeh? (Why is it important?)**
*   **Automation:** Bahut saare repetitive tasks jaise backups lena, logs clean karna, system updates check karna, in sab ko manually karna boring aur time-consuming hai. Scheduling se yeh sab automatically ho jaate hain.
*   **Efficiency:** Aapka system bina manual intervention ke smoothly chalta rehta hai.
*   **Reliability:** Important tasks kabhi miss nahi hote.

Linux mein iske liye do main tools hain:
1.  **Cron:** For **recurring** (बार-बार होने वाले) tasks. Agar aapko koi script ya command daily, weekly, ya monthly chalani hai, toh cron use karte hain. Jaise har raat 2 baje backup lena.
2.  **At:** For **one-time** (एक बार होने वाले) tasks in the future. Agar aapko koi command ya script sirf ek baar future mein chalani hai, toh 'at' use karte hain. Jaise "abhi se 30 minute baad yeh script chalao".

**Cron detailed mein:**
Cron ek daemon service hai (`crond`) jo background mein chalti rehti hai aur scheduled jobs ko run karti hai. Har user apni khud ki cron jobs define kar sakta hai. System-wide cron jobs bhi hote hain jo root ya system services ke liye hote hain.

**At detailed mein:**
'at' bhi `atd` naam ke ek daemon service par depend karta hai. Yeh future mein ek specific time par jobs run karta hai aur job complete hone ke baad uski entry hata deta hai.

---

### 2. Key Linux Commands (मुख्य Linux कमांड्स)

#### A. Cron Related Commands:

1.  **`crontab -e`**
    *   **Explanation (व्याख्या):** Yeh command aapko current user ke liye crontab file (jahan aap apni cron jobs define karte ho) ko edit karne ke liye open karta hai. `e` for edit.
    *   **Hinglish:** "Isse aap apne account ki cron jobs ko likh ya modify kar sakte ho. Yeh ek text editor mein open hoga, jahan aap commands aur unka schedule specify karoge."

2.  **`crontab -l`**
    *   **Explanation (व्याख्या):** Yeh command current user ke liye schedule kiye gaye sabhi cron jobs ko list (display) karta hai. `l` for list.
    *   **Hinglish:** "Agar aapko dekhna hai ki aapne kaun-kaun se tasks schedule kiye hain, toh yeh command use karo. Yeh aapki crontab file ka content dikhayega."

3.  **`crontab -r`**
    *   **Explanation (व्याख्या):** Yeh command current user ke liye schedule kiye gaye sabhi cron jobs ko delete kar deta hai. `r` for remove.
    *   **Hinglish:** "Dhyaan se! Yeh command aapki saari cron entries ko permanently delete kar deta hai. Use with caution! Generally, `crontab -e` mein jaakar specific lines delete karna better hota hai."

4.  **`crontab -u  -e`** (Only for root/sudo user)
    *   **Explanation (व्याख्या):** Root user ya sudo privileges wale user is command ka use karke kisi aur user ki crontab file ko edit kar sakte hain.
    *   **Hinglish:** "Agar aap root ho, toh aap kisi aur user ki cron jobs ko bhi manage kar sakte ho. `sudo crontab -u ankit -e` ankit user ki cron edit karega."

5.  **`/etc/crontab`**
    *   **Explanation (व्याख्या):** Yeh system-wide crontab file hai. Ismein environment variables aur system-level cron jobs define hote hain. Is file mein `user` field bhi specify karna padta hai ki kaun sa user job run karega.
    *   **Hinglish:** "Yeh poore system ke liye common cron settings aur jobs ki file hai. RHCE mein iska importance bahut zyada hai. Isme aapko har entry ke liye user specify karna padta hai."

6.  **`/etc/cron.d/`**
    *   **Explanation (व्याख्या):** Yeh directory system-wide cron jobs ke liye hoti hai. Aap apni custom system-level scripts ko schedule karne ke liye yahan files bana sakte hain.
    *   **Hinglish:** "Agar aap koi service ya application install karte ho jo apni khud ki cron jobs schedule karna chahti hai, toh woh yahan apni files banati hai. Har file ek separate crontab jaisi hoti hai, jismein user bhi specify hota hai."

7.  **`/etc/cron.hourly/`, `/etc/cron.daily/`, `/etc/cron.weekly/`, `/etc/cron.monthly/`**
    *   **Explanation (व्याख्या):** Yeh directories pre-defined scheduling intervals ke liye hain. In directories mein rakhi gayi scripts automatically hourly, daily, weekly, ya monthly run hoti hain (usually `run-parts` command dwara `/etc/crontab` se call hoti hain).
    *   **Hinglish:** "Agar aapko koi script har ghante, roz, har hafte ya har mahine chalani hai, toh simply use appropriate directory mein daal do. Make sure script executable (`chmod +x`) ho."

#### B. At Related Commands:

1.  **`at `**
    *   **Explanation (व्याख्या):** Yeh command future mein ek specific time par ek job ko schedule karta hai. `at` command run karne ke baad, aapko ek interactive prompt milega jahan aap apni commands type karoge. `Ctrl+D` dabane se job schedule ho jayegi.
    *   **Hinglish:** "Yeh single-use jobs ke liye hai. Aap time specify karoge, phir apni commands type karoge aur `Ctrl+D` press karoge. Yeh command ko specified time par run kar dega."
    *   **Time formats:** `now + 5 minutes`, `10:30 PM`, `tomorrow`, `noon`, `midnight`, `HH:MM DD.MM.YY`.

2.  **`atq`**
    *   **Explanation (व्याख्या):** Yeh command pending (ab tak run nahi hui) 'at' jobs ko list karta hai.
    *   **Hinglish:** "Agar aapko dekhna hai ki aapne kaun-kaun si 'at' jobs schedule ki hain aur abhi tak run nahi hui hain, toh `atq` use karo. Yeh job ID bhi dikhayega."

3.  **`atrm `**
    *   **Explanation (व्याख्या):** Yeh command ek specific 'at' job ko remove karta hai. Job ID `atq` command se milta hai.
    *   **Hinglish:** "Agar aapko koi 'at' job cancel karni hai toh `atrm` use karo aur job ID provide karo jo `atq` se mila tha."

---

### 3. Step-by-Step Examples (कदम-दर-कदम उदाहरण)

Chaliye kuch practical examples dekhte hain.

#### Example 1: User Cron Job - Daily Backup Notification

Aap chahte ho ki har subah 8 baje aapko ek message mile ki "Backup check done".

1.  **Open your crontab for editing:**
    ```bash
    crontab -e
    ```
    *   Agar pehli baar open kar rahe ho, toh editor choose karne ka option aa sakta hai (vim, nano, etc.). `nano` is user-friendly.

2.  **Add the cron entry:**
    *   Crontab entry ka format hai: `minute hour day_of_month month day_of_week command`
    *   `* * * * *` yeh paanch asterisks hain.
        *   1st `*`: Minute (0-59)
        *   2nd `*`: Hour (0-23)
        *   3rd `*`: Day of month (1-31)
        *   4th `*`: Month (1-12)
        *   5th `*`: Day of week (0-7, where 0 and 7 are Sunday)

    Subah 8 baje ke liye, hum use karenge `0 8 * * *`.
    ```cron
    # Yeh ek comment hai, hash (#) se start hota hai
    0 8 * * * echo "Daily backup check complete for user $(whoami) on $(hostname) at $(date)" >> /home/user/backup_status.log 2>&1
    ```
    *   **Explanation:**
        *   `0 8 * * *`: Har din subah 8 baje (0th minute of 8th hour).
        *   `echo ...`: Yeh command ek message print karegi.
        *   `>> /home/user/backup_status.log`: Is message ko `/home/user/backup_status.log` file mein append karega.
        *   `2>&1`: Standard error (stderr) ko standard output (stdout) ke saath redirect karega. Isse agar koi error bhi aati hai toh woh bhi log file mein store ho jayegi.

3.  **Save and Exit:**
    *   If using `nano`: `Ctrl+O` (write out), then `Enter`, then `Ctrl+X` (exit).
    *   If using `vim`: Press `Esc`, then type `:wq` and `Enter`.

4.  **Verify the job:**
    ```bash
    crontab -l
    ```
    Aapko apni entry dikhegi.

#### Example 2: RHCE Level - System-Wide Cron Job for Disk Usage Report

Aap chahte ho ki har Sunday, raat 1 baje, ek disk usage report generate ho aur `/var/log/disk_usage_report.log` mein save ho. Yeh system-wide task hai, toh hum `/etc/cron.d/` use karenge.

1.  **Create a new file in `/etc/cron.d/`:**
    *   Aapko root privileges chahiye.
    ```bash
    sudo nano /etc/cron.d/disk_report
    ```

2.  **Add the cron entry to the file:**
    *   `0 1 * * 0`: Har Sunday (0 ya 7 Sunday hota hai), raat 1 baje.
    *   System-wide cron files mein, aapko user bhi specify karna padta hai jo command run karega. Yahan hum `root` user use karenge.
    ```cron
    # /etc/cron.d/disk_report
    # Generate disk usage report every Sunday at 1 AM
    0 1 * * 0 root /usr/bin/df -h > /var/log/disk_usage_report.log 2>&1
    ```
    *   **Explanation:**
        *   `0 1 * * 0`: Har Sunday (0) raat 1 baje (1st hour, 0th minute).
        *   `root`: Yeh job root user ke roop mein chalegi.
        *   `/usr/bin/df -h`: `df -h` command chalegi (disk free in human-readable format).
        *   `> /var/log/disk_usage_report.log 2>&1`: Output ko is file mein save karegi.

3.  **Save and Exit:**
    *   `Ctrl+O`, `Enter`, `Ctrl+X` for nano.

4.  **Ensure correct permissions (Optional but good practice):**
    ```bash
    sudo chmod 644 /etc/cron.d/disk_report
    ```
    Crond daemon automatically is new file ko pick up kar lega.

#### Example 3: At Job - Shutdown PC in 15 Minutes

Aap chahte ho ki aapka system abhi se 15 minute baad shutdown ho jaye.

1.  **Schedule the 'at' job:**
    ```bash
    at now + 15 minutes
    ```
    *   Prompt (`at>`) open hoga.

2.  **Type the command and exit:**
    ```
    at> sudo shutdown -h now
    at>   # Press Ctrl+D here
    ```
    *   Output will be something like: `job 3 at 2023-10-27 10:45` (job ID aur time).

3.  **Check pending 'at' jobs:**
    ```bash
    atq
    ```
    Aapko apni scheduled job (job ID 3) dikhegi.

4.  **Cancel the 'at' job (if needed):**
    *   Agar aap apna mind change karte ho aur shutdown nahi karna chahte.
    ```bash
    atrm 3
    ```
    *   Ab `atq` chalao, job list mein nahi hogi.

---

### 4. Practice Tasks (अभ्यास कार्य)

Chalo, ab kuch haath gande karte hain! Yeh tasks aapko apne Linux VM par try karne hain.

**Task 1: Log Cleanup (Cron)**
*   **Goal:** Ek cron job banao jo `/tmp` directory mein 7 din se purani files ko har raat 3 baje delete kare.
*   **Steps:**
    1.  Ek shell script banao `cleanup.sh` jismein `find /tmp -type f -mtime +7 -delete` command ho.
    2.  Script ko executable (`chmod +x`) banao.
    3.  `crontab -e` se apni crontab file open karo.
    4.  Entry add karo jo is script ko daily subah 3:00 AM par run kare.
    5.  Confirm karo ki job `crontab -l` se dikh rahi hai.

**Task 2: System Health Check (RHCE - System Cron)**
*   **Goal:** Ek system-wide cron job banao jo har 10 minute mein `/proc/loadavg` ka content read kare aur use `/var/log/system_load.log` mein append kare.
*   **Steps:**
    1.  `sudo` use karke `/etc/cron.d/` directory mein `load_monitor` naam ki ek nayi file banao.
    2.  File mein cron entry likho jo har 10 minute mein `cat /proc/loadavg >> /var/log/system_load.log` command ko `root` user se execute kare.
    3.  File permissions `644` set karo.
    4.  Thodi der wait karo (10-20 mins) aur phir `tail /var/log/system_load.log` se check karo ki entries add ho rahi hain ya nahi.

**Task 3: Future Reminder (At)**
*   **Goal:** Ek reminder set karo ki "It's time for lunch!" jo abhi se 45 minute baad aapke terminal par display ho.
*   **Steps:**
    1.  `at` command use karke `now + 45 minutes` par ek job schedule karo.
    2.  `echo "It's time for lunch!"` command type karo.
    3.  `Ctrl+D` se job schedule karo.
    4.  `atq` se confirm karo ki job scheduled hai.
    5.  Wait karo aur dekho terminal par message aata hai ya nahi. (Agar terminal session close ho jata hai, toh message `/var/spool/mail/` mein bhi ja sakta hai.)

**Task 4: Cancel At Job**
*   **Goal:** Task 3 mein banayi gayi job ko schedule hone se pehle cancel karo.
*   **Steps:**
    1.  `atq` se job ID find karo.
    2.  `atrm ` command se use cancel karo.
    3.  `atq` se confirm karo ki job cancel ho gayi hai.

---

### 5. Pro Tips / Common Mistakes (विशेष सुझाव / आम गलतियाँ)

**Pro Tips (विशेष सुझाव):**

1.  **Use Full Paths (पूरे पाथ का उपयोग करें):** Cron jobs ka environment limited hota hai. Always use full paths for commands and scripts.
    *   **Galat:** `myscript.sh`
    *   **Sahi:** `/home/user/scripts/myscript.sh`
    *   **Galat:** `df -h`
    *   **Sahi:** `/usr/bin/df -h`
    *   Aap `which ` use karke command ka full path pata kar sakte ho.

2.  **Redirect Output (आउटपुट को रीडायरेक्ट करें):** Cron jobs ka output by default user ko email kiya jaata hai. Agar aapko emails nahi chahiye, toh output ko `/dev/null` mein redirect karo.
    ```cron
    * * * * * /path/to/command > /dev/null 2>&1
    ```
    *   `> /dev/null`: Standard output ko discard karta hai.
    *   `2>&1`: Standard error ko bhi standard output jahan ja raha hai, wahan bhej deta hai (yani `/dev/null` mein).

3.  **Use `MAILTO` Variable:** Agar aapko cron job ke output ko kisi specific email ID par send karna hai, toh crontab file ke top par `MAILTO` variable set karo.
    ```cron
    MAILTO="your_email@example.com"
    * * * * * /path/to/command
    ```

4.  **Test Scripts Separately:** Cron mein daalne se pehle, apni script ya command ko manually run karke test karo ki woh sahi se chal rahi hai.

5.  **`atd` and `crond` Services:** Ensure `atd` (for `at` jobs) and `crond` (for `cron` jobs) services are running and enabled.
    ```bash
    systemctl status atd
    systemctl status crond
    systemctl enable atd
    systemctl enable crond
    ```

6.  **Cron Security (`cron.allow`, `cron.deny`):**
    *   `/etc/cron.allow`: Agar yeh file exist karti hai, toh sirf ismein listed users hi `crontab` command use kar sakte hain.
    *   `/etc/cron.deny`: Agar `cron.allow` exist nahi karti, toh `cron.deny` mein listed users `crontab` use nahi kar sakte.
    *   Same logic `at.allow` aur `at.deny` ke liye bhi apply hota hai.
    *   **RHCE Hint:** In files ko manage karna important hai security ke liye.

**Common Mistakes (आम गलतियाँ):**

1.  **Incorrect Cron Syntax:** Most common mistake! Ek bhi asterisk ya number galat hua toh job run nahi hogi.
    *   Use online cron expression generators to verify (e.g., crontab.guru).

2.  **Relative Paths:** Cron ka working directory predict karna mushkil hota hai. Always use absolute paths.
    *   **Wrong:** `script.sh`
    *   **Right:** `/home/user/myscripts/script.sh`

3.  **Environment Variables:** Cron jobs ka environment limited hota hai. `PATH` jaisi variables us tarah set nahi hote jaise interactive shell mein hote hain. Agar aapki script ko specific environment variables ki zaroorat hai, toh unhe script ke andar define karo ya crontab file ke top par set karo.
    ```cron
    PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
    0 8 * * * myscript.sh # ab yeh PATH use karega
    ```

4.  **Permissions:** Make sure scripts scheduled by cron ya at executable hain (`chmod +x `).
    *   Also, ensure the user running the cron/at job has permissions to read/write/execute the files/directories involved.

5.  **Not Saving Crontab:** `crontab -e` mein changes karne ke baad, save karna mat bhoolna! (`:wq` for Vim, `Ctrl+O, Enter, Ctrl+X` for Nano).

6.  **`at` job running as root:** Agar `at` job mein `sudo` command use kar rahe ho, toh `at` job ko `root` user ke through schedule karna best practice hai, ya phir user ko `sudoers` file mein `NOPASSWD` entry deni padegi us particular command ke liye.

---

Umeed hai yeh comprehensive lesson aapko Cron aur At job scheduling ko samajhne mein aur use karne mein madad karega. Yeh skills aapko ek efficient aur effective Linux administrator banayengi! Practice karte rahiye, aur koi doubt ho toh puchne mein jhijhakiyega mat. All the best! ������
