Day 7: Revision & lab practice (Linux architecture, shell basics, navigation, help tools (man, info) File operations (ls, cp, mv, rm, cat, less, head, tail) I/O redirection (>, >>, 2>, tee), pipelines (|) Searching (grep, find, locate), regular expressions Scripting basics (bash, variables, if, for, while) System documentation & basic troubleshooting (whoami, id, uptime))
---
नमस्ते RHCSA/RHCE aspirants!

Aaj humara focus hai Linux ke basic magar **bahut zaroori** concepts ko revise karna aur unki **lab practice** karna. Ye woh building blocks hain jin par aapki poori Linux expertise banegi. Chahe aap RHCSA de rahe ho ya RHCE, in topics par pakad hona extremely important hai.

Chalo shuru karte hain!

---

## RHCSA + RHCE Revision & Lab Practice: Foundations of Linux

**Session Goal:** Linux architecture, shell basics, file operations, I/O redirection, searching tools, scripting essentials, aur basic troubleshooting commands ko samajhna aur unhe lab environment mein apply karna.

---

### Module 1: Linux Architecture, Shell Basics, Navigation & Help Tools

#### 1.1 Linux Architecture ka Ek Chhota Sa Recap

*   **Kernel:** Ye Linux ka **dil (heart)** hai. Hardware aur software ke beech communication karata hai. Jaise process management, memory management, device management, file system management.
*   **Shell:** Ye woh **interface** hai jahan hum user commands type karte hain aur kernel unhe samajhta hai. Ye ek **command interpreter** hai. Bahut saare shells hain, jaise Bash (Bourne Again SHell), Zsh, Csh. Hum Bash shell use karte hain.
*   **User Space:** Applications aur utilities jo hum use karte hain (jaise Firefox, `ls`, `grep`, etc.).

**Simple Samaj:** Aap (user) shell ko command dete ho, shell us command ko kernel tak pahunchata hai, kernel hardware se kaam karata hai, aur result shell ke through aapko milta hai.

#### 1.2 Shell Basics & Navigation

Jab aap terminal kholte hain, toh aapko ek prompt dikhta hai, jaise: `user@hostname:~$`
*   `user`: Aapka current username.
*   `hostname`: Aapki machine ka naam.
*   `~`: Ye sign aapki **home directory** ko represent karta hai (`/home/user/`).
*   `$`: Iska matlab aap ek normal user hain. Agar `#` hota toh aap root user hote.

**Common Commands:**

*   `clear`: Screen ko saaf karta hai.
*   `echo "Hello World"`: Output display karta hai.
*   `pwd`: **P**rint **W**orking **D**irectory. Batata hai ki aap abhi kaunsi directory mein hain.
    *   **Command:** `pwd`
    *   **Explanation:** Aapki current location dikhayega.
*   `cd`: **C**hange **D**irectory. Directories ke beech navigate karne ke liye.
    *   **Command:** `cd /var/log` (Absolute path - root se shuru)
    *   **Command:** `cd ../` (Relative path - ek directory upar)
    *   **Command:** `cd ~` or `cd` (Home directory mein jaane ke liye)
    *   **Command:** `cd -` (Previous directory mein wapas jaane ke liye)
*   `ls`: List directory contents. Files aur directories ko dikhane ke liye.
    *   **Command:** `ls`
    *   **Command:** `ls -l` (Long listing format: permissions, owner, size, date, etc.)
    *   **Command:** `ls -a` (All files, hidden files bhi dikhayega jo `.` se shuru hote hain)
    *   **Command:** `ls -lh` (Human-readable format mein size dikhayega, jaise 10K, 2.5M)
    *   **Command:** `ls -F` (File types indicate karega, jaise `/` for directory, `*` for executable)
    *   **Command:** `ls -R` (Recursive listing, sub-directories bhi dikhayega)

#### 1.3 Help Tools (Man, Info, --help)

Jab bhi koi command bhool jao ya uske options samajhne hon, ye tools kaam aate hain.

*   `man`: **Man**ual pages. Kisi bhi command ki detail documentation.
    *   **Command:** `man ls`
    *   **Explanation:** `ls` command ki poori manual page khulegi. Sections hote hain jaise NAME, SYNOPSIS, DESCRIPTION, OPTIONS, EXAMPLES. `q` dabake exit karen.
    *   **Pro Tip:** `man 5 passwd` jaise commands se aap configuration files (`/etc/passwd`) ki documentation bhi dekh sakte hain. (`5` section number hai).
