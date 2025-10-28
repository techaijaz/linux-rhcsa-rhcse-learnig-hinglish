Day 17: Service management (start, stop, enable, mask)
---
Namaste dosto! Aaj hum Linux ke ek bahut hi important aur fundamental topic par baat karenge: **Service Management**. RHCSA aur RHCE exam ke liye hi nahi, real-world scenario mein bhi yeh topic aapki daily sysadmin life ka backbone hai. `systemd` ne service management ko completely redefine kar diya hai, aur hum `systemctl` command ke through iski gehrayion ko samjhenge.

Chaliye, shuru karte hain!

---

## RHCSA + RHCE Training Lesson: Service Management (start, stop, enable, mask)

### 1. Concept Explanation (Hinglish)

Sabse pehle, yeh samajhte hain ki "service" hota kya hai aur hum use manage kyu karte hain.

*   **Service Kya Hai?**
    *   Socho, aapka server ek bade office ki tarah hai. Is office mein bahut saare employees (processes) kaam karte hain. Kuch employees (jaise SSH daemon) door se log in karne mein help karte hain, kuch employees (jaise Web server - Apache/Nginx) website dikhane ka kaam karte hain, aur kuch employees (jaise database server) data store karte hain.
    *   Ye jo employees (programs) background mein continuously chalte rehte hain, aur kuch specific tasks perform karte hain, unhe hum **services** ya **daemons** kehte hain.
    *   **Examples:** `sshd` (SSH service), `httpd` (Apache web server service), `nginx` (Nginx web server service), `firewalld` (firewall service), `mariadb` (MariaDB database service).

*   **`systemd` Aur `systemctl` Ka Role:**
    *   Purane Linux systems mein services ko manage karne ke liye `SysVinit` ya `Upstart` jaise systems the. Lekin aajkal ki modern Linux distributions (jaise RHEL, CentOS, Fedora, Ubuntu kaafi hadd tak) **`systemd`** use karti hain.
    *   **`systemd`** kya hai? Yeh aapke Linux system ka "init system" aur "service manager" hai. Simple terms mein, yeh woh pehla process hai jo boot hone par start hota hai (`PID 1`), aur phir baaki saare processes aur services ko start, stop, aur manage karta hai. Yeh aapke system ko efficient aur fast banane mein help karta hai.
    *   **`systemctl`** kya hai? `systemctl` woh command-line utility hai jiska use karke hum `systemd` se interact karte hain. Yani, services ko start, stop, enable, disable, status check karne ke liye hum `systemctl` command ka istemal karte hain.

*   **Service Ke Different States (Avasthayen):**
    *   **Running/Active:** Matlab service currently chal rahi hai aur apna kaam kar rahi hai.
    *   **Stopped/Inactive:** Matlab service currently chal nahi rahi hai.
    *   **Enabled:** Matlab service boot hone par automatically start ho jayegi. Yeh state persistent hoti hai, yani reboot ke baad bhi service auto-start hogi.
    *   **Disabled:** Matlab service boot hone par automatically start nahi hogi. Aap ise manually start kar sakte hain, lekin reboot ke baad yeh phir se band ho jayegi jab tak aap ise manually start na karein.
    *   **Masked:** Yeh ek special aur powerful state hai. Agar aap kisi service ko "mask" kar dete hain, toh woh permanently disable ho jaati hai. Koi bhi user, yahan tak ki root user bhi, us service ko manually start ya enable nahi kar payega, jab tak use "unmask" na kiya jaye. Socho, jaise kisi ne us service ka switch permanently off kar diya ho, aur us switch ko chune ki bhi ijazat na ho.
    *   **Loaded:** Matlab `systemd` ne service ki unit definition file ko read kar liya hai. Har service ki ek unit file hoti hai (jaise `sshd.service`), jo batati hai ki service kaise start hogi, kya dependencies hain, etc.

---

### 2. Key Linux Commands (Hinglish)

