Day 34: Loops & conditionals in playbooks
---
Namaste, aur swagat hai aap sabhi ka is comprehensive RHCSA + RHCE training session mein! Aaj hum Ansible playbooks ke bohot hi important topics par baat karenge: **Loops aur Conditionals**. Ye do cheezein aapke playbooks ko smart, efficient aur dynamic banati hain.

---

## Comprehensive RHCSA + RHCE Training: Loops & Conditionals in Playbooks

### 1. Concept Explanation (Hinglish)

Ansible playbooks, hum sab jante hain, automated tasks run karne ke liye use hote hain. Lekin kya ho agar aapko ek hi task multiple times karna ho, ya phir koi task sirf tab hi karna ho jab koi specific condition meet ho? Yahi par **Loops** aur **Conditionals** picture mein aate hain.

#### Loops in Ansible (दोहराव - Repetition)

Socho, aapko 10 alag-alag packages install karne hain, ya 5 alag-alag users create karne hain. Kya aap har package ya har user ke liye alag-alag task likhenge? Bilkul nahi! Yahi loops ka magic hai.

*   **क्या हैं Loops?**
    Loops aapko ek hi task ko multiple times execute karne ki facility dete hain, har baar ek naye item ke saath. Isse aapka playbook chota, clean aur easy-to-manage ho jaata hai. Aapko repetitive tasks likhne ki zaroorat nahi padti.
*   **ज़रूरत क्यों है?**
    *   Multiple packages install karna.
    *   Multiple users banana.
    *   Multiple files deploy karna.
    *   Multiple services manage karna.
    *   Database entries add karna.
    *   ...aur bhi bohot kuch!
*   **कैसे काम करता है?**
    Ansible `loop` keyword ka use karta hai. Jab aap `loop` use karte hain, toh Ansible har iteration mein current item ko ek special variable `item` mein store karta hai. Aap us `item` variable ko apne task mein use kar sakte hain.

    **Common Loop Types:**
    *   **Simple List Loop (`loop`):** Sabse basic, list of strings ya numbers par iterate karta hai.
        ```yaml
        - name: Install multiple packages
          ansible.builtin.package:
            name: "{{ item }}"
            state: present
          loop:
            - httpd
            - mariadb-server
            - php
        ```
    *   **List of Dictionaries Loop (`loop`):** Jab aapke items mein multiple properties hon (jaise user name, UID, groups).
        ```yaml
        - name: Create multiple users
          ansible.builtin.user:
            name: "{{ item.name }}"
            uid: "{{ item.uid }}"
            groups: "{{ item.groups }}"
            state: present
          loop:
            - { name: 'devuser', uid: 1001, groups: 'developers' }
            - { name: 'testuser', uid: 1002, groups: 'testers' }
        ```
    *   **`loop_control`:** Ye ek optional keyword hai jisse aap `item` variable ka default naam change kar sakte ho, ya loop ki behaviour control kar sakte ho. Isse readability improve hoti hai.
        ```yaml
        - name: Install packages with custom loop variable
          ansible.builtin.package:
            name: "{{ pkg }}" # 'item' ki jagah 'pkg' use kiya
            state: present
          loop:
            - htop
            - tree
          loop_control:
            loop_var: pkg # Specify custom variable name
        ```

#### Conditionals in Ansible (शर्तें - Conditions)

Kabhi-kabhi aapko koi task sirf tabhi run karna hota hai jab ek specific condition true ho. Jaise, Apache web server sirf CentOS machines par install karna, ya koi file sirf tabhi create karna jab wo exist na karti ho. Yahi par conditionals kaam aate hain.

*   **क्या हैं Conditionals?**
    Conditionals aapko apne playbook mein logic add karne ki permission dete hain. Aap specify karte ho ki ek task kab execute hona chahiye based on certain conditions.
*   **ज़रूरत क्यों है?**
    *   Specific OS par hi koi package install karna.
    *   Koi service tabhi start karna agar wo stopped ho.
    *   Ek directory tabhi banana agar wo exist na karti ho.
    *   Configurations apply karna based on host variables.
