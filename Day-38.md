Day 38: Roles (structure, ansible-galaxy init)
---
Namaste students! Aaj hum Ansible ka ek bahut hi powerful aur important topic explore karenge: **"Roles"**. RHCSA aur RHCE exam ke liye, aur real-world automation ke liye bhi, Roles samajhna aur use karna extremely crucial hai.

Roles aapki Ansible automation ko organized, reusable aur maintainable banate hain. Chaliye, shuru karte hain!

---

## RHCSA + RHCE Training: Ansible Roles (Structure, ansible-galaxy init)

### 1. Concept Explanation (Hinglish)

So students, imagine karo aapko ek complex application deploy karni hai – usme web server bhi hai, database bhi hai, monitoring tools bhi hain. Agar aap sab kuch ek hi bade se playbook file mein likhoge, toh kya hoga?

*   Wo file bahut **lambi** ho jaayegi.
*   Usko **read karna mushkil** hoga.
*   **Debugging** karna headache ban jaayega.
*   Aur future mein agar aapko sirf web server configuration ko kisi aur project mein use karna hai, toh aapko pura code copy-paste karna padega – jo ki **reusability** ke khilaaf hai.

Is problem ko solve karne ke liye Ansible ne **"Roles"** ka concept introduce kiya.

**Toh, Roles kya hain?**

Roles basically **Ansible content ke self-contained, independent aur reusable units** hote hain. Aap inhein ek tarah se **modules** ya **packages** samajh sakte ho Ansible ke liye. Har Role ek specific task ya functionality ko handle karta hai, jaise ki:
*   Ek web server (Apache, Nginx) install aur configure karna.
*   Ek database (MySQL, PostgreSQL) setup karna.
*   Ek specific user create karna aur uski permissions set karna.
*   Ek application deploy karna.

**Roles use karne ke fayde (Advantages):**

1.  **Modularity & Organization:** Aap apni automation ko logical units mein break kar dete ho. Har unit (role) ka apna specific kaam hota hai. Iss se code clean rehta hai.
2.  **Reusability:** Ek baar role ban gaya, toh aap usko kitne bhi playbooks mein, kitne bhi projects mein use kar sakte ho. "Don't Repeat Yourself (DRY)" principle follow hota hai.
3.  **Readability:** Roles ki standard directory structure hoti hai, toh koi bhi nayi banda aapka role dekhega toh turant samajh jaayega ki files kahan milengi (tasks, handlers, templates, etc.).
4.  **Collaboration:** Multiple log ek bade automation project par kaam kar sakte hain, jahan har koi alag-alag roles par focus karega.
5.  **Easier Debugging:** Agar koi issue aata hai, toh aapko pata hoga ki issue kis specific role ke andar hoga, usko isolate karna aasan ho jaata hai.
6.  **Best Practice:** Roles ko use karna industry standard aur recommended best practice hai Ansible automation ke liye.

**Role ki Standard Directory Structure:**

Ye sabse important part hai. Jab aap ek role banate ho, toh Ansible ek specific directory structure expect karta hai. Ye structure **`ansible-galaxy init `** command se automatically generate ho jaati hai. Chaliye har directory ka matlab dekhte hain:

```
/
├── defaults/          # Default variables for the role (lowest precedence, easily overridden)
│   └── main.yml
├── files/             # Static files to be copied to managed nodes (e.g., bash scripts, images)
│   └── some_script.sh
├── handlers/          # Handlers that can be notified by tasks (e.g., restart service)
│   └── main.yml
├── meta/              # Metadata about the role (author, license, dependencies, etc.)
│   └── main.yml
├── tasks/             # The main tasks of the role (where the actual work happens)
│   └── main.yml
├── templates/         # Jinja2 templates to be rendered on managed nodes (e.g., config files)
│   └── config.conf.j2
├── tests/             # (Optional) Test playbooks for the role
│   ├── inventory
│   └── test.yml
├── vars/              # Variables specific to this role (higher precedence than defaults)
│   └── main.yml
└── README.md          # Documentation for the role
```

Har directory ka apna ek specific purpose hai:

