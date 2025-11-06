Day 40: Automating RHCSA tasks with playbooks (firewalld, SELinux, LVM)
---
Namaste students! Main aapka Linux instructor hoon, aur aaj hum ek bahut hi important aur exciting topic par discuss karenge jo aapke RHCSA aur RHCE certification ke liye critical hai: **"Automating RHCSA tasks with Playbooks (firewalld, SELinux, LVM)"**.

Soch lo, aapko 100 servers par ek hi tarah ka configuration karna hai – firewall rule add karna hai, SELinux policy adjust karni hai, ya LVM partitions banani hai. Agar aap ye sab manually karne lagenge toh kitna time lagega aur kitni mistakes ho sakti hain? Yahi par automation picture mein aata hai! Aur automation ke liye hum use karenge **Ansible** aur uske **Playbooks**.

---

### 1. Concept Explanation (Hinglish)

**Automation in Linux (Linux mein Automation):**
Automation ka matlab hai repetitive (baar-baar hone wale) tasks ko scripts ya tools ki help se automatically perform karwana, bina manual intervention ke. Ye aapka time bachata hai, errors kam karta hai, aur consistency badhata hai.

**Why Ansible? (Ansible hi kyun?):**
Ansible ek popular automation engine hai. Iske kuch khaas features hain:
*   **Agentless:** Matlab, jin servers (managed nodes) par aap tasks run karwana chahte hain, un par koi special software (agent) install karne ki zarurat nahi. Ye standard SSH protocol use karta hai. Bahut simple!
*   **Simple YAML Syntax:** Playbooks YAML files mein likhe jaate hain, jo ki human-readable hoti hain. Bahut aasaan hai samajhna aur likhna.
*   **Idempotent:** Iska matlab hai ki aapka playbook kitni baar bhi run ho, target system ki final state wahi hogi jo aapne define ki hai. Agar task already complete hai toh Ansible use dobara nahi karega.
*   **Open Source:** Free to use aur huge community support.

**What are Playbooks? (Playbooks kya hain?):**
Playbooks Ansible ke core hain. Ye YAML format mein likhe gaye files hote hain jo define karte hain ki aap kin servers (hosts) par kaun se tasks (actions) perform karna chahte hain.
Ek playbook mein ye cheezein hoti hain:
*   **Hosts:** Kin target machines par ye playbook run hoga.
*   **Become:** Kya tasks ko sudo (elevated privileges) ke saath run karna hai?
*   **Tasks:** Actual actions jo perform karne hain. Har task ek specific Ansible module use karta hai. Jaise `firewalld` module firewall ke liye, `selinux` module SELinux ke liye, `lvg` LVM ke liye.

**Automating RHCSA tasks (RHCSA tasks ko automate karna):**
RHCSA exam mein aapko bahut saare basic administrative tasks perform karne hote hain, jaise firewalld rules manage karna, SELinux policy adjust karna, aur LVM storage set up karna. In sab tasks ko hum Ansible playbooks se automate kar sakte hain, jo aapke RHCE ke liye bhi ek strong foundation banayega. RHCE mein aapko complex scenarios aur Ansible ke advanced features (roles, variables, templates, etc.) use karne hote hain.

---

### 2. Key Linux Commands (Hinglish)

Ansible use karne ke liye aapko kuch basic commands pata hone chahiye:

1.  **`ansible  -m  -a ""`**
    *   **Explanation:** Ye command ad-hoc commands run karne ke liye use hota hai. Ad-hoc commands matlab one-off, simple tasks jo aap bina playbook file banaye execute kar sakte hain.
    *   **Example:** `ansible webservers -m ping` (webservers group mein sab machines ko ping karega)
    *   **Hinglish:** Ye command short-term, simple tasks ke liye hai. Playbook banaye bina ek specific module (jaise `ping`, `command`, `shell`) ko seedha run karna ho.