*   **कैसे काम करता है?**
    Ansible primarily `when` keyword ka use karta hai. `when` clause mein aap ek expression likhte ho jo true ya false evaluate hota hai. Agar expression true hai, toh task execute hoga; agar false hai, toh task skip ho jaayega.

    *   **Using `ansible_facts`:** Ansible automatically hosts ke baare mein bohot saari information collect karta hai, jise `ansible_facts` kehte hain. Aap in facts ka use conditionals mein kar sakte ho.
        ```yaml
        - name: Install Apache only on CentOS
          ansible.builtin.package:
            name: httpd
            state: present
          when: ansible_distribution == "CentOS"
        ```
    *   **Using `register` output:** Aap kisi previous task ke output ko `register` keyword se ek variable mein store kar sakte ho, aur phir us variable ka use apne `when` condition mein kar sakte ho.
        ```yaml
        - name: Check if a file exists
          ansible.builtin.stat:
            path: /tmp/my_file.txt
          register: file_check

        - name: Create file if it does not exist
          ansible.builtin.copy:
            content: "This file was created by Ansible."
            dest: /tmp/my_file.txt
          when: not file_check.stat.exists # file_check variable ke output ka use kiya
        ```
    *   **Logical Operators:** Aap multiple conditions ko combine karne ke liye `and`, `or`, `not` ka use kar sakte ho.
        ```yaml
        - name: Install Nginx if it's Ubuntu and server role is web
          ansible.builtin.package:
            name: nginx
            state: present
          when: ansible_distribution == "Ubuntu" and inventory_hostname in groups['webservers']
        ```

#### Loops aur Conditionals ka Combination

Ye dono milkar aapke playbooks ko incredibly powerful banate hain. Aap multiple items par loop kar sakte ho, aur har item ke liye ek conditional check laga sakte ho.
**Example:** Aap users ki ek list par loop kar sakte ho, lekin sirf un users ko create kar sakte ho jinke UID 1000 se zyada hon. Ya phir services ki list par loop karke unhe restart kar sakte ho, lekin sirf tab jab wo currently stopped hon.

```yaml
- name: Iterate through services and start if stopped on Ubuntu
  ansible.builtin.service:
    name: "{{ item.name }}"
    state: started
  loop:
    - { name: 'apache2', enabled: true }
    - { name: 'nginx', enabled: false }
    - { name: 'mariadb', enabled: true }
  when: item.enabled and ansible_distribution == "Ubuntu"
```

---

### 2. Key Ansible Constructs & Linux Commands (Hinglish)

Loops aur Conditionals directly Linux commands nahi hain, balki Ansible playbook ke andar use hone wale keywords aur constructs hain. Yahan kuch important terms hain jo aapko pata hone chahiye:

#### Ansible Constructs (प्लेबुक के अंदर के कीवर्ड्स)

*   `loop`: Ye keyword list, dictionary, ya sequence par iterate karne ke liye use hota hai.
*   `when`: Ye keyword ek condition specify karne ke liye use hota hai jiske basis par task execute hoga ya skip hoga.
*   `register`: Pichle task ke output ko ek variable mein capture karne ke liye. Iska use aksar `when` conditions ke saath hota hai.
*   `item`: Loop ke har iteration mein current element ko represent karne wala default variable.
*   `loop_control`: `item` variable ka naam badalne ya loop ki behaviour modify karne ke liye. (e.g., `loop_var: custom_name`)
*   `ansible_facts`: Host machine ke baare mein collected information (OS, IP, memory, etc.) jo conditionals mein use ki jaati hai.
*   `debug`: Variables ki values, ya kisi task ke output ko console par print karne ke liye. Troubleshooting ke liye bohot useful hai.
    ```yaml
    - name: Print item value
      ansible.builtin.debug:
        var: item
      loop: [1, 2, 3]
    ```
*   `failed_when`: Custom error handling ke liye. Aap specify kar sakte ho ki kab ek task ko "failed" mana jaaye, even if the underlying command exits successfully. Ye `register` ke output ke saath use hota hai.
    ```yaml
    - name: Check free disk space
      ansible.builtin.shell: df -h /opt | awk 'NR==2 {print $5}' | sed 's/%//'
      register: disk_usage_percent
      failed_when: disk_usage_percent.stdout | int > 90 # If disk usage is > 90%, mark as failed
    ```
*   `changed_when`: Ye control karne ke liye ki kab ek task ko "changed" mark kiya jaaye. Default mein Ansible output dekhta hai, but aap override kar sakte ho.
*   `block`: Ye ek saath multiple tasks par ek hi `when` condition ya `loop` apply karne ke liye use hota hai. RHCE ke liye kaafi useful hai.
    ```yaml
    - name: Configure web server if it's a web node
      when: inventory_hostname in groups['webservers']
      block:
        - name: Install httpd
          ansible.builtin.package:
            name: httpd
            state: present
        - name: Start httpd service
          ansible.builtin.service:
            name: httpd
            state: started
            enabled: true
    ```

