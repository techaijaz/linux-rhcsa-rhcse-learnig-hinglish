Day 16: Systemd basics (systemctl, journalctl)
---
Namaste students! Main hoon aapka Linux instructor. Aaj hum ek bahut hi important topic par baat karenge jo modern Linux systems, especially Red Hat Enterprise Linux (RHEL) aur uske derivatives jaise CentOS, Fedora, AlmaLinux, Rocky Linux ka core hai: **Systemd basics (systemctl, journalctl)**. RHCSA aur RHCE exams ke liye yeh topic bohot zaroori hai. Chaliye shuru karte hain!

---

## RHCSA + RHCE Training Lesson: Systemd Basics (systemctl, journalctl)

### 1. Concept Explanation

Linux mein pehle service management ke liye SysVinit ya Upstart jaise systems use hote the. Lekin ab modern Linux distributions, khaaskar RHEL 7 onwards, **Systemd** ko apna default init system aur service manager banaya hai.

#### 1.1 What is Systemd?

*   **PID 1:** Systemd first process hai jo boot hone par start hota hai (uska Process ID ya PID hamesha 1 hota hai). Yeh system ke saare baaki processes ko start aur manage karta hai.
*   **Init System & Service Manager:** Systemd ka main kaam hai system boot process ko handle karna aur saari services (jaise web server, database, SSH, etc.) ko manage karna – unhe start karna, stop karna, restart karna aur unka status track karna.
*   **Parallelization:** Systemd services ko parallel mein start karta hai, jisse system boot time kaafi fast ho jata hai. Old init systems sequential hote the.
*   **Units:** Systemd har cheez ko "unit" ke roop mein treat karta hai. Ye units `systemd` configuration files hote hain jo defines karte hain ki Systemd ko kya karna hai. Common unit types hain:
    *   `.service`: Background services (daemons) manage karne ke liye. Most common.
    *   `.socket`: Socket-based activation ke liye.
    *   `.target`: Group of units (like runlevels in SysVinit).
    *   `.mount`: Filesystem mount points manage karne ke liye.
    *   `.timer`: Scheduled events ke liye (cron jobs ki tarah).
    *   `.path`: Path-based activation ke liye.
*   **Dependency Management:** Systemd services ki dependencies ko automatically handle karta hai. Agar ek service ko start hone ke liye doosri service chahiye, toh Systemd pehle us dependency ko fulfill karega.

#### 1.2 What is `systemctl`?
`systemctl` Systemd ke saath interact karne ka primary command-line utility hai. Isse aap Systemd units ko control kar sakte hain, system state dekh sakte hain, aur system-wide changes kar sakte hain.
Basically, aapki gaadi ka dashboard aur control panel hai `systemctl`.

#### 1.3 What is `journalctl`?

`journalctl` Systemd ka centralized logging system hai. Pehle Linux mein logs alag-alag files mein store hote the (`/var/log/messages`, `/var/log/secure`, `/var/log/httpd/error_log`, etc.). Systemd ne `journald` service introduce ki hai jo saare logs ko ek single, structured, aur indexed binary format mein collect karti hai.
`journalctl` command isi journal se logs retrieve karne ke liye use hota hai. Yeh logs ko filter karne, specific services ke logs dekhne, ya boot time ke logs dekhne mein help karta hai.
Soch lijiye yeh aapke system ki saari "diary" ko manage karta hai.

---

### 2. Key Linux Commands

Yahan important `systemctl` aur `journalctl` commands hain:

#### 2.1 `systemctl` Commands

*   `systemctl status `: Kisi service ya unit ka current status (running, stopped, enabled, disabled, active, inactive) aur recent log messages dikhata hai.
    *   `Example: systemctl status httpd`
*   `systemctl start `: Specified unit ko start karta hai.
    *   `Example: systemctl start sshd`
*   `systemctl stop `: Specified unit ko gracefully stop karta hai.
    *   `Example: systemctl stop firewalld`
