Day 50: Full RHCSA + RHCE combined lab
---
Namaste students! Main aapka Linux instructor hoon, aur aaj hum ek bahut hi exciting aur comprehensive topic par discuss karenge – "Full RHCSA + RHCE Combined Lab". Yeh ek aisa hands-on session hoga jo aapko RHCSA ke foundational skills aur RHCE ke advanced automation aur service configuration concepts ko ek saath jodna sikhayega. Imagine kijiye ki aap ek real-world scenario mein kaam kar rahe hain, jahan aapko ek server ko scratch se set up karna hai, uspar applications deploy karni hain, aur uski security aur automation bhi dekhni hai. Toh chaliye, shuru karte hain!

---

## Full RHCSA + RHCE Combined Lab: Ek Sampoorna Margdarshan

### 1. Concept Explanation (Hinglish)

**"Full RHCSA + RHCE Combined Lab" kya hai aur yeh itna zaroori kyun hai?**

Dekho students, RHCSA (Red Hat Certified System Administrator) aapko Linux server manage karne ke basic se intermediate skills sikhata hai. Isme system configuration, storage management, user management, package management, basic networking, firewall, aur SELinux ki understanding shamil hai. Jabki, RHCE (Red Hat Certified Engineer) in skills ko next level par le jata hai. RHCE mein aap network services (jaise Web server, Database, File sharing, DNS) configure karna, unhe secure karna, aur sabse important, **Ansible automation** ka use karke in sabhi tasks ko automate karna seekhte ho.

Ek "Combined Lab" ka matlab hai ki hum ek aisa project ya scenario create karenge jisme RHCSA aur RHCE dono ke concepts ek saath apply honge. Hum ek server ko initial setup se lekar uspar complex applications deploy karne aur unhe automate karne tak ka poora lifecycle cover karenge.

**Is Lab ka Goal kya hai?**

Humara goal hoga ek complete web application server setup karna, jisme:

1.  **RHCSA Basics:** Server ki initial configuration (networking, storage, user accounts).
2.  **RHCE Services:** Apache web server aur MariaDB database server install aur configure karna.
3.  **Security:** Firewall rules aur SELinux policies ko manage karna.
4.  **Automation (RHCE Core):** In sabhi tasks ko Ansible Playbooks ka use karke automate karna, taaki hum ek click mein poora setup kar sakein.

Yeh lab aapko real-world mein kaise kaam hota hai, uska ek clear picture dega aur aapki problem-solving skills ko bhi improve karega. Bahut zaroori hai ki aap isko step-by-step follow karein aur har command ko samjhein.

---

### 2. Key Linux Commands (Hinglish)

Yahan kuch important commands hain jo hum is combined lab mein use karenge, with Hinglish explanations:

*   **System & File Management (RHCSA Core):**
    *   `fdisk /dev/sdX`: Disk partitions create, delete, modify karne ke liye. (Disk par partitions banane ya hatane ke liye.)
    *   `mkfs.ext4 /dev/sdXN`: Partition ko format karne aur uspar filesystem banane ke liye. (Partition par ek file system, jaise ext4, banane ke liye.)
    *   `mount /dev/sdXN /mnt/directory`: Filesystem ko temporary mount karne ke liye. (Ek filesystem ko temporary roop se kisi directory par mount karne ke liye.)
    *   `vi /etc/fstab`: System boot hone par filesystems ko automatically mount karne ke liye configuration file. (Permanent mounts ke liye, yeh file edit karte hain.)
    *   `pvcreate /dev/sdX`: Physical Volume (PV) create karne ke liye. (LVM ke liye physical volume banate hain.)
    *   `vgcreate myvg /dev/sdX /dev/sdY`: Volume Group (VG) create karne ke liye. (PVs ko group karke volume group banate hain.)
    *   `lvcreate -n mylv -L 10G myvg`: Logical Volume (LV) create karne ke liye. (Volume group se logical volume banate hain.)
    *   `useradd username`: Naya user account banane ke liye. (Naya user create karna.)
    *   `passwd username`: User ka password set karne ya change karne ke liye. (User ka password set karna.)
    *   `groupadd groupname`: Naya group banane ke liye. (Naya group banana.)
    *   `usermod -aG groupname username`: User ko existing group mein add karne ke liye. (User ko ek group ka member banana.)
    *   `chmod XXX file`: Files aur directories ke permissions change karne ke liye. (Permissions badalna.)
    *   `chown user:group file`: File ownership change karne ke liye. (File ka owner aur group badalna.)
    *   `dnf install package`: Software packages install karne ke liye. (Packages install karna.)
    *   `systemctl start service`: Service start karne ke liye. (Service shuru karna.)
    *   `systemctl enable service`: Service ko boot par automatically start hone ke liye enable karne ke liye. (Boot ke time service automatically run ho, uske liye enable karna.)
    *   `systemctl status service`: Service ka status check karne ke liye. (Service ka current status dekhna.)
    *   `firewall-cmd --add-port=80/tcp --permanent`: Firewall mein permanent rule add karne ke liye. (Firewall mein port access allow karna.)
    *   `firewall-cmd --reload`: Firewall rules reload karne ke liye. (Firewall rules ko apply karna.)
    *   `setsebool -P httpd_can_network_connect_db on`: SELinux boolean values set karne ke liye. (SELinux policy ko adjust karna.)
    *   `semanage fcontext -a -t httpd_sys_content_t "/mywebapp(/.*)?"`: Custom directories ke liye SELinux context set karne ke liye. (Custom directory ke liye SELinux context set karna.)
    *   `restorecon -Rv /mywebapp`: SELinux context restore karne ke liye. (File system par default SELinux context apply karna.)

