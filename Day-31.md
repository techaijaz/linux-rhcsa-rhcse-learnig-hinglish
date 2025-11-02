Day 31: Simple playbooks (YAML basics)
---
Namaste dosto! Kaise hain aap sab? Main aapka Linux instructor, aur aaj hum Ansible ke ek bohot hi powerful aur fundamental topic par baat karenge – **"Simple Playbooks aur YAML Basics"**. Ye topic aapke RHCSA aur RHCE exams ke liye critical hai, aur real-world automation mein iske bina kaam adhoora hai.

Chaliye, shuru karte hain!

---

## RHCSA + RHCE Training: Simple Playbooks (YAML Basics)

### 1. Concept Explanation (Hinglish में)

Socho, aapko 100 servers par ek hi configuration karna hai – jaise ek web server install karna, ek particular user account banana, ya kuch files copy karna. Har server par manually commands run karna kitna time consuming aur error-prone hoga, right? Yahin par Ansible aur uske **Playbooks** picture mein aate hain.

#### What is a Playbook? (Playbook kya hai?)

*   **Ansible ke "Recipes":** Playbook ek blueprint ya recipe ki tarah hai jo batata hai ki remote systems (jinhe hum "managed nodes" kehte hain) par kya-kya steps lene hain.
*   **Automation ka Script:** Ye ek YAML file hoti hai jisme aap list of tasks define karte ho. Ansible in tasks ko line-by-line managed nodes par execute karta hai.
*   **Repeatable & Consistent:** Ek baar Playbook likh liya, toh use baar-baar chala sakte ho, aur har baar aapko consistent results milenge, chahe 1 server ho ya 1000.
*   **Declarative:** Playbooks "what to do" batate hain, na ki "how to do". Aap batate ho "Nginx installed hona chahiye", Ansible apne aap figure out karta hai ki kaise install karna hai (yum, apt, dnf use karke, depending on OS).

#### Why Playbooks? (Playbooks kyun zaroori hain?)

1.  **Efficiency:** Manual tasks ko automate karke time bachata hai.
2.  **Accuracy:** Human errors ko eliminate karta hai.
3.  **Consistency:** Har server par identical configuration ensure karta hai.
4.  **Documentation:** Playbook itself ek accha documentation hai ki aapke systems kaise configured hain.
5.  **Scalability:** Ek configuration ko thousands of servers par apply karna easy ho jata hai.

#### What is YAML? (YAML kya hai?)

Ansible Playbooks **YAML** (pronounced "ya-mul") format mein likhe jaate hain.

*   **YAML Ain't Markup Language:** Ye iska full form hai. Ye ek human-friendly data serialization standard hai. Matlab, ye ek tareeka hai data ko store aur represent karne ka jo insaano ko bhi easily samajh aaye.
*   **Minimal Syntax:** Iska syntax bohot simple aur clean hota hai, jo ise reading aur writing ke liye easy banata hai.
*   **Key ke liye Value:** Ye data ko `key: value` pairs mein represent karta hai.
*   **Indentation is KING! (Indentation sabse zaroori hai!)** YAML mein spaces (indentation) ka bohot bada role hai. Ye batata hai ki kaunsa data kis group ya item se belong karta hai. **Tabs use mat karna! Sirf spaces!**

#### YAML Basic Syntax (Kuch YAML basics jo Playbooks mein kaam aayenge):

1.  **Key-Value Pairs:**
    ```yaml
    name: "John Doe"
    age: 30
    is_active: true
    ```
    *   Har `key` ke baad colon `:` aur fir space dekar uski `value` likhi jaati hai.

2.  **Lists (Arrays):**
    ```yaml
    fruits:
      - Apple
      - Banana
      - Cherry
    ```
    *   List items ko dash `-` se shuru kiya jaata hai, aur har item naye line par hota hai, proper indentation ke saath.

3.  **Dictionaries (Objects) / Nested Structures:**
    ```yaml
    person:
      name: "Jane Doe"
      age: 25
      address:
        street: "123 Main St"
        city: "Anytown"
        zip: "12345"
    ```
    *   Ek key ki value doosre key-value pairs ka set ho sakti hai (nested dictionary). Indentation use karke structure define hota hai.

4.  **Comments:**
    ```yaml
    # Ye ek comment hai, Ansible ise ignore karega.
    # Comments code ko explain karne ke liye useful hote hain.
    ```
    *   Hash `#` symbol se comments start hote hain.