Chaliye, ab dekhte hain `systemctl` ke sabse zaroori commands aur unki explanation. Remember, in commands ko run karne ke liye aapko `sudo` (root privileges) ki zaroorat padegi, services ko manage karne ke liye.

*   **`systemctl status `**
    *   **Kya karta hai:** Yeh command kisi bhi service ka current status dikhata hai. Jaise, service chal rahi hai ya nahi (`Active: active (running)`), kab se chal rahi hai, uski main process ID (PID) kya hai, aur uske recent log entries bhi.
    *   **Explanation:** "Yeh service ka heartbeat check karta hai. Sabse pehla step yahi hota hai kisi bhi service ko troubleshoot karte waqt ya uski halat janne ke liye."
    *   **Example:** `sudo systemctl status sshd`

*   **`systemctl start `**
    *   **Kya karta hai:** Ek specified service ko start karta hai.
    *   **Explanation:** "Agar service band hai, toh use abhi shuru kar do."
    *   **Example:** `sudo systemctl start httpd`

*   **`systemctl stop `**
    *   **Kya karta hai:** Ek specified service ko gracefully stop karta hai.
    *   **Explanation:** "Service ko shanti se band kar do."
    *   **Example:** `sudo systemctl stop httpd`

*   **`systemctl restart `**
    *   **Kya karta hai:** Yeh command pehle service ko stop karta hai, aur phir use dobara start karta hai. Agar service chal nahi rahi thi, toh yeh sirf use start kar dega.
    *   **Explanation:** "Service ko ek baar band karke phir se chalao. Aksar configuration changes ke baad use karte hain."
    *   **Example:** `sudo systemctl restart nginx`

*   **`systemctl reload `**
    *   **Kya karta hai:** Kuch services reload feature support karti hain. Isse service bina fully stop hue apni configuration files ko dobara padh leti hai. Yeh restart se faster hota hai aur service downtime avoid karta hai.
    *   **Explanation:** "Agar sirf configuration file mein changes kiye hain (jaise web server ki port number), toh service ko poora band karke chalane ki bajaye, use sirf config files reload karne ke liye bolo. Isse service chalti rehti hai." **(RHCE Tip: Har service `reload` support nahi karti. Agar support nahi karti toh `restart` hi chalana padega.)**
    *   **Example:** `sudo systemctl reload httpd`

*   **`systemctl enable `**
    *   **Kya karta hai:** Service ko system boot par automatically start hone ke liye configure karta hai. Yeh `systemd` ke andar symbolic link create karta hai.
    *   **Explanation:** "Service ko aisa set kar do ki jab bhi system boot ho, yeh automatically chal jaaye. Yeh state persistent hoti hai."
    *   **Example:** `sudo systemctl enable firewalld`

*   **`systemctl disable `**
    *   **Kya karta hai:** Service ko system boot par automatically start hone se rokta hai. Yeh `enable` command dwara banaye gaye symbolic links ko remove karta hai.
    *   **Explanation:** "Service ko boot pe auto-start hone se rok do. Agar yeh pehle se 'enabled' thi, toh ab nahi hogi. Lekin manually aap ise `start` kar sakte ho."
    *   **Example:** `sudo systemctl disable firewalld`

*   **`systemctl mask `**
    *   **Kya karta hai:** Yeh service ko permanently disable aur lock kar deta hai. Iska matlab hai ki service na toh manually start ho sakti hai, na hi automatically enable ho sakti hai. Yeh service ki unit file ko `/dev/null` se symbolic link kar deta hai.
    *   **Explanation:** "Yeh ek bahut hi powerful command hai. Agar aap kisi service ko absolutely nahi chalana chahte, aur chahte hain ki galti se bhi koi use start na kar paaye, toh use 'mask' kar do. Jaise, `cups` (printing service) agar aapke server par printer nahi hai."
    *   **Example:** `sudo systemctl mask cups`

*   **`systemctl unmask `**
    *   **Kya karta hai:** Mask kiye gaye service ko uski normal state par wapas lata hai, jahan use manually start ya enable kiya ja sake.
    *   **Explanation:** "Agar aapne kisi service ko 'mask' kar diya tha aur ab use wapas normal banana chahte hain, toh 'unmask' karo."
    *   **Example:** `sudo systemctl unmask cups`

