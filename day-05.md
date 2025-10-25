Day 5: Scripting basics (bash, variables, if, for, while)
---
Namaste students! Main aapka Linux instructor hoon aur aaj hum ek bahut hi important aur powerful topic par baat karenge – "Scripting Basics". Yeh RHCSA aur RHCE exams ke liye foundation hai, aur real-world server management mein aapka sabse bada 'hathiyaar' (weapon) banega. Scripting se aap apne daily tasks ko automate kar sakte hain, time bacha sakte hain aur mistakes ko kam kar sakte hain. So, let's dive in!

---

## **1. Concept Explanation (Hinglish)**

Chaliye, sabse pehle samajhte hain ki scripting kya hai aur iski zaroorat kyun hai.

### **What is a Script? (Script kya hai?)**

Ek script ek simple text file hoti hai jismein ek sequence of commands likhe hote hain, bilkul jaise aap command line par ek ke baad ek commands type karte hain. Soch lijiye ki aapko har subah 5 commands run karne hain. Aap ya toh unhe manually type karenge ya phir un 5 commands ko ek file mein likh kar, us file ko ek baar run kar denge. Yehi file ek script hai!

### **Why Scripting? (Scripting kyun zaroori hai?)**

1.  **Automation:** Jo tasks baar-baar karne padte hain (repetitive tasks), unhe automate karna. Jaise, har subah log files clean karna ya server health check karna.
2.  **Efficiency:** Manually kaam karne se time lagta hai aur galti hone ke chances hote hain. Scripting se kaam jaldi aur accurately hota hai.
3.  **Consistency:** Har baar ek hi tareeke se kaam hota hai, koi step miss nahi hota.
4.  **Complex Tasks:** Bade aur complex tasks ko chote-chote, manage-able parts mein divide karna.

### **Bash Shell (Humara Interpreter)**

Linux mein kayi tarah ke shells hote hain (sh, csh, ksh, zsh, bash). Sabse common aur powerful shell **Bash** (Bourne Again SHell) hai. Hum jo scripts likhenge woh Bash shell mein run honge. Bash hamare commands ko "samajhta" hai aur unhe execute karta hai.

### **Shebang (`#! /bin/bash`) - The Interpreter Directive**

Har script ki pehli line mein hum ek special directive likhte hain jise **Shebang** kehte hain: `#!/bin/bash`.
Yeh line operating system ko batati hai ki is script ko kis program (interpreter) ke through run karna hai. `#!/bin/bash` ka matlab hai "Is script ko `/bin/bash` program use karke execute karo." Agar aap yeh nahi likhenge, toh script default shell mein run ho sakti hai, aur ho sakta hai aapke commands theek se kaam na karein.

### **Variables (Data Storage Ke Dibbe)**

Variables "data storage ke dibbe" ki tarah hote hain. Aap inmein koi bhi value store kar sakte hain, jaise text, numbers, ya command ka output.
*   **Declare karna:** `VARIABLE_NAME="value"`
*   **Access karna:** Value ko use karne ke liye variable name ke aage `$` lagate hain, jaise `echo $VARIABLE_NAME`.
*   **Rules:** Variable names letters, numbers aur underscore se ban sakte hain. Shuruat number se nahi ho sakti. Conventionally, uppercase letters use kiye jaate hain.

### **If-Else (Conditional Logic / Sharton Par Kaam Karna)**

`if` statement se hum conditions check karte hain. Agar condition `true` hai, toh kuch set of commands run hote hain; agar `false` hai, toh doosre commands run hote hain (ya kuch bhi nahi).
*   **Basic Syntax:**
    ```bash
    if [ condition ]; then
        # Commands to execute if condition is true
    elif [ another_condition ]; then
        # Commands if another_condition is true
    else
        # Commands to execute if all conditions are false
    fi
    ```
*   **Conditions:** `[ ]` brackets ke andar hum conditions likhte hain. Spaces ka dhyan rakhna bohot zaroori hai.
    *   **Numeric Comparisons:**
        *   `-eq` (equal to)
        *   `-ne` (not equal to)
        *   `-gt` (greater than)
        *   `-lt` (less than)
        *   `-ge` (greater than or equal to)
        *   `-le` (less than or equal to)
    *   **String Comparisons:**
        *   `==` (equal to)
        *   `!=` (not equal to)
        *   `-z` (string is empty)
        *   `-n` (string is not empty)
    *   **File Comparisons:**
        *   `-f` (file exists and is regular file)
        *   `-d` (file exists and is a directory)
        *   `-e` (file exists)
        *   `-r` (file is readable)
        *   `-w` (file is writable)
        *   `-x` (file is executable)

