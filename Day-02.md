Day 2: File operations (ls, cp, mv, rm, cat, less, head, tail)
Namaste students! Main aapka Linux instructor hoon, aur aaj hum ek bahut hi important topic par baat karenge jo RHCSA aur RHCE dono certifications ke liye fundamental hai: **"File Operations"**.

Linux mein files aur directories ke saath kaam karna daily routine ka hissa hai. Agar aapko system ko efficiently manage karna hai, toh in basic file operations commands par grip hona bahut zaroori hai. Chaliye shuru karte hain!

---

## RHCSA + RHCE Training Lesson: File Operations (ls, cp, mv, rm, cat, less, head, tail)

### 1. Concept Explanation:

Linux mein sab kuch 'file' hota hai – chahe woh ek text document ho, ek image ho, ya ek hardware device (jaise ki printer ya disk). Directories (folders) files ko organize karne mein help karti hain. File operations ka matlab hai in files aur directories ke saath alag-alag actions perform karna, jaise unhe dekhna, copy karna, move karna, delete karna, ya unke contents ko inspect karna.

**Files aur Directories kya hain?**
*   **File :** Yeh ek collection of data hota hai. Jaise aapke Windows mein documents, photos hote hain, waise hi Linux mein bhi. Example: `myreport.txt`, `picture.jpg`, `script.sh`.
*   **Directory / Folder :** Yeh files aur dusri directories ko organize karne ke liye containers hote hain. Windows mein aap inhe 'folders' kehte ho. Example: `/home/user/documents`, `/var/log`.

**Kyun Zaroori Hain Yeh Commands?**
Ek System Administrator hone ke naate, aapko daily basis par files create karne honge, logs check karne honge, configuration files edit karne honge, backups manage karne honge, aur unwanted files delete karne honge. Yeh sab kaam in basic file operation commands ke bina impossible hain. RHCSA mein inki basic understanding aur usage test kiya jaata hai, jabki RHCE mein aapko in commands ko scripts mein aur complex scenarios mein use karna aana chahiye.

---

### 2. Key Linux Commands

Yahaan kuch important commands hain jo hum is lesson mein cover karenge:

#### 1. `ls` (List) - Files aur directories ko list karna
*   **Purpose:** Yeh command current directory ya specify ki gayi directory mein present files aur directories ko list karta hai.
*   **Basic Syntax:** `ls [OPTIONS] [FILE...]`
*   **Important Options:**
    *   `-l` (long format): Details ke saath list karta hai, jaise permissions, owner, group, size, date. `ls -l`
    *   `-a` (all): Hidden files (jo `.` se shuru hote hain) ko bhi list karta hai. `ls -a`
    *   `-h` (human-readable): File sizes ko human-readable format mein dikhata hai (KB, MB, GB). `-l` ke saath use hota hai: `ls -lh`
    *   `-d` (directory): Agar aap directory ka naam dete ho, toh woh directory ke contents list karne ke bajaye, directory ko hi list karta hai. `ls -ld /tmp`
    *   `-R` (recursive): Directories ke contents ko recursively list karta hai (sub-directories ke andar ke contents bhi). `ls -R`
    *   `-F` (classify): Files ke types ko indicate karta hai (e.g., `/` for directories, `*` for executables). `ls -F`
    *   `-t` (time): List ko modification time ke hisaab se sort karta hai (newest first). `ls -lt`

#### 2. `cp` (Copy) - Files aur directories ko copy karna
*   **Purpose:** Files aur directories ko ek location se dusre location par copy karta hai. Original file intact rehti hai.
*   **Basic Syntax:** `cp [OPTIONS] SOURCE DESTINATION`
*   **Important Options:**
    *   `-r` (recursive): Directories ko unke contents ke saath copy karne ke liye. `cp -r sourcedir destdir`
    *   `-i` (interactive): Agar destination file already exist karti hai, toh overwrite karne se pehle prompt karta hai. `cp -i file1 file2`
    *   `-p` (preserve): Original file ke attributes (permissions, ownership, timestamps) ko preserve karta hai. `cp -p file1 /tmp/`
    *   `-v` (verbose): Dikhta hai ki kya copy ho raha hai. `cp -v file1 file2`
    *   `-u` (update): Sirf tab copy karta hai jab source file, destination file se newer ho ya destination file exist na karti ho. `cp -u file1 /tmp/`