*   `systemctl restart `: Unit ko pehle stop karta hai, phir start karta hai. Agar service mein configuration changes kiye hain toh use apply karne ke liye use hota hai.
    *   `Example: systemctl restart nginx`
*   `systemctl reload `: Agar service hot-reloading support karti hai, toh sirf configuration files ko reload karta hai bina service ko poora restart kiye. Isse service disruption kam hota hai.
    *   `Example: systemctl reload httpd`
*   `systemctl enable `: Unit ko system boot hone par automatically start hone ke liye configure karta hai (symbolic link create karta hai `/etc/systemd/system/multi-user.target.wants/` mein).
    *   `Example: systemctl enable chronyd`
*   `systemctl disable `: Unit ko boot time par auto-start hone se rokta hai (symbolic link remove karta hai).
    *   `Example: systemctl disable postfix`
*   `systemctl is-active `: Check karta hai ki unit active (running) hai ya nahi. Output 'active' ya 'inactive' hoga.
    *   `Example: systemctl is-active mariadb`
*   `systemctl is-enabled `: Check karta hai ki unit boot time par enable hai ya nahi. Output 'enabled' ya 'disabled' hoga.
    *   `Example: systemctl is-enabled sshd`
*   `systemctl list-units`: Saare currently loaded units (active/inactive) ko list karta hai.
    *   `Example: systemctl list-units --type=service` (only services dekhne ke liye)
*   `systemctl list-unit-files`: Saare installed unit files aur unka enabled/disabled status dikhata hai. Isse aap dekh sakte hain kaunsi services boot par chalengi.
    *   `Example: systemctl list-unit-files --state=enabled`
*   `systemctl get-default`: Default system target (runlevel) batata hai (e.g., `graphical.target` or `multi-user.target`).
*   `systemctl set-default `: Default target change karta hai.
    *   `Example: systemctl set-default multi-user.target` (CLI mode mein boot hone ke liye)
*   `systemctl isolate `: System ko specified target mein switch karta hai. Jaise `init 3` ya `init 5` SysVinit mein.
    *   `Example: systemctl isolate rescue.target`
*   `systemctl poweroff`, `systemctl reboot`, `systemctl halt`: System ko shut down, restart, ya halt karne ke liye use hote hain.
*   `systemctl daemon-reload`: Systemd ko instructs karta hai ki usne naye unit files detect kiye hain ya existing unit files mein changes huye hain. **Yeh bahut important command hai jab aap apni custom unit files banate ya edit karte hain.**

#### 2.2 `journalctl` Commands

*   `journalctl`: Poore journal logs ko display karta hai, oldest entries se latest tak. `less` ki tarah output hota hai.
*   `journalctl -f`: Logs ko real-time mein follow karta hai (tail -f ki tarah). Jab naye logs aate hain toh screen par automatically dikhte hain.
*   `journalctl -u `: Specified unit (service) ke logs dikhata hai.
    *   `Example: journalctl -u httpd`
*   `journalctl --since "YY-MM-DD HH:MM:SS" --until "YY-MM-DD HH:MM:SS"`: Specific time range ke logs filter karta hai. Relative times bhi use kar sakte hain jaise `--since "1 hour ago"`, `--since "yesterday"`, `--until "now"`.
    *   `Example: journalctl --since "2023-01-15 10:00:00" --until "2023-01-15 10:30:00"`
    *   `Example: journalctl -u sshd --since "30 minutes ago"`
*   `journalctl -k`: Sirf kernel messages (dmesg ki tarah) dikhata hai.
*   `journalctl -p `: Logs ko priority level ke hisaab se filter karta hai. Priorities hain: `emerg`, `alert`, `crit`, `err`, `warning`, `notice`, `info`, `debug`.
    *   `Example: journalctl -p err -b` (current boot mein sirf error messages)
*   `journalctl _PID=`: Specific process ID (PID) se related logs dikhata hai.
    *   `Example: journalctl _PID=1234`