#### Key Linux Commands (आपकी मशीन पर इस्तेमाल होने वाले कमांड्स)

*   `ansible-playbook`: Ye main command hai jisse aap apne Ansible playbooks ko run karte hain.
    *   `ansible-playbook your_playbook.yml`
    *   `ansible-playbook your_playbook.yml --syntax-check` (Syntax check ke liye)
    *   `ansible-playbook your_playbook.yml --check` (Dry run ke liye, changes apply nahi honge)
    *   `ansible-playbook your_playbook.yml --list-hosts`
    *   `ansible-playbook your_playbook.yml --limit webservers` (Specific hosts par run karne ke liye)
*   `vim` / `nano`: Playbook files (.yml), inventory files (.ini), aur variable files (.yml) ko edit karne ke liye text editors.
*   `ls`, `cat`, `grep`: Files ko list karne, content dekhne, aur search karne ke liye basic Linux commands.
    *   `ls -l playbooks/`
    *   `cat my_playbook.yml`
    *   `grep "when:" my_playbook.yml`

---

### 3. Step-by-Step Examples (Real-World Scenarios)

Chaliye kuch practical examples dekhte hain jahan loops aur conditionals kaise use hote hain.

**Setup:**
Aapke paas ek `inventory.ini` file honi chahiye, jaise:
```ini
[webservers]
web1 ansible_host=192.168.1.10
web2 ansible_host=192.168.1.11

[dbservers]
db1 ansible_host=192.168.1.20

[all:vars]
ansible_user=devops
ansible_private_key_file=~/.ssh/id_rsa
```
Make sure `devops` user exists on target machines and can sudo without password or prompt.

#### Example 1: Multiple Package Installation using `loop`

**Scenario:** Aapko `htop`, `tree`, aur `git` packages apne `webservers` par install karne hain.

1.  **Playbook File:** `install_packages.yml`
    ```yaml
    ---
    - name: Install essential packages
      hosts: webservers
      become: yes # Root privileges ke liye
      tasks:
        - name: Ensure packages are present
          ansible.builtin.package:
            name: "{{ item }}"
            state: present
          loop:
            - htop
            - tree
            - git
    ```
2.  **Explanation:**
    *   `hosts: webservers`: Ye playbook sirf `webservers` group par chalega.
    *   `become: yes`: Tasks ko root privileges ke saath run karega (sudo).
    *   `loop`: Ye `htop`, `tree`, aur `git` ki list par iterate karega.
    *   `name: "{{ item }}"`: Har iteration mein, `item` variable current package ka naam hold karega, aur `package` module us package ko install karega.
3.  **Run Playbook:**
    ```bash
    ansible-playbook install_packages.yml
    ```
    Output mein aap dekhenge ki Ansible ne teen alag-alag baar `package` task run kiya, har baar ek naye package ke saath.

#### Example 2: Creating Multiple Users using `loop` with dictionaries

**Scenario:** Aapko do naye users (`devuser`, `testuser`) create karne hain with specific UIDs aur groups.

1.  **Playbook File:** `create_users.yml`
    ```yaml
    ---
    - name: Create multiple users with specific properties
      hosts: all # Sabhi hosts par
      become: yes
      tasks:
        - name: Create users with custom UIDs and groups
          ansible.builtin.user:
            name: "{{ item.name }}"
            uid: "{{ item.uid }}"
            groups: "{{ item.groups }}"
            state: present
          loop:
            - { name: 'devuser', uid: 1001, groups: 'developers,sudo' }
            - { name: 'testuser', uid: 1002, groups: 'testers' }
            - { name: 'adminuser', uid: 1003, groups: 'wheel', shell: '/bin/bash' }
    ```
2.  **Explanation:**
    *   `loop`: Ab list of dictionaries use kar rahe hain. Har dictionary mein user ka `name`, `uid`, aur `groups` define kiye gaye hain.
    *   `name: "{{ item.name }}"`, `uid: "{{ item.uid }}"`, `groups: "{{ item.groups }}"`: `item` ab ek dictionary hai, toh hum `item.key` syntax se uske values access karte hain.
3.  **Run Playbook:**
    ```bash
    ansible-playbook create_users.yml
    ```