*   `info`: **Info** pages. `man` se zyada structured aur hypertext-like documentation.
    *   **Command:** `info ls`
    *   **Explanation:** `man` jaisa hi hai, but isme links hote hain jin par aap navigate kar sakte hain. `n` (next node), `p` (previous node), `u` (up a node), `q` (quit).
*   `--help`: Most commands ka quick help option. Sirf important options aur usage dikhayega.
    *   **Command:** `ls --help`
    *   **Explanation:** `ls` ke common options aur unka brief description dikhayega.

---

**Practice Task 1 (Foundation Practice):**

1.  Apni home directory mein ek folder banaye `my_linux_labs`.
    *   **Hint:** `mkdir` command
2.  `my_linux_labs` folder ke andar do aur sub-folders banaye `scripts` aur `data`.
3.  `data` folder mein navigate karen.
4.  Wahan se apni home directory mein wapas jaye, sirf `cd` command ka use karke.
5.  `my_linux_labs` folder ke contents ko list karen, saath mein hidden files aur human-readable size bhi dikhayen.
6.  `cd` command ki manual page open karen aur usme `-` option ka use search karen. Search karne ke liye `/` dabake term type karen.
7.  `grep` command ka quick help dekhen.

---

### Module 2: File Operations

Files aur directories ko manage karna Linux ka bread and butter hai.

*   `touch`: Empty file banane ke liye ya existing file ka timestamp update karne ke liye.
    *   **Command:** `touch file1.txt file2.txt`
*   `cat`: File ka content display karne aur files ko concatenate karne ke liye.
    *   **Command:** `cat file1.txt`
    *   **Command:** `cat file1.txt file2.txt` (Dono files ka content ek saath dikhayega)
    *   **Command:** `cat > newfile.txt` (Input accept karega aur `newfile.txt` mein save karega. `Ctrl+D` se save aur exit.)
*   `less`: Large files ko page by page view karne ke liye. `cat` bade files ke liye efficient nahi hota.
    *   **Command:** `less /var/log/syslog`
    *   **Explanation:** Scroll karne ke liye `space` (page down), `b` (page up), `q` (quit), `/pattern` (search).
*   `head`: File ki shuru ki lines dikhane ke liye (default 10 lines).
    *   **Command:** `head file1.txt`
    *   **Command:** `head -n 5 file1.txt` (Pehli 5 lines dikhayega)
*   `tail`: File ki aakhri ki lines dikhane ke liye (default 10 lines). Ye logs monitor karne ke liye bahut useful hai.
    *   **Command:** `tail file1.txt`
    *   **Command:** `tail -n 3 file1.txt` (Aakhri 3 lines dikhayega)
    *   **Command:** `tail -f /var/log/auth.log` (File ko continuously monitor karega changes ke liye. `Ctrl+C` se exit.)
*   `cp`: Copy files aur directories.
    *   **Command:** `cp file1.txt backup_file.txt`
    *   **Command:** `cp -v file1.txt /tmp/` (`-v` verbose, dikhayega kya copy ho raha hai)
    *   **Command:** `cp -r my_folder/ new_folder_copy/` (`-r` recursive, directory aur uske andar sab kuch copy karega)
*   `mv`: Move (relocate) files/directories ya rename kare.
    *   **Command:** `mv file1.txt /tmp/` (File ko `/tmp/` mein move karega)
    *   **Command:** `mv backup_file.txt renamed_file.txt` (File rename karega)
*   `rm`: Remove (delete) files aur directories. **Dhayan se use karen!**
    *   **Command:** `rm file1.txt`
    *   **Command:** `rm -i file2.txt` (Interactive, delete se pehle confirmation puchega)
    *   **Command:** `rm -r my_folder/` (Recursive, directory aur uske andar sab kuch delete karega)
    *   **Command:** `rm -rf sensitive_data/` (`-f` force, bina confirmation ke delete karega. **Extremely Dangerous!** Real world mein bahut soch-samajh ke use karen.)

---

**Practice Task 2 (File Operations Practice):**

1.  `my_linux_labs/data` folder mein jaye.
2.  Ek file banaye `server_log.txt` aur usme ye content dale (har line ke baad Enter dabaye, Ctrl+D se save):
    ```
    INFO: System startup initiated.
    WARNING: Low disk space detected on /dev/sda1.
    ERROR: Failed to connect to database.
    INFO: User 'admin' logged in from 192.168.1.10.
    DEBUG: Processing request 12345.
    WARNING: CPU usage is high (85%).
    ERROR: Service 'nginx' failed to start.
    INFO: System shutdown initiated.
    ```