### **For Loop (Ek List Par Ghoomna)**

`for` loop ka use tab hota hai jab aapko ek set of items (jaise numbers ki list, files ki list, ya users ke names) par ek hi kaam baar-baar karna ho.
*   **Syntax:**
    ```bash
    for item in list_of_items; do
        # Commands to execute for each item
    done
    ```
*   `item` ek temporary variable hota hai jo list ke har item ko ek-ek karke store karta hai.

### **While Loop (Jab Tak Ek Shart Poori Ho Rahi Hai)**

`while` loop ka use tab hota hai jab aapko commands ko tab tak execute karna ho jab tak ek specific condition `true` ho. Jab condition `false` ho jaati hai, loop ruk jaata hai.
*   **Syntax:**
    ```bash
    while [ condition ]; do
        # Commands to execute as long as condition is true
    done
    ```
*   `while` loop mein infinite loop se bachne ke liye, make sure ki loop ke andar koi na koi command condition ko `false` karne ka kaam kare.

---

## **2. Key Linux Commands (Hinglish Explanations)**

Yahan kuch important commands hain jo scripting mein bahut use hote hain:

1.  **`echo`**:
    *   **Uddeshya (Purpose):** Screen par text ya variable ki value display karne ke liye. Jaise `print` command doosri languages mein hota hai.
    *   **Udaharan (Example):** `echo "Hello, World!"` ya `echo "Mera naam $NAME hai."`

2.  **`read`**:
    *   **Uddeshya (Purpose):** User se input lene ke liye. User jo type karta hai, usko ek variable mein store karta hai.
    *   **Udaharan (Example):** `read -p "Apna naam bataiye: " USER_NAME`. `-p` option prompt message display karta hai.

3.  **`chmod`**:
    *   **Uddeshya (Purpose):** File permissions change karne ke liye. Scripts ko run karne ke liye executable permission (`+x`) dena zaroori hai.
    *   **Udaharan (Example):** `chmod +x my_script.sh` (executable banata hai), `chmod 755 my_script.sh` (owner ko read/write/execute, group/others ko read/execute).

4.  **`expr` / `(( ))`**:
    *   **Uddeshya (Purpose):** Basic arithmetic operations (addition, subtraction, multiplication, division) perform karne ke liye. `(( ))` modern aur preferred tareeka hai Bash mein.
    *   **Udaharan (Example - expr):** `RESULT=$(expr 10 + 5)` (spaces important, aur command substitution use karna padta hai).
    *   **Udaharan (Example - (( ))):** `(( RESULT = 10 + 5 ))` ya `echo $(( 10 * 5 ))`. Yeh zyada user-friendly hai.

5.  **`test` / `[` `]`**:
    *   **Uddeshya (Purpose):** Conditions check karne ke liye. `[` bhi `test` command ka hi shortcut hai.
    *   **Udaharan (Example):** `if test -f myfile.txt; then ... fi` ya `if [ -d /tmp/my_dir ]; then ... fi`. (Spaces ka dhyaan rakhein).

6.  **`basename`**:
    *   **Uddeshya (Purpose):** Ek path mein se sirf file ka naam nikalne ke liye (directory part hata kar).
    *   **Udaharan (Example):** `FILE_NAME=$(basename /home/user/document.txt)` will set `FILE_NAME` to `document.txt`.

7.  **`dirname`**:
    *   **Uddeshya (Purpose):** Ek path mein se sirf directory part nikalne ke liye (file name hata kar).
    *   **Udaharan (Example):** `DIR_NAME=$(dirname /home/user/document.txt)` will set `DIR_NAME` to `/home/user`.

---

## **3. Step-by-Step Examples (Real-World Scenarios)**

Chaliye, kuch practical examples dekhte hain:

### **Example 1: Basic Hello World Script with Variables & Input**

Yeh script user se uska naam poochegi aur use greet karegi.

1.  **Script File Banayein:**
    ```bash
    nano hello.sh
    ```
2.  **Content Add Karein:**
    ```bash
    #!/bin/bash
    # Yeh ek simple script hai jo user ko greet karta hai.

    echo "Hello, Linux Students!" # Ek simple message display karein

    # User ka naam poochne ke liye read command use karein
    read -p "Apna naam daliye, please: " USER_NAME

    # Agar user ne naam nahi dala, toh default name use karein
    if [ -z "$USER_NAME" ]; then
        USER_NAME="Guest"
    fi

    echo "Aapka swagat hai, $USER_NAME!" # Variable ki value display karein
    echo "Aaj ki date hai: $(date)" # Command ka output bhi display kar sakte hain
    ```