#### Example 3: Conditional Service Start based on OS (`when` + `ansible_facts`)

**Scenario:** Aapko Apache web server start karna hai, lekin sirf un machines par jo CentOS ya RedHat hain, aur Nginx sirf Ubuntu par.

1.  **Playbook File:** `manage_web_services.yml`
    ```yaml
    ---
    - name: Manage web services based on OS
      hosts: webservers
      become: yes
      tasks:
        - name: Install and start Apache on RHEL/CentOS
          ansible.builtin.service:
            name: httpd
            state: started
            enabled: true
          when: ansible_distribution == "CentOS" or ansible_distribution == "RedHat"

        - name: Install and start Nginx on Ubuntu
          ansible.builtin.service:
            name: nginx
            state: started
            enabled: true
          when: ansible_distribution == "Ubuntu"
    ```
2.  **Explanation:**
    *   `when: ansible_distribution == "CentOS" or ansible_distribution == "RedHat"`: Ye task sirf tab chalega jab `ansible_distribution` fact ki value "CentOS" ya "RedHat" ho.
    *   `when: ansible_distribution == "Ubuntu"`: Ye task sirf tab chalega jab `ansible_distribution` fact ki value "Ubuntu" ho.
    *   Ansible har target host se `ansible_facts` collect karta hai automatically, jisme `ansible_distribution` bhi shaamil hai.
3.  **Run Playbook:**
    ```bash
    ansible-playbook manage_web_services.yml
    ```
    Aap dekhenge ki non-matching OS par tasks skip ho jayenge (`skipping` message).

#### Example 4: Conditional File Creation using `register`

**Scenario:** Aapko ek `/tmp/ansible_managed.txt` file banani hai, lekin sirf tab jab wo file already exist na karti ho.

1.  **Playbook File:** `create_file_conditionally.yml`
    ```yaml
    ---
    - name: Create file if it does not exist
      hosts: all
      become: yes
      tasks:
        - name: Check if /tmp/ansible_managed.txt exists
          ansible.builtin.stat: # Stat module file/directory ki info check karta hai
            path: /tmp/ansible_managed.txt
          register: file_status # Is task ka output 'file_status' variable mein save karo

        - name: Create the file if it doesn't exist
          ansible.builtin.copy:
            content: "This file was created by Ansible on {{ ansible_hostname }}."
            dest: /tmp/ansible_managed.txt
            mode: '0644'
          when: not file_status.stat.exists # 'file_status' variable ke andar 'stat.exists' property ko check kiya
          notify: File Created # Ek handler ko trigger kar sakte hain

      handlers:
        - name: File Created
          ansible.builtin.debug:
            msg: "/tmp/ansible_managed.txt was just created!"
    ```
2.  **Explanation:**
    *   `register: file_status`: `stat` module ke output ko `file_status` variable mein store karta hai. `stat` module output mein `exists`, `isreg`, `isdir` jaisi properties provide karta hai.
    *   `when: not file_status.stat.exists`: Dusra task tabhi execute hoga jab `file_status.stat.exists` false ho (yani file exist nahi karti).
    *   `notify: File Created`: Agar file create hoti hai, toh `File Created` handler ko run karega.
3.  **Run Playbook:**
    ```bash
    ansible-playbook create_file_conditionally.yml
    ```
    Pehli baar run karne par file create hogi. Dobara run karne par `stat` task chalega, lekin `copy` task skip ho jayega (`skipping` message) kyunki condition `file_status.stat.exists` true hogi, aur `not` ke karan `when` false ho jayega.

#### Example 5: Combined Loop & Conditional (Start services only if configured and OS is compatible)

**Scenario:** Aapke paas kuch services hain, jinko aapko start karna hai aur enable karna hai, lekin sirf tab jab service ko enable kiya gaya ho (aapke variables mein) aur machine RedHat-based ho.

1.  **Playbook File:** `manage_services_combo.yml`
    ```yaml
    ---
    - name: Manage multiple services conditionally
      hosts: all
      become: yes
      vars:
        services_to_manage:
          - { name: 'httpd', enabled: true }
          - { name: 'nginx', enabled: false } # Nginx ko hum disable rakhenge by default
          - { name: 'mariadb', enabled: true }
          - { name: 'firewalld', enabled: true }
      tasks:
        - name: Start and enable service if configured and on RHEL/CentOS
          ansible.builtin.service:
            name: "{{ item.name }}"
            state: started
            enabled: true
          loop: "{{ services_to_manage }}"
          when:
            - item.enabled # Check if 'enabled' property is true in our list
            - ansible_distribution == "CentOS" or ansible_distribution == "RedHat" # Check OS
    ```
