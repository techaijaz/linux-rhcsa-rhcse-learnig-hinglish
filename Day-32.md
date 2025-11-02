Day 32: Ansible modules for users, packages, and services
---
Namaste! Main aapka experienced Linux instructor, aur aaj hum RHCSA aur RHCE exam ke liye ek bahut hi crucial aur practical topic mein gehri dive lagayenge: **"Ansible modules for users, packages, and services"**. Yeh woh modules hain jo har system administrator apni daily routine mein use karta hai, aur Ansible ke through inhe automate karna aapki productivity ko next level par le jayega.

---

## 1. Concept Explanation 

Toh, shuru karte hain!

**Ansible Kya Hai?**
Ansible ek open-source automation engine hai jo IT tasks jaise configuration management, application deployment, aur intra-service orchestration ko automate karta hai. Isme aapko koi agent software target servers par install karne ki zaroorat nahi padti (agentless architecture). Yeh sirf SSH ke through communicate karta hai.

**Ye Modules Kyun Important Hain?**
System administration mein teen sabse common tasks hote hain:
1.  **Users Manage Karna:** Naye users banana, unhe groups mein add karna, unke privileges manage karna.
2.  **Packages Manage Karna:** Software install karna, update karna, ya remove karna.
3.  **Services Manage Karna:** Web servers (Apache, Nginx), database servers (MySQL, PostgreSQL), ya anya system services ko start, stop, restart, ya enable/disable karna.

Agar aapke paas 10, 50, ya 100 servers hain, toh in tasks ko manually karna ek nightmare ho sakta hai. Ansible ke dedicated modules in sabhi tasks ko ek single command se, multiple servers par automate karne ki power dete hain.

**Idempotency ka Matlab:**
Ansible ka ek bahut bada fayda hai ki yeh 'idempotent' hai. Iska matlab hai ki agar aap koi task baar-baar run karte ho, toh woh system ko sirf tab change karega jab desired state achieve na hui ho. Agar desired state (jaise "package already installed hai") achieve ho chuki hai, toh woh kuch nahi karega, sirf 'OK' report karega. Isse system ki consistent state maintain rehti hai aur unnecessary changes avoid hote hain.

**Hum Kon Se Modules Dekhenge?**

*   **`user` Module:**
    *   **Purpose:** Operating system users ko create, modify, aur delete karne ke liye.
    *   **Parameters (Kuch Important):** `name`, `state` (present/absent), `uid`, `group`, `groups`, `shell`, `home`, `password`.

*   **`dnf` / `yum` / `apt` Modules (Package Management):**
    *   **Purpose:** Software packages ko install, update, aur remove karne ke liye.
    *   **Note:** RHEL-based systems (Red Hat, CentOS, Fedora) ke liye `dnf` (ya older `yum`) use karte hain, jabki Debian-based systems (Ubuntu, Debian) ke liye `apt` module use karte hain. `package` naam ka ek generic module bhi hai jo OS ke hisaab se sahi package manager choose kar leta hai, but specific modules like `dnf` provide more control. RHCSA/RHCE ke liye, `dnf` sabse zyada relevant hai.
    *   **Parameters (Kuch Important):** `name`, `state` (present/latest/absent), `version`.

*   **`systemd` / `service` Modules:**
    *   **Purpose:** System services ko manage karne ke liye (start, stop, restart, enable/disable at boot).
    *   **Note:** Modern Linux distributions (RHEL 7+, Ubuntu 15.04+) `systemd` use karti hain, toh `systemd` module preferred hai. Older systems ya generic usage ke liye `service` module bhi use ho sakta hai.
    *   **Parameters (Kuch Important):** `name`, `state` (started/stopped/restarted/reloaded), `enabled` (yes/no), `daemon_reload` (yes/no).

---

## 2. Key Linux Commands

Ansible chalane ke baad, yeh commands aapko target system par verify karne mein help karenge ki aapke changes successfully apply hue ya nahi.