*   **RHCE Specific (Services & Automation):**
    *   `httpd`: Apache HTTP Web Server (Web pages serve karne ke liye).
    *   `mariadb-server`: MariaDB Database Server (Databases manage karne ke liye).
    *   `ansible-playbook`: Ansible playbooks execute karne ke liye command. (Automation scripts run karna.)
    *   `vi inventory.ini`: Ansible hosts file create ya edit karne ke liye. (Jin servers par automation karni hai, unki list.)
    *   `vi playbook.yml`: Ansible playbooks create ya edit karne ke liye. (Automation steps likhne ke liye.)

---

### 3. Step-by-Step Examples (Real-world Scenario)

Chaliye, ek real-world scenario lete hain:
**Scenario:** Aapko ek naya "DevOps Web Server" set up karna hai jisme ek web application (jo MariaDB database use karti hai) host hogi. Server ko secure karna hai, aur poore setup process ko automate karna hai.

**Lab Setup:**
Aapko kam se kam do RHEL 8/9 VMs ki zaroorat padegi:
1.  **Control Node (Ansible Host):** Jaha se aap Ansible commands run karoge. Isme Ansible software installed hoga. (RHCE focus)
2.  **Managed Node (DevOps Web Server):** Jisko aap configure karoge. (RHCSA + RHCE focus)

**Managed Node par Pre-requisites:**
*   `root` access (ya `sudo` privileges)
*   SSH server running (`systemctl status sshd`)
*   Password-less SSH access Control Node se Managed Node par (bahut zaroori hai Ansible ke liye).

**Let's begin!**

#### Task 1: Managed Node - Initial RHCSA Setup (Manual, then Automate with Ansible)

**Step 1.1: Storage Configuration (LVM)**
Hum `/var/www/html` (web content ke liye) aur `/var/lib/mysql` (database ke liye) ke liye separate logical volumes banayenge. Iske liye hum do naye virtual disks add karenge (e.g., `/dev/sdb`, `/dev/sdc`) to your Managed Node VM.

```bash
# Managed Node par login karein as root
# 1. Physical Volumes banayein
pvcreate /dev/sdb /dev/sdc

# 2. Volume Group banayein
vgcreate app_data_vg /dev/sdb /dev/sdc

# 3. Logical Volumes banayein
lvcreate -n web_lv -L 5G app_data_vg # Web content ke liye 5GB
lvcreate -n db_lv -L 5G app_data_vg  # Database ke liye 5GB

# 4. Filesystems banayein
mkfs.xfs /dev/app_data_vg/web_lv
mkfs.xfs /dev/app_data_vg/db_lv

# 5. Directories banayein jahan mount karna hai
mkdir -p /var/www/html
mkdir -p /var/lib/mysql

# 6. /etc/fstab mein entries add karein for permanent mounts
echo "/dev/mapper/app_data_vg-web_lv /var/www/html xfs defaults 0 0" >> /etc/fstab
echo "/dev/mapper/app_data_vg-db_lv /var/lib/mysql xfs defaults 0 0" >> /etc/fstab

# 7. Mount karein aur verify karein
mount -a
df -hT
```