2.  **Explanation:**
    *   `vars`: Humne ek list of dictionaries (`services_to_manage`) define ki hai jisme har service ka naam aur uski `enabled` status hai.
    *   `loop: "{{ services_to_manage }}"`: Ye is list par iterate karega.
    *   `when:` block mein do conditions hain:
        *   `item.enabled`: Check karega ki current loop item mein `enabled` key ki value true hai ya nahi.
        *   `ansible_distribution == "CentOS" or ansible_distribution == "RedHat"`: Check karega ki target host RedHat-based OS par hai.
    *   Dono conditions `and` (implicitly) hain, toh task tabhi chalega jab dono true hon.
3.  **Run Playbook:**
    ```bash
    ansible-playbook manage_services_combo.yml
    ```
    Agar aapke inventory mein Ubuntu machine hai, toh uspar saare tasks skip ho jayenge. Agar CentOS machine hai, toh `httpd`, `mariadb`, `firewalld` start aur enable honge, lekin `nginx` skip ho jayega (kyunki `item.enabled` false hai).

---

### 4. Practice Tasks (Lab Exercises)

Chaliye kuch practical exercises try karte hain. Apne `inventory.ini` aur `ansible.cfg` files ko sahi se configure kar lijiye.

**Task 1: Install Specific Packages (Loop)**

*   **Goal:** `htop`, `tree`, `jq`, aur `telnet` packages ko apne `all` hosts par install karo.
*   **Requirements:**
    *   Ek playbook banao jisme `loop` ka use ho.
    *   Packages ko `present` state mein rakho.
    *   Ensure karein ki `become: yes` use ho.

**Task 2: Manage User Groups Conditionally**

*   **Goal:** `devops` aur `security` naam ke do users banao. `devops` user ko `wheel` group mein add karo aur `security` user ko `audit` group mein (agar exist karta ho). Ye tasks sirf `webservers` group ke hosts par chalne chahiye.
*   **Requirements:**
    *   Ek playbook banao.
    *   List of dictionaries ka use karo `loop` ke saath.
    *   `when` condition ka use karo taaki tasks sirf `webservers` par chalein.
    *   Users ko `state: present` rakho.
    *   Ensure karo ki `become: yes` use ho.

**Task 3: Ensure Service State (Conditional + Register)**

*   **Goal:** Check karo ki `httpd` (CentOS/RHEL) ya `apache2` (Ubuntu) service running hai ya nahi. Agar running nahi hai, toh use start karo. Ye sirf un machines par karo jahan service install ho.
*   **Requirements:**
    *   Ek playbook banao.
    *   Pehle `service_facts` collect karo ya `ansible.builtin.service_info` (ya `systemd_service`) module ka use karke service ki current state check karo. `register` ka use karo.
    *   `when` condition ka use karke sirf tab start karo jab service `stopped` ho.
    *   OS-specific service name (`httpd` ya `apache2`) ke liye `when` use karo.

**Task 4: Deploy Multiple Files Conditionally**

*   **Goal:** `/opt/myapps/` directory create karo agar wo exist nahi karti. Phir us directory mein do files (`/opt/myapps/app_config.txt` aur `/opt/myapps/app_readme.txt`) copy karo, jinka content aap playbook mein define karoge.
*   **Requirements:**
    *   Ek playbook banao.
    *   Pehle `stat` module aur `register` ka use karke check karo ki `/opt/myapps/` exist karti hai ya nahi.
    *   `when` condition ka use karke directory create karo agar exist nahi karti.
    *   `loop` ka use karo do files copy karne ke liye. Har file ke liye custom content aur name define karo loop item mein.
    *   Files ki permissions `0644` set karo.

---

### 5. Pro Tips / Common Mistakes

Loops aur Conditionals bahut powerful hain, lekin unka sahi tarike se istemal karna zaroori hai.

#### Pro Tips (बेहतरीन तरीके)

1.  **`debug` Module ka Use Karen (Troubleshooting ke liye):** Jab bhi aapko variables ki values ya loop items ke content mein confusion ho, `debug` module ka use karein.
    ```yaml
    - name: Debug current item
      ansible.builtin.debug:
        var: item
      loop: "{{ list_of_dicts }}"

    - name: Debug registered variable
      ansible.builtin.debug:
        var: my_registered_var.stdout_lines
    ```