2.  **`ansible-playbook `**
    *   **Explanation:** Ye command playbooks ko execute karne ke liye use hota hai. Ye sabse common command hai Ansible mein automation ke liye.
    *   **Example:** `ansible-playbook my_first_playbook.yml`
    *   **Hinglish:** Hamare saare complex tasks aur automation isi command se run honge. Ye YAML file mein likhi hui instructions ko execute karta hai.

3.  **`ansible-playbook --check `**
    *   **Explanation:** Ye command playbook ko "check mode" mein run karta hai. Isse aap dekh sakte hain ki playbook kya changes karega, bina actual changes apply kiye. Bahut useful hai testing ke liye.
    *   **Hinglish:** Dry-run ki tarah samajh lo. Ye bataega ki kaun se tasks change honge, par unhe actual mein apply nahi karega. Pehle check karo, phir apply!

4.  **`ansible-playbook --diff `**
    *   **Explanation:** Ye command `--check` ke saath ya akela bhi use ho sakta hai. Ye detailed diff output dikhata hai ki kaun si lines change hongi ya kaun si files modify hongi.
    *   **Hinglish:** Ye option aapko detailed output deta hai ki kya changes honge. Kya file modify hogi, kya content add/remove hoga, sab dikhaega.

5.  **`ansible-galaxy` (RHCE context)**
    *   **Explanation:** Ye command Ansible Galaxy se roles download karne ya apne roles manage karne ke liye use hota hai. Roles advanced way hain playbooks ko structure karne ka.
    *   **Hinglish:** Ye thoda advanced hai, RHCE mein zyada use hota hai. Isse aap pre-built automation code (roles) download kar sakte ho, ya apne code ko roles mein organize kar sakte ho.

**Related RHCSA Manual Commands (Jinhe hum Automate karenge):**
*   **Firewalld:** `firewall-cmd --add-port`, `firewall-cmd --add-service`, `firewall-cmd --runtime-to-permanent`, `firewall-cmd --reload`
*   **SELinux:** `setenforce`, `semanage fcontext`, `restorecon`, `setsebool`
*   **LVM:** `pvcreate`, `vgcreate`, `lvcreate`, `mkfs`, `mount`, `vi /etc/fstab`

---

### 3. Step-by-Step Examples

Aapke paas ek **Ansible Control Node** hona chahiye jahan Ansible installed ho. Aur ek ya do **Managed Nodes** (target servers) jin par aap tasks run karenge.
**Prerequisites:**
1.  Ansible Control Node par `ansible` package installed ho.
2.  Control Node se Managed Nodes par **SSH passwordless access** setup ho (SSH keys use karke).
3.  Managed Nodes par Python installed ho (generally default hota hai).

**Inventory File (`/etc/ansible/hosts` ya custom file):**
Pehle apni inventory file banate hain. Maine ek example `inventory.ini` file banayi hai.
```ini
[webservers]
server1 ansible_host=192.168.1.100
server2 ansible_host=192.168.1.101

[databases]
dbserver1 ansible_host=192.168.1.200

[all:vars]
ansible_user=devops
ansible_private_key_file=/home/devops/.ssh/id_rsa
```
(Replace IPs, `ansible_user` and `ansible_private_key_file` with your actual values)

---

#### Example 1: Firewalld Automation

**Scenario:** Aapko `webservers` group par HTTP (port 80) service allow karni hai, aur ek custom port `8080/tcp` bhi open karna hai.

**Playbook: `firewall_config.yml`**
```yaml
---
- name: Configure firewalld on webservers
  hosts: webservers
  become: yes # Ye tasks root privileges ke saath run honge

  tasks:
    - name: Ensure firewalld is running and enabled
      ansible.builtin.systemd:
        name: firewalld
        state: started
        enabled: yes

    - name: Allow HTTP service permanently
      ansible.posix.firewalld:
        service: http
        state: enabled
        permanent: yes
        immediate: yes # Changes ko immediately apply karega

    - name: Allow custom port 8080/tcp permanently
      ansible.posix.firewalld:
        port: 8080/tcp
        state: enabled
        permanent: yes
        immediate: yes

    - name: Reload firewalld to apply changes (optional, immediate=yes se ho jata hai)
      ansible.builtin.command: firewall-cmd --reload
      # Ye task sirf tab run hoga jab pichle firewalld tasks mein changes hue hon
      when: result_http.changed or result_port.changed # Example for conditional execution
```
*(Note: `immediate: yes` usually handles the reload. The explicit reload task is shown for demonstration of `command` module and `when` condition.)*

