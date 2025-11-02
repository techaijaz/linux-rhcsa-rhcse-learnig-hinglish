Day 33: Variables & facts (ansible_facts)
---
Namaste, dosto! Main aapka Linux instructor, aur aaj hum Ansible ke ek bahut hi **fundamental aur powerful topic** par dive karenge: **"Variables & Facts (ansible_facts)"**. Ye topic RHCSA aur RHCE dono exams ke liye crucial hai, aur real-world automation mein iske bina kaam karna bahut mushkil hai.

Chaliye shuru karte hain!

---

## **RHCSA + RHCE Training Lesson: Variables & Facts (ansible_facts)**

### 1. Concept Explanation (Hinglish)

Sochho, aapko 100 servers par ek hi configuration apply karni hai, lekin har server ka hostname ya IP address alag hai. Ya fir, kuch servers RedHat hain aur kuch Debian, aur aapko unke OS ke hisaab se alag-alag packages install karne hain. Yahan par "Variables" aur "Facts" ka role aata hai.

**Variables (Variables):**
Variables, jaise ki naam se hi pata chalta hai, **placeholders** hote hain jinme aap values store kar sakte hain. Ye values badalti rehti hain ya aap unhe badal sakte hain. Ansible mein, variables aapko apne playbooks ko **dynamic aur reusable** banane mein help karte hain.

*   **Socho aise:** Agar aapko 50 servers par NGINX install karna hai aur uska port `8080` rakhna hai. Aap har playbook mein `8080` hardcode kar sakte ho. Lekin kal ko agar aapko port `8081` karna pada, toh aapko 50 playbooks mein jaakar change karna padega. Iski jagah, agar aap ek variable `nginx_port: 8080` define kar do, toh aapko bas ek jagah variable ki value change karni hogi, aur saare playbooks automatically update ho jayenge. Bahut zaroori hai na!
*   **Kahan define karte hain:**
    *   **Playbook Level:** Directly playbook file mein `vars:` keyword ke andar.
    *   **Inventory Level:** `inventory.ini` file mein hosts ya groups ke liye.
        *   `host_vars/` directory mein host-specific variables.
        *   `group_vars/` directory mein group-specific variables.
    *   **Extra Variables (`-e`):** Command line se runtime par pass kiye gaye variables. Ye bahut powerful hote hain for quick overrides.
    *   **Role Variables:** Jab aap roles use karte hain, toh role ke andar bhi variables define kiye ja sakte hain.
    *   **Vault Variables:** Sensitive information (jaise passwords) ko store karne ke liye `ansible-vault` use karte hain.
*   **Kaise use karte hain:** Variables ko `{{ variable_name }}` syntax ke saath use karte hain. For example, `{{ nginx_port }}`.

**Facts (ansible_facts):**
Facts, target systems (managed hosts) ke baare mein **automatically discovered information** hoti hai. Jab Ansible kisi host par tasks execute karta hai, toh by default wo us host ki saari details gather karta hai. Is process ko **Fact Gathering** kehte hain, aur ye `setup` module ke through hota hai.

*   **Socho aise:** Aap apne laptop ke baare mein kya-kya jaante ho? OS ka naam, version, IP address, total RAM, CPU cores, network interfaces, disk space, etc. Ansible managed hosts ke baare mein bhi ye sab jaankari khud hi discover kar leta hai. Is information ko `ansible_facts` ke object mein store kiya jata hai.
*   **Ye information kis kaam aati hai:**
    *   **Conditional Logic:** Agar OS RedHat hai toh `yum` use karo, agar Debian hai toh `apt` use karo. (`when: ansible_os_family == "RedHat"`)
    *   **Reporting:** Servers ki inventory banani ho toh `ansible_default_ipv4.address` ya `ansible_memory_mb.real.total` jaise facts use kar sakte hain.
    *   **Dynamic Configuration:** Server ke IP address ko configuration file mein dynamically add karna.