*   **Other Useful Commands (RHCE Level):**
    *   **`systemctl is-active `:** Check karta hai ki service active hai ya nahi. Output `active` ya `inactive` hota hai.
    *   **`systemctl is-enabled `:** Check karta hai ki service enabled hai ya nahi. Output `enabled`, `disabled`, `static`, ya `masked` hota hai.
    *   **`systemctl list-units --type=service`:** Sabhi loaded services ko list karta hai. `--all` flag ke saath inactive services bhi dikhti hain.
    *   **`systemctl list-unit-files --type=service`:** Sabhi installed service unit files ko unke "enabled" status ke saath dikhata hai.
    *   **`systemctl get-default`:** Default target unit batata hai (e.g., `multi-user.target` for CLI, `graphical.target` for GUI). RHCE mein targets bahut important hote hain.
    *   **`systemctl set-default `:** Default boot target change karta hai. **(Caution: Galat target set karne se system boot issues aa sakte hain.)**

---

### 3. Step-by-Step Examples (Real-World Scenarios)

Chaliye kuch practical examples dekhte hain. Maan lijiye aapke paas ek RHEL server hai.

**Scenario 1: SSH Service Management (Basic RHCSA)**

1.  **SSH service ka status dekho:**
    ```bash
    sudo systemctl status sshd
    ```
    *   Output mein aapko dikhega ki service `active (running)` hai ya `inactive (dead)`.

2.  **SSH service ko stop karo:**
    ```bash
    sudo systemctl stop sshd
    ```
    *   **Warning:** Agar aap current SSH session se ye command chala rahe hain, toh aapka session disconnect ho jayega! Production server par carefully karein.

3.  **Dobara status check karo:**
    ```bash
    sudo systemctl status sshd
    ```
    *   Ab dikhega `Active: inactive (dead)`.

4.  **SSH service ko start karo:**
    ```bash
    sudo systemctl start sshd
    ```

5.  **Confirm karo ki service chal rahi hai:**
    ```bash
    sudo systemctl status sshd
    ```
    *   Ab `Active: active (running)` dikhega.

6.  **Ensure karo ki SSH boot par auto-start ho:**
    ```bash
    sudo systemctl enable sshd
    ```
    *   Usually, `sshd` already enabled hota hai. Output mein "Created symlink..." dikh sakta hai.

7.  **SSH ko boot par auto-start hone se roko (Agar kabhi zaroorat pade):**
    ```bash
    sudo systemctl disable sshd
    ```
    *   Output mein "Removed symlink..." dikhega.

**Scenario 2: Nginx Web Server Configuration Change (RHCE Focus: reload vs. restart)**

Maan lijiye aapne Nginx install kiya hai (`sudo dnf install nginx -y`) aur use start/enable kiya hai.

1.  **Nginx service ka status dekho:**
    ```bash
    sudo systemctl status nginx
    ```
    *   Make sure it's `active (running)`.

2.  **Nginx ki default config file edit karo:**
    ```bash
    sudo vi /etc/nginx/nginx.conf
    ```
    *   `http` block ke andar, koi chhota sa change karo, jaise `server_tokens off;` add karna ya `worker_processes` ki value badhana. Save aur exit karo.

3.  **Reload Nginx service:**
    ```bash
    sudo systemctl reload nginx
    ```
    *   Yeh command Nginx ko config changes apply karne ke liye bolta hai bina service ko poori tarah se restart kiye. Isse aapki website users ke liye continue available rehti hai.
    *   Agar koi syntax error hai config file mein, toh `reload` fail ho jayega. Use `nginx -t` to check config syntax beforehand.

4.  **Verify status:**
    ```bash
    sudo systemctl status nginx
    ```
    *   Service abhi bhi `active (running)` dikhegi, uptime bhi wahi rahega, but changes applied ho chuke honge.

