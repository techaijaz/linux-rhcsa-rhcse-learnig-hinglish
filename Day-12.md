Day 12: SSH setup, key authentication
---
Namaste students! Red Hat Certified System Administrator (RHCSA) aur Red Hat Certified Engineer (RHCE) certification ki journey mein aapka swagat hai. Aaj hum ek bahut hi crucial topic cover karenge: **SSH (Secure Shell) Setup aur Key Authentication**.

Yeh sirf exam ke liye hi nahi, balki real-world server management ke liye bhi foundation hai. Toh chaliye, shuru karte hain!

---

## RHCSA + RHCE Training: SSH Setup aur Key Authentication – Secure Remote Access ki Masterclass!

**Module:** Networking aur Security
**Level:** Intermediate (RHCSA-level basics, RHCE-level advanced security & configuration)
**Duration:** Approx. 90-120 minutes (including practice)

### Introduction (Parichay)

Imagine kijiye aapka server data center mein hai, ya cloud par chal raha hai. Aap usko physically touch nahi kar sakte. Toh access kaise karenge? **SSH** ki madad se!

SSH ek cryptographic network protocol hai jo secure data communication, remote command-line login, aur remote command execution provide karta hai. Simply put, yeh aapko ek remote computer (server) se encrypted tarike se connect karne deta hai, jaise aap uske saamne baithe ho.

**Aaj hum kya seekhenge?** (Learning Objectives)

*   SSH server aur client ko setup karna.
*   Basic SSH connection banana.
*   SSH server configuration file ko manage karna (`sshd_config`).
*   SSH Key-based authentication generate aur deploy karna.
*   Password authentication ko disable karke system ko aur secure banana.
*   `~/.ssh/config` file ka use karke SSH client experience ko optimize karna.

---

### **Section 1: SSH Basics aur Setup (RHCSA Focus)**

**Goal:** SSH server ko install aur configure karna, aur basic connectivity test karna.

#### Step 1: SSH Server Install aur Start karna

Zyadatar RHEL systems mein SSH server (OpenSSH) already install hota hai. Agar nahi hai, toh install kar sakte hain.

**Server Machine par:**

1.  **Check installation:**
    ```bash
    rpm -q openssh-server
    ```
    Agar install hai, toh output mein package name dikhega. Agar nahi, toh:

2.  **Install SSH Server:**
    ```bash
    sudo dnf install openssh-server openssh-clients -y
    ```
    *(`openssh-clients` generally already installed hota hai, par install kar dena safe side ke liye hai)*

3.  **SSH Service Start aur Enable karna:**
    SSH service ka naam `sshd` (SSH Daemon) hota hai. Ise start aur boot par automatically start hone ke liye enable karna zaroori hai.
    ```bash
    sudo systemctl start sshd
    sudo systemctl enable sshd
    ```

4.  **Service Status Verify karna:**
    ```bash
    sudo systemctl status sshd
    ```
    Output mein `Active: active (running)` dikhna chahiye.

#### Step 2: Firewall Rules Configure karna

RHEL mein **firewalld** default firewall hai. SSH port (default 22) ko firewall mein allow karna padega taaki remote connections aa saken.

**Server Machine par:**

1.  **SSH service ko permanent allow karna:**
    ```bash
    sudo firewall-cmd --permanent --add-service=ssh
    ```

2.  **Firewall rules reload karna:**
    ```bash
    sudo firewall-cmd --reload
    ```

3.  **Verify karna:**
    ```bash
    sudo firewall-cmd --list-all | grep ssh
    ```
    Output mein `services: ssh` dikhna chahiye.

#### Step 3: Basic SSH Connection banana

Ab jab server ready hai, toh client machine se connect karke dekhte hain.

**Client Machine par:**

1.  **SSH Client Install karna (agar nahi hai):**
    ```bash
    sudo dnf install openssh-clients -y
    ```