*   `journalctl --disk-usage`: Journal logs ka disk par kitna space use ho raha hai, woh batata hai.
*   `journalctl --vacuum-size=1G`: Journal files ko truncate karta hai, total size 1GB se zyada na ho.
*   `journalctl --vacuum-time=1week`: 1 week se zyada purane journal entries ko delete karta hai.
*   `journalctl --list-boots`: Available boot sessions ki list dikhata hai.
*   `journalctl -b `: Specific boot session ke logs dikhata hai. `0` current boot, `-1` previous boot, `-2` usse pehle ki boot, etc.
    *   `Example: journalctl -b -1` (last boot ke logs dekhne ke liye)
*   `journalctl -e`: Show the latest entries, and if a pager is used, scroll to the end.

---

### 3. Step-by-Step Examples

Chaliye kuch practical examples dekhte hain:

#### Example 1: HTTPD Web Server Management

Hum Apache HTTP Server (`httpd`) ko manage karenge. Agar aapke system par installed nahi hai toh pehle install kar lein: `sudo dnf install httpd -y`

1.  **Check HTTPD service status:**
    ```bash
    sudo systemctl status httpd
    ```
    (Output mein "inactive (dead)" ya "disabled" dikhega agar pehle se start nahi hai)

2.  **Start HTTPD service:**
    ```bash
    sudo systemctl start httpd
    ```

3.  **Verify service status again:**
    ```bash
    sudo systemctl status httpd
    ```
    (Ab output mein "active (running)" dikhna chahiye)

4.  **Verify web server accessibility:**
    Apne browser mein `http://localhost` type karein ya `curl` command use karein:
    ```bash
    curl http://localhost
    ```
    (Agar Apache ka default page dikhta hai, toh service successfully chal rahi hai)

5.  **Enable HTTPD service to start on boot:**
    ```bash
    sudo systemctl enable httpd
    ```
    (Isse agar system reboot hoga toh `httpd` automatically start ho jayega)

6.  **Simulate a configuration change and reload:**
    Man lijiye aapne `/etc/httpd/conf/httpd.conf` mein koi configuration change kiya. Changes apply karne ke liye `reload` ya `restart` use kar sakte hain.
    `reload` preferred hai agar service support karti hai, kyunki isse service downtime nahi hota.
    ```bash
    sudo systemctl reload httpd
    ```
    Agar service `reload` support nahi karti, ya aapko pura process clean start karna hai, toh `restart` use karein:
    ```bash
    sudo systemctl restart httpd
    ```

7.  **Stop HTTPD service:**
    ```bash
    sudo systemctl stop httpd
    ```

8.  **Disable HTTPD service from starting on boot:**
    ```bash
    sudo systemctl disable httpd
    ```

#### Example 2: Analyzing Logs for a Failed Service

Hum ek dummy service banayenge jo fail ho jaati hai, aur phir `journalctl` se uske logs analyze karenge.

1.  **Create a dummy failing service script:**
    ```bash
    echo -e '#!/bin/bash\nexit 1' | sudo tee /usr/local/bin/myfailingservice.sh
    sudo chmod +x /usr/local/bin/myfailingservice.sh
    ```
    (Yeh script immediately `exit 1` kar degi, indicating a failure)

2.  **Create a Systemd unit file for the failing service:**
    ```bash
    sudo tee /etc/systemd/system/myfailingservice.service < nginx`
    `sudo systemctl  nginx`

3.  **Verify karein ki Nginx boot hone par automatically start ho.**
    `sudo systemctl  nginx`
    `sudo systemctl  nginx`

4.  **Ek simple HTML file `/usr/share/nginx/html/index.html` mein create karein** (default Nginx web root). Usme kuch text likhein, jaise "Hello from my Nginx Server!".
    `echo "
Hello from my Nginx Server!
" | sudo tee /usr/share/nginx/html/index.html`

5.  **Nginx configuration ko reload karein** (service restart kiye bina).
    `sudo systemctl  nginx`