5.  **Ab Nginx ko restart karke dekho (Downtime hoga):**
    ```bash
    sudo systemctl restart nginx
    ```
    *   Is case mein Nginx kuch milliseconds ke liye down jayega. Agar aap `status` check karenge, toh aapko naya uptime dikhega, indicating ki service reboot hui hai.

**Scenario 3: Masking a Service (RHCE - Security/Policy)**

Maan lijiye aapko `cups` (Common Unix Printing System) service ki bilkul zaroorat nahi hai, aur aap chahte hain ki galti se bhi koi ise chala na paaye.

1.  **`cups` service ka status dekho:**
    ```bash
    sudo systemctl status cups
    ```
    *   Agar yeh `active` hai, toh pehle use `stop` kar do: `sudo systemctl stop cups`.

2.  **`cups` service ko mask karo:**
    ```bash
    sudo systemctl mask cups
    ```
    *   Output: `Created symlink /etc/systemd/system/cups.service -> /dev/null.`
    *   Iska matlab hai `cups.service` ab `/dev/null` ko point kar rahi hai, jiska matlab hai ki yeh service effectively "delete" ho chuki hai `systemd` ke liye.

3.  **Masked service ko start karne ki koshish karo:**
    ```bash
    sudo systemctl start cups
    ```
    *   Output: `Failed to start cups.service: Unit cups.service is masked.`
    *   Dekha? Ab koi ise start nahi kar payega.

4.  **Masked service ko enable karne ki koshish karo:**
    ```bash
    sudo systemctl enable cups
    ```
    *   Output: `Failed to enable unit: Unit file cups.service is masked.`
    *   Yeh enable bhi nahi ho sakti.

5.  **`cups` service ko unmask karo:**
    ```bash
    sudo systemctl unmask cups
    ```
    *   Output: `Removed /etc/systemd/system/cups.service.`
    *   Ab `cups` service wapas normal state mein aa gayi hai.

6.  **Ab aap ise manually start kar sakte hain:**
    ```bash
    sudo systemctl start cups
    sudo systemctl status cups
    ```

---

### 4. Practice Tasks (Lab Tasks)

Ab aapki baari hai! In tasks ko apne Linux VM (Red Hat based, jaise CentOS Stream, Fedora, ya RHEL trial) par try karo.

1.  **HTTPD Service ko Manage Karo (RHCSA Level):**
    *   `httpd` package install karo (`sudo dnf install httpd -y`).
    *   `httpd` service ka status check karo.
    *   `httpd` service ko start karo.
    *   Confirm karo ki service chal rahi hai.
    *   Apne browser mein `http://` par jaakar default Apache page dekho.
    *   `httpd` service ko enable karo, taaki yeh boot par auto-start ho.
    *   `httpd` service ko stop karo.
    *   Confirm karo ki service band ho gayi hai.
    *   **Challenge:** `httpd` service ko disable karo aur phir restart karke dekho ki woh auto-start hoti hai ya nahi.

2.  **Chronyd Service ka status aur enable/disable (RHCSA Level):**
    *   `chronyd` service (time synchronization ke liye) ka status dekho.
    *   Check karo ki yeh `enabled` hai ya `disabled`.
    *   Agar yeh `enabled` hai, toh ise `disabled` karo, aur phir `is-enabled` command se verify karo.
    *   Dobara ise `enabled` karo.

3.  **NFS Server Service (RHCE Level):**
    *   `nfs-server` package install karo (`sudo dnf install nfs-utils -y`).
    *   `nfs-server` service ka status dekho.
    *   Ise `enable` aur `start` karo.
    *   Confirm karo ki yeh `active (running)` aur `enabled` hai.
    *   `nfs-server` service ko `mask` karo.
    *   Koshish karo ise `start` karne ki, aur error dekho.
    *   Ise `unmask` karo aur phir se `enable` aur `start` karo.