3.  **Save aur Exit karein** (Ctrl+O, Enter, Ctrl+X).
4.  **Executable Permissions Dein:**
    ```bash
    chmod +x hello.sh
    ```
5.  **Script Run Karein:**
    ```bash
    ./hello.sh
    ```
    *Output:*
    ```
    Hello, Linux Students!
    Apna naam daliye, please: Rahul
    Aapka swagat hai, Rahul!
    Aaj ki date hai: Tue Oct 26 10:30:00 AM IST 2023
    ```
    Agar aap naam nahi dalenge:
    ```
    Hello, Linux Students!
    Apna naam daliye, please: 
    Aapka swagat hai, Guest!
    Aaj ki date hai: Tue Oct 26 10:30:00 AM IST 2023
    ```

### **Example 2: File Existence Checker (If-Else)**

Yeh script check karegi ki user dwara specify ki gayi file ya directory exist karti hai ya nahi.

1.  **Script File Banayein:**
    ```bash
    nano check_path.sh
    ```
2.  **Content Add Karein:**
    ```bash
    #!/bin/bash
    # Yeh script check karta hai ki provide kiya gaya path file hai, directory hai, ya exist nahi karta.

    read -p "Enter a file or directory path: " PATH_TO_CHECK

    if [ -f "$PATH_TO_CHECK" ]; then
        echo "'$PATH_TO_CHECK' ek regular file hai."
        echo "File size: $(du -h "$PATH_TO_CHECK" | awk '{print $1}')"
    elif [ -d "$PATH_TO_CHECK" ]; then
        echo "'$PATH_TO_CHECK' ek directory hai."
        echo "Directory contents:"
        ls -l "$PATH_TO_CHECK"
    elif [ -e "$PATH_TO_CHECK" ]; then
        echo "'$PATH_TO_CHECK' exist karta hai, lekin na toh regular file hai na hi directory (ho sakta hai symlink ya special file ho)."
    else
        echo "'$PATH_TO_CHECK' exist nahi karta."
    fi
    ```
3.  **Save aur Exit karein.**
4.  **Executable Permissions Dein:**
    ```bash
    chmod +x check_path.sh
    ```
5.  **Script Run Karein:**
    ```bash
    ./check_path.sh
    ```
    *Output (example for existing file):*
    ```
    Enter a file or directory path: /etc/passwd
    '/etc/passwd' ek regular file hai.
    File size: 4.0K
    ```
    *Output (example for existing directory):*
    ```
    Enter a file or directory path: /tmp
    '/tmp' ek directory hai.
    Directory contents:
    total 0
    drwxr-xr-x. 2 root root 6 Oct 26 10:30 my_test_dir
    ```
    *Output (example for non-existent path):*
    ```
    Enter a file or directory path: /nonexistent/path
    '/nonexistent/path' exist nahi karta.
    ```

### **Example 3: Simple Backup Script (For Loop)**

Yeh script ek specific directory ke andar ki sari `.log` files ko ek backup directory mein copy karegi.

1.  **Script File Banayein:**
    ```bash
    nano backup_logs.sh
    ```
2.  **Content Add Karein:**
    ```bash
    #!/bin/bash
    # Yeh script specific directory se .log files ko backup karta hai.

    SOURCE_DIR="/var/log" # Jis directory se files leni hain
    BACKUP_DIR="/tmp/log_backups_$(date +%Y%m%d_%H%M%S)" # Backup ke liye new directory banayenge

    echo "Source directory: $SOURCE_DIR"
    echo "Backup directory: $BACKUP_DIR"

    # Backup directory create karein agar exist nahi karti
    mkdir -p "$BACKUP_DIR"

    # Check karein ki source directory exist karti hai ya nahi
    if [ ! -d "$SOURCE_DIR" ]; then
        echo "Error: Source directory '$SOURCE_DIR' exist nahi karti. Exit kar rahe hain."
        exit 1 # Script ko error code ke saath exit karein
    fi

    # For loop se sari .log files ko iterate karein
    echo "Backing up .log files from $SOURCE_DIR to $BACKUP_DIR..."
    for log_file in "$SOURCE_DIR"/*.log; do
        if [ -f "$log_file" ]; then # Make sure it's a regular file
            echo "Copying $log_file..."
            cp "$log_file" "$BACKUP_DIR/"
        fi
    done

    echo "Backup complete!"
    echo "Backup files located at: $BACKUP_DIR"
    ls -l "$BACKUP_DIR"
    ```
3.  **Save aur Exit karein.**
4.  **Executable Permissions Dein:**
    ```bash
    chmod +x backup_logs.sh
    ```