**Step 1.2: User & Group Management**
Ek dedicated `webadmin` user aur `webdevs` group banayenge.

```bash
# Managed Node par login karein as root
groupadd webdevs
useradd -G webdevs webadmin
passwd webadmin # Password set karein
```

**Step 1.3: Firewall & SELinux Basics**
Web server aur database ke liye basic firewall rules aur SELinux context set karenge.

```bash
# Managed Node par login karein as root
# Firewall rules
firewall-cmd --permanent --add-service=http # HTTP (port 80) allow karein
firewall-cmd --permanent --add-service=https # HTTPS (port 443) allow karein
firewall-cmd --permanent --add-port=3306/tcp # MariaDB default port allow karein
firewall-cmd --reload # Rules apply karein

# SELinux for web content (will be covered more in RHCE section)
# Abhi ke liye, default context check karein
ls -Zd /var/www/html
```

#### Task 2: RHCE Services Configuration (Manual, then Automate with Ansible)

**Step 2.1: Apache HTTP Web Server Setup**

```bash
# Managed Node par login karein as root
# 1. Apache install karein
dnf install httpd -y

# 2. Service start aur enable karein
systemctl start httpd
systemctl enable httpd

# 3. Ek simple web page banayein (test karne ke liye)
echo "
Welcome to DevOps Web Server!
" > /var/www/html/index.html

# 4. Check karein ki Apache chal raha hai
systemctl status httpd
```
Ab Control Node ya aapke local system ke browser se Managed Node ka IP address access karke verify karein.

**Step 2.2: MariaDB Database Server Setup**

```bash
# Managed Node par login karein as root
# 1. MariaDB install karein
dnf install mariadb-server -y

# 2. Service start aur enable karein
systemctl start mariadb
systemctl enable mariadb

# 3. Initial security setup run karein (ROOT password set karne ke liye zaroori hai)
mysql_secure_installation # Prompt follow karein: set root password, remove anonymous users, disallow root login remotely, remove test database.

# 4. Ek test database aur user banayein
mysql -u root -p # Jab prompt aaye, root password enter karein
# SQL commands enter karein:
CREATE DATABASE devops_app_db;
CREATE USER 'devops_user'@'localhost' IDENTIFIED BY 'Pa$$w0rd';
GRANT ALL PRIVILEGES ON devops_app_db.* TO 'devops_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

**Step 2.3: Advanced SELinux for Web Server & Database**
Agar aapki web app database se connect karegi, toh SELinux ko adjust karna padega.

```bash
# Managed Node par login karein as root
# Agar Apache, database se network par connect karega
setsebool -P httpd_can_network_connect_db on

# Agar aapke web app ke liye koi custom directory hai, toh uska context set karein
# For example, agar aapka content /opt/webapp mein hai:
# semanage fcontext -a -t httpd_sys_content_t "/opt/webapp(/.*)?"
# restorecon -Rv /opt/webapp
```

#### Task 3: Automation with Ansible (RHCE Core)

Ab tak humne sab kuch manually kiya. RHCE mein hum yeh sab automate karna seekhte hain Ansible se.

**Step 3.1: Ansible Control Node Setup**
Make sure aapke Control Node par Ansible installed hai.
`dnf install ansible -y`

**Step 3.2: Ansible Inventory File (`inventory.ini`)**
Control Node par ek inventory file banayein, jisme aapke Managed Node ki details hongi.

```ini
# /etc/ansible/hosts or ~/ansible/inventory.ini
[webservers]
devops_web_server ansible_host=YOUR_MANAGED_NODE_IP ansible_user=root ansible_password=YOUR_ROOT_PASSWORD

