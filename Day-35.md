Day 35: Revision & mini Ansible practice (Install Ansible, configure control node, inventory Basic ad-hoc commands (ansible all -m ping) Simple playbooks (YAML basics) Ansible modules for users, packages, and services Variables & facts (ansible_facts) Loops & conditionals in playbooks)
---
नमस्ते future Linux experts! Aaj hum ek bahut hi important aur powerful topic revise karne wale hain jo aapko RHCSA aur RHCE dono mein strong banayega: **Ansible**. Yeh ek aisa tool hai jo aapke system administration ke tasks ko automate karke aapka life bahut easy bana dega. Chalo, shuru karte hain!

---

# RHCSA + RHCE Training: Ansible Revision & Mini Practice

### 1. Concept Explanation (Hinglish)

Dekho, modern IT mein manual tasks karne ka time nahi hai. Har cheez ko automate karna padta hai. Aur automation ke liye, **Ansible** ek superstar hai.

**Ansible Kya Hai?**
Ansible ek open-source **automation engine** hai. Iska kaam hai configurations manage karna, application deployment karna, aur system administration tasks automate karna.
*   **Agentless:** Sabse badi baat, iske liye aapko apne managed servers par koi agent ya software install nahi karna padta. Yeh sirf standard SSH use karta hai communication ke liye. Amazing, right?
*   **Simple YAML:** Iski configurations aur playbooks YAML (YAML Ain't Markup Language) mein likhe jaate hain, jo ki human-readable aur easy to understand hai.
*   **Idempotent:** Iska matlab hai ki aap ek task ko kitni baar bhi run karo, result hamesha same rahega. Agar koi change already present hai, toh Ansible use dobara nahi karega. Yeh system ko desired state mein rakhta hai.

**Key Components:**

*   **Control Node:** Jis machine se aap Ansible run karte ho. Aapke playbooks aur inventory files yahi par hote hain.
*   **Managed Nodes (Target Hosts):** Woh servers jin par Ansible operations perform karta hai.
*   **Inventory:** Ek file jisme aap apne managed nodes ki list rakhte ho (IP addresses ya hostnames). Aap groups bhi bana sakte ho (e.g., `[webservers]`, `[dbservers]`).
*   **Modules:** Chote programs jo Ansible execute karta hai managed nodes par. Har module ka ek specific task hota hai (e.g., `ping` for connectivity, `yum` for package installation, `user` for user management, `service` for service control).
*   **Ad-hoc Commands:** Single commands jo aap jaldi se ek task perform karne ke liye run karte ho, bina playbook likhe.
*   **Playbooks:** Ye Ansible ke heart hain. Ye ek YAML file mein likhe hue ordered list of tasks hain. Playbooks se aap complex deployments aur configuration changes automate karte ho.
*   **Variables:** Dynamic values jo aap apne playbooks mein use karte ho taaki wo more flexible aur reusable ban sake.
*   **Facts (`ansible_facts`):** Managed nodes ke baare mein collected information (OS type, memory, network interfaces, etc.). Ansible automatically inhein gather karta hai. Aap in facts ko apne playbooks mein conditions ya logic ke liye use kar sakte ho.
*   **Loops:** Jab aapko ek hi task ko multiple items par run karna ho (e.g., multiple packages install karna).
*   **Conditionals:** Jab aapko ek task ko tabhi run karna ho jab koi specific condition meet ho (e.g., ek package install karo agar OS RedHat family ka ho).

**RHCSA aur RHCE mein iska importance:**
RHCSA mein aapko basic automation concepts aur thode ad-hoc commands pata hone chahiye. RHCE mein toh Ansible hi main focus hai! Aapko complex playbooks likhna, roles create karna, vault use karna, aur dynamic inventories manage karna aana chahiye. Aaj hum uski foundation banayenge.

---

### 2. Key Linux Commands (Hinglish)

Yahan kuch important commands hain jo Ansible ke saath use hote hain:

*   **`sudo dnf install ansible`**:
    *   **Kya karta hai:** Red Hat-based systems (jaise RHEL, CentOS, Fedora) par Ansible software ko install karta hai.
    *   **Explanation:** `dnf` package manager hai. `sudo` root privileges deta hai. Is command se aapki control node par Ansible ready ho jaayega.

*   **`ssh-keygen`**:
    *   **Kya karta hai:** SSH keys generate karta hai (public aur private key pair).
    *   **Explanation:** Ansible managed nodes se communicate karne ke liye SSH use karta hai. Passwordless SSH setup karna best practice hai, aur uske liye keys generate karna pehla step hai.

*   **`ssh-copy-id user@managed_node_ip`**:
    *   **Kya karta hai:** Aapki public SSH key ko managed node par copy karta hai, `~/.ssh/authorized_keys` file mein add karta hai.
    *   **Explanation:** Ek baar key copy ho gayi, toh aap bina password ke managed node par SSH kar sakte ho. Yeh Ansible ke liye bahut zaroori hai.

*   **`ansible --version`**:
    *   **Kya karta hai:** Installed Ansible ki version information dikhata hai.
    *   **Explanation:** Hamesha check karo ki aapka Ansible properly installed hai aur kaunsa version run ho raha hai.

*   **`ansible all -m ping`**:
    *   **Kya karta hai:** Aapke inventory ke sabhi hosts par `ping` module run karta hai to check connectivity.
    *   **Explanation:** Yeh ek basic ad-hoc command hai connectivity test karne ke liye. Output mein `pong` aana chahiye agar connectivity theek hai.

*   **`ansible-doc `**:
    *   **Kya karta hai:** Kisi bhi Ansible module ki comprehensive documentation dikhata hai.
    *   **Explanation:** Agar aapko kisi module ke parameters, examples ya usage ke baare mein jaanna hai, toh yeh command aapka best friend hai. For example, `ansible-doc yum` ya `ansible-doc user`.

*   **`ansible-playbook `**:
    *   **Kya karta hai:** Ek Ansible playbook ko execute karta hai.
    *   **Explanation:** Jab aapne apni automation logic YAML file mein likh li hai, toh use run karne ke liye yeh command use hota hai.

*   **`ansible-playbook  --check`**:
    *   **Kya karta hai:** Playbook ko dry-run mode mein run karta hai. Changes apply nahi hote, bas dikhata hai ki kya changes honge.
    *   **Explanation:** Bahut useful hai changes apply karne se pehle verify karne ke liye. Always use this first!

*   **`ansible-playbook  --diff`**:
    *   **Kya karta hai:** `check` mode ke saath, yeh actual diff (differences) bhi dikhata hai jo files mein honge.
    *   **Explanation:** Bahut helpful hai changes ko visualize karne ke liye.

*   **`vi /etc/ansible/hosts` (ya custom inventory file path)**:
    *   **Kya karta hai:** Ansible ke default inventory file ko open karta hai editing ke liye.
    *   **Explanation:** Yahan aap apne managed hosts ko list karte ho, unhein groups mein divide karte ho. `vi` ek text editor hai.

*   **`ansible-inventory -i  --list`**:
    *   **Kya karta hai:** Ek specific inventory file ke content ko parse karke JSON format mein list karta hai.
    *   **Explanation:** Verify karne ke liye ki aapka inventory file sahi se parsed ho raha hai ya nahi.

---

### 3. Step-by-Step Examples

Chalo, real-world examples dekhte hain!

**Assumptions:**
*   Aapke paas ek **Control Node** (jahan Ansible install hoga) aur kam se kam do **Managed Nodes** (jin par Ansible commands run honge) hain. Sabhi RHEL/CentOS based hain.
*   Control Node se Managed Nodes tak basic network connectivity hai.

---

#### Example 1: Ansible Control Node Setup aur Inventory Configuration

**Goal:** Control Node par Ansible install karna, passwordless SSH setup karna, aur inventory file banana.

1.  **Control Node par Ansible Install karein:**
    ```bash
    # Control Node par
    sudo dnf install ansible -y
    ```
    *   Output mein installation progress dikhega.

2.  **Passwordless SSH Keys Generate karein (Control Node par):**
    ```bash
    # Control Node par
    ssh-keygen -t rsa -b 4096 -N "" -f ~/.ssh/id_rsa
    ```
    *   `id_rsa` (private key) aur `id_rsa.pub` (public key) generate ho jaayenge `~/.ssh/` directory mein.

3.  **Public Key Managed Nodes par Copy karein:**
    *   Apne Managed Nodes ke IP address ya hostname replace karein.
    *   Pehli baar jab aap yeh command run karenge, toh aapko managed node ka password enter karna padega.
    ```bash
    # Control Node par
    ssh-copy-id root@192.168.1.101 # Managed Node 1 ka IP
    ssh-copy-id root@192.168.1.102 # Managed Node 2 ka IP
    # Aap kisi aur user ke liye bhi kar sakte hain, agar woh user sudo access rakhta hai.
    # ssh-copy-id devops@192.168.1.101
    ```
    *   **Verification:** Try `ssh root@192.168.1.101` from Control Node. It should log in without asking for a password.

4.  **Ansible Inventory File Configure karein:**
    *   Hum default inventory file use karenge.
    ```bash
    # Control Node par
    sudo vi /etc/ansible/hosts
    ```
    *   File mein yeh content add karein (ya existing content modify karein):
        ```ini
        # /etc/ansible/hosts

        [webservers]
        web1.example.com ansible_host=192.168.1.101
        web2.example.com ansible_host=192.168.1.102

        [dbservers]
        db1.example.com ansible_host=192.168.1.102 # You can assign a host to multiple groups

        [all:vars]
        ansible_user=root
        # agar aap root user use nahi kar rahe, toh isko us user ke naam se badal dein
        # aur ensure karein ki woh user sudo access rakhta hai.
        # ansible_python_interpreter=/usr/bin/python3 # Agar managed node par python3 path alag hai
        ```
    *   `ansible_host`: Host ka IP ya hostname.
    *   `ansible_user`: SSH login ke liye kaunsa user use karna hai.

5.  **Connectivity Test karein (Ad-hoc Command):**
    ```bash
    # Control Node par
    ansible all -m ping
    ansible webservers -m ping
    ```
    *   **Expected Output:**
        ```
        web1.example.com | SUCCESS => {
            "ansible_facts": {
                "discovered_interpreter_python": "/usr/bin/python"
            },
            "changed": false,
            "ping": "pong"
        }
        ... (for other hosts)
        ```
    *   `"ping": "pong"` matlab connectivity sahi hai!

---

#### Example 2: Basic Ad-hoc Commands

Ad-hoc commands single tasks ke liye best hain.

1.  **Check uptime on all hosts:**
    ```bash
    # Control Node par
    ansible all -m command -a "uptime"
    ```
    *   `command` module kisi bhi arbitrary command ko run karta hai. `-a` argument pass karne ke liye hai.

2.  **Install `htop` on `webservers` group:**
    ```bash
    # Control Node par
    ansible webservers -m yum -a "name=htop state=present" --become
    ```
    *   `yum` module package management ke liye hai (RHEL/CentOS).
    *   `name=htop`: `htop` package install karna hai.
    *   `state=present`: Ensure karein ki package install ho.
    *   `--become`: `sudo` privileges ke liye (jaisa `sudo yum install htop` karte hain).

3.  **Start `httpd` service on `webservers` (agar installed hai):**
    ```bash
    # Control Node par
    ansible webservers -m service -a "name=httpd state=started" --become
    ```
    *   `service` module services manage karta hai (start, stop, restart, enable).
    *   `name=httpd`: `httpd` service.
    *   `state=started`: Ensure karein ki service running hai.

---

#### Example 3: Simple Playbook (YAML Basics) - User, Package, Service

**Goal:** Ek playbook likhna jo ek naya user banaye, ek package install kare, service start kare, aur firewall allow kare.

1.  **Playbook File Create karein:**
    ```bash
    # Control Node par
    vi first_playbook.yml
    ```
2.  **Playbook Content:**
    ```yaml
    # first_playbook.yml
    --- # YAML file ki shuruaat indicate karta hai

    - name: Configure Web Server and User
      hosts: webservers # Ye playbook webservers group par run hoga
      become: yes       # Sabhi tasks root privileges ke saath run honge (sudo)

      tasks: # List of tasks
        - name: Create a new user 'devops'
          user:
            name: devops
            state: present
            shell: /bin/bash
            home: /home/devops

        - name: Install Nginx web server
          yum:
            name: nginx
            state: present

        - name: Ensure Nginx service is running and enabled
          service:
            name: nginx
            state: started
            enabled: yes

        - name: Allow Nginx HTTP port (80) through firewalld
          firewalld:
            service: http
            permanent: yes
            state: enabled
            immediate: yes # Apply changes immediately
    ```

3.  **Run the Playbook (first with --check, then for real):**
    ```bash
    # Control Node par
    ansible-playbook first_playbook.yml --check --diff # Pehle check aur diff dekho
    ansible-playbook first_playbook.yml # Fir run karo
    ```
    *   **Verification (Managed Node par):**
        ```bash
        # Managed Node par
        id devops
        sudo dnf list installed nginx
        sudo systemctl status nginx
        sudo firewall-cmd --list-all
        ```
    *   You should see the `devops` user, `nginx` installed, running, and firewall rule active.

---

#### Example 4: Variables & Facts (`ansible_facts`)

**Goal:** Playbook mein variables use karna aur system facts access karna.

1.  **Playbook File Create karein:**
    ```bash
    # Control Node par
    vi vars_facts_playbook.yml
    ```
2.  **Playbook Content:**
    ```yaml
    # vars_facts_playbook.yml
    ---

    - name: Manage packages based on variables and facts
      hosts: webservers
      become: yes

      vars: # Yahan hum variables define kar rahe hain
        my_package: httpd
        my_service: httpd
        my_user: automation_user

      tasks:
        - name: Gather facts (optional, Ansible does this by default)
          setup: # Ye module sare facts collect karta hai

        - name: Print OS family (using a fact)
          debug:
            msg: "This host is running on {{ ansible_facts['os_family'] }} operating system."

        - name: Create a user using a variable
          user:
            name: "{{ my_user }}" # Variable ko curly braces mein wrap karte hain
            state: present

        - name: Install a package using a variable
          yum:
            name: "{{ my_package }}"
            state: present

        - name: Start and enable a service using a variable
          service:
            name: "{{ my_service }}"
            state: started
            enabled: yes
    ```
    *   `ansible_facts['os_family']`: Yeh `setup` module se collected facts mein se `os_family` fact ko access kar raha hai.

3.  **Run the Playbook:**
    ```bash
    # Control Node par
    ansible-playbook vars_facts_playbook.yml
    ```
    *   **Expected Output:** Aap `debug` module ka output dekhenge jisme OS family likhi hogi, aur baaki tasks run honge.

---

#### Example 5: Loops & Conditionals in Playbooks

**Goal:** Playbook mein multiple items par loop karna aur specific conditions ke base par tasks run karna.

1.  **Playbook File Create karein:**
    ```bash
    # Control Node par
    vi loops_conditionals_playbook.yml
    ```
2.  **Playbook Content:**
    ```yaml
    # loops_conditionals_playbook.yml
    ---

    - name: Practice Loops and Conditionals
      hosts: all
      become: yes

      tasks:
        - name: Install multiple packages using a loop
          yum:
            name: "{{ item }}"
            state: present
          loop: # List of items to loop through
            - vim
            - wget
            - zip
            - unzip
          when: ansible_facts['os_family'] == "RedHat" # Ye task tabhi chalega agar OS RedHat family ka hai

        - name: Only install EPEL repo if it's a RedHat based system and not already present
          yum:
            name: epel-release
            state: present
          when:
            - ansible_facts['os_family'] == "RedHat"
            - "'epel-release' not in ansible_facts['packages']" # Yahan thoda advanced logic hai, checks if package is installed

        - name: Print a message if the host has less than 2GB RAM
          debug:
            msg: "Warning: This host ({{ inventory_hostname }}) has low RAM ({{ ansible_facts['memtotal_mb'] }} MB)."
          when: ansible_facts['memtotal_mb'] < 2048 # Condition based on RAM size

        - name: Create multiple directories using a loop
          file:
            path: "/opt/app/{{ item }}"
            state: directory
            mode: '0755'
          loop:
            - logs
            - backups
            - config
    ```
    *   `loop:`: `item` variable ke through list ke har item par task run hoga.
    *   `when:`: Condition. Agar condition `True` hogi toh hi task run hoga.

3.  **Run the Playbook:**
    ```bash
    # Control Node par
    ansible-playbook loops_conditionals_playbook.yml
    ```
    *   **Expected Output:** Aap dekhenge ki packages install honge (agar OS RedHat hai), directories banengi, aur RAM warning tabhi print hogi jab condition match hogi.

---

### 4. Practice Tasks

Ab aapki baari hai! In tasks ko apne lab setup mein try karein:

1.  **Basic Setup & Test (RHCSA Level):**
    *   Ek naya Control Node aur kam se kam **do Managed Nodes** prepare karein.
    *   Control Node par Ansible install karein.
    *   Managed Nodes par `root` user ke liye passwordless SSH configure karein.
    *   Ek custom inventory file (`my_inventory.ini` naam se) banayein. Isme `prod_web` aur `dev_db` naam ke do groups ho, aur har group mein kam se kam ek host ho.
    *   `ansible -i my_inventory.ini all -m ping` command se connectivity test karein.
    *   `ansible -i my_inventory.ini prod_web -m command -a "df -h"` command se `prod_web` hosts par disk usage check karein.

2.  **Ad-hoc Operations (RHCSA Level):**
    *   Apne `dev_db` group ke sabhi hosts par `mariadb-server` package install karein (agar RedHat-based hai).
    *   `prod_web` group ke hosts par `firewalld` service ko stop aur disable karein (caution: production mein aisa na karein, this is for practice).
    *   `dev_db` group ke hosts par `/tmp/ansible_test.txt` naam ki file create karein jisme "Hello from Ansible" content ho using `ansible all -m copy -a "content='Hello from Ansible' dest=/tmp/ansible_test.txt"`

3.  **Playbook Creation (RHCE Level):**
    *   Ek playbook (`system_config.yml`) likhein jo:
        *   `devops_admin` naam ka user create kare aur use `wheel` group mein add kare.
        *   `git` aur `vim-enhanced` packages install kare.
        *   `httpd` service install kare aur ensure kare ki woh `running` aur `enabled` hai.
        *   `firewalld` mein `http` service permanently allow kare.
        *   Yeh playbook **sirf `prod_web` group par apply ho**.
    *   Playbook ko `ansible-playbook system_config.yml --check --diff` se test karein, fir run karein.
    *   Managed Node par changes verify karein.

4.  **Loops & Conditionals Practice (RHCE Level):**
    *   Apne `system_config.yml` playbook ko modify karein:
        *   `git` aur `vim-enhanced` packages ko **ek single task mein loop use karke install karein**.
        *   `httpd` service install aur configure karne wala task **tabhi run karein jab `ansible_distribution` fact `CentOS` ho**. (Hint: `when: ansible_facts['distribution'] == "CentOS"`)
        *   Ek `debug` task add karein jo `ansible_architecture` fact print kare.
    *   Playbook run karein aur output observe karein.

---

### 5. Pro Tips / Common Mistakes

**Pro Tips:**

*   **`ansible-doc` is your Best Friend:** Kisi bhi module ki functionality, parameters, aur examples ke liye hamesha `ansible-doc ` use karein.
*   **Always Use `--check` and `--diff`:** Playbook ko run karne se pehle, hamesha `ansible-playbook --check --diff` se dry-run karein. Yeh aapko batayega ki kya changes honge, bina unhein apply kiye. Bohot saare headaches se bach jaoge.
*   **Version Control (Git):** Apne playbooks ko Git jaise version control system mein rakhein. Changes track karna aur collaborate karna easy ho jaata hai.
*   **Idempotency ki Samjh:** Yaad rakho, Ansible idempotent hai. Apne tasks aise likho ki unhein baar-baar run karne par bhi system par undesirable side effects na hon. Jaise, `state: present` for packages.
*   **Roles ka Use karein:** Jab aapke playbooks bade aur complex ho jayein, toh roles use karein. Yeh playbooks ko structured aur reusable banate hain. RHCE mein roles bahut important hain.
*   **Ansible Vault:** Sensitive information (jaise passwords, API keys) ko playbooks mein plain text mein na rakhein. Ansible Vault use karke unhein encrypt karein.
*   **Python Interpreter:** Kai baar RHEL-based systems par `python3` default nahi hota ya path alag hota hai. Aap `ansible.cfg` mein ya inventory mein `ansible_python_interpreter=/usr/bin/python3` specify kar sakte hain.

**Common Mistakes to Avoid:**

*   **YAML Indentation Errors:** YAML spaces-sensitive hai. Incorrect indentation (spaces ki jagah tabs, ya galat number of spaces) syntax error deta hai. Hamesha 2 spaces ka indentation use karein.
*   **SSH Connectivity Issues:**
    *   Managed nodes tak reachability na hona (firewall blocks SSH port 22).
    *   `ssh-copy-id` na kiya hona ya galat user ke liye kiya hona.
    *   SSH keys ke permissions galat hona (`~/.ssh/id_rsa` should be `600`).
*   **`become: yes` Bhool Jaana:** Jab root privileges (sudo) ki zaroorat ho (packages install karna, services start karna, users add karna), toh `become: yes` ya `ansible_become=yes` use karna na bhulein.
*   **Inventory File Syntax Errors:** Hosts ya groups ko galat tarike se define karna.
*   **Module Parameters Galat Dena:** `ansible-doc` use na karna aur galat module parameters type karna.
*   **Testing na karna:** Directly production par playbook run karna without `--check` ya testing on dev environment.
*   **Variables ko Correctly Access na Karna:** Variables ko `{{ variable_name }}` syntax mein wrap karna bhool jaana.

---

Umeed hai yeh comprehensive lesson aapko Ansible ki duniya mein ek strong foundation dega. Practice karte raho, aur aap dekhenge ki Ansible kitna powerful aur useful tool hai. Best of luck for your RHCSA & RHCE journey! ������
