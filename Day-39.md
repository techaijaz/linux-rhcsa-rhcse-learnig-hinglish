Day 39: Tags, includes, imports
---
Namaste aur swagat hai aap sabhi ka hamare RHCSA + RHCE training session mein! Aaj hum Ansible ke ek bohot hi important topic par baat karenge jo hai **"Tags, Includes, aur Imports"**. Ye teenon cheezein aapke Ansible playbooks ko manage karne, reusable banane aur bade automation tasks ko handle karne mein kaafi madad karti hain. Chaliye, shuru karte hain!

---

## **RHCSA + RHCE Training: Tags, Includes, Imports in Ansible**

### Introduction (परिचय)

Jab aap bade aur complex Ansible playbooks likhte hain, toh unko maintain karna aur specific parts ko run karna mushkil ho jaata hai. Yahin par 'Tags', 'Includes', aur 'Imports' aapke rescue mein aate hain. Ye aapko apne playbooks ko modular banane, code reuse karne aur selectively tasks run karne ki capability dete hain. Socho ki aapke paas ek badi kitaab hai; Tags chapter headings ki tarah hain jo aapko specific chapter par jaane dete hain, aur Includes/Imports aapko chote chote modules ya sections ko alag files mein rakh kar unko main kitaab mein jodane mein help karte hain.

---

### 1. Concept Explanation (अवधारणा की व्याख्या)

#### 1.1. Tags (टैग्स)

**Tags kya hain?**
Ansible mein, `tags` ek way hai jisse aap apne tasks, plays, ya roles ko ek specific naam de sakte hain. Imagine karo ki aapke paas 100 tasks ka ek playbook hai, aur aapko sirf 5 tasks run karne hain jo "network configuration" se related hain. Agar un tasks ko `network` tag diya gaya hai, toh aap poore 100 tasks ko run kiye bina, sirf tagged tasks ko run kar sakte ho.

**Tags kyun use karte hain?**
*   **Selective Execution:** Poore playbook ko run kiye bina, specific parts ko run karna.
*   **Debugging:** Jab aap kisi naye feature par kaam kar rahe ho, toh sirf us related task ko run karke test kar sakte ho.
*   **Faster Iteration:** Development cycle ko speed up karta hai.
*   **Maintenance:** Jab aapko kisi existing setup mein minor change karna ho.

**Example:** Aap ek web server deploy kar rahe ho. Usme packages install karne ke tasks honge, config files modify karne ke tasks honge, aur service restart karne ke tasks honge. Aap `install`, `config`, `restart` jaise tags de sakte ho.

#### 1.2. Includes (इन्क्लूड्स) vs. Imports (इम्पोर्ट्स)

`Includes` aur `Imports` dono hi dusri files se tasks ya plays ko current playbook mein load karne ke liye use hote hain. Lekin inke kaam karne ka tareeka thoda alag hai – main difference hai **jab wo process hote hain** (parsing time vs. execution time).

**Think of it like this:**
*   **`import` (Static):** Jab aap apni kitaab likh rahe ho, tabhi aap decide kar lete ho ki kaun sa chapter kahan se aayega. Compiler/parser playbook ko run karne se pehle hi saari imported files ko ek single file ki tarah treat karta hai.
*   **`include` (Dynamic):** Aapki kitaab mein ek reference hai, ki "detail ke liye chapter X dekho". Jab reader wahan pahunchta hai, tabhi wo chapter X ko dekhta hai. `include` directives playbook run hote waqt, jab unki baari aati hai, tabhi files ko process karte hain.

##### 1.2.1. `include_tasks` vs. `import_tasks`

Ye directives aapko dusri YAML files se individual tasks load karne ki facility dete hain.

*   **`import_tasks` (Static Load):**
    *   **Jab parse hota hai:** `import_tasks` wali file ko playbook run hone se **pehle hi** parser include kar leta hai. Yaani, playbook execute hone se pehle, imported tasks main playbook ka hi part ban jaate hain.
    *   **Use case:** Jab aapko tasks ke ek set ko baar-baar use karna ho aur aapko runtime flexibility ki zaroorat na ho. Ye performance mein thoda better ho sakta hai kyunki parsing ek hi baar hoti hai.
    *   **Limitations:** Loops, conditions (जैसे `when`) directly `import_tasks` statement par apply nahin ho sakte. Wo imported tasks ke *andar* hi apply honge.