2.  **Server se Connect karna:**
    SSH connect karne ka basic syntax hai: `ssh [username]@[IP_ADDRESS_or_HOSTNAME]`
    Maan lijiye server ka IP address `192.168.1.100` hai aur aap `devops` user se login karna chahte hain.

    ```bash
    ssh devops@192.168.1.100
    ```

    *   **First time connection:** Aapko ek message dikhega "The authenticity of host '192.168.1.100 (192.168.1.100)' can't be established." aur "Are you sure you want to continue connecting (yes/no/[fingerprint])?". Yahan `yes` type karke Enter dabayein. Server ka fingerprint aapke client ke `~/.ssh/known_hosts` file mein save ho jayega.
    *   **Password Prompt:** Aapko `devops` user ka password enter karna hoga.

    Agar sab theek raha, toh aap server ke command prompt par login ho jayenge!
    Login ke baad aap `hostname` ya `ip a` jaise commands chala kar verify kar sakte hain.

    `exit` type karke aap server session se bahar aa sakte hain.

#### Step 4: SSH Server Configuration (sshd_config)

SSH server ki settings `/etc/ssh/sshd_config` file mein hoti hain. Yeh file modify karke aap security aur functionality ko enhance kar sakte hain.

**Server Machine par:**

1.  **Configuration file open karna:**
    ```bash
    sudo vi /etc/ssh/sshd_config
    ```

2.  **Important Settings (RHCSA point of view):**

    *   **`Port 22`**: Default SSH port 22 hai. Security ke liye ise non-standard port par change karna ek common practice hai (e.g., `Port 2222`).
        ```
        #Port 22       <-- uncomment this line and change the port
        Port 2222
        ```
        **Important:** Agar port change karte hain, toh firewall mein naya port bhi open karna padega aur client se connect karte waqt `-p` option use karna hoga (`ssh -p 2222 devops@192.168.1.100`).

    *   **`PermitRootLogin no`**: Root user se direct SSH login karna secure nahi mana jata. Is setting ko `no` karna best practice hai. Agar root access chahiye, toh normal user se login karke `sudo su -` use karein.
        ```
        #PermitRootLogin prohibit-password   <-- Comment/Change this
        PermitRootLogin no
        ```

    *   **`PasswordAuthentication yes`**: Yeh setting password-based authentication ko allow karti hai. Hum isse baad mein `no` karenge jab key-based auth setup ho jayega. Abhi ke liye `yes` hi rehne dein.
        ```
        #PasswordAuthentication yes  <-- Make sure it's uncommented and set to 'yes'
        ```

    *   **`AllowUsers`, `DenyUsers`**: Aap specific users ko SSH access allow ya deny kar sakte hain.
        ```
        AllowUsers devops admin
        ```
        *(Agar yeh line add karte hain, toh sirf `devops` aur `admin` users hi SSH kar payenge. Baki sab users ke liye access denied ho jayega.)*

3.  **Changes apply karna:** Har change ke baad `sshd` service ko restart karna zaroori hai.
    ```bash
    sudo systemctl restart sshd
    ```

    **Firewall change (agar port change kiya hai):**
    Agar aapne port 22 se 2222 kiya hai, toh naya port firewall mein add karna padega aur purana remove karna padega:
    ```bash
    sudo firewall-cmd --permanent --remove-service=ssh
    sudo firewall-cmd --permanent --add-port=2222/tcp
    sudo firewall-cmd --reload
    ```

---

### **Section 2: SSH Key-Based Authentication (RHCE Focus)**

**Goal:** SSH key-based authentication set up karna, jo password-based authentication se zyada secure aur convenient hai. Password authentication ko disable karna.

#### Why Key-Based Authentication?

*   **Security:** Keys passwords se zyada lambe aur complex hote hain, jo brute-force attacks se bachne mein madad karte hain.
*   **Convenience:** Ek baar setup ho jane ke baad, aapko baar-baar password type nahi karna padta.
*   **Automation:** Scripts aur automated tasks ke liye keys ideal hote hain.