*   **Kaise access karte hain:** Facts ko bhi variables ki tarah `{{ ansible_fact_name }}` syntax ke saath access karte hain. For example, `{{ ansible_os_family }}`.
*   **Fact Gathering Disable karna:** Agar aapko facts ki zaroorat nahi hai, toh performance improve karne ke liye aap `gather_facts: no` set kar sakte hain playbook mein.

**Variable Precedence (Variables ki Priority):**
Ye bahut important topic hai. Agar aapne ek hi variable ko alag-alag jagahon par define kiya hai (jaise inventory mein aur playbook mein), toh Ansible kaunsi value use karega? Iske liye ek precedence order hota hai. **(RHCE level ke liye bahut zaroori hai)**.

Generally, jo variable definition **tasks ke close** hoti hai, uski priority jyada hoti hai. Ek simplified order (high to low):

1.  Extra Variables (`-e`)
2.  `vars:` defined in `include_vars` or `file` or `lookup`
3.  `vars:` in playbook (defined directly in `vars:` block)
4.  `vars:` from `vars_files:`
5.  Role variables (vars in `roles/x/vars/main.yml`)
6.  `group_vars/` (related to the playbook)
7.  `host_vars/` (related to the playbook)
8.  Inventory variables (defined in `inventory.ini`)
9.  Facts (discovered `ansible_facts`)
10. Default role variables (vars in `roles/x/defaults/main.yml`)

Samajhna ye hai ki, jo value sabse specific hoti hai, wo usually jeet jati hai.

---

### 2. Key Linux Commands (Hinglish)

Ansible mein variables aur facts se related kuch important commands:

*   **`ansible -m setup [hostname_or_group]`**
    *   **Kaam:** Ye command kisi bhi managed host se saare facts (discovered information) collect karke display karta hai. Aapko pata chal jayega ki Ansible us particular host ke baare mein kya-kya jaanta hai.
    *   **Upyog:** Apne target systems ki details explore karne ke liye, ya kisi specific fact ka exact naam pata karne ke liye.
    *   **Example:** `ansible -m setup webserver1`

*   **`ansible -m setup [hostname] --args "filter=ansible_os_family"`**
    *   **Kaam:** Agar aapko saare facts nahi, bas kuch specific facts dekhne hain, toh `filter` argument use kar sakte hain. Ye output ko chhota aur relevant banata hai.
    *   **Upyog:** Jab aapko kisi specific fact ki value verify karni ho.
    *   **Example:** `ansible -m setup webserver1 --args "filter=ansible_distribution"`

*   **`ansible-playbook -e "key=value" [playbook_name.yml]`**
    *   **Kaam:** Playbook execute karte samay command line se "extra variables" pass karne ke liye. Ye sabse highest precedence rakhte hain aur existing variables ko override kar sakte hain.
    *   **Upyog:** Runtime par configuration ko adjust karne ke liye, bina playbook ya inventory files ko change kiye. Bahut flexible hai.
    *   **Example:** `ansible-playbook configure_app.yml -e "app_version=2.0"`

*   **`ansible-inventory --list` (or `--graph`)**
    *   **Kaam:** Apni inventory mein defined hosts, groups, aur unke variables ko JSON format mein ( `--list`) ya graph format mein ( `--graph`) dekhne ke liye. Ye aapko apni inventory structure aur variable definitions ko visualize karne mein help karta hai.
    *   **Upyog:** Verify karne ke liye ki inventory variables sahi se define hue hain ya nahi.
    *   **Example:** `ansible-inventory -i inventory.ini --list`

---

### 3. Step-by-Step Examples (Real-world Scenarios)

Chalo ab kuch real-world examples dekhte hain jahan hum variables aur facts ka use karenge. Assume karte hain aapke paas ek `inventory.ini` file hai aur kuch managed hosts hain.

**Setup `inventory.ini`:**