#### 3. `mv` (Move / Rename) - Files aur directories ko move ya rename karna
*   **Purpose:** Files aur directories ko ek location se dusre location par move karta hai, ya unhe rename karta hai. Original file move hone par delete ho jaati hai.
*   **Basic Syntax:** `mv [OPTIONS] SOURCE DESTINATION`
*   **Important Options:**
    *   `-i` (interactive): Agar destination file already exist karti hai, toh overwrite karne se pehle prompt karta hai. `mv -i file1 file2`
    *   `-v` (verbose): Dikhta hai ki kya move ho raha hai. `mv -v oldname newname`

#### 4. `rm` (Remove) - Files aur directories ko delete karna
*   **Purpose:** Files aur directories ko permanently delete karta hai. Careful rahiye is command ke saath!
*   **Basic Syntax:** `rm [OPTIONS] FILE...`
*   **Important Options:**
    *   `-i` (interactive): Har deletion se pehle confirmation poochta hai. `rm -i file.txt`
    *   `-r` (recursive): Directories aur unke contents ko delete karne ke liye. `rm -r mydir`
    *   `-f` (force): Without confirmation files delete karta hai (interactive mode ko override karta hai). **EXTREMELY DANGEROUS, CAUTION!** `rm -f file.txt`
    *   `-rf` (recursive force): Directories aur unke contents ko force delete karta hai. **HIGHLY DANGEROUS!** `rm -rf mydir`

#### 5. `cat` (Concatenate and display) - File contents ko display karna
*   **Purpose:** Ek ya zyada files ke contents ko standard output (terminal) par display karta hai.
*   **Basic Syntax:** `cat [OPTIONS] FILE...`
*   **Important Options:**
    *   `-n` (number): Har output line ko number karta hai. `cat -n file.txt`
    *   `-b` (number-nonblank): Non-blank output lines ko number karta hai. `cat -b file.txt`
    *   `-E` (show-ends): Har line ke end mein `$` sign dikhata hai. `cat -E file.txt`

#### 6. `less` (Page through text) - Large files ko page-by-page dekhna
*   **Purpose:** Large files ko ek time par ek screen (page) display karta hai, jisse aap upar-neeche scroll kar sakte ho. `cat` bade files ke liye practical nahi hai.
*   **Basic Syntax:** `less FILE`
*   **Navigation inside `less`:**
    *   `spacebar` / `f` : Next page scroll karna.
    *   `b` : Previous page scroll karna.
    *   `j` / `down arrow` : Ek line neeche jaana.
    *   `k` / `up arrow` : Ek line upar jaana.
    *   `/pattern` : File mein 'pattern' search karna (forward). `n` next match ke liye.
    *   `?pattern` : File mein 'pattern' search karna (backward). `N` next match ke liye.
    *   `g` : File ke beginning mein jaana.
    *   `G` : File ke end mein jaana.
    *   `q` : `less` se exit karna.

#### 7. `head` (Display first part of files) - File ki shuruwaat dekhna
*   **Purpose:** Ek file ke starting lines ko display karta hai. Default 10 lines.
*   **Basic Syntax:** `head [OPTIONS] FILE...`
*   **Important Options:**
    *   `-n NUM` : Top `NUM` lines specify karna. Example: `head -n 5 file.txt` (first 5 lines).
    *   `-c NUM` : Top `NUM` bytes specify karna. Example: `head -c 100 file.txt` (first 100 bytes).

#### 8. `tail` (Display last part of files) - File ka ant dekhna
*   **Purpose:** Ek file ke ending lines ko display karta hai. Default 10 lines. Bahut useful hai log files ko real-time monitor karne ke liye.
*   **Basic Syntax:** `tail [OPTIONS] FILE...`
*   **Important Options:**
    *   `-n NUM` : Bottom `NUM` lines specify karna. Example: `tail -n 7 file.txt` (last 7 lines).
    *   `-c NUM` : Bottom `NUM` bytes specify karna. Example: `tail -c 50 file.txt` (last 50 bytes).
    *   `-f` (follow): File ko continuously monitor karta hai. Jab file mein naya content add hota hai, woh automatically screen par display hota hai. Log files ke liye bahut useful hai. `tail -f /var/log/messages`
    *   `-F` (follow, and retry): `-f` ki tarah hi kaam karta hai, but agar file rename ya recreate ho jaaye, toh bhi usko follow karna continue karta hai. Production servers par log watching ke liye zyada robust hai.

---

### 3. Step-by-Step Examples (कदम-दर-कदम उदाहरण)

Chalo, ek scenario imagine karte hain. Aapko ek naye server par kuch initial setup aur file management karna hai.

**Setup Environment:**
Pehle hum kuch dummy files aur directories banayenge.

```bash
mkdir my_project
cd my_project

echo "This is report line 1." > report.txt
echo "This is report line 2." >> report.txt
echo "This is report line 3." >> report.txt

echo "Config item A = value1" > config.ini
echo "Config item B = value2" >> config.ini

mkdir logs
echo "Apache log entry 1" > logs/apache_access.log
echo "Apache log entry 2" >> logs/apache_access.log
echo "System log message 1" > logs/syslog.log

touch .hidden_file
mkdir my_empty_dir
```

Ab hum `my_project` directory mein hain.

#### Example 1: `ls` ka istemal
1.  **Current directory ke contents list karna:**
    ```bash
    ls
    ```
    *Output:* `config.ini  logs  my_empty_dir  report.txt` (Aapko hidden file `.hidden_file` nahi dikhegi)

2.  **Long format mein details ke saath list karna:**
    ```bash
    ls -l
    ```
    *Output:* Permissions, owner, group, size, creation date, file/dir name dikhenge.

3.  **Hidden files ko bhi list karna:**
    ```bash
    ls -a
    ```
    *Output:* `.`, `..`, `.hidden_file` bhi dikhenge.

4.  **Human-readable sizes ke saath long format:**
    ```bash
    ls -lh
    ```

5.  **`logs` directory ke contents dekhna (recursively nahi):**
    ```bash
    ls logs
    ```
    *Output:* `apache_access.log  syslog.log`

6.  **Recursively list karna (`logs` directory ke andar bhi):**
    ```bash
    ls -R
    ```
    *Output:* Current directory ke saath-saath `logs` directory ke contents bhi dikhenge.

#### Example 2: `cp` ka istemal
1.  **`report.txt` ki ek copy banana `report_backup.txt` ke naam se:**
    ```bash
    cp report.txt report_backup.txt
    ls
    ```
    *Output:* `report_backup.txt` ab list mein hoga.

2.  **`config.ini` ko `logs` directory mein copy karna:**
    ```bash
    cp config.ini logs/
    ls logs/
    ```
    *Output:* `logs/` directory mein `config.ini` bhi dikhega.

3.  **`my_empty_dir` ko `archive` naam se copy karna (directory copy ke liye `-r` zaroori hai):**
    ```bash
    cp -r my_empty_dir archive
    ls -F
    ```
    *Output:* `archive/` ab dikhegi.

4.  **`report.txt` ko overwrite karne se pehle prompt karna (agar file already exist karti ho):**
    ```bash
    cp -i report.txt report_backup.txt
    ```
    *Output:* `cp: overwrite 'report_backup.txt'?` (Y dabakar enter karein to overwrite hoga, N se cancel).

#### Example 3: `mv` ka istemal
1.  **`report_backup.txt` ka naam badal kar `old_report.txt` karna:**
    ```bash
    mv report_backup.txt old_report.txt
    ls
    ```
    *Output:* `report_backup.txt` gayab, `old_report.txt` dikhega.

2.  **`old_report.txt` ko `archive` directory mein move karna:**
    ```bash
    mv old_report.txt archive/
    ls
    ls archive/
    ```
    *Output:* `old_report.txt` current directory se gayab aur `archive/` mein dikhega.

#### Example 4: `rm` ka istemal
1.  **`config.ini` ko delete karna (prompt ke saath):**
    ```bash
    rm -i config.ini
    ```
    *Output:* `rm: remove regular file 'config.ini'?` (Y/N)

2.  **`my_empty_dir` ko delete karna (directories ke liye `-r` zaroori hai):**
    ```bash
    rm -r my_empty_dir
    ls -F
    ```
    *Output:* `my_empty_dir/` ab list mein nahi hoga.

3.  **`archive` directory aur uske contents ko force delete karna (bahut careful!):**
    ```bash
    # rm -rf archive/   # Is command ko chalane se pehle sochna.
    ```
    *Output:* `archive/` aur uske andar ke sab files permanently delete ho jayenge without any prompt.

#### Example 5: `cat` ka istemal
1.  **`report.txt` ka content display karna:**
    ```bash
    cat report.txt
    ```
    *Output:*
    ```
    This is report line 1.
    This is report line 2.
    This is report line 3.
    ```

2.  **`report.txt` ke contents ko line numbers ke saath display karna:**
    ```bash
    cat -n report.txt
    ```

3.  **Multiple files ke contents ko concatenate karna:**
    ```bash
    cat logs/apache_access.log logs/syslog.log
    ```
    *Output:* Dono files ka content ek saath dikhega.

4.  **Dono files ke contents ko ek nayi file mein save karna:**
    ```bash
    cat logs/apache_access.log logs/syslog.log > combined_logs.txt
    cat combined_logs.txt
    ```