*   **`include_tasks` (Dynamic Load):**
    *   **Jab execute hota hai:** `include_tasks` wali file ko playbook run hote waqt, jab us `include_tasks` directive ki baari aati hai, tabhi wo process karta hai.
    *   **Use case:** Jab aapko `loop` ke saath tasks ko include karna ho, ya `when` condition ke base par alag-alag tasks include karne ho. Jab aapko runtime flexibility chahiye.
    *   **Flexibility:** Aap `loop` aur `when` conditions ko `include_tasks` statement ke saath directly use kar sakte ho. Ye kaafi powerful hai dynamic scenarios ke liye.

##### 1.2.2. `include_playbook` vs. `import_playbook`

Ye directives aapko poore ke poore playbooks ko load karne ki facility dete hain.

*   **`import_playbook` (Static Load):**
    *   **Functionality:** Ek poora playbook (`.yml` file jismein `hosts`, `tasks`, etc. define kiye gaye hon) ko current playbook mein include karta hai.
    *   **Jab parse hota hai:** Playbook run hone se pehle hi `import_playbook` kiye gaye playbooks ko parser process kar leta hai.
    *   **Use case:** Complex, multi-stage deployments jahan aap alag-alag modules (e.g., web server setup, database setup) ke liye alag-alag playbooks banate ho aur unko ek master playbook se orchestrate karte ho. Variables inherit hote hain.

*   **`include_playbook` (Dynamic Load):**
    *   **Functionality:** `import_playbook` ki tarah hi, but dynamic nature ke saath.
    *   **Jab execute hota hai:** Playbook run hote waqt, jab `include_playbook` directive ki baari aati hai, tabhi specified playbook ko process karta hai.
    *   **Use case:** Similar to `import_playbook`, but provides more runtime flexibility. Aap `loop` ke saath multiple playbooks include kar sakte ho, ya conditions ke base par alag playbooks run kar sakte ho. Ye kaafi naya feature hai (Ansible 2.8+ mein aaya hai).

---

### 2. Key Ansible Commands (मुख्य Ansible कमांड्स)

Ye commands aap Ansible control node par run karte hain:

*   **`ansible-playbook --tags  `**
    *   **Explanation (व्याख्या):** Ye command aapke specified playbook ko run karta hai, lekin sirf un tasks (ya plays/roles) ko execute karta hai jinhe `` tag diya gaya hai. Aap multiple tags ko comma separated bhi de sakte hain, jaise `--tags "install,config"`.
*   **`ansible-playbook --skip-tags  `**
    *   **Explanation (व्याख्या):** Is command se aap specify karte hain ki kaun se tags wale tasks ko **skip** karna hai. Baaki sab run honge. Ye bhi comma separated tags accept karta hai.
*   **`ansible-playbook --list-tags `**
    *   **Explanation (व्याख्या):** Ye command aapke playbook mein available sabhi tags ko list karta hai, bina playbook ko execute kiye. Ye debugging aur planning ke liye bohot useful hai.
*   **`ansible-playbook --list-tasks `**
    *   **Explanation (व्याख्या):** Ye command playbook mein मौजूद saare tasks ki list dikhata hai. Kabhi-kabhi, jab includes/imports hote hain, toh tags dekhne se pehle tasks ki list dekhna helpful hota hai.
*   **`ansible-playbook --start-at-task "" `**
    *   **Explanation (व्याख्या):** Ye command playbook ko ek specific task se start karta hai. Ye tags se alag hai, tags multiple tasks par apply ho sakte hain, lekin `--start-at-task` exactly us task se shuru karta hai aur aage badhta hai.

**Inside Playbooks (Playbook ke andar use hone wale directives):**