**Explanation:**
*   `hosts: webservers`: Ye playbook sirf `webservers` group par apply hoga.
*   `become: yes`: Tasks ko `sudo` ke through root permissions se run karega.
*   `systemd` module: Firewalld service ko start aur enable karta hai.
*   `firewalld` module:
    *   `service: http`, `port: 8080/tcp`: HTTP service aur port 8080 ko allow karta hai.
    *   `state: enabled`: Iska matlab enable karna hai. `disabled` se remove kar sakte hain.
    *   `permanent: yes`: Ye rule `/etc/firewalld/` mein save ho jayega taaki reboot ke baad bhi rahe.
    *   `immediate: yes`: Changes ko turant apply karta hai (runtime par).

**How to run:**
```bash
ansible-playbook firewall_config.yml
```

---

#### Example 2: SELinux Automation

**Scenario:** Aapko `webservers` par SELinux ko `permissive` mode mein karna hai temporary aur permanently. Phir `httpd_can_network_connect` boolean ko `on` karna hai. Aur `/web/data` directory ke liye custom SELinux context `httpd_sys_content_t` set karna hai.

**Playbook: `selinux_config.yml`**
```yaml
---
- name: Configure SELinux on webservers
  hosts: webservers
  become: yes

  tasks:
    - name: Set SELinux to permissive mode temporarily
      ansible.posix.selinux:
        state: permissive
      # Note: This is runtime only, for permanent change, need to edit /etc/selinux/config
      # (Though selinux module can do this, setting it to permissive temporarily is common for debugging)

    - name: Set SELinux to enforcing mode permanently
      ansible.posix.selinux:
        state: enforcing
        policy: targeted
        config: enforce # /etc/selinux/config mein changes apply karega

    - name: Set SELinux boolean httpd_can_network_connect to on
      ansible.posix.seboolean:
        name: httpd_can_network_connect
        state: yes
        persistent: yes # Ye change reboot ke baad bhi rahega

    - name: Create a custom web directory for demonstration
      ansible.builtin.file:
        path: /web/data
        state: directory
        mode: '0755'

    - name: Set SELinux fcontext for /web/data
      community.general.sefcontext: # Use sefcontext module for file context
        target: '/web/data(/.*)?' # Regex for directory and its contents
        setype: httpd_sys_content_t
        state: present

    - name: Apply SELinux context to /web/data
      ansible.builtin.command: restorecon -Rv /web/data
      # Ye task tabhi run hoga jab sefcontext module ne kuch changes kiye hon
      when: sefcontext_result.changed is defined and sefcontext_result.changed
      register: sefcontext_result # Register the output of sefcontext for 'when' condition
```
*(Note: `community.general.sefcontext` module is available in `community.general` collection. You might need to install it: `ansible-galaxy collection install community.general`)*

**Explanation:**
*   `selinux` module: SELinux state (`enforcing`/`permissive`) set karta hai. `config: enforce` se `/etc/selinux/config` file update hoti hai.
*   `seboolean` module: Specific SELinux booleans ko enable/disable karta hai (`persistent: yes` for permanent change).
*   `file` module: Directory banata hai.
*   `sefcontext` module: File context rule banata hai.
*   `command` module with `restorecon`: Actual mein file system par context apply karta hai. (`when` condition ensures `restorecon` only runs if the `sefcontext` rule was newly created or changed).

**How to run:**
```bash
ansible-playbook selinux_config.yml
```

---

#### Example 3: LVM Automation

**Scenario:** `dbserver1` par ek naya Physical Volume (PV) create karna hai `/dev/sdb` par, ek Volume Group (VG) `mydatavg` create karna hai, usmein ek Logical Volume (LV) `datastorelv` create karna hai 10GB ka, use `xfs` filesystem se format karna hai, aur `/mnt/data` par mount karke `/etc/fstab` mein permanent entry karni hai.