5.  **Document Separators:**
    ```yaml
    ---
    # Ye Playbook ka start hai.
    # Ek single YAML file mein multiple documents ho sakte hain.
    # Ansible Playbooks hamesha '---' se shuru hote hain.
    ```
    *   `---` (three dashes) YAML document ki shuruat ko denote karta hai. Ansible Playbooks mein ye compulsory hai.
    *   `...` (three dots) document ke end ko denote karta hai (less commonly used in simple playbooks).

#### Basic Playbook Structure (Ek Simple Playbook ka ढाँचा)

Ek simple Playbook kuch aisa dikhta hai:

```yaml
---
# Ye ek YAML document hai, Playbook ki shuruat.

- name: Meri Pehli Ansible Playbook
  hosts: webservers             # Konse hosts par run karna hai (inventory se).
  become: yes                   # Root privileges chahiye? (Equivalent to sudo)

  tasks:                        # Ye list of tasks hain jo execute honge.
    - name: Ensure Nginx package is installed
      ansible.builtin.dnf:      # 'dnf' module use kar rahe hain package install karne ke liye.
        name: nginx
        state: present

    - name: Ensure Nginx service is running and enabled
      ansible.builtin.systemd:  # 'systemd' module use kar rahe hain service manage karne ke liye.
        name: nginx
        state: started
        enabled: true
```

**Explanation of Structure (ढाँचे का स्पष्टीकरण):**

*   `---`: Playbook ki shuruat.
*   `- name: Meri Pehli Ansible Playbook`: Ye poore Playbook ka naam hai, optional hai par acchi practice hai. Dash `-` indicates ki ye ek list item hai, aur har playbook entry ek list item hoti hai.
*   `hosts: webservers`: Ye specify karta hai ki ye Playbook inventory file mein define kiye gaye `webservers` group ke hosts par run hoga. Aap `all` bhi likh sakte hain saare hosts ke liye.
*   `become: yes`: Agar tasks ko root privileges (sudo) ke saath run karna hai, toh `become: yes` use karte hain. Ye important hai packages install karne ya services manage karne ke liye.
*   `tasks:`: Ye ek list of tasks hai jo is playbook mein execute honge.
    *   `- name: Ensure Nginx package is installed`: Har task ka apna ek `name` hota hai. Ye output mein dikhta hai jab playbook chalta hai.
    *   `ansible.builtin.dnf:`: Ye Ansible module hai jo package management ke liye use hota hai. (Agar Debian/Ubuntu hota toh `ansible.builtin.apt` use hota.) `ansible.builtin.` prefix optional hai for common modules.
        *   `name: nginx`: `dnf` module ka argument, package ka naam.
        *   `state: present`: `dnf` module ka argument, ensure karta hai ki package install ho (agar `absent` hota toh uninstall karta).
    *   `ansible.builtin.systemd:`: Ye module services manage karne ke liye hai.
        *   `name: nginx`: Service ka naam.
        *   `state: started`: Ensure karta hai ki service run ho rahi ho.
        *   `enabled: true`: Ensure karta hai ki service system boot par automatically start ho.

---

### 2. Key Linux Commands (महत्वपूर्ण Linux Commands)

Ansible Playbooks ko manage aur execute karne ke liye kuch khaas commands hain:

1.  `ansible-playbook `
    *   **Explanation (स्पष्टीकरण):** Ye main command hai kisi Playbook ko run karne ke liye. Aap apne inventory file aur Playbook file ka path specify karte hain.
    *   **Hinglish:** "Ye sabse important command hai Playbook ko actual mein run karne ke liye. `ansible-playbook` ke baad apni Playbook YAML file ka naam do, aur Ansible use execute kar dega."

2.  `ansible-playbook --syntax-check `
    *   **Explanation (स्पष्टीकरण):** Playbook ko execute karne se pehle, ye command YAML syntax aur basic Playbook structure ko validate karta hai. Agar koi indentation error ya syntax mistake hai, toh ye bata dega.
    *   **Hinglish:** "Hamesha Playbook run karne se pehle ye command chalao! Ye check karta hai ki aapki YAML file mein koi syntax error ya indentation problem toh nahi hai. Ek tarah se ye aapka 'spell check' hai Playbook ke liye."

3.  `ansible-playbook --list-hosts `
    *   **Explanation (स्पष्टीकरण):** Ye command batata hai ki Playbook kin hosts par execute hoga, based on your `hosts:` directive and inventory.
    *   **Hinglish:** "Ye janne ke liye ki aapka Playbook kin-kin servers par run hone wala hai, ye command use karo. Ye aapke inventory aur Playbook mein diye gaye `hosts:` ke hisaab se targets dikhata hai."