SSH key authentication mein do keys hoti hain:
1.  **Private Key:** Yeh aapki client machine par secret rakhi jati hai (jaise aapka ghar ka chabi).
2.  **Public Key:** Yeh server par rakhi jati hai (jaise ghar ka tala).

Jab aap connect karte hain, toh server public key se verify karta hai ki aapke paas matching private key hai ya nahi.

#### Step 1: SSH Key Pair Generate karna

**Client Machine par:**

1.  **Key pair generate karna:**
    ```bash
    ssh-keygen
    ```
    *   **`Enter file in which to save the key (/home/youruser/.ssh/id_rsa):`**: Default location `~/.ssh/id_rsa` hai. Ise enter press karke accept karein.
    *   **`Enter passphrase (empty for no passphrase):`**: Yeh aapke private key ke liye ek password hai. Agar aapko extra security chahiye toh passphrase set kar sakte hain. Har baar jab aap private key use karenge, toh yeh passphrase enter karna hoga. Automation ke liye empty (no passphrase) rakhna common hai, lekin security ke liye passphrase recommend kiya jata hai. **Hum yahan passphrase set karenge** takay aapko iska concept samajh aaye. Aap `redhat123` jaisa kuch set kar sakte hain.

2.  **Generated keys ko check karna:**
    Keys `~/.ssh/` directory mein save honge.
    ```bash
    ls -l ~/.ssh/
    ```
    Aapko `id_rsa` (private key) aur `id_rsa.pub` (public key) dikhenge.
    **`id_rsa` file ko kisi ke saath share na karein!**

#### Step 2: Public Key Server par Copy karna

Apni public key (`id_rsa.pub`) ko server par copy karna hoga. Iske do tareeke hain:

**Method A: `ssh-copy-id` Utility (Recommended & Easiest)**

`ssh-copy-id` utility automatically public key ko server ke `~/.ssh/authorized_keys` file mein add kar deta hai.

**Client Machine par:**

```bash
ssh-copy-id devops@192.168.1.100
```
*(Agar aapne server par port change kiya hai, toh `-p` option use karein: `ssh-copy-id -p 2222 devops@192.168.1.100`)*

*   Aapko `devops` user ka password enter karna hoga (yeh last time hoga!).
*   Output mein "Number of keys added: 1" dikhna chahiye.

**Method B: Manual Copy (Understanding the underlying process)**

Agar `ssh-copy-id` available nahi hai ya aap manual process samajhna chahte hain:

**Client Machine par:**

1.  **Apni public key ka content dekhein:**
    ```bash
    cat ~/.ssh/id_rsa.pub
    ```
    Is output ko copy kar lein (e.g., `ssh-rsa AAAAB3NzaC... your_user@your_client_hostname`).

**Server Machine par (password se login karke):**

1.  **`.ssh` directory create karein (agar nahi hai):**
    ```bash
    mkdir -p ~/.ssh
    ```

2.  **Permissions set karein (bahut important!):**
    ```bash
    chmod 700 ~/.ssh
    ```
    `~/.ssh` directory ki permissions `700` (read, write, execute by owner only) honi chahiye, varna SSH server key ko ignore kar dega.

3.  **Public key ko `authorized_keys` file mein add karein:**
    ```bash
    echo "PASTE_YOUR_PUBLIC_KEY_CONTENT_HERE" >> ~/.ssh/authorized_keys
    ```
    **Example:** `echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCu7fN... youruser@yourclient" >> ~/.ssh/authorized_keys`

4.  **`authorized_keys` file ki permissions set karein:**
    ```bash
    chmod 600 ~/.ssh/authorized_keys
    ```
    `authorized_keys` file ki permissions `600` (read, write by owner only) honi chahiye.

#### Step 3: Key-Based Login Test karna

Ab, client machine se phir se connect karein.

**Client Machine par:**

```bash
ssh devops@192.168.1.100
```
*(Agar port change kiya hai, toh: `ssh -p 2222 devops@192.168.1.100`)*