3.  `server_log.txt` ki pehli 3 lines display karen.
4.  `server_log.txt` ki aakhri 2 lines display karen.
5.  `server_log.txt` ko `old_server_log.txt` se rename karen.
6.  `old_server_log.txt` ki ek copy banaye `backup_logs/old_server_log_backup.txt` naam se. (`backup_logs` folder bhi banaye agar nahi hai).
7.  `old_server_log.txt` ko delete karen.

---

### Module 3: I/O Redirection & Pipelines

Linux mein commands ka input, output aur errors ko control karna bahut powerful hai.

*   **Standard Streams:**
    *   **stdin (0):** Standard Input. Command ko input milta hai. Default keyboard hota hai.
    *   **stdout (1):** Standard Output. Command ka normal output yahan jata hai. Default screen hota hai.
    *   **stderr (2):** Standard Error. Command ki error messages yahan jati hain. Default screen hota hai.

#### 3.1 I/O Redirection

*   `>`: Redirect stdout to a file (overwrite). Agar file existing hai, toh uska content mitakar naya content dalega.
    *   **Command:** `ls -l /etc > etc_contents.txt`
*   `>>`: Redirect stdout to a file (append). Agar file existing hai, toh content uske end mein add karega.
    *   **Command:** `date >> etc_contents.txt`
*   `2>`: Redirect stderr to a file (overwrite).
    *   **Command:** `ls -l /nonexistent_path 2> error_log.txt` (Error message `error_log.txt` mein jayegi)
*   `&>` or `2>&1`: Redirect both stdout and stderr to a file (overwrite).
    *   **Command:** `find / -name "passwd" &> find_output.txt` (Dono output/error `find_output.txt` mein jayega)
*   `<`: Redirect stdin from a file. (Kam use hota hai in basics, but for completeness)
    *   **Command:** `wc -l < file_with_data.txt` (`wc -l` ko input `file_with_data.txt` se milega)
*   `tee`: stdout ko screen par display bhi karega aur ek file mein save bhi karega. "Ek teer se do nishane!"
    *   **Command:** `ls -l | tee output_and_screen.txt`

#### 3.2 Pipelines (`|`)

*   Pipe operator (`|`) ek command ke `stdout` ko doosre command ke `stdin` se connect karta hai. Ye commands ko chain karne ka ek powerful tareeka hai.
    *   **Command:** `ls -l /etc | grep "conf"`
    *   **Explanation:** `ls -l /etc` ka output (saari files aur directories `/etc` mein) `grep "conf"` ke input banega, jo sirf "conf" string wali lines ko filter karega.

---

**Practice Task 3 (I/O Redirection & Pipelines Practice):**

1.  Apni home directory mein ek file banaye `my_commands_output.txt`.
2.  `date` command ka output `my_commands_output.txt` mein save karen.
3.  `whoami` command ka output `my_commands_output.txt` mein append karen.
4.  `ls -l /root` command run karen. Is command se jo error aayegi (permission denied), us error ko `permission_errors.log` file mein redirect karen. `my_commands_output.txt` mein sirf normal output (agar koi ho) aaye.
5.  `ls -l /etc` command ka output screen par bhi dikhe aur `etc_files_list.txt` file mein save bhi ho.
6.  `/var/log` directory mein `.log` extension wali saari files ko list karen aur unme se "ERROR" word wali lines ko filter karke screen par display karen.

---

### Module 4: Searching (Grep, Find, Locate) & Regular Expressions

Files aur unke andar content ko search karna ek essential skill hai.

#### 4.1 Searching with `grep`

`grep` (Global Regular Expression Print) files ke andar patterns search karne ke liye use hota hai.

*   **Command:** `grep "ERROR" server_log.txt` (File `server_log.txt` mein "ERROR" string wali lines dikhayega)
*   **Command:** `grep -i "warning" server_log.txt` (`-i` ignore case, "warning" ya "WARNING" sab dikhayega)
*   **Command:** `grep -v "INFO" server_log.txt` (`-v` invert match, "INFO" wali lines ko chhodkar baki sab dikhayega)
*   **Command:** `grep -n "fail" server_log.txt` (`-n` line number bhi dikhayega)
*   **Command:** `grep -r "kernel" /var/log/` (`-r` recursive, `/var/log` aur uske sub-directories mein "kernel" search karega)
*   **Command:** `grep "^ERROR" server_log.txt` (Line ki shuruat "ERROR" se ho)
*   **Command:** `grep "initiated.$" server_log.txt` (Line ka end "initiated." se ho)

#### 4.2 Regular Expressions (Regex) - Brief Intro

Regex patterns hain jo complex searches ke liye use hote hain. `grep` in patterns ko samajhta hai.