```ini
# inventory.ini
[webservers]
web1 ansible_host=192.168.56.101 ansible_user=vagrant
web2 ansible_host=192.168.56.102 ansible_user=vagrant

[databases]
db1 ansible_host=192.168.56.103 ansible_user=vagrant
```

#### Example 1: Basic Variable in a Playbook

Is example mein hum playbook ke andar hi ek variable define karenge.

**File: `simple_vars.yml`**

```yaml
---
- name: Playbook with simple variables
  hosts: webservers
  gather_facts: no # Abhi facts ki zaroorat nahi hai
  vars:
    app_name: MyWebApp
    app_version: 1.0

  tasks:
    - name: Display application name and version
      debug:
        msg: "Deploying {{ app_name }} version {{ app_version }} to {{ inventory_hostname }}"

    - name: Show a different variable
      debug:
        var: app_name # 'var:' keyword variable ki value print karta hai
```

**Run the playbook:**

```bash
ansible-playbook -i inventory.ini simple_vars.yml
```

**Expected Output (similar):**

```
PLAY [Playbook with simple variables] ******************************************

TASK [Gathering Facts] *********************************************************
skipping: [web1]
skipping: [web2]

TASK [Display application name and version] ************************************
ok: [web1] => {
    "msg": "Deploying MyWebApp version 1.0 to web1"
}
ok: [web2] => {
    "msg": "Deploying MyWebApp version 1.0 to web2"
}

TASK [Show a different variable] ***********************************************
ok: [web1] => {
    "app_name": "MyWebApp"
}
ok: [web2] => {
    "app_name": "MyWebApp"
}

PLAY RECAP *********************************************************************
web1                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web2                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

#### Example 2: Inventory Variables (Group and Host Specific)

Ab hum `inventory.ini` aur `group_vars`/`host_vars` ka use karenge.

**Update `inventory.ini`:**

```ini
# inventory.ini
[webservers]
web1 ansible_host=192.168.56.101 ansible_user=vagrant
web2 ansible_host=192.168.56.102 ansible_user=vagrant app_port=8081 # Host-specific variable

[databases]
db1 ansible_host=192.168.56.103 ansible_user=vagrant

[all:vars]
global_message="Hello from global vars!" # Global variable for all hosts
```

**Create `group_vars/webservers.yml`:**

```yaml
# group_vars/webservers.yml
group_app: "NGINX Web Service"
app_port: 8080 # Default port for webservers group
```

**File: `inventory_vars_test.yml`**

```yaml
---
- name: Use variables from inventory and group_vars
  hosts: webservers
  gather_facts: no

  tasks:
    - name: Display group-specific and host-specific variables
      debug:
        msg: |
          Hostname: {{ inventory_hostname }}
          Group App: {{ group_app }}
          App Port (from inventory/group_vars): {{ app_port }}
          Global Message: {{ global_message }}
```

**Run the playbook:**

```bash
ansible-playbook -i inventory.ini inventory_vars_test.yml
```

**Expected Output (notice `app_port` for web1 and web2):**

```
PLAY [Use variables from inventory and group_vars] *****************************

TASK [Display group-specific and host-specific variables] **********************
ok: [web1] => {
    "msg": "Hostname: web1\nGroup App: NGINX Web Service\nApp Port (from inventory/group_vars): 8080\nGlobal Message: Hello from global vars!"
}
ok: [web2] => {
    "msg": "Hostname: web2\nGroup App: NGINX Web Service\nApp Port (from inventory/group_vars): 8081\nGlobal Message: Hello from global vars!"
}

PLAY RECAP *********************************************************************
web1                       : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web2                       : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
**Observation:** `web2` ke liye `app_port` `8081` aaya kyuki `inventory.ini` mein host-specific variable ki precedence `group_vars` se jyada hoti hai.

#### Example 3: Displaying and Filtering Facts

Ab hum `ansible_facts` ka jaadu dekhenge.

**File: `show_facts.yml`**