*   **`import_tasks: path/to/tasks.yml`**
    *   **Explanation:** Ye directive `tasks.yml` file mein define kiye gaye tasks ko statically load karta hai.
*   **`include_tasks: path/to/tasks.yml`**
    *   **Explanation:** Ye directive `tasks.yml` file mein define kiye gaye tasks ko dynamically load karta hai.
*   **`import_playbook: path/to/another_playbook.yml`**
    *   **Explanation:** Ye directive `another_playbook.yml` file mein define kiye gaye poore playbook ko statically load karta hai.
*   **`include_playbook: path/to/another_playbook.yml`**
    *   **Explanation:** Ye directive `another_playbook.yml` file mein define kiye gaye poore playbook ko dynamically load karta hai. (Requires Ansible 2.8+)

---

### 3. Step-by-Step Examples (स्टेप-बाय-स्टेप उदाहरण)

Hum assume karte hain ki aapke paas ek working Ansible setup hai jismein ek control node aur ek ya do managed nodes (e.g., `server1`, `server2`) hain. Aapki inventory file (`inventory.ini`) kuchh aisi ho sakti hai:

```ini
[webservers]
server1 ansible_host=192.168.1.10

[dbservers]
server2 ansible_host=192.168.1.11

[all:vars]
ansible_user=devops
ansible_ssh_private_key_file=~/.ssh/id_rsa
```

#### 3.1. Tags Example

**Goal:** Ek playbook banayenge jismein kuch tasks tagged honge aur hum unko selectively run karenge.

**Step 1: Create a playbook `web_deployment.yml`**
```yaml
# web_deployment.yml
---
- name: Deploy a simple web server
  hosts: webservers
  become: yes

  tasks:
    - name: Ensure Apache httpd is installed
      ansible.builtin.yum:
        name: httpd
        state: present
      tags: install, webserver

    - name: Ensure firewalld is running and configured for http
      ansible.builtin.firewalld:
        service: http
        permanent: yes
        state: enabled
      tags: firewall

    - name: Start and enable httpd service
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: yes
      tags: service, webserver

    - name: Create a custom index.html file
      ansible.builtin.copy:
        content: "
Welcome to Ansible Deployed Webserver!
"
        dest: /var/www/html/index.html
        mode: '0644'
      tags: config, webserver

    - name: Ensure SELinux allows httpd to connect to the network (if needed)
      ansible.posix.seboolean:
        name: httpd_can_network_connect
        state: yes
        persistent: yes
      tags: selinux
      when: ansible_facts['selinux']['status'] == 'enabled'
```

**Step 2: Check available tags**
```bash
ansible-playbook --list-tags web_deployment.yml
```
**Expected Output:**
```
playbook: web_deployment.yml

  plays:

    - Deploy a simple web server on webservers:
      tags:
        - always
        - all
        - firewall
        - install
        - config
        - selinux
        - service
        - webserver
```
`always` aur `all` tags by default Ansible provide karta hai. `always` tag wale tasks har baar run hote hain, chahe aap koi bhi tag specify karein.

**Step 3: Run only 'install' and 'service' related tasks**
```bash
ansible-playbook --tags "install,service" web_deployment.yml -i inventory.ini
```
**Output (partial):**
Aap dekhenge ki sirf `Ensure Apache httpd is installed` aur `Start and enable httpd service` tasks hi run honge. Baaki tasks `SKIPPED` dikhayenge.

**Step 4: Run the playbook, but skip 'firewall' tasks**
```bash
ansible-playbook --skip-tags "firewall" web_deployment.yml -i inventory.ini
```
**Output (partial):**
Abki baar `Ensure firewalld is running and configured for http` task `SKIPPED` ho jayega, baaki sab run honge.

#### 3.2. `import_tasks` Example

**Goal:** Tasks ko alag files mein store karna aur `import_tasks` use karke unhe main playbook mein load karna.

**Step 1: Create separate task files**

*   **`tasks/install_web.yml`**
    ```yaml
    # tasks/install_web.yml
    - name: Ensure Apache httpd is installed (from imported tasks)
      ansible.builtin.yum:
        name: httpd
        state: present
    ```