#### Example 6: `less` ka istemal
Imagine aapke paas ek bahut bada log file hai. Hum ek dummy large file banate hain:
```bash
for i in $(seq 1 100); do echo "Log entry number $i for server activity." >> large_log.txt; done
```
1.  **`large_log.txt` ko `less` se dekhna:**
    ```bash
    less large_log.txt
    ```
    *Output:* Aapko file ka first page dikhega. `spacebar` se next page, `b` se previous, `q` se exit karein.

#### Example 7: `head` ka istemal
1.  **`large_log.txt` ki pehli 10 lines dekhna:**
    ```bash
    head large_log.txt
    ```

2.  **`large_log.txt` ki pehli 5 lines dekhna:**
    ```bash
    head -n 5 large_log.txt
    ```

#### Example 8: `tail` ka istemal
1.  **`large_log.txt` ki last 10 lines dekhna:**
    ```bash
    tail large_log.txt
    ```

2.  **`large_log.txt` ki last 7 lines dekhna:**
    ```bash
    tail -n 7 large_log.txt
    ```

3.  **`logs/syslog.log` ko real-time monitor karna (ek alag terminal mein):**
    Naye terminal mein:
    ```bash
    tail -f logs/syslog.log
    ```
    Ab apne original terminal mein, `syslog.log` mein kuch append karein:
    ```bash
    echo "New system message at $(date)" >> logs/syslog.log
    ```
    *Output:* Aap dekhenge ki naye terminal mein `tail -f` automatically naya content display kar raha hai.

---

### 4. Practice Tasks (अभ्यास कार्य)

Chalo ab aap khud in commands ko practice karo. Apne `my_project` directory ke bahar ek naya directory banao aur wahan practice karo, ya `my_project` ko delete karke naye सिरे से shuru karo.

**Task 1: Directory Structure Create Karna**
1.  Apni home directory (`cd ~`) mein ek naya directory banao `my_workspace`.
2.  `my_workspace` ke andar do sub-directories banao: `documents` aur `scripts`.
3.  `scripts` directory ke andar ek aur sub-directory banao: `backups`.

**Task 2: Files Create aur Inspect Karna**
1.  `my_workspace/documents` ke andar ek file banao `notes.txt` jismein koi bhi 3-4 lines ka text ho.
2.  `my_workspace/documents` ke andar ek aur file banao `report_2023.txt` jismein 50 lines ka dummy text ho (loops use karke `echo` aur `>>` ka istemal kar sakte ho).
3.  `my_workspace/scripts` ke andar ek empty file banao `backup_script.sh`.
4.  `my_workspace` directory mein ek hidden file banao `.my_config`.

**Task 3: Listing Files**
1.  `my_workspace` ke sabhi contents (hidden files ke saath) ko long format aur human-readable sizes mein list karo.
2.  `my_workspace/scripts` directory ke contents ko recursively list karo.
3.  `my_workspace/documents` directory ko as a directory (uske contents nahi) long format mein list karo.

**Task 4: Files Copy aur Move Karna**
1.  `notes.txt` ki ek copy `my_workspace/documents` mein hi banao, naam rakho `meeting_notes.txt`.
2.  `report_2023.txt` ko `my_workspace/scripts/backups` mein copy karo, lekin iski permissions, ownership aur timestamp original file ki tarah hi honi chahiye.
3.  `backup_script.sh` file ko `my_workspace/scripts` se `my_workspace/scripts/backups` mein move karo, aur move karte waqt uska naam `daily_backup.sh` kar do.

**Task 5: File Content View Karna**
1.  `notes.txt` ke contents ko line numbers ke saath display karo.
2.  `report_2023.txt` ke first 15 lines display karo.
3.  `report_2023.txt` ke last 20 lines display karo.
4.  `report_2023.txt` ko `less` command se open karo aur:
    *   File ke end tak jao.
    *   File ke beginning mein wapas aao.
    *   "line 25" (ya koi aur specific text jo aapne add kiya ho) search karo.
    *   `less` se exit karo.

**Task 6: Files Delete Karna**
1.  `my_workspace/documents/meeting_notes.txt` ko delete karo, lekin deletion se pehle confirmation poochha jana chahiye.
2.  `my_workspace/scripts/backups` directory ko uske contents ke saath delete karo.

**Task 7 (RHCE Level): Logging Simulation**
1.  `my_workspace` mein ek file banao `server_activity.log`.
2.  Ek terminal mein `server_activity.log` ko `tail -f` se monitor karo.
3.  Dusre terminal mein, har 2 second mein ek nayi log entry `server_activity.log` mein add karte raho (loop aur `sleep` command ka use karke). Dekho ki naya terminal mein log entries real-time dikh rahi hain ya nahi.