```yaml
---
- name: Displaying system facts
  hosts: webservers
  gather_facts: yes # By default ye 'yes' hi hota hai, par explicit likhna achha hai

  tasks:
    - name: Print OS family
      debug:
        msg: "The OS family is: {{ ansible_os_family }}"

    - name: Print default IPv4 address
      debug:
        msg: "The default IP address is: {{ ansible_default_ipv4.address }}"

    - name: Print total memory in MB
      debug:
        msg: "Total system memory: {{ ansible_memory_mb.real.total }} MB"

    - name: Print all facts for a host (just for demo, avoid in prod for large output)
      debug:
        var: ansible_facts
      when: inventory_hostname == 'web1' # Sirf ek host ke liye print karenge, warna output bahut bada ho jayega
```

**Run the playbook:**

```bash
ansible-playbook -i inventory.ini show_facts.yml
```

**Expected Output (similar, IPs will vary):**

```
PLAY [Displaying system facts] *************************************************

TASK [Gathering Facts] *********************************************************
ok: [web1]
ok: [web2]

TASK [Print OS family] *********************************************************
ok: [web1] => {
    "msg": "The OS family is: Debian"
}
ok: [web2] => {
    "msg": "The OS family is: Debian"
}

TASK [Print default IPv4 address] **********************************************
ok: [web1] => {
    "msg": "The default IP address is: 192.168.56.101"
}
ok: [web2] => {
    "msg": "The default IP address is: 192.168.56.102"
}

TASK [Print total memory in MB] ************************************************
ok: [web1] => {
    "msg": "Total system memory: 978 MB"
}
ok: [web2] => {
    "msg": "Total system memory: 978 MB"
}

TASK [Print all facts for a host (just for demo, avoid in prod for large output)] ***
ok: [web1] => {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "10.0.2.15",
            "192.168.56.101"
        ],
        "ansible_apparmor": {
            "status": "disabled"
        },
        // ... many more facts ...
        "ansible_os_family": "Debian",
        // ...
    }
}
skipping: [web2]

PLAY RECAP *********************************************************************
web1                       : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web2                       : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

#### Example 4: Using Facts for Conditional Logic

Ye bahut powerful use case hai.

**File: `conditional_os.yml`**

```yaml
---
- name: Install packages based on OS family
  hosts: all
  gather_facts: yes # Facts ki zaroorat hai

  tasks:
    - name: Install NGINX on RedHat-like systems
      ansible.builtin.yum:
        name: nginx
        state: present
      when: ansible_os_family == "RedHat" # Fact ka use!

    - name: Install Apache2 on Debian-like systems
      ansible.builtin.apt:
        name: apache2
        state: present
        update_cache: yes
      when: ansible_os_family == "Debian" # Fact ka use!

    - name: Debug message for other OS types
      debug:
        msg: "Skipping package installation for {{ ansible_os_family }}."
      when: ansible_os_family != "RedHat" and ansible_os_family != "Debian"
```

**Run the playbook:**

```bash
ansible-playbook -i inventory.ini conditional_os.yml
```

**Expected Output (assuming your managed hosts are Debian-based):**

```
PLAY [Install packages based on OS family] *************************************

TASK [Gathering Facts] *********************************************************
ok: [web1]
ok: [web2]
ok: [db1]

TASK [Install NGINX on RedHat-like systems] ************************************
skipping: [web1]
skipping: [web2]
skipping: [db1]

TASK [Install Apache2 on Debian-like systems] **********************************
changed: [web1] => {"changed": true, "msg": "All specified packages are already installed."}
changed: [web2] => {"changed": true, "msg": "All specified packages are already installed."}
changed: [db1] => {"changed": true, "msg": "All specified packages are already installed."}
# (Ya 'ok' agar already installed hain)

TASK [Debug message for other OS types] ****************************************
skipping: [web1]
skipping: [web2]
skipping: [db1]