*   `.` (dot): Matches any single character. (e.g., `gr.p` matches "grip", "grap", "grep")
*   `*`: Matches zero or more occurrences of the preceding character. (e.g., `go*gle` matches "ggle", "gogle", "gooogle")
*   `+`: Matches one or more occurrences of the preceding character. (e.g., `go+gle` matches "gogle", "gooogle" but not "ggle")
*   `?`: Matches zero or one occurrence of the preceding character. (e.g., `colou?r` matches "color" and "colour")
*   `[]`: Character set. Matches any one character inside the brackets. (e.g., `[aeiou]` matches any vowel)
*   `[0-9]`: Matches any digit.
*   `[a-zA-Z]`: Matches any letter.
*   `[^char]`: Matches any character NOT in the set. (e.g., `[^0-9]` matches any non-digit)
*   `()`: Grouping, for applying operators to a sequence.

#### 4.3 Searching with `find`

`find` files aur directories ko unke naam, size, type, modification time, owner etc. ke hisab se search karta hai. Ye filesystem tree mein search karta hai.

*   **Command:** `find . -name "*.txt"` (Current directory mein .txt files search karega)
*   **Command:** `find /etc -type f -name "*-conf"` (`/etc` mein files jinka naam `*-conf` se end ho)
    *   `-type f`: Files
    *   `-type d`: Directories
*   **Command:** `find /var/log -size +1G` (`/var/log` mein 1GB se bade files)
    *   `+1G`: Greater than 1 Gigabyte
    *   `-1M`: Less than 1 Megabyte
    *   `10K`: Exactly 10 Kilobytes
*   **Command:** `find /home -user john` (`/home` mein "john" user ke files)
*   **Command:** `find /tmp -mtime -7` (`/tmp` mein woh files jo pichle 7 dinon mein modify hue hain)
    *   `+7`: More than 7 days ago
    *   `-7`: Less than 7 days ago
    *   `7`: Exactly 7 days ago
*   **Command:** `find . -name "*.bak" -exec rm {} \;` (Saare `.bak` files dhund kar delete karega. **Use with caution!**)
    *   `-exec`: Found files par command execute karega. `{}` placeholder hai file name ke liye. `\;` command end karta hai.

#### 4.4 Searching with `locate`

`locate` command ek database (`/var/lib/mlocate/mlocate.db`) ka use karta hai files ko fast search karne ke liye. Ye command `find` se fast hai, lekin database update hone tak new files nahi dikhayega.

*   **Command:** `locate passwd`
*   **Command:** `sudo updatedb` (Database ko update karta hai. Isko run karne ke baad hi `locate` new files ko dhund payega.)

---

**Practice Task 4 (Searching & Regex Practice):**

1.  `my_linux_labs/data` folder mein `server_log.txt` ko use karte hue, woh saari lines dhundhe jinme "WARNING" ya "ERROR" ho (case-insensitive).
2.  `/etc` directory mein woh saari files dhundhe jinka naam `hosts` ho ya `resolv` ho.
3.  `/var/log` directory mein pichle 24 ghante mein modify hui saari files ko list karen.
4.  Apni home directory mein, saari files (.txt, .log, .conf, etc.) dhundhe jinka size 100KB se zyada ho.
5.  `passwd` string wali saari files ko locate kare. Agar results outdated lag rahe hain, toh database update karen aur phir se try karen.

---

### Module 5: Scripting Basics (Bash)

Bash scripts automate repetitive tasks. Ye RHCSA aur RHCE dono ke liye crucial hain.

*   **What is a Script?** Commands ki ek series jo ek file mein likhi hoti hai, jo execute hone par sequentially run hoti hain.
*   **Shebang:** `#!/bin/bash`
    *   Ye script ki pehli line hoti hai. Kernel ko batati hai ki is script ko kis interpreter se run karna hai (yahan Bash shell).
*   **Permissions:** Script ko executable banana padta hai.
    *   **Command:** `chmod +x my_script.sh`
    *   **Run:** `./my_script.sh`
*   **Comments:** `#` se shuru hone wali lines comments hoti hain, shell unhe ignore karta hai.
*   **Variables:** Data store karne ke liye.
    *   **Syntax:** `VAR_NAME="value"` (Space nahi dena `=` ke aage peeche)
    *   **Access:** `$VAR_NAME` ya `${VAR_NAME}`
    *   **Example:** `NAME="Alice"`, `echo "Hello, $NAME"`
*   **`read` command:** User se input lene ke liye.
    *   **Command:** `read -p "Enter your name: " USER_NAME`
    *   **Explanation:** `-p` prompt display karega. User ka input `USER_NAME` variable mein store hoga.