*   **`tasks/main.yml`**: Ye role ka core hota hai. Saari actual automation logic (packages install karna, services start karna, users create karna) yahi likhi jaati hai.
*   **`handlers/main.yml`**: Jab kisi task ko "notify" karna hota hai (e.g., configuration change ke baad service restart karna), toh wo handlers mein defined hote hain. Handlers tabhi run hote hain jab unhein explicitly notify kiya jaaye, aur ek se zyada baar notify karne par bhi wo sirf ek baar run hote hain.
*   **`defaults/main.yml`**: Yahan role ke liye default variables define kiye jaate hain. Ye variables sabse kam precedence ke hote hain, matlab inhein aasaani se inventory, extra-vars, ya playbook mein override kiya ja sakta hai. Ye generally user-configurable options ke liye use hote hain.
*   **`vars/main.yml`**: Yahan bhi variables define kiye jaate hain, lekin inki precedence `defaults` se high hoti hai. Ye generally role-internal variables ya constants ke liye use hote hain jinhe aap chahte ho ki generally override na kiya jaaye.
*   **`files/`**: Is directory mein woh static files rakhe jaate hain jo aap managed nodes par copy karna chahte ho, bina kisi modification ke. Jaise ki koi script, ek specific banner image, ya pre-compiled binaries. `ansible.builtin.copy` module automatically files ko is directory mein dhoondhta hai.
*   **`templates/`**: Is directory mein Jinja2 templates rakhe jaate hain. Ye files managed nodes par copy hone se pehle render (process) kiye jaate hain. Matlab, aap variables ko templates ke andar use kar sakte ho takay dynamic configuration files bana sako. `ansible.builtin.template` module automatically templates ko is directory mein dhoondhta hai.
*   **`meta/main.yml`**: Isme role ke baare mein metadata hoti hai, jaise author, license, platform support, aur role dependencies (agar ek role ko chalane ke liye doosre role ki zarurat hai).
*   **`tests/`**: Optional directory, isme aap role ke liye testing playbooks bana sakte ho.

### 2. Key Linux Commands (Hinglish)

Roles se related kuch important commands:

1.  **`ansible-galaxy init `**
    *   **Purpose:** Ye command ek naye Ansible role ka standard directory structure automatically create karta hai. Ye aapka time bachata hai aur ensures karta hai ki aap best practices follow kar rahe ho.
    *   **Explanation:** `` ki jagah aap apne role ka naam dete ho (e.g., `webserver`, `database`, `user_mgmt`). Ye command us naam se ek directory banayega aur uske andar standard subdirectories (tasks, handlers, defaults, etc.) aur `main.yml` files ko create kar dega.
    *   **Example:** `ansible-galaxy init my_web_role`

2.  **`tree `** (Agar `tree` installed na ho toh `yum install tree` ya `apt install tree` kar sakte hain)
    *   **Purpose:** Directory structure ko ek graphical/hierarchical way mein display karne ke liye.
    *   **Explanation:** Ye command aapko help karta hai dekhne mein ki `ansible-galaxy init` ne kya-kya directories aur files banayi hain.
    *   **Example:** `tree my_web_role`

3.  **`ls -R `**
    *   **Purpose:** Ye bhi `tree` ki tarah recursive listing karta hai directory contents ki, agar `tree` install na ho toh ye ek accha alternative hai.
    *   **Explanation:** Recursively lists the contents of directories.
    *   **Example:** `ls -R my_web_role`

4.  **`cd `**
    *   **Purpose:** Directory change karne ke liye.
    *   **Explanation:** Roles ke directories mein navigate karne ke liye essential.
    *   **Example:** `cd my_web_role/tasks`

5.  **`touch `**
    *   **Purpose:** Empty file create karne ke liye.
    *   **Explanation:** Agar aap `ansible-galaxy init` ke baad additional files banana chahte ho (e.g., `tasks/nginx.yml` instead of `tasks/main.yml` for specific tasks), toh iska use hota hai. (Although for basic roles, `main.yml` is sufficient.)
    *   **Example:** `touch my_web_role/files/index.html`

6.  **`ansible-playbook `**
    *   **Purpose:** Ansible playbook ko run karne ke liye.
    *   **Explanation:** Jab aap apna role bana lete ho aur usko ek playbook mein refer karte ho, toh us playbook ko run karne ke liye ye command use hota hai.
    *   **Example:** `ansible-playbook site.yml -i inventory.ini`

### 3. Step-by-Step Examples

Chaliye, ek real-world scenario dekhte hain. Hum ek role banayenge jo Apache HTTP web server ko install aur configure karega.

**Scenario:** Create an Ansible Role to install Apache HTTPD, ensure it's running, and deploy a custom `index.html` page.

**Step 1: Role Structure Create Karna (`ansible-galaxy init`)**

Sabse pehle, hum role ka basic structure banayenge.

```bash
# Apne workspace directory mein jao
cd ~/ansible_project

# Role ko initialize karo
ansible-galaxy init apache_webserver_role
```

**Output:**

```
- Role apache_webserver_role was created successfully
```

**Step 2: Structure Verify Karna (`tree` ya `ls -R`)**

Ab dekho kya banaya hai `ansible-galaxy` ne:

```bash
tree apache_webserver_role
```

**Expected Output:**