5.  **Script Run Karein:**
    ```bash
    ./backup_logs.sh
    ```
    *Output (example):*
    ```
    Source directory: /var/log
    Backup directory: /tmp/log_backups_20231026_104530
    Backing up .log files from /var/log to /tmp/log_backups_20231026_104530...
    Copying /var/log/boot.log...
    Copying /var/log/maillog...
    Copying /var/log/messages...
    ...
    Backup complete!
    Backup files located at: /tmp/log_backups_20231026_104530
    total 40
    -rw-------. 1 root root 8219 Oct 26 10:45 boot.log
    -rw-------. 1 root root 2275 Oct 26 10:45 maillog
    -rw-------. 1 root root 7644 Oct 26 10:45 messages
    ```

### **Example 4: Countdown Timer (While Loop)**

Yeh script user se ek number legi aur us number se zero tak countdown karegi.

1.  **Script File Banayein:**
    ```bash
    nano countdown.sh
    ```
2.  **Content Add Karein:**
    ```bash
    #!/bin/bash
    # Yeh script user se number lekar countdown karta hai.

    read -p "Enter a starting number for the countdown: " COUNT

    # Input validation (RHCE level): Check if input is a positive number
    if ! [[ "$COUNT" =~ ^[0-9]+$ ]] || [ "$COUNT" -le 0 ]; then
        echo "Error: Please enter a positive integer."
        exit 1
    fi

    echo "Starting countdown from $COUNT..."

    while [ "$COUNT" -gt 0 ]; do
        echo "$COUNT"
        sleep 1 # 1 second ke liye pause karein
        (( COUNT-- )) # COUNT ko ek se kam karein (decrement)
    done

    echo "Blast Off!"
    ```
3.  **Save aur Exit karein.**
4.  **Executable Permissions Dein:**
    ```bash
    chmod +x countdown.sh
    ```
5.  **Script Run Karein:**
    ```bash
    ./countdown.sh
    ```
    *Output (for input 3):*
    ```
    Enter a starting number for the countdown: 3
    Starting countdown from 3...
    3
    2
    1
    Blast Off!
    ```
    *Output (for invalid input):*
    ```
    Enter a starting number for the countdown: hello
    Error: Please enter a positive integer.
    ```

---

## **4. Practice Tasks (Aapke Liye Lab Tasks)**

Ab aap khud in tasks ko try karke apni scripting skills ko polish karein:

1.  **Task 1: User Creator Script (RHCSA Focus)**
    *   Ek script likhein jo user se ek username pooche.
    *   Agar user exist nahi karta, toh use `useradd` command se create karein.
    *   Use ek default password dein (e.g., `Pa$$w0rd1`) using `echo "password" | passwd --stdin username`.
    *   Confirm karein ki user successfully create ho gaya hai ya nahi.
    *   **Hint:** `id -u username` command check karta hai ki user exist karta hai ya nahi. Iska exit status check karein (0 for success, non-zero for failure).

2.  **Task 2: Disk Space Monitor (RHCSA Focus)**
    *   Ek script banayein jo `/` partition ka free disk space check kare.
    *   Agar free space 20% se kam hai, toh screen par ek warning message display kare (e.g., "WARNING: Disk space low on /! Only X% free.").
    *   **Hint:** `df -h /` command ka output parse karne ke liye `awk` ya `sed` use karein.

3.  **Task 3: Directory Cleaner (RHCE Focus)**
    *   Ek script likhein jo ek specified directory (e.g., `/tmp/my_old_files`) mein 7 din se purani files ko identify kare.
    *   User se confirmation pooche ki "Kya aap in files ko delete karna chahte hain? (y/n)".
    *   Agar user 'y' type kare, toh un files ko delete kare.
    *   **Hint:** `find /path/to/dir -type f -mtime +7` command use karein purani files dhoondhne ke liye. Input validation bhi add karein.

4.  **Task 4: Simple Menu-Driven Script (RHCE Focus)**
    *   Ek script banayein jo user ko ek menu de (e.g., "1. Show current date & time", "2. Show disk usage", "3. Exit").
    *   User ki choice ke hisaab se relevant command run kare (`date`, `df -h`).
    *   Jab tak user "Exit" choose na kare, menu ko repeat karte rahein.
    *   **Hint:** `while` loop aur `case` statement (advanced conditional) ka use karein. (Aap `if/elif/else` se bhi kar sakte hain, but `case` zyada elegant hota hai menus ke liye).

---

## **5. Pro Tips / Common Mistakes (RHCE Level Insight)**

Yeh tips aapko behtar aur robust scripts likhne mein help karenge aur common pitfalls se bachayenge:

### **Pro Tips:**

1.  **Always use Shebang (`#!/bin/bash`):** Hamesha script ki pehli line mein shebang include karein. Yeh ensure karta hai ki script aapke intended shell (Bash) mein run ho, regardless of user's default shell.
2.  **Make Scripts Executable (`chmod +x`):** Bhoolna nahi! Script ko run karne ke liye executable permission dena zaroori hai.
3.  **Use Comments (`#`):** Apne scripts mein comments add karein taaki aap aur doosre log samajh paayein ki kaunsa part kya kaam kar raha hai.
4.  **Quote Variables (`"$VARIABLE"`):** Variables ko hamesha double quotes mein enclose karein (`echo "$MY_VAR"`). Yeh spaces aur special characters ko expand hone se rokta hai, especially jab filenames ya paths mein spaces hon.
5.  **Use `set -e` for Error Handling (RHCE):** Apni scripts ki shuruat mein `set -e` add karein. Isse script automatically exit ho jaegi agar koi command non-zero exit status ke saath fail hota hai. Yeh unexpected behavior ko rokta hai.
    ```bash
    #!/bin/bash
    set -e # Script will exit if any command fails
    # ... rest of your script
    ```
6.  **Use `set -x` for Debugging (RHCE):** Jab aapki script mein koi problem ho, toh `set -x` use karein. Yeh har command ko execute hone se pehle print karta hai, jo debugging mein bahut helpful hota hai.
    ```bash
    #!/bin/bash
    set -x # Commands will be printed before execution for debugging
    # ... rest of your script
    ```
    Debugging ke baad isko hata dena ya comment out kar dena chahiye.
7.  **Meaningful Variable Names:** Variables ko aise naam dein jo unke purpose ko clearly batayein (e.g., `USER_INPUT`, `LOG_FILE_PATH`, `BACKUP_DIR`).
8.  **Input Validation (RHCE):** User input ko hamesha validate karein. Kya user ne number daala hai jab number chahiye tha? Kya file path exist karta hai?
9.  **Use Functions (RHCE):** Bade scripts ko chote, reusable functions mein divide karein. Isse code cleaner aur easier to maintain banta hai. (Yeh thoda advanced topic hai, but keep it in mind).
10. **Use `shellcheck` (RHCE):** `shellcheck` ek static analysis tool hai jo aapke scripts mein potential errors, bad practices aur warnings ko identify karta hai. `yum install shellcheck` ya `apt install shellcheck` karke install karein.
    ```bash
    shellcheck your_script.sh
    ```

### **Common Mistakes (Aur Unse Kaise Bachein):**

1.  **Missing Spaces in `[` `]`:** Conditional statements (`if [ condition ]`) mein brackets aur condition ke beech spaces ka hona zaroori hai.
    *   **Galat (Wrong):** `if [condition];`
    *   **Sahi (Correct):** `if [ "$VAR" == "value" ];`
2.  **No Quotes Around Variables:** Jaise upar bataya, variables ko quotes na karna unexpected behavior create kar sakta hai.
    *   **Galat (Wrong):** `if [ $FILE_NAME == "my file.txt" ];` (Agar $FILE_NAME mein space hai toh error dega)
    *   **Sahi (Correct):** `if [ "$FILE_NAME" == "my file.txt" ];`
3.  **Using `=` instead of `==` for String Comparison:** Bash mein string equality check karne ke liye `==` ya single `=` use kar sakte hain, lekin `==` zyada common aur recommended hai kyunki `=` assignment operator bhi hota hai.
    *   **Galat (Avoid):** `if [ "$NAME" = "John" ];`
    *   **Sahi (Better):** `if [ "$NAME" == "John" ];`
4.  **Forgetting `exit` in Error Conditions:** Agar script mein koi critical error ho aur aap chahte hain ki script ruk jaye, toh `exit 1` (ya koi non-zero exit code) ka use karein. `set -e` iska automatic tareeka hai.
5.  **Hardcoding Paths/Values:** Agar koi value ya path baar-baar use ho raha hai ya future mein change ho sakta hai, toh use variable mein store karein. Isse maintainability badhti hai.
6.  **Infinite Loops:** `while` loop mein agar condition kabhi false nahi hoti, toh script infinite loop mein chali jayegi. Hamesha ensure karein ki loop ke andar koi step condition ko change kare. `Ctrl+C` se aap infinite loop ko stop kar sakte hain.

---

I hope yeh comprehensive lesson aapko scripting basics achhe se samajhne mein help karega. Practice karte rahiye, kyunki "Practice makes a man perfect!" All the best for your RHCSA and RHCE journey!