PLAY RECAP *********************************************************************
db1                        : ok=2    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
web1                       : ok=2    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
web2                       : ok=2    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```
**Observation:** RedHat task skip ho gaya aur Debian task execute hua. Ye hai facts ka real power!

#### Example 5: Using Extra Variables (`-e`)

**File: `extra_vars_test.yml`**

```yaml
---
- name: Test extra variables
  hosts: webservers
  gather_facts: no
  vars:
    greeting: "Hello from Playbook!"
    port: 80

  tasks:
    - name: Display greeting
      debug:
        msg: "{{ greeting }}"

    - name: Display port
      debug:
        msg: "The application is running on port: {{ port }}"
```

**Run with extra variables:**

```bash
ansible-playbook -i inventory.ini extra_vars_test.yml -e "greeting='Hello from CLI!' port=443"
```

**Expected Output:**

```
PLAY [Test extra variables] ****************************************************

TASK [Gathering Facts] *********************************************************
skipping: [web1]
skipping: [web2]

TASK [Display greeting] ********************************************************
ok: [web1] => {
    "msg": "Hello from CLI!"
}
ok: [web2] => {
    "msg": "Hello from CLI!"
}

TASK [Display port] ************************************************************
ok: [web1] => {
    "msg": "The application is running on port: 443"
}
ok: [web2] => {
    "msg": "The application is running on port: 443"
}

PLAY RECAP *********************************************************************
web1                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
web2                       : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
**Observation:** Playbook ke `vars:` block mein define kiye gaye `greeting` aur `port` ki values ko `-e` option se override kar diya gaya. Highest precedence!

---

### 4. Practice Tasks (Lab Tasks)

Ab aapki baari hai! In tasks ko try karo to reinforce your learning.

1.  **Inventory aur `group_vars` ka istemal karein:**
    *   Ek `inventory.ini` file banayein jismein `dev` aur `prod` naam ke do groups ho. Har group mein kam se kam do hosts honge.
    *   `group_vars/dev.yml` file banayein, aur usmein `environment: development` aur `db_name: dev_db` define karein.
    *   `group_vars/prod.yml` file banayein, aur usmein `environment: production` aur `db_name: prod_db` define karein.
    *   Ek playbook `env_info.yml` banayein jo `all` hosts par run kare. Ye playbook har host par uske `environment` aur `db_name` ko `debug` module se print kare.
    *   Verify karein ki `dev` hosts ke liye `development` aur `dev_db` print ho raha hai, aur `prod` hosts ke liye `production` aur `prod_db`.

2.  **Facts aur Conditional Logic:**
    *   Ek playbook `os_specific_message.yml` banayein jo `all` hosts par run kare.
    *   Is playbook mein `gather_facts: yes` rakhein.
    *   Ek task banayein jo RedHat-like systems ke liye `debug` message print kare: "This is a RedHat-based system with IP: {{ ansible_default_ipv4.address }}".
    *   Ek dusra task banayein jo Debian-like systems ke liye `debug` message print kare: "This is a Debian-based system with IP: {{ ansible_default_ipv4.address }}".
    *   Run karein aur dekhein ki har host par sahi message print ho raha hai.

3.  **Extra Variables ka istemal kar ke override karein:**
    *   Ek playbook `app_config.yml` banayein. Ismein `vars:` block ke andar `app_port: 80` aur `env: staging` define karein.
    *   Ek `debug` task banayein jo `app_port` aur `env` ki values print kare.
    *   Playbook ko normal run karein.
    *   Ab playbook ko `extra-vars` ka use karke run karein, jahan `app_port` ko `8080` aur `env` ko `production` par set karein.
    *   Observe karein ki `extra-vars` ne playbook variables ko override kar diya.