```
apache_webserver_role/
├── defaults
│   └── main.yml
├── files
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── README.md
├── tasks
│   └── main.yml
├── templates
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml

9 directories, 7 files
```

Dekha? Kitni clean aur organized structure ban gayi.

**Step 3: `tasks/main.yml` mein Logic Add Karna**

Ab hum `apache_webserver_role/tasks/main.yml` file mein web server install aur configure karne ki logic likhenge.

```yaml
# apache_webserver_role/tasks/main.yml
---
- name: Ensure Apache HTTPD package is installed
  ansible.builtin.yum:  # For RHEL/CentOS
    name: httpd
    state: present
  when: ansible_os_family == "RedHat" # OS family ke hisaab se package manager choose karo

- name: Ensure Apache HTTPD service is running and enabled
  ansible.builtin.systemd:
    name: httpd
    state: started
    enabled: yes

- name: Copy custom index.html to web root
  ansible.builtin.copy:
    src: index.html       # Ye file 'files/' directory mein hoga
    dest: /var/www/html/index.html
    mode: '0644'
  notify: Restart Apache  # Jab index.html copy ho jaaye toh Apache ko restart karo
```

**Step 4: `handlers/main.yml` mein Handler Define Karna**

`Restart Apache` handler ko `apache_webserver_role/handlers/main.yml` mein define karte hain.

```yaml
# apache_webserver_role/handlers/main.yml
---
- name: Restart Apache
  ansible.builtin.systemd:
    name: httpd
    state: restarted
```

**Step 5: `files/` directory mein `index.html` create Karna**

Hum `apache_webserver_role/files/` directory mein apna custom `index.html` file banayenge.

```bash
# File banao
touch apache_webserver_role/files/index.html

# Usme content add karo
echo "
Hello from Ansible!
This page was deployed by the Apache Webserver Role.

" > apache_webserver_role/files/index.html
```

**Step 6: Role ko Call karne ke liye Ek Playbook Banana**

Ab hum ek simple playbook (`site.yml`) banayenge jo is `apache_webserver_role` ko call karega. Ye playbook aapke `ansible_project` directory ke root mein hoga.

```yaml
# site.yml
---
- name: Deploy Apache Web Server using a Role
  hosts: webservers        # Apni inventory mein is host group ko define karo
  become: yes              # Root privileges chahiye honge
  roles:
    - apache_webserver_role # Yahan apne role ka naam specify karte hain
```

**Step 7: Inventory File Banana**

Make sure aapke paas ek `inventory.ini` file hai apne `ansible_project` directory mein.

```ini
# inventory.ini
[webservers]
web1.example.com  ansible_user=devopsuser
web2.example.com  ansible_user=devopsuser
```
*(Replace `web1.example.com`, `web2.example.com` with your actual managed node IPs/hostnames and `devopsuser` with your SSH user.)*

**Step 8: Playbook Run Karna**

Finally, playbook ko run karo.

```bash
ansible-playbook -i inventory.ini site.yml
```

**Expected Output (partial):**

```
PLAY [Deploy Apache Web Server using a Role] ***********************************

TASK [Gathering Facts] *********************************************************
ok: [web1.example.com]
ok: [web2.example.com]

TASK [apache_webserver_role : Ensure Apache HTTPD package is installed] ********
changed: [web1.example.com]
changed: [web2.example.com]

TASK [apache_webserver_role : Ensure Apache HTTPD service is running and enabled] ***
changed: [web1.example.com]
changed: [web2.example.com]

TASK [apache_webserver_role : Copy custom index.html to web root] **************
changed: [web1.example.com] => {"changed": true, "checksum": "...", "dest": "/var/www/html/index.html", "gid": 0, "group": "root", "mode": "0644", "owner": "root", "path": "/var/www/html/index.html", "size": 95, "state": "file", "uid": 0}
changed: [web2.example.com] => {"changed": true, "checksum": "...", "dest": "/var/www/html/index.html", "gid": 0, "group": "root", "mode": "0644", "owner": "root", "path": "/var/www/html/index.html", "size": 95, "state": "file", "uid": 0}

RUNNING HANDLER [apache_webserver_role : Restart Apache] ***********************
changed: [web1.example.com]
changed: [web2.example.com]

PLAY RECAP *********************************************************************
web1.example.com           : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web2.example.com           : ok=5    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Ab aap apne web browser mein `http://web1.example.com` (ya jo bhi aapki managed node ka IP hai) visit karoge toh aapko "Hello from Ansible!" message dikhega.

### 4. Practice Tasks

Ye kuch tasks hain jo aap try kar sakte ho apni understanding ko solid karne ke liye:

1.  **Nginx Web Server Role Banayein:**
    *   Ek naya role banayein `nginx_webserver_role` naam se.
    *   Is role ko use karke Nginx web server install karein (RHEL/CentOS ke liye `epel-release` install karna pad sakta hai pehle, ya `nginx` package).
    *   Ensure karein ki Nginx service `started` aur `enabled` hai.
    *   Ek custom `index.html` page deploy karein `nginx_webserver_role/files/` se `/usr/share/nginx/html/index.html` par.
    *   Ek handler define karein `Restart Nginx` jo service ko restart kare jab `index.html` change ho.
    *   Ek playbook banayein jo is role ko `webservers` group par apply kare.

2.  **User Management Role:**
    *   Ek role banayein `user_mgmt_role` naam se.
    *   Is role mein `tasks/main.yml` mein do users create karein: `devuser` aur `testuser`. Ensure karein ki ye users ban chuke hain.
    *   `devuser` ko `wheel` group ka member banayein (sudo access ke liye).
    *   Ek playbook banayein jo is role ko `all` hosts par apply kare.

3.  **Role Variables ka Istemaal:**
    *   Apne `apache_webserver_role` mein, `defaults/main.yml` mein ek variable define karein, for example: `apache_doc_root: /var/www/html`.
    *   `tasks/main.yml` mein jahan bhi `/var/www/html` use ho raha hai, usko `{{ apache_doc_root }}` se replace karein.
    *   Playbook mein role call karte waqt, `apache_doc_root` ki value override karein, jaise ki `apache_doc_root: /opt/my_web_content`. Verify karein ki `index.html` ab naye location par deploy ho raha hai.

### 5. Pro Tips / Common Mistakes

**✅ Pro Tips:**

*   **Small and Focused Roles:** Har role ko ek specific functionality (e.g., install Apache, configure database) par focus karna chahiye. Avoid creating monolithic roles that do too many things.
*   **Idempotency:** Hamesha ensure karein ki aapke roles idempotent hain. Matlab, chahe aap role ko kitni baar bhi run karo, system ki state same honi chahiye aur jab tak koi actual change na ho, `changed` status nahi aana chahiye.
*   **Use `defaults/` wisely:** `defaults` mein aise variables rakhein jo commonly overridden ho sakte hain. Ye role ko flexible banata hai.
*   **Use `vars/` for Role-Internal Variables:** Agar koi variable role ke andar hi use hota hai aur aap chahte ho ki wo generally override na ho, toh use `vars/main.yml` mein rakhein.
*   **`meta/main.yml` ka use karein:** Especially agar aap role ko share kar rahe ho (Ansible Galaxy par ya apni team mein), toh `meta` mein role ki description, author, license, aur dependencies mention karna important hai.
*   **Version Control:** Apne roles ko Git (ya koi bhi VCS) mein store karein. Ye collaboration, version tracking aur rollbacks ke liye essential hai.
*   **Test Your Roles:** `tests/` directory ka use karein. Aap apne roles ko `molecule` jaise tools se bhi test kar sakte hain.

**❌ Common Mistakes to Avoid:**

*   **Manual Directory Creation:** `ansible-galaxy init` ka use na karna aur directories ko manually banana. Iss se spelling mistakes ho sakti hain ya incorrect structure ban sakti hai. Hamesha `ansible-galaxy init` use karein.
*   **Incorrect Directory Usage:** `tasks` ko `handlers` mein, ya `templates` ko `files` mein rakh dena. Har directory ka apna specific purpose hai.
*   **Forgetting `become: yes`:** Services install/start karne ya files `/var` mein copy karne ke liye root privileges chahiye hote hain. Agar `become: yes` nahi hoga playbook mein, toh permission errors aayenge.
*   **Not using `notify` with Handlers:** Handlers automatically run nahi hote. Unhein tasks se `notify` kiya jaana chahiye. Agar aap `notify` karna bhool gaye aur config file update ki, toh service restart nahi hogi.
*   **Hardcoding Values:** Variables ka use na karna aur values ko directly tasks mein hardcode kar dena. Iss se role reusable nahi rehta.
*   **Roles not being Idempotent:** Agar aapka role har run par `changed` state dikha raha hai, bhale hi kuch change na hua ho, toh usme idempotency issues hain. Example: `shell: command_to_install_package` instead of `yum: name=package state=present`.
*   **Variable Precedence Confusion:** Kis variable ki value kab override hogi, isko na samajhna. Yaad rakhein `defaults` ki precedence sabse kam hoti hai.

---

Umeed hai ki aapko Roles ka concept, unki importance, structure aur unhein kaise use karte hain, acche se samajh aa gaya hoga. Ye Ansible mastery ke liye ek bahut bada step hai. Practice karte rahiye! All the best!