*   **Users se Related Commands:**
    *   `id `:
        *   **Explanation :** Yeh command kisi user ki numerical user ID (UID), primary group ID (GID), aur uske sabhi group memberships ko display karta hai. Isse aap user ki existence aur group affiliations ko confirm kar sakte hain.
        *   **Example:** `id devops_user`
    *   `cat /etc/passwd | grep `:
        *   **Explanation :** `/etc/passwd` file mein sabhi user accounts ki details hoti hain (username, UID, GID, home directory, shell). `grep` ka use karke aap specific user ki entry dekh sakte hain.
        *   **Example:** `cat /etc/passwd | grep devops_user`
    *   `cat /etc/group | grep `:
        *   **Explanation :** `/etc/group` file mein system groups ki details hoti hain. Yeh command specific group ki existence aur uske members ko check karta hai.
        *   **Example:** `cat /etc/group | grep web_admins`
    *   `ls -ld /home/`:
        *   **Explanation :** User ke home directory ki permissions, ownership, aur existence check karta hai. `ld` options se directory ki details aati hain, na ki uske contents ki.
        *   **Example:** `ls -ld /home/devops_user`

*   **Packages se Related Commands:**
    *   `dnf list installed `:
        *   **Explanation :** Red Hat-based systems par specific package installed hai ya nahi, aur uska version kya hai, yeh check karta hai. `yum` users `yum list installed` use karenge.
        *   **Example:** `dnf list installed httpd`
    *   `dnf info `:
        *   **Explanation :** Package ki detailed information (description, architecture, installed size, repositories) display karta hai.
        *   **Example:** `dnf info tree`
    *   `rpm -qa | grep `:
        *   **Explanation :** RPM Package Manager se installed sabhi packages ki query karta hai (`-qa` means query all) aur `grep` se specific package filter karta hai. Yeh generic tarika hai RPM-based systems par package existence check karne ka.
        *   **Example:** `rpm -qa | grep httpd`

*   **Services se Related Commands:**
    *   `systemctl status `:
        *   **Explanation :** Kisi service ka current status (running, stopped, active, inactive, enabled, disabled, loaded) aur recent logs display karta hai.
        *   **Example:** `systemctl status httpd`
    *   `systemctl is-active `:
        *   **Explanation :** Directly check karta hai ki service active (running) hai ya nahi. Output `active` ya `inactive` hota hai.
        *   **Example:** `systemctl is-active httpd`
    *   `systemctl is-enabled `:
        *   **Explanation :** Directly check karta hai ki service system boot par automatically start hone ke liye enabled hai ya nahi. Output `enabled` ya `disabled` hota hai.
        *   **Example:** `systemctl is-enabled httpd`

---

## 3. Step-by-Step Examples (कदम-दर-कदम उदाहरण)

In examples ko run karne se pehle, make sure aapke paas ek working Ansible setup hai.

**Prerequisites:**
1.  **Ansible Control Node:** Jahan se aap Ansible playbooks run karenge.
2.  **Target Nodes (Managed Hosts):** Jin servers par aap changes karna chahte hain (e.g., `server1`, `server2`). Main RHEL-based (CentOS/AlmaLinux/Rocky Linux) servers assume kar raha hoon.
3.  **SSH Connectivity:** Control node se target nodes tak passwordless SSH access (public key authentication) hona chahiye.
4.  **Sudo Privileges:** Connect hone wale user ke paas target nodes par `sudo` privileges hone chahiye (`NOPASSWD` entry in `/etc/sudoers` is ideal).

**Inventory File (`/etc/ansible/hosts` ya custom):**
Hum is inventory file ka use karenge:
```ini
# /etc/ansible/hosts (or any custom inventory file, e.g., my_inventory.ini)
[webservers]
server1 ansible_host=192.168.1.10 # Replace with your server's IP
server2 ansible_host=192.168.1.11 # Replace with your server's IP

[databases]
dbserver1 ansible_host=192.168.1.12 # Replace with your server's IP

[all:vars]
ansible_user=devops        # Your SSH user on target nodes
ansible_become=yes         # Use sudo for privileged tasks
ansible_become_method=sudo # Specify sudo (default)
ansible_private_key_file=~/.ssh/id_rsa # Path to your SSH private key
```
**Note:** `ansible_host` mein apne actual server IPs daalna mat bhoolna.

