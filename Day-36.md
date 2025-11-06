Day 36: Templates (Jinja2), handlers, notify
---
नमस्ते Linux enthusiasts aur mere future RHCSA/RHCE champs!

Aaj hum Ansible ke teen bahut hi powerful aur inter-related concepts ke baare mein baat karenge jo aapki automation capabilities ko next level par le jayenge: **Templates (Jinja2), Handlers, aur Notify**. Yeh topics RHCSA aur RHCE dono exams ke liye critical hain, aur real-world DevOps scenarios mein to inka use almost har dusre playbook mein hota hai.

Chaliye, shuru karte hain!

---

## ������ Comprehensive RHCSA + RHCE Training: Templates (Jinja2), Handlers, & Notify in Ansible

### 1. Concept Explanation (Hinglish)

#### 1.1 Templates (Jinja2)

**Kya Hai Templates?**
Imagine karo, aapke paas 100 web servers hain aur har server par aapko ek `nginx.conf` file deploy karni hai. Har server ka `listen_port` ya `server_name` thoda alag ho sakta hai (jaise server A port 80 par sunega, server B port 8080 par). Ab aap har server ke liye alag se config file banoge toh kaam kitna mushkil ho jayega, right?

Yahi problem solve karte hain **Templates**. Template ek blueprint jaisa hota hai, ek master copy jisme hum variables use karte hain. Ansible jab is template ko target server par deploy karta hai, toh woh in variables ko actual values se replace kar deta hai.

**Jinja2 Kya Hai?**
Ansible internally **Jinja2** naam ka ek templating engine use karta hai. Jinja2 Python par based hai aur aapko bahut saari flexibility deta hai:

*   **Variables:** `{{ variable_name }}` syntax se aap playbook ya inventory se variables templates mein inject kar sakte ho.
*   **Loops:** `{% for item in list_of_items %}` loop se aap repetitive content generate kar sakte ho.
*   **Conditionals:** `{% if condition %}` statements se aap conditional logic dal sakte ho (jaise, agar OS RedHat hai toh yeh line add karo).
*   **Filters:** `{{ variable | filter_name }}` se aap variables ki values ko modify kar sakte ho (jaise, string ko uppercase karna, integer mein convert karna).

**Kyun Zaroori Hain Templates?**
*   **Dynamic Configuration:** Aap ek hi template se multiple servers ke liye customized config files bana sakte hain.
*   **Maintainability:** Configuration logic ek centralized template mein hoti hai, changes karna aasan ho jata hai.
*   **Reusability:** Templates ko multiple playbooks aur roles mein reuse kiya ja sakta hai.

#### 1.2 Handlers

**Kya Hain Handlers?**
Socho, aapne apne Nginx server ki configuration file (jaise `nginx.conf`) ko template use karke update kiya. Nginx server ko pata kaise chalega ki uski config file badal gayi hai? Usko restart ya reload karna padega, right?

Yahan aate hain **Handlers**! Handlers special tasks hote hain jo tabhi run karte hain jab unko explicitly "notify" kiya jaye. Woh regular tasks ki tarah har baar nahi chalte, sirf tab chalte hain jab unki zaroorat ho (e.g., jab koi configuration file change hui ho).

**Handlers ka Purpose:**
*   Service restarts/reloads (most common use case).
*   Database schema updates.
*   System reboots (though less common with handlers).
*   Koi bhi action jo tabhi hona chahiye jab ek previous task ne system state ko change kiya ho.

**Key Difference from Regular Tasks:**
*   Regular tasks har playbook run mein execute hote hain (unless skipped by conditions).
*   Handlers sirf tab execute hote hain jab unhe kisi *changed* task ne `notify` kiya ho.
*   Ek handler kitni bhi baar `notify` kiya jaye, woh playbook run ke end mein sirf ek baar execute hota hai (ensuring idempotency for actions like restarts).

#### 1.3 Notify

**Kya Hai Notify?**
`notify` keyword woh mechanism hai jisse ek task kisi handler ko batata hai ki "Bhai, mera kaam ho gaya hai, aur maine system mein kuch change kiya hai, ab tum apna kaam kar sakte ho."