*   **`tasks/configure_web.yml`**
    ```yaml
    # tasks/configure_web.yml
    - name: Create a custom index.html file (from imported tasks)
      ansible.builtin.copy:
        content: "
Welcome! This is configured via imported tasks.
"
        dest: /var/www/html/index.html
        mode: '0644'

    - name: Start and enable httpd service (from imported tasks)
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: yes
    ```
    *Note:* `tasks` directory playbook ke same level par hona chahiye ya fir relative path sahi ho.

**Step 2: Create a main playbook `master_web_import.yml`**
```yaml
# master_web_import.yml
---
- name: Master playbook using import_tasks
  hosts: webservers
  become: yes

  tasks:
    - name: Pre-import task
      ansible.builtin.debug:
        msg: "Starting web server setup..."

    - name: Import installation tasks
      import_tasks: tasks/install_web.yml

    - name: Import configuration tasks
      import_tasks: tasks/configure_web.yml

    - name: Post-import task
      ansible.builtin.debug:
        msg: "Web server setup completed."
```

**Step 3: Run the master playbook**
```bash
ansible-playbook master_web_import.yml -i inventory.ini
```
**Output (partial):**
Aap dekhenge ki saare tasks, including those from `install_web.yml` and `configure_web.yml`, sequentially run honge. Parser ne run hone se pehle hi unhe main playbook ka part bana diya tha.

#### 3.3. `include_tasks` Example (with loop)

**Goal:** `include_tasks` ki dynamic nature ko use karte hue, loop ke saath multiple configuration files deploy karna.

**Step 1: Create a generic configuration task file `tasks/deploy_config.yml`**
```yaml
# tasks/deploy_config.yml
- name: Deploy {{ config_name }} configuration file
  ansible.builtin.copy:
    content: "This is the content for {{ config_name }}. Deployed on {{ ansible_hostname }}."
    dest: "/tmp/{{ config_name }}.conf"
    mode: '0644'
```
*Note:* Yahan hum `config_name` variable use kar rahe hain, jo loop ke har iteration mein change hoga.

**Step 2: Create a main playbook `master_web_include.yml`**
```yaml
# master_web_include.yml
---
- name: Master playbook using include_tasks with loop
  hosts: webservers
  become: yes

  vars:
    config_files:
      - apache_vhost
      - app_settings
      - database_connection

  tasks:
    - name: Initial setup
      ansible.builtin.debug:
        msg: "Beginning dynamic configuration deployment."

    - name: Loop through configurations and include tasks
      include_tasks: tasks/deploy_config.yml
      loop: "{{ config_files }}"
      loop_control:
        loop_var: config_name # This maps loop item to config_name variable inside included tasks

    - name: Final check
      ansible.builtin.debug:
        msg: "All configurations deployed dynamically."
```

**Step 3: Run the master playbook**
```bash
ansible-playbook master_web_include.yml -i inventory.ini
```
**Output (partial):**
Aap dekhenge ki `Deploy configuration file` task teen baar run hoga, har baar `config_name` variable ki value `apache_vhost`, `app_settings`, `database_connection` mein se ek hogi. Isse `/tmp/apache_vhost.conf`, `/tmp/app_settings.conf`, `/tmp/database_connection.conf` files create hongi. Ye `include_tasks` ki dynamic capability ka best example hai.

#### 3.4. `import_playbook` Example

**Goal:** Multiple, independent playbooks ko ek master playbook se orchestrate karna.

**Step 1: Create child playbooks**

*   **`playbooks/setup_firewall.yml`**
    ```yaml
    # playbooks/setup_firewall.yml
    ---
    - name: Configure firewall for webservers
      hosts: webservers
      become: yes
      tasks:
        - name: Ensure firewalld is running and configured for http (child playbook)
          ansible.builtin.firewalld:
            service: http
            permanent: yes
            state: enabled
            immediate: yes
    ```