4.  `ansible-playbook --list-tasks `
    *   **Explanation (स्पष्टीकरण):** Ye Playbook mein define kiye gaye sabhi tasks ko list karta hai, unke names ke saath.
    *   **Hinglish:** "Apne Playbook mein kya-kya tasks hain, unki ek list dekhne ke liye is command ka use karo. Ye aapko Playbook ki functionality ka quick overview deta hai."

5.  `ansible-playbook --check ` (or `-C`)
    *   **Explanation (स्पष्टीकरण):** "Dry Run" mode. Ye Playbook ko execute karta hai lekin actual mein koi changes apply nahi karta remote hosts par. Ye bas batata hai ki kya changes *hone wale the*.
    *   **Hinglish:** "Bohot, bohot, bohot important command! Production servers par koi bhi Playbook run karne se pehle hamesha `--check` flag use karo. Ye Playbook ko run karta hai par actually koi changes apply nahi karta. Ye bas dikhata hai ki 'agar run hota toh ye ye changes hote'."

6.  `ansible-playbook -v ` (or `-vv`, `-vvv`, `-vvvv`)
    *   **Explanation (स्पष्टीकरण):** Verbosity flag. `-v` detailed output deta hai, `-vv` aur bhi detailed, aur `-vvv` ya `-vvvv` debug level ka output deta hai jo troubleshooting ke liye useful hota hai.
    *   **Hinglish:** "Agar aapko Playbook ke execution ka detailed output dekhna hai ya koi problem aa rahi hai toh debug karna hai, toh `-v` ya usse zyada `-vv`, `-vvv` use karo. Ye har step ki detailed info dikhayega."

7.  `ansible-doc `
    *   **Explanation (स्पष्टीकरण):** Ye kisi bhi Ansible module ke baare mein documentation dikhata hai – uske arguments, options aur examples ke saath. Playbook likhte waqt bohot helpful hai.
    *   **Hinglish:** "Koi naya module use karna hai aur uske arguments ya usage samajh nahi aa raha? `ansible-doc` se us module ki poori jaankari, examples ke saath mil jayegi."

8.  `vim ` / `nano `
    *   **Explanation (स्पष्टीकरण):** Ye text editors hain jinmein aap apni YAML Playbook files create aur edit karoge.
    *   **Hinglish:** "Playbook files banane aur edit karne ke liye ye standard Linux text editors hain. Aapki marzi hai kaunsa use karna hai."

---

### 3. Step-by-Step Examples (कदम-दर-कदम उदाहरण)

Chalo, kuch real-world scenarios mein in commands ka use dekhte hain.

**Prerequisites (पूर्व-आवश्यकताएँ):**

*   Ek Ansible Control Node jahan se aap commands run karoge.
*   Kam se kam ek Managed Node (jaise CentOS/RHEL VM) jahan Playbook execute hoga.
*   Apni `~/.ansible/hosts` ya `/etc/ansible/hosts` inventory file mein managed node ka entry hona chahiye.
    *   Example Inventory (`/etc/ansible/hosts`):
        ```ini
        [webservers]
        web1.example.com ansible_user=devopsuser ansible_ssh_private_key_file=~/.ssh/id_rsa
        # web1.example.com ko replace karein apne Managed Node ke IP ya hostname se.
        # ansible_user aur ansible_ssh_private_key_file apne setup ke hisaab se adjust karein.

        [all:vars]
        ansible_python_interpreter=/usr/bin/python3
        ```
*   Managed Node par `python3` aur `dnf` (or `yum`) installed hona chahiye.

#### Scenario 1: Basic Web Server Setup (RHCSA Focus)

**Goal:** `webservers` group ke servers par Nginx web server install aur configure karna.

**Step 1: Playbook File Create Karna**
Control Node par ek file banayein `nginx_web.yml` naam se:

```bash
cd /etc/ansible/
sudo nano nginx_web.yml
```

Aur usme ye content paste karein:

```yaml
---
- name: Setup Nginx Web Server
  hosts: webservers
  become: yes # Root privileges ke liye

  tasks:
    - name: Ensure Nginx package is installed
      ansible.builtin.dnf:
        name: nginx
        state: present

    - name: Ensure Nginx service is running and enabled
      ansible.builtin.systemd:
        name: nginx
        state: started
        enabled: true

    - name: Ensure firewall allows HTTP traffic
      ansible.posix.firewalld: # firewalld module
        service: http
        permanent: true
        state: enabled
        immediate: true # Changes ko immediately apply kare
```