**Playbook: `lvm_setup.yml`**
```yaml
---
- name: Setup LVM on dbserver1
  hosts: databases
  become: yes

  tasks:
    - name: Install xfsprogs (required for xfs filesystem)
      ansible.builtin.yum:
        name: xfsprogs
        state: present

    - name: Create Physical Volume on /dev/sdb
      community.general.parted: # parted module can be used for PVs too
        device: /dev/sdb
        number: 1 # We'll use the whole disk as a single partition
        state: present
        fs_type: LVM
        part_type: primary
      # Alternative for PV creation using community.general.pv:
      # community.general.pv:
      #   pv_name: /dev/sdb
      #   state: present

    - name: Create Volume Group 'mydatavg' from /dev/sdb
      community.general.vg:
        vg_name: mydatavg
        pvs: /dev/sdb
        state: present

    - name: Create Logical Volume 'datastorelv' (10GB)
      community.general.lvol:
        vg: mydatavg
        lv: datastorelv
        size: 10g
        state: present

    - name: Format the Logical Volume with XFS filesystem
      community.general.filesystem:
        fstype: xfs
        dev: /dev/mydatavg/datastorelv # Path to the LV
        force: no # Don't format if it's already formatted

    - name: Create mount point directory
      ansible.builtin.file:
        path: /mnt/data
        state: directory
        mode: '0755'

    - name: Mount the Logical Volume and add to /etc/fstab
      ansible.posix.mount:
        path: /mnt/data
        src: /dev/mydatavg/datastorelv
        fstype: xfs
        opts: defaults
        state: mounted # mount the filesystem
        fstab_file: /etc/fstab # Add entry to fstab
```
*(Note: `community.general.parted`, `community.general.vg`, `community.general.lvol`, `community.general.filesystem` modules are from `community.general` collection. Install it if needed: `ansible-galaxy collection install community.general`)*

**Explanation:**
*   `yum` module: `xfsprogs` package install karta hai (XFS formatting ke liye).
*   `parted` module: `/dev/sdb` ko LVM physical volume ke liye prepare karta hai.
*   `vg` module: Physical Volume `/dev/sdb` se `mydatavg` naam ka Volume Group banata hai.
*   `lvol` module: `mydatavg` VG mein `datastorelv` naam ka 10GB ka Logical Volume create karta hai.
*   `filesystem` module: Logical Volume ko XFS format karta hai.
*   `file` module: Mount point directory `/mnt/data` banata hai.
*   `mount` module:
    *   `path: /mnt/data`: Jis directory par mount karna hai.
    *   `src: /dev/mydatavg/datastorelv`: Source device (Logical Volume ka path).
    *   `fstype: xfs`: Filesystem type.
    *   `state: mounted`: Mount operation perform karta hai aur agar `fstab_file` diya ho toh entry bhi add karta hai.
    *   `fstab_file: /etc/fstab`: Isse entry `/etc/fstab` mein add ho jayegi, jo reboot ke baad bhi persistent rahegi.

**How to run:**
```bash
ansible-playbook lvm_setup.yml
```

---

### 4. Practice Tasks (Lab Exercises)

Ab aapko apne skills test karne hain. Ek fresh Managed Node par (ya current ko reset karke) ye tasks perform kijiye:

1.  **Web Server Setup Automation:**
    *   Ek playbook likho jo `webserver` group par Apache HTTP Server (httpd) install kare.
    *   Firewall mein HTTP (port 80) aur HTTPS (port 443) services ko permanently enable kare.
    *   `/var/www/html/mysite` naam ki ek directory banaye aur usmein `index.html` file banaye jismein "Welcome to Ansible Automated Site!" likha ho.
    *   `/var/www/html/mysite` directory ke liye SELinux context `httpd_sys_content_t` set kare aur `restorecon` apply kare.
    *   Apache service ko start aur enable kare.
    *   `httpd_can_network_connect_db` boolean ko `on` kare permanently.