2.  **`loop_control` for Readability:** Jab aap list of dictionaries par loop kar rahe ho, toh `loop_control` ka use karke `item` ka naam badalna playbook ko zyada readable bana deta hai.
    ```yaml
    - name: Create users with custom loop_var
      ansible.builtin.user:
        name: "{{ user.name }}"
        uid: "{{ user.id }}"
      loop:
        - { name: 'alpha', id: 2001 }
        - { name: 'beta', id: 2002 }
      loop_control:
        loop_var: user # Ab 'item' ki jagah 'user' use karenge
    ```
3.  **`failed_when` for Custom Error Handling:** Default mein, Ansible zero exit code ko success manta hai. Lekin kai baar command successfully run hone ke baad bhi output mein error indication hota hai. `failed_when` aapko custom failure conditions define karne deta hai.
    ```yaml
    - name: Check free disk space on /var
      ansible.builtin.shell: df -h /var | awk 'NR==2 {print $5}' | sed 's/%//'
      register: var_disk_usage
      failed_when: var_disk_usage.stdout | int > 90 # If /var is more than 90% full, mark as failed
    ```
4.  **Idempotency ka Dhyaan Rakhein:** Aapke tasks hamesha idempotent hone chahiye, matlab unhe kitni bhi baar run karo, system state wahi rahe jo aap chahte ho, bina koi undesirable side effect ke. Conditionals aur loops idempotency maintain karne mein help karte hain (e.g., "create directory only if not exists").
5.  **`block` with `when`:** Agar aapko ek set of tasks par ek hi condition apply karni hai, toh `block` keyword ka use karein. Ye `when` condition ko har task par repeat karne se bachata hai aur playbook ko concise banata hai. (RHCE level ke liye important)
    ```yaml
    - name: Perform web server configuration
      when: inventory_hostname in groups['webservers']
      block:
        - name: Install httpd package
          ansible.builtin.package:
            name: httpd
            state: present
        - name: Copy index.html
          ansible.builtin.copy:
            src: files/index.html
            dest: /var/www/html/index.html
        - name: Start httpd service
          ansible.builtin.service:
            name: httpd
            state: started
            enabled: true
    ```

#### Common Mistakes (आमतौर पर होने वाली गलतियाँ)

1.  **YAML Indentation Errors:** Ansible YAML format par depend karta hai. Galat indentation se playbook parse nahi hoga aur aapko errors milenge (`ERROR! We were unable to read either the YAML file ...`). Always use spaces (usually 2 or 4, be consistent) for indentation, not tabs.
2.  **Incorrect Variable References:** `item` variable ko galat tarike se access karna.
    *   Agar loop list of strings hai: `{{ item }}`
    *   Agar loop list of dictionaries hai: `{{ item.key_name }}`
    *   Agar `loop_var: my_var` use kiya hai: `{{ my_var }}` ya `{{ my_var.key_name }}`
3.  **Missing Quotes in `when` Conditions:** Jab aap string comparison kar rahe ho, toh values ko quotes mein rakhna important hai, khaaskar jab numbers jaisi dikhne wali strings hon.
    ```yaml
    # Correct
    when: ansible_distribution == "CentOS"
    # Incorrect (could work but bad practice, especially with numbers)
    # when: ansible_distribution == CentOS
    ```
4.  **Overly Complex `when` Statements:** Bahut lambe aur complex `when` conditions ko avoid karein. Agar condition bahut complex ho rahi hai, toh use multiple conditions mein split karein ya jin values par condition lag rahi hai unhe pehle `set_fact` ya `debug` se inspect karein.
5.  **Forgetting `register` with `when`:** Agar aapko kisi previous task ke output par conditional logic apply karni hai, toh us task ke output ko `register` karna bhoolna nahi chahiye. Otherwise, `when` condition mein variable undefined hoga.
6.  **Not Testing Incrementally:** Ek bada playbook ek saath na run karein. Chote sections mein likhein aur test karein, khaas kar loops aur conditionals wale parts ko. `ansible-playbook --syntax-check`, `ansible-playbook --check`, aur `ansible-playbook --step` (interactive mode) ka use karein.

Umeed hai ye comprehensive lesson aapko Loops aur Conditionals ko Ansible playbooks mein use karne mein help karega. Happy Automating!
A password is required to access 23XXXXX368.pdf. Please enter the password