# Agar aap passwordless SSH use kar rahe hain, toh ansible_password ki zaroorat nahi hai.
# SSH key-based authentication is preferred for production.
# example: devops_web_server ansible_host=192.168.1.10 ansible_user=devops_user ansible_become=yes
```
**`ansible_become=yes`** ka matlab hai ki Ansible `sudo` ka use karke commands run karega. Agar aap `root` user se connect kar rahe hain, toh iski zaroorat nahi hai.

**Step 3.3: Ansible Playbooks Creation**
Hum alag-alag tasks ke liye playbooks banayenge.

**Playbook 1: `storage_setup.yml` (Storage Configuration)**
```yaml
# storage_setup.yml
- name: Configure Storage (LVM) for Web and DB
  hosts: webservers
  become: yes # Root privileges ki zaroorat hai
  tasks:
    - name: Create Physical Volumes
      community.general.parted:
        device: "{{ item }}"
        number: 1
        state: present
        fs_type: LVM2_member
      loop:
        - /dev/sdb
        - /dev/sdc
      ignore_errors: yes # Agar PVs already exist karte hain toh error ignore karein

    - name: Create Volume Group 'app_data_vg'
      community.general.lvg:
        vg: app_data_vg
        pvs: /dev/sdb1,/dev/sdc1 # Physical volumes (partition number ke saath)
        state: present

    - name: Create Logical Volume 'web_lv'
      community.general.lvol:
        vg: app_data_vg
        lv: web_lv
        size: 5G
        state: present

    - name: Create Logical Volume 'db_lv'
      community.general.lvol:
        vg: app_data_vg
        lv: db_lv
        size: 5G
        state: present

    - name: Format web_lv with XFS
      community.general.filesystem:
        fstype: xfs
        dev: /dev/mapper/app_data_vg-web_lv
        state: present

    - name: Format db_lv with XFS
      community.general.filesystem:
        fstype: xfs
        dev: /dev/mapper/app_data_vg-db_lv
        state: present

    - name: Create mount points
      ansible.builtin.file:
        path: "{{ item }}"
        state: directory
        mode: '0755'
      loop:
        - /var/www/html
        - /var/lib/mysql

    - name: Add web_lv to /etc/fstab
      ansible.builtin.mount:
        path: /var/www/html
        src: /dev/mapper/app_data_vg-web_lv
        fstype: xfs
        state: mounted
        opts: defaults

    - name: Add db_lv to /etc/fstab
      ansible.builtin.mount:
        path: /var/lib/mysql
        src: /dev/mapper/app_data_vg-db_lv
        fstype: xfs
        state: mounted
        opts: defaults
```
*(Note: `community.general.parted` aur `community.general.lvg`/`lvol`/`filesystem` modules ke liye, aapko `ansible-galaxy collection install community.general` run karna pad sakta hai Control Node par.)*

**Playbook 2: `base_setup.yml` (Users, Groups, Firewall, SELinux)**
```yaml
# base_setup.yml
- name: Configure Base System (Users, Groups, Firewall, SELinux)
  hosts: webservers
  become: yes
  tasks:
    - name: Create 'webdevs' group
      ansible.builtin.group:
        name: webdevs
        state: present

    - name: Create 'webadmin' user and add to 'webdevs' group
      ansible.builtin.user:
        name: webadmin
        groups: webdevs
        append: yes
        state: present
        password: "{{ 'Pa$$w0rd' | password_hash('sha512') }}" # Strong password hash

    - name: Install firewalld
      ansible.builtin.dnf:
        name: firewalld
        state: present

    - name: Start and enable firewalld service
      ansible.builtin.systemd:
        name: firewalld
        state: started
        enabled: yes

    - name: Add firewall rules for HTTP, HTTPS, MariaDB
      ansible.posix.firewalld:
        service: "{{ item }}"
        permanent: yes
        state: enabled
      loop:
        - http
        - https
        - mariadb # This service name might need to be 'mysql' or port 3306

    - name: Add explicit port 3306/tcp if 'mariadb' service is not recognized
      ansible.posix.firewalld:
        port: 3306/tcp
        permanent: yes
        state: enabled
      when: ansible_os_family == "RedHat" # RHEL specific check

    - name: Reload firewalld to apply changes
      ansible.builtin.command: firewall-cmd --reload
      changed_when: true # Always mark as changed, as reload is not idempotent

    - name: Ensure SELinux is in enforcing mode
      ansible.builtin.selinux:
        state: enforcing
        policy: targeted

    - name: Allow httpd to connect to network databases (SELinux boolean)
      ansible.posix.seboolean:
        name: httpd_can_network_connect_db
        state: yes
        persistent: yes