*   **`playbooks/install_deps.yml`**
    ```yaml
    # playbooks/install_deps.yml
    ---
    - name: Install dependencies on webservers
      hosts: webservers
      become: yes
      tasks:
        - name: Install common packages (child playbook)
          ansible.builtin.yum:
            name:
              - git
              - unzip
            state: present
    ```

**Step 2: Create a master orchestration playbook `master_orchestration.yml`**
```yaml
# master_orchestration.yml
---
- name: Main Orchestration Playbook
  hosts: localhost # Master playbook often runs on localhost or control node for orchestration

  tasks:
    - name: Pre-orchestration message
      ansible.builtin.debug:
        msg: "Starting overall deployment process."

    - name: Import playbook for firewall setup
      import_playbook: playbooks/setup_firewall.yml

    - name: Import playbook for dependency installation
      import_playbook: playbooks/install_deps.yml

    - name: Post-orchestration message
      ansible.builtin.debug:
        msg: "Overall deployment process completed."
```
*Note:* Jab aap `import_playbook` karte hain, toh imported playbook ki `hosts` directive hi use hoti hai. Master playbook ka `hosts` agar `localhost` hai, toh wo sirf master playbook ke tasks ke liye apply hoga.

**Step 3: Run the master playbook**
```bash
ansible-playbook master_orchestration.yml -i inventory.ini
```
**Output (partial):**
Aap dekhenge ki `setup_firewall.yml` aur `install_deps.yml` dono playbooks ke tasks run honge, although unhe `master_orchestration.yml` ne `localhost` par run karne ke liye kaha tha. Ye demonstrate karta hai ki `import_playbook` target hosts ko imported playbook se hi leta hai.

---

### 4. Practice Tasks (अभ्यास के कार्य)

Khud se try karne ke liye yahan kuch exercises hain:

1.  **Tag-based Webserver Management:**
    *   Ek playbook banao jo Apache (`httpd`), Nginx, aur MariaDB server ko install/configure kar sake.
    *   Tasks ko suitable tags do (e.g., `apache_install`, `nginx_config`, `mariadb_service`, `common`).
    *   Command line se sirf Apache ko install aur configure karo.
    *   Command line se Nginx ko configure karo, lekin MariaDB ko skip karo.
    *   List all available tags from your playbook.

2.  **Modular Task Management with `import_tasks` and `include_tasks`:**
    *   Ek directory structure banao: `tasks/common_setup.yml`, `tasks/app_deploy.yml`, `tasks/db_setup.yml`.
    *   `common_setup.yml` mein kuch common setup tasks rakho (e.g., update `yum` cache, install `epel-release`).
    *   `app_deploy.yml` mein ek task rakho jo `/opt/app` directory banaye aur ek simple `app.conf` file deploy kare.
    *   `db_setup.yml` mein ek task rakho jo `/var/lib/mysql/` mein `db_init.sql` file deploy kare.
    *   Ek main playbook banao jo:
        *   `common_setup.yml` ko `import_tasks` kare.
        *   Ek `loop` ka use karte hue, `include_tasks` ka use kare, jismein `app_deploy.yml` aur `db_setup.yml` ko dynamically include kiya ja sake. Iske liye aap `vars` mein ek list bana sakte ho jismein `app` aur `db` jaisi entries ho aur unke base par files include karein. (Hint: `include_tasks: tasks/{{ item }}_deploy.yml` will not work directly, you need a common task file that can use variables passed in a loop). A better approach would be to have a single generic task file and pass parameters via loop.

3.  **Multi-Tier Application Orchestration:**
    *   Do alag playbooks banao: `web_tier.yml` (webservers group ke liye) aur `db_tier.yml` (dbservers group ke liye).
    *   `web_tier.yml` mein Apache installation aur basic config ke tasks rakho.
    *   `db_tier.yml` mein MariaDB installation aur basic config ke tasks rakho.
    *   Ek `main_deployment.yml` banao jo `import_playbook` ka use karte hue pehle `web_tier.yml` aur fir `db_tier.yml` ko run kare. Ensure ki har tier apne respective host group par hi run ho.
    *   Try to pass a variable from `main_deployment.yml` to `web_tier.yml` (e.g., `web_port: 8080`) and use it inside `web_tier.yml`.