*   Is baar, aapko `devops` user ka password nahi, balki **apne private key ka passphrase** enter karna hoga (agar set kiya hai toh).
*   Agar aapne passphrase empty chhoda tha, toh aap bina kisi password/passphrase ke seedhe login ho jayenge!

Congratualtions! Aapne key-based authentication successfully set up kar liya hai.

#### Step 4: Password Authentication Disable karna (RHCE Security Standard)

Key-based authentication set up hone ke baad, password authentication ko disable karna ek critical security step hai. Isse aapka server brute-force attacks se bach jata hai.

**Server Machine par:**

1.  **`sshd_config` file open karein:**
    ```bash
    sudo vi /etc/ssh/sshd_config
    ```

2.  **Following lines ko `no` par set karein:**
    ```
    PasswordAuthentication no
    ChallengeResponseAuthentication no
    UsePAM no
    ```
    *   `PasswordAuthentication no`: Password-based login disable karta hai.
    *   `ChallengeResponseAuthentication no`: Interactive authentication methods (like keyboard-interactive) ko disable karta hai.
    *   `UsePAM no`: Pluggable Authentication Modules (PAM) ko SSH ke liye disable karta hai. Yeh password authentication ko bypass karne ke attempts ko bhi rokta hai.

    **HAMESHA SURE RAHEIN KI KEY-BASED LOGIN KAAM KAR RAHA HAI PASSWORD AUTHENTICATION DISABLE KARNE SE PEHLE! WARNA AAP SERVER SE LOCK OUT HO SAKTE HAIN.**

3.  **SSH service restart karein:**
    ```bash
    sudo systemctl restart sshd
    ```

4.  **Verify karein:** Ab client se password attempt karke dekhein.
    ```bash
    ssh devops@192.168.1.100
    ```
    Agar password authentication disabled hai, toh aapko password prompt nahi aayega, aur agar key-based login bhi fail hua toh "Permission denied" error aayega. Key-based login hi kaam karna chahiye.

---

### **Section 3: Advanced SSH Client Configuration (`~/.ssh/config`) (RHCE Enhancement)**

**Goal:** SSH client configuration file ka use karke connections ko simplify aur automate karna.

Har baar `-p`, user, aur IP address type karna tedious ho sakta hai. `~/.ssh/config` file aapko custom configurations define karne deta hai.

**Client Machine par:**

1.  **`~/.ssh` directory mein `config` file create/open karein:**
    ```bash
    vi ~/.ssh/config
    ```
    *(Agar `~/.ssh` nahi hai toh `mkdir ~/.ssh` pehle kar lein, aur `chmod 700 ~/.ssh` bhi karein.)*

2.  **Entry add karein:**
    ```
    Host myserver                 # Yeh ek short alias hai jo aap define karte hain
      Hostname 192.168.1.100      # Server ka actual IP address ya hostname
      User devops                 # Server par login karne wala user
      Port 2222                   # Agar aapne non-standard port use kiya hai
      IdentityFile ~/.ssh/id_rsa  # Aapki private key ka path (agar default nahi hai)
      ForwardAgent yes            # Agent forwarding enable karta hai
    ```

3.  **Permissions set karein:**
    ```bash
    chmod 600 ~/.ssh/config
    ```

4.  **Connect karein (using alias):**
    Ab aap simple command se connect kar sakte hain:
    ```bash
    ssh myserver
    ```
    Aapko sirf private key ka passphrase enter karna hoga (agar set kiya hai toh). Kitna convenient hai na?

---

### **Practice Tasks (Ab aapki baari!)**

**Setup:** Aapke paas kam se kam do VMs honi chahiye: ek **Server** aur ek **Client**. Dono mein RHEL OS ho aur network connectivity ho.

---

#### **Task 1: Basic SSH Setup aur Hardening (RHCSA)**