4.  **Custom Dummy Service Banana (RHCE Level):**
    *   Ek simple service unit file banao (`/etc/systemd/system/myfirst.service`).
    *   Content:
        ```ini
        [Unit]
        Description=My First Custom Service
        After=network.target

        [Service]
        ExecStart=/bin/bash -c "echo 'Hello from MyFirstService' >> /tmp/myfirst.log"
        Type=oneshot
        RemainAfterExit=yes

        [Install]
        WantedBy=multi-user.target
        ```
    *   `sudo systemctl daemon-reload` chalao (yeh `systemd` ko batata hai ki ek nayi unit file hai).
    *   `sudo systemctl start myfirst.service` chalao.
    *   `cat /tmp/myfirst.log` karke dekho, kya output aata hai?
    *   `sudo systemctl status myfirst.service` dekho.
    *   Ise `enable` karo aur `reboot` karke dekho ki `/tmp/myfirst.log` mein naya entry aata hai ya nahi.

---

### 5. Pro Tips / Common Mistakes (Hinglish)

Yeh kuch important tips aur galtiyan hain jinse aap bach sakte hain:

*   **`sudo` is King!** Hamesha yaad rakhna, services ko manage karne ke liye root privileges (`sudo`) ki zaroorat padegi. Bina `sudo` ke `systemctl` commands aksar `Failed to start/stop/...` ka error denge.
*   **Pehle `status` Check Karo:** Koi bhi action (start, stop, restart) lene se pehle, service ka `status` zaroor check karo. Isse aapko current situation pata chalegi aur unexpected behavior se bachoge. "Hamesha service ki naadi check karo pehle!"
*   **`reload` vs. `restart`:**
    *   `reload` tab use karo jab sirf configuration files change hui hon (jaise Nginx ya Apache ki config). Isse service mein downtime nahi aata.
    *   `restart` tab use karo jab service mein koi bada update kiya ho, ya `reload` option available na ho. Isme brief downtime hoga.
*   **Masking Wisely:** `mask` command bahut powerful hai. Iska use bahut soch samajh kar karna, aur sirf tab jab aap sure ho ki woh service kabhi bhi nahi chalni chahiye. Galat service ko mask karne se system boot issues ya critical functionality break ho sakti hai. "Jaha surgical strike ki zaroorat ho, wahi mask use karna."
*   **Logs Dekho (`journalctl`):** Agar koi service start nahi ho rahi, ya restart ke baad bhi theek se kaam nahi kar rahi, toh `systemctl status ` ke output mein niche logs dekhna. Agar wahan enough info nahi milti, toh `journalctl -u  -e` command use karo. Yeh service ke detailed logs dikhayega. **(RHCE Focus: `journalctl` bahut important hai debugging ke liye.)**
    *   Example: `sudo journalctl -u httpd.service -e` (last entries dikhayega)
    *   `sudo journalctl -u httpd.service --since "1 hour ago"` (pichle ek ghante ke logs)
*   **Unit File Locations:** Services ki unit files normally `/usr/lib/systemd/system/` (installed packages se aati hain) aur `/etc/systemd/system/` (custom ya override files) mein hoti hain. Jab aap `enable` karte hain, toh symbolic links `/etc/systemd/system/` mein bante hain jo asli unit file ko point karte hain. **(RHCE Focus: Custom services banane ke liye `/etc/systemd/system/` ka use hota hai.)**
*   **Dependencies Samjho:** Kuch services dusri services par depend karti hain. Jaise, ek database service shayad network service ke start hone par depend kare. `systemd` in dependencies ko automatically handle karta hai. `systemctl list-dependencies ` se aap dependencies dekh sakte hain. **(RHCE Focus)**

---

**Conclusion:**

Dosto, service management Linux sysadmin ka ek fundamental skill hai. `systemctl` command aapka sabse acha dost ban jayega. Is lesson mein diye gaye concepts, commands aur practice tasks ko acche se samajhna aur practice karna aapko RHCSA aur RHCE exam ke liye aur real-world challenges ke liye solid foundation dega.

Koi doubt ho toh poochhna! Keep practicing! All the best!