```

**Playbook 3: `web_db_setup.yml` (Apache & MariaDB Configuration)**
```yaml
# web_db_setup.yml
- name: Configure Web and Database Servers
  hosts: webservers
  become: yes
  tasks:
    - name: Install Apache HTTP server
      ansible.builtin.dnf:
        name: httpd
        state: present

    - name: Start and enable httpd service
      ansible.builtin.systemd:
        name: httpd
        state: started
        enabled: yes

    - name: Deploy simple index.html
      ansible.builtin.copy:
        content: "
Ansible Deployed DevOps Web Server!
"
        dest: /var/www/html/index.html
        owner: root
        group: root
        mode: '0644'

    - name: Install MariaDB server
      ansible.builtin.dnf:
        name: mariadb-server
        state: present

    - name: Start and enable mariadb service
      ansible.builtin.systemd:
        name: mariadb
        state: started
        enabled: yes

    - name: Configure MariaDB root password and basic security
      ansible.builtin.community.mysql.mysql_user:
        name: root
        host: localhost
        password: "YOUR_MARIADB_ROOT_PASSWORD"
        login_unix_socket: /var/lib/mysql/mysql.sock # Use socket auth for root
      no_log: true # Prevent password from appearing in logs

    - name: Create database 'devops_app_db'
      community.mysql.mysql_db:
        name: devops_app_db
        state: present
        login_user: root
        login_password: "YOUR_MARIADB_ROOT_PASSWORD"

    - name: Create database user 'devops_user'
      community.mysql.mysql_user:
        name: devops_user
        password: "Pa$$w0rd"
        host: localhost
        priv: "devops_app_db.*:ALL"
        state: present
        login_user: root
        login_password: "YOUR_MARIADB_ROOT_PASSWORD"