---

### 5. Pro Tips / Common Mistakes (प्रो टिप्स / सामान्य गलतियाँ)

#### 5.1. Tags Tips

*   **Be Specific but Not Overly Granular:** Tags ko bohot zyada detailed mat banao. `install`, `config`, `service`, `security`, `database`, `network` jaise general tags acche hote hain.
*   **Use `always` Tag Wisely:** Agar koi task hai jo har haal mein run hona chahiye (e.g., cleanup task, reporting task), toh use `always` tag do.
    ```yaml
    - name: Critical cleanup task
      ansible.builtin.command: /usr/local/bin/cleanup.sh
      tags: always
    ```
*   **Default Tag:** Agar aap kisi task ko koi tag nahin dete hain, toh wo by default `all` tag ke saath run hota hai.
*   **Tags on Plays and Roles:** Aap tasks ki tarah, poore plays ya roles par bhi tags apply kar sakte hain.

#### 5.2. Includes/Imports Tips

*   **Modularity:** Apne playbooks ko chote, manageable files mein break karo. Ye readability aur maintainability badhata hai.
*   **`roles` is the Next Step:** Jab aapko aur zyada structure aur reusability chahiye (tasks, handlers, vars, templates, files sab ek saath group karne hon), tab `roles` ka use karo. `roles` internally `include_tasks` ka hi use karte hain. Ye topic RHCE ke liye bahut important hai.
*   **Variable Scope:** Variables ko handle karte waqt careful raho. Imported files parent playbook ke variables ko access kar sakte hain. `include_tasks` ke saath `vars` block use karke aap included tasks ko specific variables pass kar sakte ho:
    ```yaml
    - name: Include tasks with extra vars
      include_tasks: tasks/my_specific_tasks.yml
      vars:
        my_variable: "value_for_this_include"
    ```
*   **Relative Paths:** Jab `include_tasks` ya `import_tasks` use karte ho, toh paths relative to the calling playbook file specify karo.

#### 5.3. Common Mistakes to Avoid

*   **Confusing Static vs. Dynamic:** `import` aur `include` ke beech ka difference bhool jaana sabse common mistake hai. Yaad rakho:
    *   **`import` = Static = Parse time = No loops/conditions on the directive itself.**
    *   **`include` = Dynamic = Run time = Loops/conditions allowed on the directive.**
*   **Over-Reliance on Includes/Imports:** Jab aapka logic bohot complex ho jaata hai aur files ka jungle ban jaata hai, toh consider using `roles`. Roles ek better structure dete hain.
*   **Incorrect Pathing:** Ensure ki aapki `include` ya `import` statements mein file paths correct hain. Relative paths mostly recommended hote hain.
*   **Debugging Included/Imported Content:** Jab koi issue aata hai, toh yaad rakho ki imported content main playbook ka hi part ban jaata hai. `ansible-playbook --syntax-check` ya `-vvv` verbose output aapko debugging mein help karega.
*   **`include_playbook` and `import_playbook` with `hosts: localhost`:** Agar aapka master playbook `hosts: localhost` par run ho raha hai, toh imported/included child playbooks **apne `hosts` directive ko hi honour karenge**. Unki hosts value master playbook se override nahin hogi. Is baat ka dhyan rakhein ki kis playbook ka `hosts` kaun se targets par apply hoga.

---

### Conclusion (निष्कर्ष)

Toh dosto, aaj humne Tags, Includes, aur Imports ke baare mein detailed mein seekha. Ye Ansible ki bohot powerful features hain jo aapke automation workflows ko organized, efficient aur maintainable banate hain. Tags aapko selective execution ki power dete hain, jabki Includes aur Imports aapko apne playbooks ko modular banane mein madad karte hain. Practice karte rahiye, aur aap dekhenge ki Ansible automation kitna smooth ho jaata hai!

Agar aapke koi sawal hain, toh bilkul poochiye. Happy Automating!