**`notify` Kaise Kaam Karta Hai:**
1.  Aap apne playbook mein ek task likhte ho (jaise `template` module use karke Nginx config file deploy karna).
2.  Agar woh task successfully run hota hai aur usne system mein *actual change* kiya hai (Ansible us task ko "changed" state mein mark karta hai), toh woh task apne `notify` keyword mein specified handler ko trigger karta hai.
3.  Yeh trigger immediately nahi hota. Ansible saare regular tasks ko finish karta hai.
4.  Jab saare regular tasks khatam ho jaate hain, tab Ansible ek-ek karke un saare handlers ko run karta hai jinhe `notify` kiya gaya tha, aur har handler sirf ek baar run hota hai.

**Example Flow:**
*   `template` module runs, deploys `nginx.conf` and detects a change.
*   This task has `notify: restart nginx`.
*   `restart nginx` handler is added to a list of handlers to be run.
*   Playbook finishes all other regular tasks.
*   Finally, the `restart nginx` handler runs, restarting Nginx.

---

### 2. Key Ansible Modules / Keywords (Hinglish)

Yeh topic mostly Ansible keywords aur modules par centered hai, na ki direct Linux shell commands par.

*   **`ansible-playbook`**:
    *   **Explanation:** Yeh woh command hai jisse aap apne Ansible playbooks ko run karte ho. Aapke saare templates, handlers, aur notify isi command ke through execute honge.
    *   **Usage:** `ansible-playbook your_playbook.yml`

*   **`template` module (Ansible module):**
    *   **Explanation:** Yeh module Jinja2 templates ko process karke target machine par deploy karta hai. Source template file (`.j2` extension use karna best practice hai) ko destination path par copy karta hai, variables ko replace karta hai, aur permissions set karta hai.
    *   **Key Parameters:**
        *   `src`: Local path to your Jinja2 template file.
        *   `dest`: Remote path where the processed file will be copied.
        *   `owner`: File ka owner.
        *   `group`: File ka group.
        *   `mode`: File permissions.
    *   **Usage in Playbook:**
        ```yaml
        - name: Deploy custom Nginx config
          ansible.builtin.template:
            src: templates/nginx.conf.j2
            dest: /etc/nginx/nginx.conf
            owner: root
            group: root
            mode: '0644'
        ```

*   **`handlers` keyword (Ansible playbook keyword):**
    *   **Explanation:** Yeh ek special section hai aapke playbook mein jahan aap un tasks ko define karte ho jo as a handler run honge. Inhe `notify` keyword se call kiya jata hai.
    *   **Usage in Playbook:**
        ```yaml
        - name: My awesome playbook
          hosts: webservers
          tasks:
            # ... regular tasks ...

          handlers:
            - name: restart nginx
              ansible.builtin.service:
                name: nginx
                state: restarted
        ```

*   **`notify` keyword (Ansible task keyword):**
    *   **Explanation:** Is keyword ko kisi task ke andar use kiya jata hai. Jab woh task successfuly run hota hai aur usne "changed" state report ki hai, toh `notify` keyword mein specified handler ko invoke kiya jata hai.
    *   **Usage in Playbook:**
        ```yaml
        - name: Deploy custom Nginx config
          ansible.builtin.template:
            src: templates/nginx.conf.j2
            dest: /etc/nginx/nginx.conf
            owner: root
            group: root
            mode: '0644'
          notify: restart nginx # This will notify the handler named 'restart nginx'
        ```

*   **Jinja2 Syntax Elements:**
    *   `{{ variable_name }}`: Variable substitution.
    *   `{% for item in list %}`: Loops.
    *   `{% if condition %}`: Conditionals.
    *   `| filter_name`: Filters for data manipulation.

---

### 3. Step-by-Step Examples: Nginx Deployment with Custom Config

Chalo, ek real-world scenario dekhte hain. Hum Nginx ko deploy karenge aur uski configuration ko Jinja2 template se manage karenge. Jab bhi config file mein koi change hoga, Nginx automatically restart ho jayega.