**Step 2: Playbook Syntax Check Karna**
Hamesha pehle syntax check karein:

```bash
ansible-playbook --syntax-check nginx_web.yml
```
*   **Expected Output (अगर सब सही है):**
    ```
    playbook: nginx_web.yml
    ```
*   **Hinglish:** "Agar koi error nahi hai, toh aisa output aayega. Agar `ERROR!` dikhe, toh samajh jao YAML indentation ya syntax mein gadbad hai."

**Step 3: Konse Hosts Target ho rahe hain, check karein**

```bash
ansible-playbook --list-hosts nginx_web.yml
```
*   **Expected Output:**
    ```
    playbook: nginx_web.yml

      hosts (1):
        web1.example.com
    ```
*   **Hinglish:** "Ye command confirm karta hai ki aapka Playbook sahi managed node par ja raha hai ya nahi. Yahan `web1.example.com` dikh raha hai matlab sahi hai."

**Step 4: Dry Run (Check Mode) Karna**
Actual changes apply karne se pehle, dekho kya hone wala hai:

```bash
ansible-playbook --check nginx_web.yml
```
*   **Expected Output:** Aapko output mein changes dikhenge jo *hone wale the*, par `changed` status nahi dikhega jab tak aap dry run mein ho. Example: `changed=false`, but tasks would show.
    ```
    PLAY [Setup Nginx Web Server] **************************************************

    TASK [Gathering Facts] *********************************************************
    ok: [web1.example.com]

    TASK [Ensure Nginx package is installed] ***************************************
    ok: [web1.example.com]

    TASK [Ensure Nginx service is running and enabled] *****************************
    ok: [web1.example.com]

    TASK [Ensure firewall allows HTTP traffic] *************************************
    ok: [web1.example.com]

    PLAY RECAP *********************************************************************
    web1.example.com           : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```
*   **Hinglish:** "Ye output bata raha hai ki agar hum Playbook run karte toh kya hota. Sab `ok` dikh raha hai, matlab koi error nahi aaya aur Ansible ko laga ki Nginx install karna padega ya service start karni padegi. Par `changed=0` hai kyunki ye sirf dry run tha."

**Step 5: Playbook Actual mein Run Karna**
Ab jab sab checks ho gaye, Playbook ko execute karein:

```bash
ansible-playbook nginx_web.yml
```
*   **Expected Output (jab pehli baar chalega):**
    ```
    PLAY [Setup Nginx Web Server] **************************************************

    TASK [Gathering Facts] *********************************************************
    ok: [web1.example.com]

    TASK [Ensure Nginx package is installed] ***************************************
    changed: [web1.example.com]

    TASK [Ensure Nginx service is running and enabled] *****************************
    changed: [web1.example.com]

    TASK [Ensure firewall allows HTTP traffic] *************************************
    changed: [web1.example.com]

    PLAY RECAP *********************************************************************
    web1.example.com           : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```
*   **Hinglish:** "Ab `changed=3` dikh raha hai! Matlab, Ansible ne 3 tasks successfully execute kiye aur system mein changes apply kiye. Nginx install ho gaya, service start ho gayi aur firewall port open ho gaya."

**Step 6: Idempotency Check ( दोबारा चला कर देखना )**
Ansible idempotent hota hai. Matlab, agar aap ek Playbook ko baar-baar chalate hain, toh wo sirf tabhi changes karega jab system desired state mein na ho. Jab system desired state mein aa jata hai, toh dobara run karne par koi changes nahi hote.

```bash
ansible-playbook nginx_web.yml
```
*   **Expected Output (dobara chalane par):**
    ```
    PLAY [Setup Nginx Web Server] **************************************************

    TASK [Gathering Facts] *********************************************************
    ok: [web1.example.com]

    TASK [Ensure Nginx package is installed] ***************************************
    ok: [web1.example.com]

    TASK [Ensure Nginx service is running and enabled] *****************************
    ok: [web1.example.com]

    TASK [Ensure firewall allows HTTP traffic] *************************************
    ok: [web1.example.com]

    PLAY RECAP *********************************************************************
    web1.example.com           : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```