1.  **Server VM par:**
    *   `openssh-server` install karein (agar nahi hai).
    *   `sshd` service ko start aur enable karein.
    *   Firewall mein SSH service allow karein.
    *   `sshd_config` file ko edit karein:
        *   SSH port ko `22` se `2202` par change karein.
        *   `PermitRootLogin` ko `no` par set karein.
        *   `AllowUsers` directive ka use karke sirf `testuser` aur `adminuser` ko SSH access allow karein (pehle yeh users create karein `sudo adduser testuser` etc.).
    *   Firewall mein naya port `2202` allow karein aur purana SSH service rule remove karein.
    *   `sshd` service ko restart karein.

2.  **Client VM par:**
    *   `testuser` se server ke naye port `2202` par SSH connect karne ki koshish karein. Verify karein ki login successful hai.
    *   `root` user se server par SSH connect karne ki koshish karein. Verify karein ki login denied hota hai.
    *   `anotheruser` (jo `AllowUsers` list mein nahi hai) se SSH connect karne ki koshish karein. Verify karein ki login denied hota hai.

---

#### **Task 2: SSH Key-Based Authentication aur Security (RHCE)**

1.  **Client VM par:**
    *   Apne client machine par `ssh-keygen` command se ek naya SSH key pair generate karein. Iss baar, **no passphrase set karein** (empty chhod dein).
    *   Iss naye public key ko `testuser` ke liye Server VM par copy karein `ssh-copy-id` ka use karke (port 2202 use karna na bhulein).
    *   Verify karein ki aap `testuser` se bina password/passphrase ke login kar pa rahe hain.

2.  **Server VM par:**
    *   `sshd_config` file ko edit karein aur `PasswordAuthentication`, `ChallengeResponseAuthentication`, aur `UsePAM` ko `no` par set karein.
    *   `sshd` service ko restart karein.

3.  **Client VM par:**
    *   Phir se `testuser` se server par login karein. Verify karein ki bina password/passphrase ke login ho raha hai.
    *   Ab `adminuser` se login karne ki koshish karein (jiske liye aapne key authentication set nahi ki thi). Kya hota hai? (Expected: "Permission denied (publickey).")

---

#### **Task 3: Client-Side Configuration Optimization (RHCE)**

1.  **Client VM par:**
    *   Apne `~/.ssh/config` file mein `myserver` ke liye ek entry create karein.
    *   Is entry mein `Hostname`, `User`, `Port` (2202), aur `IdentityFile` (aapki naye generate ki gayi key ka path) specify karein.
    *   `ssh myserver` command se connect karke verify karein ki login successful hai aur aapko kam se kam commands type karne pade.

---

### **Summary (Saransh)**

Aaj humne SSH ki power aur flexibility dekhi. Humne basic setup se lekar advanced key-based authentication aur client-side configuration tak sab kuch cover kiya. Yeh skills aapko RHCSA aur RHCE exams ke saath-saath real-world Red Hat environments mein bhi success dilayenge.

**Key Takeaways:**

*   **SSH Setup:** `openssh-server` install karo, `sshd` service manage karo, firewall set karo.
*   **`sshd_config`:** Server-side security settings (Port, PermitRootLogin, AllowUsers, PasswordAuthentication).
*   **Key Authentication:** `ssh-keygen` se keys banao, `ssh-copy-id` se keys deploy karo. Yeh zyada secure aur convenient hai.
*   **Security Best Practice:** Key-based authentication set hone ke baad `PasswordAuthentication no` karo.
*   **`~/.ssh/config`:** Client-side connections ko simplify karne ke liye.

---

### **Next Steps (Aage kya?):**

*   **SSH Agent:** Passphrase ko baar-baar type karne se bachne ke liye `ssh-agent` ka use karna seekhein.
*   **SCP / SFTP:** Files transfer karne ke liye `scp` aur `sftp` ka use karein.
*   **Port Forwarding / Tunneling:** Secure tunnels banana seekhein.
*   **X11 Forwarding:** Remote GUI applications run karna seekhein.

Good luck with your RHCSA/RHCE journey! Keep practicing, aur koi bhi doubt ho toh poochhne se jhijhken nahi.