---

### Example 1: Managing Users (उपयोगकर्ताओं का प्रबंधन)

Ek playbook banate hain `users.yml` naam se:

```yaml
---
- name: Manage users and groups on webservers
  hosts: webservers
  become: yes # Ye play mein sabhi tasks ke liye sudo use karega

  tasks:
    - name: Ensure 'web_admins' group exists
      group:
        name: web_admins
        state: present
        gid: 2000 # Optional: Specific GID assign kar sakte hain

    - name: Create a new user 'devops_user'
      user:
        name: devops_user
        state: present
        comment: "DevOps Team Member"
        uid: 2001 # Optional: Specific UID assign kar sakte hain
        group: web_admins # Primary group
        groups: sudo,wheel # Secondary groups
        shell: /bin/bash
        home: /home/devops_user
        password: "{{ 'YourSecretPassword' | password_hash('sha512') }}" # Password hash generate karein

    - name: Ensure 'temp_user' is absent (delete if exists)
      user:
        name: temp_user
        state: absent
        remove: yes # Home directory bhi delete kar dega
        force: yes # Agar user logged in ho toh bhi delete kar dega

    - name: Modify 'devops_user' shell to /bin/zsh (if zsh is installed)
      user:
        name: devops_user
        shell: /bin/zsh # Make sure zsh is installed on target for this to work
      # Jab hum sirf 'shell' change kar rahe hain, toh state: present implied hai.
      # Agar zsh installed nahi hai, toh yeh task fail ho sakta hai.
      # Abhi ke liye, /bin/bash hi rakhte hain for simplicity ya ensure zsh is installed first.
      # Let's stick to modifying an existing attribute for clarity.
      # Agar shell already /bin/bash hai, toh ye task "ok" dikhayega (idempotency).
```

**Password Hashing:** `password: "{{ 'YourSecretPassword' | password_hash('sha512') }}"`
*   `password_hash` filter se aap plaintext password ko hash kar sakte hain. Jab aap playbook run karenge, Ansible yeh hashing control node par karega aur hashed value target par bhejeega.
*   **Security Note:** Playbooks mein plaintext passwords directly hardcode karna generally recommended nahi hai. Ansible Vault ka use karna best practice hai.

**Playbook Run Karna:**
```bash
ansible-playbook users.yml
```

**Verification (Target Server par):**
```bash
# Verify devops_user
id devops_user
cat /etc/passwd | grep devops_user
cat /etc/group | grep web_admins
ls -ld /home/devops_user

# Verify temp_user is gone
id temp_user # Should return "no such user"
cat /etc/passwd | grep temp_user # Should show no output
```

---

### Example 2: Managing Packages (पैकेजों का प्रबंधन)

Ek playbook banate hain `packages.yml` naam se:

```yaml
---
- name: Manage packages on all webservers
  hosts: webservers
  become: yes

  tasks:
    - name: Install 'httpd' (Apache web server)
      dnf: # RHEL/CentOS ke liye 'dnf' module. Debian/Ubuntu ke liye 'apt' use karein.
        name: httpd
        state: present # Ensure httpd package is installed

    - name: Install 'tree' utility
      dnf:
        name: tree
        state: present

    - name: Ensure 'telnet' package is removed
      dnf:
        name: telnet
        state: absent # Ensure telnet package is uninstalled

    - name: Upgrade all packages on the system (like 'dnf update')
      dnf:
        name: '*' # '*' means all packages
        state: latest # Ensure all packages are at their latest version
      # Note: Yeh task system ko reboot kar sakta hai agar kernel update ho. Careful!
```

**Playbook Run Karna:**
```bash
ansible-playbook packages.yml
```