6.  **Browser ya `curl` se verify karein** ki aapka naya HTML content dikh raha hai.
    `curl http://localhost`

7.  **Nginx service ko stop karein aur disable karein.**
    `sudo systemctl  nginx`
    `sudo systemctl  nginx`

8.  **Nginx ke logs check karein** pichle 10 minutes ke liye.
    `journalctl -u nginx --since "10 minutes ago"`

#### Task 2: Custom Timer Service Creation

Hum ek simple script banayenge jo har 1 minute mein ek message log karega, aur use Systemd timer unit se manage karenge.

1.  **Ek simple script create karein:**
    ```bash
    sudo mkdir -p /opt/my_app
    sudo tee /opt/my_app/mylogger.sh <> /tmp/my_timer_log.txt
    EOF
    sudo chmod +x /opt/my_app/mylogger.sh
    ```

2.  **Ek `.service` unit file create karein (`/etc/systemd/system/my_timer_app.service`):**
    ```bash
    sudo tee /etc/systemd/system/my_timer_app.service <`. Yeh `/etc/systemd/system/.d/` directory mein ek override file banata hai.
*   **`journalctl -e`:** Agar aapko hamesha latest logs dekhne hain, toh `journalctl` ke saath `-e` flag use karein. Yeh pager ko end tak scroll kar dega.
*   **Persistent vs. Volatile Journal:** By default, journal logs reboot ke baad clear ho jate hain (volatile). Agar aapko logs reboot ke baad bhi store karne hain, toh `/etc/systemd/journald.conf` mein `Storage=persistent` set karein. Isse logs `/var/log/journal/` mein store honge.
*   **Boot Time Analysis:** `systemd-analyze` command boot process ko analyze karne mein help karta hai:
    *   `systemd-analyze`: Total boot time dikhata hai.
    *   `systemd-analyze blame`: Dikhta hai ki kaunsi service ne boot hone mein kitna time liya.
    *   `systemd-analyze critical-chain`: Critical boot path dikhata hai.
*   **Understanding Unit File Directives:** RHCE level par, unit files banana aur troubleshoot karna aana chahiye. Important sections hain `[Unit]`, `[Service]`, `[Install]`. Directives jaise `Description`, `After`, `Requires`, `Wants`, `ExecStart`, `Type`, `Restart`, `WantedBy` etc. ko samajhna zaroori hai.

#### 5.2 Common Mistakes

*   **`enable` karna bhool jaana:** Service ko `start` karne ka matlab yeh nahi ki woh reboot ke baad bhi chalti rahegi. `enable` karna zaroori hai agar aap service ko boot par automatic start karna chahte hain.
*   **`daemon-reload` na karna:** Naye unit files add karne ya modify karne ke baad `systemctl daemon-reload` na karne se Systemd un changes ko apply nahi karta.
*   **`reload` aur `restart` mein confusion:** Agar service `reload` support karti hai, toh hamesha `reload` ko prefer karein downtime avoid karne ke liye. Bina soche samjhe `restart` karne se unnecessary service interruptions ho sakte hain.
*   **`sudo` ka use na karna:** `systemctl` commands jo system state change karte hain (start, stop, enable, disable, set-default, etc.) ya sensitive logs access karte hain, unke liye root privileges (`sudo`) ki zaroorat hoti hai.
*   **`journalctl` ko ignore karna:** Jab koi service fail hoti hai ya expectedly behave nahi karti, toh `systemctl status` ke baad `journalctl -u ` se detailed logs check karna hamesha pehla troubleshooting step hona chahiye.
*   **Default System Unit Files ko edit karna:** Never modify files directly in `/usr/lib/systemd/system/`. Hamesha `/etc/systemd/system/` mein override files banayein.

---

Umeed hai yeh comprehensive lesson aapko Systemd basics ko samajhne mein aur `systemctl`, `journalctl` commands ko effectively use karne mein madad karega. Yeh RHCSA aur RHCE dono ke liye foundation hai. Keep practicing! All the best!