```
*(Note: `community.mysql.mysql_user` aur `mysql_db` modules ke liye, aapko `ansible-galaxy collection install community.mysql` run karna pad sakta hai Control Node par.)*

**Step 3.4: Running the Playbooks**
Ab Control Node par se in playbooks ko run karein:

```bash
# Control Node par
ansible-playbook -i inventory.ini storage_setup.yml
ansible-playbook -i inventory.ini base_setup.yml
ansible-playbook -i inventory.ini web_db_setup.yml
```
Ya aap ek master playbook banakar sabko ek saath bhi run kar sakte hain:
`master_deploy.yml`
```yaml
- import_playbook: storage_setup.yml
- import_playbook: base_setup.yml
- import_playbook: web_db_setup.yml
```
Then run: `ansible-playbook -i inventory.ini master_deploy.yml`

**Verification:**
Playbooks run hone ke baad, Managed Node par login karke ya browser se access karke verify karein:
*   `df -hT` se check karein ki LVs mounted hain.
*   `systemctl status httpd mariadb` se services check karein.
*   `firewall-cmd --list-all` se firewall rules check karein.
*   Browser se Managed Node ka IP access karein.
*   `mysql -u devops_user -p` se database connect karein.

---

### 4. Practice Tasks

Ab jab aapne basic setup automate kar liya hai, yahan kuch extra tasks hain jo aap try kar sakte ho to enhance your RHCSA + RHCE skills:

1.  **Virtual Host Configuration (RHCE):**
    *   Apache mein ek naya Virtual Host setup karein (e.g., `www.example.com`).
    *   Iske liye ek alag document root (`/var/www/vhosts/example.com`) banayein.
    *   Ansible playbook banayein jo is Virtual Host ko configure kare.

2.  **NFS File Sharing (RHCE):**
    *   Managed Node par ek NFS share (`/shared_data`) create karein.
    *   Ek naya client machine banayein.
    *   NFS share ko client machine par mount karein.
    *   Ansible playbooks banayein NFS server aur client configuration ke liye.

3.  **Cron Job Automation (RHCSA/RHCE):**
    *   Ek cron job setup karein jo har 5 minute mein `/var/log/httpd/access_log` se last 10 lines ko ek file `/tmp/httpd_recent_access.log` mein copy kare.
    *   Is cron job ko Ansible se deploy karein.

4.  **SELinux Custom Policy (RHCSA/RHCE Advanced):**
    *   Ek web application banayein jo `/opt/my_custom_app` mein store ho.
    *   Is directory ke liye custom SELinux policy rules banayein taaki Apache usko serve kar sake (agar default `httpd_sys_content_t` sufficient nahi hai).
    *   Isko Ansible se automate karein.

5.  **Role-Based Access Control (RHCSA/RHCE):**
    *   Ek naya user `dbadmin` banayein jiske paas sirf MariaDB database ko manage karne ki permissions hon.
    *   Ansible se is user ko deploy karein.

6.  **Ansible Vault (RHCE Security):**
    *   Apne database passwords ko plaintext mein playbooks mein store karne ki bajaye, Ansible Vault ka use karke encrypt karein.
    *   Playbooks ko vault ke saath run karein.

7.  **Troubleshooting:**
    *   Janboojh kar firewall rule delete karein aur web server ko access karne ki koshish karein. Fir troubleshoot karein.
    *   SELinux ko `permissive` mode mein daalkar dekhein ki kya problem solve hoti hai, fir usko `enforcing` mode mein laakar sahi policy apply karein.

---

### 5. Pro Tips / Common Mistakes

**Pro Tips:**

*   **Practice, Practice, Practice:** Theory padhne se zyada, hands-on practice bahut zaroori hai. Har task ko khud se karke dekhein.
*   **Use Snapshots:** VMs par kaam karte samay, important stages par snapshots zaroor lein. Agar kuch galat ho jaye, toh revert karna aasan hoga.
*   **Read Documentation:** `man` pages, `info` pages, aur Red Hat documentation (`access.redhat.com`) aapke best friends hain. Ansible modules ki documentation bhi dekhein (`ansible-doc module_name`).
*   **Break Down Problems:** Bade problems ko chote, manageble tasks mein divide karein. Ek time par ek hi problem solve karein.
*   **Idempotency (Ansible):** Ansible playbooks idempotent hone chahiye, matlab unhe kitni bhi baar run karo, server state wahi rahegi jo aap define karna chahte ho. Agar resource already exist karta hai, toh Ansible use skip kar dega.
*   **Test After Every Step:** Har major configuration change ke baad test karein ki sab theek se kaam kar raha hai.
*   **Version Control:** Apne Ansible playbooks aur inventory files ko Git jaise version control system mein store karein. Yeh changes track karne aur collaborate karne mein helpful hoga.

**Common Mistakes to Avoid:**

*   **Forgetting `firewall-cmd --reload`:** Naye firewall rules add karne ke baad `reload` karna bhool jate hain, aur rules apply nahi hote.
*   **SELinux Issues:** SELinux ko disable kar dena (galti se ya jaan boojh kar) instead of understanding aur correct policy apply karna. Exam mein SELinux ko disable karna allowed nahi hai. Hamesha `audit2allow` aur `semanage` ka use karein.
*   **Incorrect `fstab` Entries:** Galat `fstab` entry system ko boot hone se rok sakti hai. Hamesha `mount -a` se test karein.
*   **Ansible `become: yes` Bhool Jana:** Jin tasks ko root privileges ki zaroorat hoti hai, wahan `become: yes` specify karna bhool jate hain.
*   **SSH Key Misconfiguration:** Ansible ke liye passwordless SSH key authentication bahut important hai. Agar yeh sahi se set up nahi hai, toh connection issues honge.
*   **Not Checking Logs:** Jab koi service kaam na kare, toh logs check karna bhool jate hain. `journalctl -u service_name`, `/var/log/messages`, `/var/log/httpd/error_log` bahut helpful hote hain.
*   **Ignoring Ansible Warnings/Errors:** Output mein warnings ya errors ko dhyaan se na padhna, jisse actual problem ka pata nahi chalta.
*   **Hardcoding Passwords:** Production mein passwords ko playbooks mein directly likhna unsafe hai. Ansible Vault ka use karein.

---

I hope yeh comprehensive combined lab aapko RHCSA aur RHCE concepts ko jodkar ek practical way mein samajhne mein madad karega. Best of luck for your learning journey! Keep practicing!