4.  **Custom Facts (RHCE specific):**
    *   Apne kisi managed host par login karein (for example, `web1`).
    *   `web1` par `/etc/ansible/facts.d/` directory banayein.
    *   Is directory ke andar `my_app.fact` naam ki ek executable file (shell script or Python script) banayein. Is file mein JSON output generate karein, for example:
        ```bash
        #!/bin/bash
        echo '{ "app_status": "running", "app_version": "1.2.3" }'
        ```
        (File ko `chmod +x my_app.fact` karna na bhoolein)
    *   Apne Ansible control node par wapas aayein.
    *   Ek playbook banayein `custom_facts_test.yml` jo `web1` par run kare.
    *   Is playbook mein `gather_facts: yes` rakhein.
    *   Ek `debug` task banayein jo `ansible_local.my_app.app_status` aur `ansible_local.my_app.app_version` ki values print kare.
    *   Run karein aur dekhein ki custom facts successfully discover ho rahe hain.

---

### 5. Pro Tips / Common Mistakes

**Pro Tips:**

*   **`vars_files` ka use karein:** Agar aapke bahut saare variables hain, toh unhe alag `.yml` files mein define karke `vars_files:` keyword se include kar sakte hain. Ye playbook ko clean rakhta hai.
    ```yaml
    - name: My playbook
      hosts: all
      vars_files:
        - common_vars.yml
        - env_specific_vars.yml
    ```
*   **Fact Caching:** Large environments mein, bar-bar facts gather karna slow ho sakta hai. Aap facts ko cache kar sakte hain (e.g., Redis, JSON file). Ye RHCE ka advanced topic hai. `ansible.cfg` mein `fact_caching = jsonfile` configure hota hai.
*   **`ansible-vault` for Sensitive Data:** Never hardcode passwords or API keys directly in playbooks or inventory. Hamesha `ansible-vault` ka use karein variables ko encrypt karne ke liye.
*   **Registered Variables:** Kisi task ke output ko ek variable mein store karna (register karna) aur use baad mein use karna bahut common practice hai.
    ```yaml
    - name: Get uptime
      ansible.builtin.command: uptime
      register: system_uptime

    - name: Print uptime
      debug:
        msg: "System uptime is: {{ system_uptime.stdout }}"
    ```
*   **Jinja2 Filters:** Variables aur facts ko manipulate karne ke liye Jinja2 filters ka use karein (e.g., `{{ var | upper }}`, `{{ list_var | join(',') }}`). Ye bahut flexible banata hai aapke automation ko.

**Common Mistakes to Avoid:**

*   **Syntax Errors in Jinja2:** `{{ var_name }}` mein curly braces ya spaces ki galti (`{{var_name}}` ya `{{ var_name}}` wrong ho sakte hain depending on context, `{{ var_name }}` is standard). Double-check the syntax.
*   **Variable Precedence ko na samajhna:** Jab ek variable ki value expected na ho, toh sabse pehle variable precedence order check karein. Kahi `extra-vars` ya host-specific variable ne override toh nahi kar diya?
*   **Facts ko disable karna jab unki zaroorat ho:** Agar aap `gather_facts: no` set kar dete hain, aur aapke tasks mein `ansible_os_family` jaise facts ka use ho raha hai, toh playbook fail ho jayega.
*   **Nested Facts ko galat tarike se access karna:** Facts usually nested dictionaries hote hain. For example, `ansible_default_ipv4.address` sahi hai, `ansible_default_ipv4['address']` bhi sahi hai, lekin `ansible_default_ipv4.ip` galat ho sakta hai agar 'ip' key exist nahi karti. `ansible -m setup` se exact key names check karein.
*   **Variables ko quotes mein rakhna:** Jab aap `vars:` section mein variables define karte hain, toh string values ko quotes (`""` ya `''`) mein rakhna achha hota hai, khaas kar tab jab value mein special characters hon ya number jaisi dikhe, but actually string ho (e.g. `version: "2.0"` vs `version: 2.0`).

---

Toh dosto, yeh tha hamara comprehensive lesson "Variables & Facts" par. I hope aapko Hinglish mein explanation samajh mein aaya hoga aur examples ne concepts ko clear kiya hoga. Inhe practice karein, aur aap Ansible mein ek step aage badh jayenge! All the best!