*   **Hinglish:** "Dekho! Ab `changed=0` hai. Kyunki Nginx pehle se hi installed hai, service chal rahi hai, aur firewall rule bhi applied hai, Ansible ne koi aur change nahi kiya. Ye hai Ansible ki idempotency ki power!"

**Step 7: Verification (Pushti karna)**
Managed node par login karke verify karein:
```bash
ssh devopsuser@web1.example.com
systemctl status nginx
sudo firewall-cmd --list-all
```
Aur apne control node se `curl` karein:
```bash
curl http://web1.example.com
```
*   **Expected Output:** Nginx status `active (running)` dikhega aur `curl` se Nginx ka default welcome page aa jayega.

#### Scenario 2: User Management (RHCE Focus - Thoda Advanced)

**Goal:** `webservers` group par ek naya user `ansuser` banana, use `wheel` group mein add karna, aur uski `authorized_keys` mein public key add karna.

**Step 1: Playbook File Create Karna**
Control Node par `user_management.yml` banayein:

```bash
sudo nano user_management.yml
```

Aur usme ye content paste karein (replace `ssh-rsa AAAA...` with your actual public key):

```yaml
---
- name: User and SSH Key Management
  hosts: webservers
  become: yes

  vars:
    new_user: ansuser
    ssh_public_key: "ssh-rsa AAAA...your_actual_public_key_here... user@example.com" # Apni public key yahan daalein

  tasks:
    - name: Create a new user {{ new_user }}
      ansible.builtin.user:
        name: "{{ new_user }}"
        state: present
        groups: wheel # sudo access ke liye, ya koi aur group
        append: true   # Agar user already exists toh group add kare

    - name: Add SSH public key for {{ new_user }}
      ansible.builtin.authorized_key:
        user: "{{ new_user }}"
        state: present
        key: "{{ ssh_public_key }}"
        path: "/home/{{ new_user }}/.ssh/authorized_keys" # Optional, default path hi hai
        exclusive: false # Agar doosri keys bhi hain, unhe delete na kare
```

**Step 2: Syntax Check, List Hosts/Tasks, Dry Run, Execute**

```bash
ansible-playbook --syntax-check user_management.yml
ansible-playbook --list-hosts user_management.yml
ansible-playbook --list-tasks user_management.yml
ansible-playbook --check user_management.yml
ansible-playbook user_management.yml
```
*   **Hinglish:** "Ye sab commands Scenario 1 ki tarah hi chalayenge. `user` module user creation ke liye hai aur `authorized_key` module SSH keys manage karne ke liye. `vars` section mein hum variables define kar sakte hain, jo Playbook ko more flexible banate hain."

**Step 3: Verification**
Managed node par login karke verify karein:
```bash
ssh devopsuser@web1.example.com
id ansuser
sudo grep ansuser /etc/sudoers.d/ # Agar sudoers.d mein entry banayi hai
cat /home/ansuser/.ssh/authorized_keys
```
*   **Hinglish:** "Verify karein ki `ansuser` ban gaya hai, `wheel` group mein hai (agar `wheel` group `sudo` access deta hai), aur uski public key `authorized_keys` file mein hai."
*   **Try logging in as `ansuser` from control node:**
    ```bash
    ssh ansuser@web1.example.com
    ```
    If successful, it means your public key was correctly added.

---

### 4. Practice Tasks (अभ्यास कार्य)

Ye kuch practical exercises hain jo aap try kar sakte hain:

1.  **File Management Playbook:**
    *   Ek Playbook banayein jo `webservers` group par `/tmp/ansible_test_file.txt` naam ki file create kare.
    *   Us file ke content mein "Hello from Ansible!" likhe.
    *   File ke permissions `0644` set karein aur owner `root:root` ho.
    *   *(Hint: `ansible.builtin.copy` module use karein, `content` parameter ke saath)*

2.  **Package Installation & Removal Playbook:**
    *   **Task A (Install):** Ek Playbook likhein jo `htop` aur `git` packages install kare `webservers` par.
    *   **Task B (Verify):** Playbook run karein, fir verify karein ki packages install ho gaye hain (`dnf list installed htop git`).
    *   **Task C (Remove):** Ek naya Playbook ya existing Playbook ko modify karein jo inhi packages ko uninstall kare.
    *   *(Hint: `ansible.builtin.dnf` module, `state: present` for install, `state: absent` for remove)*