**Lab Setup:**
Aapko ek Ansible control node aur ek target node (jahan Nginx install hoga) chahiye. Ensure ki SSH access aur `sudo` privileges configured hain.

**Step 1: Inventory Setup**

Control node par apni inventory file (`hosts.ini`) banayein:
```ini
# hosts.ini
[webservers]
web1 ansible_host=your_target_server_ip_or_hostname
```
*`your_target_server_ip_or_hostname` ko apne target server ke IP ya hostname se replace karein.*

**Step 2: Create a Jinja2 Template for Nginx Configuration**

Ek directory banao `templates` aur uske andar `nginx.conf.j2` file banao:
```bash
mkdir templates
vi templates/nginx.conf.j2
```
`templates/nginx.conf.j2` file ka content yeh hoga:
```jinja2
# templates/nginx.conf.j2
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;

    server {
        listen {{ nginx_listen_port | default(80) }}; # Default to 80 if not specified
        server_name {{ ansible_fqdn | default('localhost') }}; # Use FQDN or localhost

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }
}
```
*Is template mein humne `nginx_listen_port` aur `ansible_fqdn` jaise variables use kiye hain.*
*`| default(80)` ek Jinja2 filter hai jo agar `nginx_listen_port` define nahi hai toh 80 use karega.*
*`ansible_fqdn` ek Ansible magic variable hai jo target host ka fully qualified domain name deta hai.*

**Step 3: Create a Playbook with `template` Module and Handlers**

Apni playbook file banao (`nginx_deploy.yml`):
```bash
vi nginx_deploy.yml
```
`nginx_deploy.yml` ka content yeh hoga:
```yaml
# nginx_deploy.yml
---
- name: Deploy Nginx web server with templated config
  hosts: webservers
  become: yes # Root privileges ke liye

  vars:
    nginx_listen_port: 8080 # Custom port define kar rahe hain

  tasks:
    - name: Ensure Nginx is installed
      ansible.builtin.yum: # ya apt agar Debian/Ubuntu hai
        name: nginx
        state: present
      register: nginx_install_status

    - name: Start and enable Nginx service
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: yes

    - name: Deploy custom Nginx config from template
      ansible.builtin.template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
        owner: root
        group: root
        mode: '0644'
      notify: restart nginx # Jab config file change ho, toh 'restart nginx' handler ko notify karo.

    - name: Create a simple index.html
      ansible.builtin.copy:
        content: "
Hello from {{ ansible_hostname }} (Port {{ nginx_listen_port }})!
"
        dest: /usr/share/nginx/html/index.html
        owner: nginx
        group: nginx
        mode: '0644'

  handlers:
    - name: restart nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
      listen: "restart nginx" # Handlers multiple names se listen kar sakte hain
```

**Step 4: Run the Playbook and Observe**

**First Run:**
```bash
ansible-playbook -i hosts.ini nginx_deploy.yml
```
**Output mein dekho:**
*   `Deploy custom Nginx config from template` task `changed` state mein hoga.
*   Playbook ke end mein `Running handlers:` section mein `restart nginx` handler run hoga.
*   Target server par `http://your_target_server_ip_or_hostname:8080` browse karke dekho. Aapko custom message dikhega.

**Second Run (No Changes):**
```bash
ansible-playbook -i hosts.ini nginx_deploy.yml
```
**Output mein dekho:**
*   Saare tasks `ok` state mein honge (no changes detected).
*   `Deploy custom Nginx config from template` task bhi `ok` state mein hoga.
*   **Most importantly:** `restart nginx` handler run nahi hoga, kyunki koi change detect nahi hua. This demonstrates idempotency!

**Third Run (Change a Variable):**
Playbook `nginx_deploy.yml` mein `nginx_listen_port` ko change karo (e.g., `80`).
```yaml
  vars:
    nginx_listen_port: 80 # Port ko 80 par change kiya
```
Ab playbook ko fir se run karo:
```bash
ansible-playbook -i hosts.ini nginx_deploy.yml
```
**Output mein dekho:**
*   `Deploy custom Nginx config from template` task will again be `changed` (kyunki template output mein port change ho gaya).
*   Playbook ke end mein `restart nginx` handler fir se run hoga.
*   Ab Nginx port 80 par sun raha hoga.