**Verification (Target Server par):**
```bash
# Verify httpd installation
dnf list installed httpd
rpm -qa | grep httpd

# Verify tree installation
dnf list installed tree

# Verify telnet removal
dnf list installed telnet # Should show "No matching Packages"
rpm -qa | grep telnet # Should show no output
```

---

### Example 3: Managing Services (सेवाओं का प्रबंधन)

Ek playbook banate hain `services.yml` naam se:

```yaml
---
- name: Manage system services on webservers
  hosts: webservers
  become: yes

  tasks:
    - name: Ensure 'httpd' service is running
      systemd: # Modern Linux systems ke liye 'systemd' module preferred hai.
        name: httpd
        state: started # Service chal rahi honi chahiye
        enabled: yes   # Service boot par automatically start honi chahiye

    - name: Stop and disable 'firewalld' service
      systemd:
        name: firewalld
        state: stopped # Service band honi chahiye
        enabled: no    # Service boot par automatically start nahi honi chahiye

    - name: Restart 'httpd' service (if configuration changed, for example)
      systemd:
        name: httpd
        state: restarted # Service ko restart karega
      # Note: 'restarted' state hamesha changed report karta hai, bhale hi service pehle se running ho.
      # Aam taur par, yeh tab use hota hai jab koi configuration file change hoti hai,
      # aur aap chahte hain ki service naye config ke saath reload ho.
      # Is case mein, yeh task sirf demo ke liye hai, real-world mein handler ke saath use hoga.
```

**Playbook Run Karna:**
```bash
ansible-playbook services.yml
```

**Verification (Target Server par):**
```bash
# Verify httpd service
systemctl status httpd
systemctl is-active httpd # Should output 'active'
systemctl is-enabled httpd # Should output 'enabled'

# Verify firewalld service
systemctl status firewalld # Should show 'inactive (dead)'
systemctl is-active firewalld # Should output 'inactive'
systemctl is-enabled firewalld # Should output 'disabled'
```

---

## 4. Practice Tasks (अभ्यास कार्य)

Ab aapki baari hai! In tasks ko try karke apni understanding solid banaiye. Har task ke liye alag playbook banao aur verify karo.

1.  **Task 1 (Users):**
    *   **Goal:** `dbserver1` par ek naya user `monitor_user` create karein.
    *   **Requirements:**
        *   User `monitor_user` ka primary group `monitor_group` hona chahiye (agar group exist nahi karta toh use bhi create karo).
        *   Yeh user `sre_team` secondary group ka member bhi hona chahiye (agar group exist nahi karta toh use bhi create karo).
        *   User ka shell `/bin/bash` hona chahiye.
        *   User ka UID `2000` set karo.
        *   Us user ke liye ek password set karo (hashed, not plaintext).

2.  **Task 2 (Packages):**
    *   **Goal:** `webservers` group ke sabhi servers par package management perform karein.
    *   **Requirements:**
        *   Ensure `nginx` web server package `present` ho.
        *   Ensure `mariadb-server` package `absent` ho.
        *   Ensure `wget` utility ka `latest` version installed ho.

3.  **Task 3 (Services):**
    *   **Goal:** `webservers` group ke servers par services manage karein.
    *   **Requirements:**
        *   `server1` par, `nginx` service running aur enabled honi chahiye at boot.
        *   `server2` par, `postfix` service stopped aur disabled honi chahiye at boot.

4.  **Task 4 (Combined - RHCE Level):**
    *   **Goal:** `webservers` group ke sabhi servers par ek complete setup automate karein.
    *   **Requirements:**
        *   Ek user `webadmin` create karo.
        *   Ensure `httpd` package installed hai.
        *   `httpd` service running aur enabled hai.
        *   `/var/www/html/index.html` file create karo jisme "Hello from {{ inventory_hostname }}" likha ho (Hint: `copy` module use karo, aur `inventory_hostname` ek magic variable hai).
        *   `httpd` service ko restart karo agar `index.html` file mein koi change hua ho (Hint: Handlers ka use karo).