2.  **Database Storage Automation:**
    *   `dbserver` group par (`/dev/sdc` ya koi dusra unused disk assume kijiye) ek naya 20GB ka LVM setup kare.
    *   PV create kare `/dev/sdc` par.
    *   VG create kare `dbstoragevg` naam se.
    *   LV create kare `dbdata` naam se, 20GB ka.
    *   LV ko `ext4` filesystem se format kare.
    *   `/data/database` directory par mount kare aur `/etc/fstab` mein entry add kare.
    *   Firewall mein MariaDB service (port 3306) ko permanently enable kare.

3.  **Security Hardening Automation:**
    *   Ek playbook banaye jo `all` hosts par SELinux ko `enforcing` mode mein set kare (permanently).
    *   Ensure kare ki `firewalld` service `running` aur `enabled` hai.
    *   Firewall mein `ssh` service ko permanently allow kare.
    *   Baki sabhi unnecessary ports ko block kar de (iska matlab hai ki aap specific ports hi allow karoge, aur default zone mein dusre rules nahi honge).

---

### 5. Pro Tips / Common Mistakes

**Pro Tips:**

*   **Idempotency ka dhyan rakho:** Ansible ka magic hi idempotency hai. Hamesha aise tasks likho jo multiple times run hone par bhi system ko break na karein ya unnecessary changes na karein. Modules generally idempotent hote hain.
*   **Use `ansible-playbook --check` and `--diff`:** Playbook ko production mein run karne se pehle hamesha `check` mode aur `diff` mode mein test karo. Ye aapko unexpected changes se bachayega.
*   **Be specific with `state`:** `state: present`, `state: absent`, `state: started`, `state: stopped`, `state: enabled`, `state: disabled` ka sahi istemal karein.
*   **`become: yes` ka sahi istemal:** Jab root privileges ki zarurat ho, tabhi `become: yes` use karein. Apne `ansible_user` ko `sudo` access hona chahiye `NOPASSWD` ke saath.
*   **Organize your playbooks:** Chote, single-purpose playbooks likho. Jab complexity badhe toh **Roles** ka use karo (RHCE level).
*   **Variables ka use karein:** Hardcode values (jaise port numbers, paths) ke bajaye variables ka use karo. Isse playbooks reusable aur maintainable bante hain.

**Common Mistakes:**

*   **YAML Syntax Errors:** YAML indentation bahut strict hota hai. Space ki jagah tab use karna ya wrong indentation dena common mistake hai. Use a YAML linter or editor with YAML support.
*   **SSH Connectivity Issues:** Managed nodes tak SSH access na hona. Ensure `ansible_user` ke paas passwordless SSH access ho.
*   **`become` privilege issues:** Managed node par `ansible_user` ke paas `sudo` access na hona ya `sudoers` file mein sahi entry na hona.
*   **Incorrect Module Arguments:** Module ke parameters galat dena. Hamesha Ansible documentation (search `ansible `) refer karein.
*   **Inventory file errors:** Hosts ke IPs galat hona, groups galat hona.
*   **Filesystem / LVM path errors:** LVM mein devices ke paths sahi se na dena (`/dev/vgname/lvname`).
*   **Forgetting `permanent: yes` or `persistent: yes`:** Firewalld rules ya SELinux booleans ko `permanent`/`persistent` na karne se reboot ke baad changes lost ho sakte hain.
*   **Not reloading services:** Agar koi configuration change kiya hai (jaise firewalld rules), toh service ko reload karna ya restart karna zaruri hota hai taaki changes apply ho jayein. (Ansible modules jaise `firewalld` module mein `immediate: yes` ya handlers use kar sakte hain).

---

Mujhe ummeed hai ki ye comprehensive lesson aapko "Automating RHCSA tasks with playbooks" topic ko achhe se samajhne mein help karega. Practise karte rahiye, aur automation ki power ko embrace kijiye! All the best for your RHCSA and RHCE journey!