3.  **Service Management Playbook:**
    *   `webservers` par Apache web server (`httpd`) install karein.
    *   Ensure karein ki `httpd` service running aur enabled ho.
    *   Firewall mein HTTP service allow karein.
    *   *(Hint: `ansible.builtin.dnf`, `ansible.builtin.systemd`, `ansible.posix.firewalld` modules)*

4.  **Directory Creation & Ownership:**
    *   `webservers` par `/opt/mydata` naam ki directory banayein.
    *   Is directory ka owner `ansuser` (jo abhi banaya) ho aur group bhi `ansuser` ho.
    *   Permissions `0755` set karein.
    *   *(Hint: `ansible.builtin.file` module)*

---

### 5. Pro Tips / Common Mistakes (विशेष सुझाव / आम गलतियाँ)

#### Pro Tips (विशेष सुझाव):

1.  **Start Simple:** Pehle chhote Playbooks likho, ek ya do tasks ke saath, fir dheere-dheere complex karte jao.
2.  **Use `name:` for Tasks:** Har task ko ek descriptive `name` dein. Ye Playbook ke output ko readable banata hai aur debugging mein help karta hai.
3.  **`--syntax-check` is Your Best Friend:** Har baar Playbook ko run karne se pehle, syntax check zaroor karein. Ye bohot saara time bachayega.
4.  **`--check` (Dry Run) Always:** Production environments mein changes apply karne se pehle hamesha `--check` flag use karein. Ye aapko potential issues se bachayega.
5.  **Use Verbosity (`-v`, `-vvv`):** Jab kuch galat ho, toh verbosity flags use karein. `-vvv` ya `-vvvv` se detailed output milta hai jo debugging ke liye bohot useful hota hai.
6.  **Read Module Documentation (`ansible-doc`):** Naye modules ke baare mein janne ke liye ya unke arguments dekhne ke liye `ansible-doc ` use karein. Ye aapko sahi tarike se modules use karne mein help karega.
7.  **Idempotency ka Fayda Uthayein:** Ansible automatically idempotent hai. Iska matlab hai ki aapka Playbook baar-baar run karne par system ko same desired state mein hi rakhega, bina unnecessary changes kiye. Agar koi resource pehle se hi desired state mein hai, toh Ansible use skip kar dega (`ok` status).
8.  **`become: yes` ka Use:** Privileged operations (jaise package install karna, service start karna, files modify karna) ke liye `become: yes` (sudo ke barabar) zaroor use karein.

#### Common Mistakes (आम गलतियाँ):

1.  **Indentation Errors (SABSE ZAROORI!):** YAML mein spaces ka galat use karna sabse common galti hai.
    *   **Mistake:** Tabs use karna.
    *   **Solution:** Hamesha **spaces** use karein, tabs nahi. Two spaces ya four spaces ka consistent indentation rakhein.
    *   **Hint:** Text editors jaise `nano` ya `vim` mein `set expandtab` aur `set tabstop=2` ya `tabstop=4` set kar sakte hain.
2.  **Colon (`:`) Bhool Jaana:** Key-value pairs mein colon missing hona ya uske baad space na dena.
    *   **Mistake:** `name:Nginx` instead of `name: nginx`
    *   **Solution:** Hamesha `key: value` format follow karein, colon ke baad ek space zaroor ho.
3.  **Incorrect Module Arguments:** Module ke liye galat arguments pass karna.
    *   **Mistake:** `dnf: name=nginx state=present` (ye `ansible` command line syntax hai, Playbook mein nahi)
    *   **Solution:** Playbook mein `key: value` format use karein:
        ```yaml
        ansible.builtin.dnf:
          name: nginx
          state: present
        ```
    *   **Hint:** `ansible-doc` se sahi arguments dekhein.
4.  **`become: yes` Bhool Jaana:** Jab root privileges ki zaroorat ho aur `become: yes` na diya ho.
    *   **Mistake:** Package install karne ka task `permission denied` error dega.
    *   **Solution:** Jahan bhi root rights ki zaroorat ho, Playbook ya particular task ke level par `become: yes` add karein.
5.  **Inventory File Issues:** Managed nodes ka inventory mein galat entry, ya SSH access na hona.
    *   **Mistake:** `unreachable` errors.
    *   **Solution:** `ansible ping ` se connectivity aur authentication check karein pehle.

---

Umeed karta hoon ki ye detailed lesson aapko Ansible Playbooks aur YAML basics ko samajhne mein bohot help karega. Ab waqt hai in concepts ko apne haathon se implement karne ka! Practice, practice, and more practice! All the best!