---

## 5. Pro Tips / Common Mistakes (विशेष सुझाव / सामान्य गलतियाँ)

**Pro Tips (विशेष सुझाव):**

*   **Idempotency ka Fayda Uthao:** Always define the *desired state* (`state: present`, `state: started`) rather than just the action. Isse aapke playbooks predictable aur reliable bante hain. Agar aapko koi service bas restart karni hai irrespective of its current state, tab `state: restarted` use karein.
*   **Specific Modules ka Use:** Jahan possible ho, generic `package` ya `service` module ke bajaye OS-specific (`dnf`, `apt`, `systemd`) modules ka use karein. Yeh zyada features aur better control provide karte hain.
*   **`check_mode` aur `diff`:** Playbook run karne se pehle, hamesha `ansible-playbook --check --diff ` ka use karein.
    *   `--check` (या `-C`): Playbook ko simulate karta hai without making any actual changes.
    *   `--diff` (या `-D`): Shows what changes *would* be made (especially useful with modules like `copy`, `template`).
    *   Yeh aapko unexpected changes se bachayega.
*   **Variables ka Sahi Istemal:** Usernames, package names, service names, aur anya configuration values ko variables mein rakho (`vars` section, `vars_files`, ya `group_vars`/`host_vars`). Isse aapke playbooks reusable aur maintainable bante hain.
*   **Handlers for Service Restarts:** Agar aap kisi configuration file ko modify karte hain (jaise Apache ki config), aur us change ke baad service ko restart karna zaroori hai, toh `notify` aur `handlers` ka use karein. Isse service sirf tab restart hoti hai jab uski associated config file change hoti hai (idempotent way).
    ```yaml
    - name: Copy custom httpd config
      copy:
        src: files/httpd.conf
        dest: /etc/httpd/conf/httpd.conf
      notify: restart httpd

    # ... later in the playbook ...
    handlers:
      - name: restart httpd
        systemd:
          name: httpd
          state: restarted
    ```
*   **Ansible Vault:** Sensitive information (jaise passwords) ko plaintext mein playbooks mein store na karein. Ansible Vault ka use karke inhe encrypt karein.

**Common Mistakes (सामान्य गलतियाँ):**

*   **Permissions Issues (`become`):** Agar `ansible_become=yes` set hai, toh make sure ki `ansible_user` ke paas target host par `sudo` privileges hon, aur agar password required hai toh `ansible_become_password` ya `ansible_ask_become_pass` use karein, ya `/etc/sudoers` mein NOPASSWD entry add karein.
*   **Inventory Mistakes:** Galat hostname, IP address, ya `ansible_user` specify karna. `ansible -m ping all` se connectivity test karein.
*   **YAML Syntax Errors:** YAML indentation bahut strict hoti hai. Ek space bhi galat hua toh error aa sakta hai. Ek achhe text editor (VS Code with YAML plugin) ka use karein ya online YAML linter use karein.
*   **`state` Value Confusion:**
    *   `state: present` (users, packages): Matlab user/package exist karna chahiye.
    *   `state: absent` (users, packages): Matlab user/package exist nahi karna chahiye (delete/uninstall).
    *   `state: started` (services): Matlab service chal rahi honi chahiye.
    *   `state: stopped` (services): Matlab service band honi chahiye.
    *   `state: latest` (packages): Matlab package ka latest version installed hona chahiye.
    *   Inhe galat use karne se unexpected results mil sakte hain.
*   **Verification Bhool Jana:** Playbook run karne ke baad, hamesha target system par manually (ya `ansible -m command` se) verify karein ki changes apply hue hain ya nahi. `check_mode` sirf simulate karta hai, actual verification zaroori hai.

---

Umeed hai yeh comprehensive lesson aapko "Ansible modules for users, packages, and services" ko achhe se samajhne mein madad karega. Practice karte rahiye, aur aap bahut jald ek proficient Ansible user ban jayenge! All the best!