---

### 4. Practice Tasks (Hinglish)

Yahan kuch practical exercises hain jinhe aap try kar sakte ho apni understanding ko strengthen karne ke liye:

**Task 1: Apache Config Template with Multiple Variables**

*   **Goal:** Apache web server ki `httpd.conf` file ko template se manage karo.
*   **Steps:**
    1.  Apache install karne ke liye ek playbook banao.
    2.  `templates/httpd.conf.j2` naam ka ek template banao.
    3.  Is template mein kam se kam teen variables use karo, jaise:
        *   `apache_listen_port` (e.g., 80, 8080)
        *   `apache_document_root` (e.g., `/var/www/html`, `/srv/web`)
        *   `apache_server_admin` (e.g., `webmaster@example.com`)
    4.  Playbook mein `apache_listen_port` aur `apache_document_root` ko `host_vars` ya `group_vars` mein define karo (taaki alag-alag hosts ke liye alag values ho sakein).
    5.  `template` module use karke `httpd.conf.j2` ko `/etc/httpd/conf/httpd.conf` par deploy karo.
    6.  Ek handler define karo jo Apache service ko `restarted` karega.
    7.  `template` task mein is handler ko `notify` karo.
    8.  Playbook run karo aur changes observe karo.
    9.  Variables ki values change karke fir se run karo aur dekho ki handler kab trigger hota hai.

**Task 2: Dynamic User Creation Script Template**

*   **Goal:** Ek shell script template banao jo ek naya user create karega, aur us script ko `template` module se deploy karke `command` module se execute karo.
*   **Steps:**
    1.  `templates/create_user.sh.j2` naam ki ek file banao.
    2.  Is template mein `username` aur `home_dir` variables use karo. Script user ko add kare aur uski home directory banaye.
        ```jinja2
        #!/bin/bash
        useradd -m -d {{ home_dir }} -s /bin/bash {{ username }}
        echo "User {{ username }} created with home directory {{ home_dir }}"
        ```
    3.  Playbook banao jo:
        *   `create_user.sh.j2` ko `/tmp/create_user.sh` par deploy kare.
        *   `file` module se script ko executable banaye (`mode: '0755'`).
        *   `command` module se `/tmp/create_user.sh` ko execute kare.
        *   *(Optional)* Ek handler define karo jo ek log file mein entry add kare jab koi user create ho, aur `command` task se use `notify` karo.

**Task 3: Multiple Handlers and Conditional Logic**

*   **Goal:** Nginx example ko extend karo jahan configuration change hone par multiple handlers trigger hon.
*   **Steps:**
    1.  Apne `nginx_deploy.yml` playbook ko modify karo.
    2.  `handlers` section mein ek naya handler add karo, jaise `log config change`.
        ```yaml
        - name: log config change
          ansible.builtin.command: "echo 'Nginx config changed on {{ ansible_hostname }} at {{ ansible_date_time.iso8601_micro }}' >> /var/log/nginx_config_changes.log"
          args:
            creates: /var/log/nginx_config_changes.log # Agar file exist karti hai toh run na ho
        ```
    3.  Apne `template` task mein `notify` ko list mein change karo taaki woh dono handlers ko notify kare:
        ```yaml
        - name: Deploy custom Nginx config from template
          ansible.builtin.template:
            src: templates/nginx.conf.j2
            dest: /etc/nginx/nginx.conf
            owner: root
            group: root
            mode: '0644'
          notify:
            - restart nginx
            - log config change
        ```
    4.  Playbook run karo, configuration change karo, aur fir se run karo. Target server par `/var/log/nginx_config_changes.log` file check karo.

---

### 5. Pro Tips / Common Mistakes (Hinglish)

#### Pro Tips:

*   **Idempotency is King:** Ansible ka main principle idempotency hai. Handlers bhi idempotent hone chahiye. Jaise, `state: restarted` service ko hamesha restart karega, bhale hi woh pehle se running ho. Agar aap `state: reloaded` use kar sakte ho (jo service ko bina downtime ke config reload karta hai), toh use prefer karo.
*   **Jinja2 Filters ka Bharpur Upyog:** Jinja2 mein bahut saare useful filters hain. Kuch common ones:
    *   `| default(value)`: Agar variable undefined hai toh default value provide karta hai.
    *   `| upper`, `| lower`, `| capitalize`: String manipulation.
    *   `| int`, `| float`: Type conversion.
    *   `| to_nice_json`: Debugging ke liye data ko readable JSON format mein print karta hai.
    *   `| join(separator)`: List elements ko string mein join karta hai.
*   **`check_mode` aur `diff` ka use karein:** Playbook run karne se pehle, `ansible-playbook --check --diff -i hosts.ini your_playbook.yml` command se dekh lo ki kya changes honge. Yeh Jinja2 template ki final output bhi dikhayega.
*   **Handler Naming:** Handlers ko unique aur descriptive names do. Yeh `notify` karte waqt clarity provide karta hai.
*   **`listen` keyword for Handlers:** Jab aap `notify` mein multiple handlers ko trigger karte ho ya roles mein handlers define karte ho, toh `listen` keyword handlers ko aur flexible banata hai. Ek handler multiple events ko `listen` kar sakta hai.
    ```yaml
    # In handler definition
    - name: restart web service
      ansible.builtin.service:
        name: httpd
        state: restarted
      listen:
        - "restart httpd"
        - "reload web config" # This handler can be triggered by either name
    ```
*   **Pre-Handlers/Post-Handlers (Roles mein):** Roles ke andar `pre_tasks`, `tasks`, aur `post_tasks` sections hote hain. `handlers` ek alag directory (`handlers/main.yml`) mein rehte hain. Kabhi-kabhi aapko kuch actions play ke start ya end mein run karne padte hain, tab `pre_tasks` ya `post_tasks` kaam aate hain.

#### Common Mistakes:

*   **`notify` bhool jana:** Ek task configuration file deploy kar deta hai, lekin `notify` keyword add karna bhool gaye. Result: config change ho gayi, lekin service restart nahi hui. Hamesha verify karein ki `template` module ke saath `notify` hai agar service restart ya reload ki zaroorat hai.
*   **Incorrect Handler Name:** `notify: restart web` likha, lekin handler ka name `restart_apache_service` hai. Ansible handler ko find nahi kar payega. `notify` aur `handler name` exact match hone chahiye.
*   **Expecting Handler to Run Every Time:** Yeh galti bahut common hai beginners mein. Handlers sirf tab run hote hain jab unhe `notify` karne wala task "changed" state mein ho. Agar task "ok" state mein hai, toh handler nahi chalega. Isliye, configuration mein actual change hone par hi handler trigger hota hai.
*   **Putting Complex Logic in Handlers:** Handlers ko simple aur specific rakho (e.g., service restart, reload). Complex logic ko regular tasks mein ya alag scripts mein rakho.
*   **Jinja2 Syntax Errors:** Curly braces (`{{ }}` for variables, `{% %}` for logic) mein typos. Ek choti si mistake bhi template ko render hone se rok sakti hai.
*   **Using `copy` instead of `template` for Jinja2 files:** Agar aap `.j2` file ko `copy` module se deploy karte ho, toh Jinja2 engine use process nahi karega aur aapko raw template file, including `{{ }}` and `{% %}` syntax, target server par milegi. Hamesha `template` module use karein `.j2` files ke liye.

---

Umeed hai yeh comprehensive lesson aapko Templates, Handlers, aur Notify ke concepts ko achhe se samajhne mein madad karega. Yeh Ansible ke bahut powerful features hain jo aapki automation ko efficient aur robust banate hain. Ab practice karo, aur RHCSA/RHCE exams mein rock karo!