---

### 5. Pro Tips / Common Mistakes (प्रो टिप्स / सामान्य गलतियाँ)

#### Pro Tips:
1.  **Tab Completion (टैब कंप्लीशन):** Iska bharpoor istemal karo! Jab bhi aap koi command type karte ho aur file/directory ka naam aadha likhte ho, `Tab` dabane se Linux baki naam autocomplete kar deta hai. Yeh type karne mein time bachata hai aur typos se bachata hai.
    *   Example: `ls /var/l` -> `ls /var/log/`
2.  **`man` Pages:** Har command ka apna manual page hota hai. Agar kisi command ya option ke baare mein detail jaankari chahiye, toh `man command_name` type karo (e.g., `man ls`). `q` se exit hota hai.
3.  **Absolute vs. Relative Paths:**
    *   **Absolute Path:** Root directory (`/`) se shuru hota hai. Example: `/home/user/documents/file.txt`. Aap kahi bhi ho, yeh path hamesha same location ko point karega.
    *   **Relative Path:** Current working directory (`pwd` se pata chalta hai) ke reference mein hota hai. Example: Agar aap `/home/user` mein ho, toh `documents/file.txt` ek relative path hai. `.` current directory, `..` parent directory ko denote karta hai.
    *   **Hamesha clear raho ki aap kaunsa path use kar rahe ho!**
4.  **Wildcards (`*`, `?`, `[]`):**
    *   `*`: Kisi bhi number of characters ko match karta hai. Example: `ls *.txt` (saari .txt files). `rm logs/*` (logs directory ke andar sab delete).
    *   `?`: Single character ko match karta hai. Example: `ls file?.txt` (file1.txt, fileA.txt, etc.).
    *   `[]`: Characters ki range match karta hai. Example: `ls [abc]*.txt` (a, b, ya c se shuru hone wali .txt files).
5.  **Piping (`|`) aur Redirection (`>`, `>>`):**
    *   `|` (Pipe): Ek command ka output dusre command ka input banana. Example: `ls -l | grep ".txt"` (sirf .txt files ko list mein se filter karna).
    *   `>` (Redirect stdout): Command ke output ko ek file mein save karna, file agar exist karti hai toh overwrite ho jaati hai. Example: `ls -l > file_list.txt`.
    *   `>>` (Append stdout): Command ke output ko ek file mein append karna. Example: `echo "New log entry" >> /var/log/mylogs.log`.
6.  **`tail -F` vs `tail -f` (RHCE Tip):** `tail -F` zyada robust hai. Agar jis log file ko aap monitor kar rahe ho, woh rotate ho (yani purani file ko rename karke nayi empty file bana di jaye), toh `tail -f` purani (ab renamed) file ko hi follow karta rahega. `tail -F` naye recreated file ko automatically follow karega.

#### Common Mistakes (सामान्य गलतियाँ):
1.  **`rm -rf /` or `rm -rf *` in wrong directory:** **Yeh sabse khatarnak command hai!** Root directory (`/`) par `rm -rf` chalana aapke poore system ko delete kar sakta hai. Kabhi bhi `rm -rf` commands chalate waqt double check karein ki aap sahi directory mein hain ya nahi aur sahi files ko target kar rahe hain ya nahi. `rm -rf *` current directory ke saare contents delete kar dega.
2.  **Overwriting Files without Confirmation:** `cp` ya `mv` karte waqt `-i` option ka istemal na karna purani files ko bina poochhe overwrite kar sakta hai. Hamesha `-i` use karein agar aapko overwrite ke baare mein unsure ho.
3.  **Forgetting `-r` for Directories:** `cp` ya `rm` karte waqt directories ke liye `-r` (recursive) option bhool jaana. Example: `rm my_directory` kaam nahi karega, aapko `rm -r my_directory` use karna padega.
4.  **Incorrect Paths:** `cp` ya `mv` mein galat source ya destination path dena. Isse files ya toh galat jagah copy/move ho jaati hain, ya error aata hai. Hamesha paths ko carefully check karein.
5.  **`cat` for Large Files:** `cat` command bade files ke liye accha nahi hai. Agar file ka size screen se zyada hai, toh content tezi se scroll ho jayega aur aap dekh nahi payenge. Aise mein `less` ya `more` ka istemal karein.
6.  **Not Checking Disk Space:** Bade files copy karte waqt destination partition mein sufficient disk space check na karna. `df -h` command se disk space check kar sakte hain.

---

Umeed hai yeh lesson aapke liye kaafi comprehensive aur helpful raha hoga. In commands ko baar-baar practice karein, tabhi aap in par expert ban payenge! All the best!