*   **`if` statements:** Conditional logic.
    *   **Syntax:**
        ```bash
        if [ condition ]; then
            # code if true
        else
            # code if false (optional)
        fi
        ```
    *   **Common Conditions (use `[` `]` or `[[` `]]`):**
        *   `-f file`: File exists and is a regular file.
        *   `-d directory`: Directory exists.
        *   `-eq`, `-ne`, `-gt`, `-lt`, `-ge`, `-le`: Numeric comparison (equal, not equal, greater than, etc.)
        *   `==`, `!=`: String comparison.
    *   **Example:**
        ```bash
        #!/bin/bash
        FILE_TO_CHECK="/etc/hosts"
        if [ -f "$FILE_TO_CHECK" ]; then
            echo "$FILE_TO_CHECK exists and is a regular file."
        else
            echo "$FILE_TO_CHECK does not exist or is not a regular file."
        fi
        ```
*   **`for` loops:** Iterate over a list of items.
    *   **Syntax:**
        ```bash
        for ITEM in list_of_items; do
            # code to execute for each item
        done
        ```
    *   **Example:**
        ```bash
        #!/bin/bash
        for FRUIT in Apple Banana Cherry; do
            echo "I like $FRUIT."
        done
        ```
*   **`while` loops:** Repeat as long as a condition is true.
    *   **Syntax:**
        ```bash
        COUNT=1
        while [ "$COUNT" -le 5 ]; do
            echo "Count is: $COUNT"
            COUNT=$((COUNT + 1)) # Increment COUNT
        done
        ```

---

**Practice Task 5 (Scripting Basics Practice):**

1.  `my_linux_labs/scripts` folder mein jaye.
2.  Ek script banaye `my_info.sh` naam se.
3.  Is script mein shebang add karen.
4.  Ek variable `MY_NAME` banaye aur usme apna naam store karen.
5.  Script execute hone par "Hello, [MY_NAME]! Today is [current date]" print kare. Current date ke liye `date` command ka output use karen.
6.  User se uska favourite color puche aur usko ek variable `FAV_COLOR` mein store karen.
7.  Agar user ka `FAV_COLOR` "Red" hai, toh print kare "Red is a bold choice!". Otherwise, print kare "Interesting color choice!".
8.  Script mein ek loop add kare jo 1 se 3 tak numbers print kare. (Hint: `for` loop ya `while` loop use karen)
9.  Script ko executable banaye aur run karen.

---

### Module 6: System Documentation & Basic Troubleshooting

System ki health aur information check karne ke liye kuch basic commands.

*   `whoami`: Batata hai ki aap abhi kis user ke roop mein logged in hain.
    *   **Command:** `whoami`
*   `id`: Current user ki user ID (UID), primary group ID (GID), aur all groups jinse user belong karta hai, dikhata hai.
    *   **Command:** `id`
    *   **Command:** `id john` (Kisi specific user ki details)
*   `uptime`: System kitne time se chal raha hai, kitne users logged in hain, aur average system load kya hai, ye sab batata hai.
    *   **Command:** `uptime`
*   `hostname`: System ka hostname (machine ka naam) dikhata hai.
    *   **Command:** `hostname`
*   `date`: Current date aur time dikhata hai.
    *   **Command:** `date`
    *   **Command:** `date "+%Y-%m-%d %H:%M:%S"` (Custom format mein date)

---

**Practice Task 6 (System Info & Troubleshooting Practice):**

1.  Apne current user ka username check karen.
2.  Apne current user ki UID, GID aur groups ko check karen.
3.  Apne system ka uptime aur load average check karen. Load average kya indicate karta hai, is par thoda research karen.
4.  Apne system ka hostname check karen.
5.  Current date aur time ko "YYYY/MM/DD - HH:MM:SS" format mein display karen.

---

### Conclusion & Next Steps

Badhai ho! Aapne Linux ke in foundational topics ko revise kiya hai. Ye commands aur concepts RHCSA aur RHCE dono exams ke liye **bahut zaroori** hain.

**Key Takeaways:**

*   **Practice is Key:** Sirf padhne se kuch nahi hoga, har command ko terminal mein type karke dekhen.
*   **Read Man Pages:** Jab bhi doubt ho, `man` page aapka sabse achha dost hai.
*   **Understand, Don't Memorize:** Commands ke syntax se zyada, unke peeche ka logic aur unka use cases samajhna zaroori hai.
*   **Think Like an Administrator:** Har command ko is perspective se dekhen ki ek system admin ise kyun use karega.

Apni practice jaari rakhen! Agle session mein hum aur advanced topics par focus karenge. Koi bhi sawaal ho toh poochiye!

Happy Learning!
