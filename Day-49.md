Day 49: Revision (Error handling, blocks, rescue Ansible for networking & services (NTP, SSH, firewall) Podman basics (containers, images, volumes) Ansible & containers (playbooks for containerized services) Security automation with Ansible (SELinux, sudoers) Final playbook project – full environment automation) & mock RHCE test
---
नमस्ते दोस्तों! RHCSA aur RHCE certification ki iss final revision class mein aapka bohot bohot swagat hai. Aaj hum kuch sabse important topics ko cover karenge, jo aapke certification exams ke liye critical hain, khaaskar RHCE ke perspective se. Hum dekhenge ki kaise difficult situations ko handle karte hain (error handling, rescue), Ansible se networking aur services ko automate kaise karte hain, Podman containers ke basics, Ansible ke saath containers ko kaise manage karte hain, aur security automation (SELinux, sudoers) kaise karte hain. Finally, ek bada project aur ek mock RHCE test bhi discuss karenge.

Toh chaliye, shuru karte hain iss comprehensive journey ko!

---

## **RHCSA + RHCE Revision & Mock Test: Comprehensive Lesson**

### 1. Concept Explanation: Revision (Error handling, blocks, rescue), Ansible for Networking & Services, Podman, Ansible & Containers, Security Automation

**Hinglish Explanation:**

RHCSA aur RHCE exams mein sirf configuration karna hi kaafi nahi hota, mushkil situations ko handle karna, system ko recover karna, aur complex tasks ko automate karna bhi utna hi zaroori hai. Yeh topics aapko ek strong foundation provide karenge.

*   **Revision (Error handling, blocks, rescue):**
    *   **System Level (Error handling & Rescue):** Imagine karo aapka server boot nahi ho raha, ya aap apna root password bhool gaye ho. Aisi situations mein `rescue.target`, `rd.break` jaise options use karke system ko bootable state mein wapas lana ya password reset karna hi *system rescue* hai. `chroot` ka use karke non-booting system ke filesystem ko access karna aur repair karna bhi iska part hai.
    *   **Ansible Level (Error handling, blocks):** Playbooks run karte waqt errors aana common hai. `ignore_errors` se aap Ansible ko bol sakte ho ki "agar yeh task fail bhi ho jaye, toh bhi aage badho". `failed_when` aur `changed_when` se aap custom conditions define kar sakte ho ki kab ek task 'fail' ya 'changed' maana jayega.
        `block`, `rescue`, aur `always` constructs Ansible mein advanced error handling ke liye use hote hain. `block` ke andar aap normal tasks rakhte ho. Agar `block` ke andar koi task fail ho jata hai, toh `rescue` block ke tasks run hote hain (jaise ki cleanup ya notification). Aur `always` block ke tasks hamesha run hote hain, chahe `block` success ho ya fail. Yeh try-catch-finally ki tarah hai programming mein.

*   **Ansible for Networking & Services (NTP, SSH, Firewall):**
    *   **NTP (Network Time Protocol):** Servers pe time sync karna bohot zaroori hota hai consistency aur logging ke liye. Ansible se hum `chronyd` service ko configure aur manage karte hain, taaki saare servers ka time ek jaisa rahe.
    *   **SSH (Secure Shell):** Remote access ka king! Ansible se aap `sshd_config` file ko manage kar sakte ho (port change karna, password auth disable karna, key auth enable karna). Users ke SSH keys ko distribute karna bhi Ansible se automatic ho jata hai.
    *   **Firewall (`firewalld`):** Security ka pehla defence! Ansible `firewalld` module use karke aap ports open/close kar sakte ho, services allow/deny kar sakte ho, aur zones manage kar sakte ho, sab automatically.

*   **Podman Basics (Containers, Images, Volumes):**
    *   **Containers:** Light-weight, isolated environments jahan applications run karte hain. Imagine ek chota sa virtual machine, but bohot fast aur resource efficient. `podman` Red Hat ka container engine hai, jo Docker ke jaisa hi hai but daemon-less.
    *   **Images:** Read-only templates jinse containers banate hain. Ek image mein application, libraries aur uski dependencies sab pre-packaged hoti hain.
    *   **Volumes:** Containers ephemeral hote hain (data delete ho sakta hai container hatane par). Permanent data store karne ke liye `volumes` use karte hain. Yeh host machine ke filesystem par data store karte hain jo container restart hone par bhi available rehta hai.

*   **Ansible & Containers (Playbooks for containerized services):**
    *   Ansible ki power se hum Podman containers ko automate kar sakte hain. Yani containers ko create karna, run karna, stop karna, remove karna, unke images ko pull karna, aur custom images build karna bhi Ansible playbooks se kiya ja sakta hai. Yeh DevOps pipeline mein bohot useful hai.

*   **Security Automation with Ansible (SELinux, sudoers):**
    *   **SELinux (Security-Enhanced Linux):** Linux mein ek extra layer of security jo processes aur files ke access ko control karta hai. Isko `enforcing`, `permissive`, ya `disabled` modes mein set kar sakte hain. Ansible se hum SELinux policies ko manage kar sakte hain, booleans set kar sakte hain (jaise ki web server ko home directories access karne ki permission dena), aur port contexts set kar sakte hain.
    *   **Sudoers:** Users ko root privileges dena without actually giving them the root password. `/etc/sudoers` file mein entries add karke hum control karte hain ki kaun sa user kaun si commands `sudo` ke through run kar sakta hai. Ansible se iss file ko automate karna `lineinfile` module ka use karke common hai.

### 2. Key Linux Commands & Ansible Modules

**System Level (Rescue & Troubleshooting):**

*   `rd.break`: Boot process ko interrupt karta hai initial ramdisk stage par, emergency shell provide karta hai.
*   `mount -o remount,rw /sysroot`: Read-only filesystem ko read-write mode mein remount karta hai `chroot` environment ke liye.
*   `chroot /sysroot`: Root directory ko `/sysroot` mein change karta hai, jisse aap original OS ko modify kar sako.
*   `passwd root`: Root password reset karne ke liye `chroot` ke andar.
*   `touch /.autorelabel`: SELinux filesystem ko re-label karne ke liye, jab `chroot` ke baad boot karte ho.
*   `journalctl -xe`: Detailed boot logs aur service errors dekhne ke liye.
*   `systemctl get-default`: Default boot target (e.g., `graphical.target`, `multi-user.target`) dekhne ke liye.
*   `systemctl set-default multi-user.target`: Default boot target ko change karne ke liye.

**Podman Commands:**

*   `podman info`: Podman client and host information dekhta hai.
*   `podman pull `: Docker Hub ya kisi aur registry se image download karta hai.
*   `podman images`: Locally available images list karta hai.
*   `podman run -d -p 8080:80 --name mywebserver nginx`: Nginx container ko detached mode mein run karta hai, port mapping ke saath.
*   `podman ps -a`: All containers (running and stopped) list karta hai.
*   `podman stop `: Running container ko stop karta hai.
*   `podman rm `: Stopped container ko remove karta hai.
*   `podman rmi `: Image ko remove karta hai.
*   `podman volume create mydata`: Ek new volume create karta hai.
*   `podman volume ls`: All Podman volumes list karta hai.
*   `podman build -t myapp:1.0 .`: `Dockerfile` se custom image build karta hai.

**Ansible Modules:**

*   `ansible.builtin.lineinfile`: File mein lines add, modify ya delete karta hai. SSH config, sudoers entry ke liye best.
*   `ansible.builtin.template`: Jinja2 templates use karke configuration files generate karta hai. NTP config, custom service files ke liye.
*   `ansible.builtin.service`: Services ko start, stop, restart, enable/disable karta hai. `chronyd`, `sshd`, `firewalld` services ke liye.
*   `ansible.posix.firewalld`: Firewall rules manage karta hai (`firewall-cmd` ka Ansible equivalent).
*   `community.general.podman_container`: Podman containers create, manage aur delete karta hai.
*   `community.general.podman_image`: Podman images pull, build aur delete karta hai.
*   `ansible.posix.selinux`: SELinux mode set karta hai (enforcing/permissive/disabled).
*   `ansible.posix.seboolean`: SELinux booleans ko enable/disable karta hai.
*   `ansible.posix.seport`: Custom ports ke liye SELinux context set karta hai.

### 3. Step-by-Step Examples

#### Example 1: Ansible Advanced Error Handling (Block/Rescue/Always)

**Scenario:** Aap ek service deploy kar rahe ho. Agar installation fail ho jaye, toh ek cleanup message print hona chahiye, aur finally ek completion message print hona chahiye.

```yaml
# playbook_error_handling.yaml
---
- name: Deploy a dummy service with error handling
  hosts: all
  become: yes
  tasks:
    - name: Ensure some prerequisite is met (this might fail)
      ansible.builtin.command: "false" # Intentionally fail this task for demonstration
      # ansible.builtin.command: "true" # Uncomment this to see success path
      register: result_prereq
      ignore_errors: yes # We'll handle this in the rescue block

    - name: Use block/rescue/always for robust deployment
      block:
        - name: Actual service deployment task (this will not run if prerequisite fails)
          ansible.builtin.debug:
            msg: "Prerequisite passed. Proceeding with service deployment..."
          when: result_prereq.rc == 0 # Only run if prerequisite was successful

        - name: Install Nginx (example task inside block)
          ansible.builtin.yum:
            name: nginx
            state: present
          when: result_prereq.rc == 0

        - name: Start Nginx service
          ansible.builtin.service:
            name: nginx
            state: started
            enabled: yes
          when: result_prereq.rc == 0

      rescue:
        - name: Handle deployment failure
          ansible.builtin.debug:
            msg: "Deployment failed! Performing cleanup..."
        - name: Log the error
          ansible.builtin.copy:
            content: "Deployment failed on {{ inventory_hostname }} at {{ ansible_date_time.iso8601_micro }}"
            dest: /var/log/ansible_deployment_errors.log
            mode: '0644'
        - name: Notify admin (dummy task)
          ansible.builtin.debug:
            msg: "Admin notified about failure on {{ inventory_hostname }}"

      always:
        - name: Always run this task (e.g., final logging or status check)
          ansible.builtin.debug:
            msg: "Deployment attempt finished on {{ inventory_hostname }}. Check logs for details."
```

**Run command:** `ansible-playbook playbook_error_handling.yaml`

#### Example 2: Ansible for NTP, SSH & Firewall

**Scenario:** Aap chahte ho ki aapke server `server1.example.com` ka time NTP server `pool.ntp.org` se sync ho, SSH ka default port `22` se `2222` ho jaye, aur `2222` port `firewalld` mein open ho.

```yaml
# playbook_net_services.yaml
---
- name: Configure NTP, SSH, and Firewall
  hosts: server1.example.com
  become: yes
  tasks:
    - name: Ensure chrony is installed
      ansible.builtin.yum:
        name: chrony
        state: present

    - name: Configure chrony for NTP sync
      ansible.builtin.template:
        src: chrony.conf.j2
        dest: /etc/chrony.conf
        owner: root
        group: root
        mode: '0644'
      notify: Restart chronyd

    - name: Ensure chrony service is running and enabled
      ansible.builtin.service:
        name: chronyd
        state: started
        enabled: yes

    - name: Change SSH port to 2222
      ansible.builtin.lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^#?Port\s+22'
        line: 'Port 2222'
        validate: '/usr/sbin/sshd -t %s'
      notify: Restart sshd

    - name: Allow new SSH port (2222) in firewalld
      ansible.posix.firewalld:
        port: 2222/tcp
        permanent: yes
        state: enabled
        zone: public
      notify: Reload firewalld

    - name: Remove default SSH port (22) from firewalld
      ansible.posix.firewalld:
        service: ssh
        permanent: yes
        state: disabled
        zone: public
      notify: Reload firewalld

  handlers:
    - name: Restart chronyd
      ansible.builtin.service:
        name: chronyd
        state: restarted

    - name: Restart sshd
      ansible.builtin.service:
        name: sshd
        state: restarted

    - name: Reload firewalld
      ansible.builtin.command: firewall-cmd --reload
```

**Template file: `templates/chrony.conf.j2`**
(Place this in a `templates` directory next to your playbook)

```jinja
# Use public NTP servers from the pool.ntp.org project.
pool {{ ntp_server | default('pool.ntp.org') }} iburst

# Record the rate at which the system clock gains or loses time.
driftfile /var/lib/chrony/drift

# Enable kernel synchronization of the real-time clock (RTC).
rtcsync

# In some situations, you might want to disable ntpq access from remote hosts.
# allow 192.168.0.0/16
# cmdallow 127.0.0.1
# cmdallow 192.168.0.0/16

# This directive enables a systemd service that updates the RTC.
# You will most likely want to enable this on bare metal installations.
# rtcdevice /dev/rtc0

# This directive configures authentication and encryption.
# ntpauthkey /etc/chrony.keys

# This directive configures the system to write out the current time
# to a file at shutdown, and read it back at startup.
# makestep 0.1 3
```

**Run command:** `ansible-playbook playbook_net_services.yaml`

#### Example 3: Ansible & Podman for a Containerized Service

**Scenario:** Aap ek Nginx web server container `my_nginx_app` deploy karna chahte ho jo host ke port `8080` par listen karega aur ek custom HTML page serve karega. Custom HTML page ek host volume se mount hoga.

```yaml
# playbook_podman_nginx.yaml
---
- name: Deploy Nginx container with custom content
  hosts: all
  become: yes
  tasks:
    - name: Ensure podman is installed
      ansible.builtin.yum:
        name: podman
        state: present

    - name: Create directory for Nginx content
      ansible.builtin.file:
        path: /opt/my_nginx_content
        state: directory
        mode: '0755'

    - name: Create custom index.html
      ansible.builtin.copy:
        content: |
          
          
          
              
          
          

              
Hello from Podman & Ansible!

              
This page is served by Nginx in a container.


              
Hostname: {{ ansible_hostname }}


          

          
        dest: /opt/my_nginx_content/index.html
        mode: '0644'

    - name: Run Nginx container
      community.general.podman_container:
        name: my_nginx_app
        image: nginx:latest
        state: started
        ports:
          - "8080:80"
        volume:
          - "/opt/my_nginx_content:/usr/share/nginx/html:ro" # Read-only mount
        restart_policy: always
        cleanup: yes # Remove container if it stops
```

**Run command:** `ansible-playbook playbook_podman_nginx.yaml`
**Verify:** `curl http://:8080` (After the playbook runs successfully)

#### Example 4: Security Automation with Ansible (SELinux & Sudoers)

**Scenario:** Aapke server par SELinux `enforcing` mode mein hona chahiye, aur `apache` user ko `systemctl restart httpd` command run karne ki permission honi chahiye without password.

```yaml
# playbook_security_automation.yaml
---
- name: Automate SELinux and Sudoers
  hosts: all
  become: yes
  tasks:
    - name: Ensure SELinux is in enforcing mode
      ansible.posix.selinux:
        state: enforcing
        # Make sure to reboot if changing from disabled to enforcing or vice-versa
        # If changing from permissive to enforcing, no reboot is needed.
        # This task will usually not cause a reboot unless the system was disabled previously.

    - name: Create apache user (if not exists)
      ansible.builtin.user:
        name: apache
        state: present
        comment: "Apache service user"

    - name: Add sudoers entry for apache user to restart httpd
      ansible.builtin.lineinfile:
        path: /etc/sudoers.d/apache_httpd_restart
        create: yes
        mode: '0440'
        line: 'apache ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart httpd'
        validate: '/usr/sbin/visudo -cf %s' # Validate sudoers file syntax
```

**Run command:** `ansible-playbook playbook_security_automation.yaml`
**Verify (on remote host):**
1.  `sestatus` (should show `Current mode: enforcing`)
2.  `sudo -l -U apache` (as root or another sudo user) - should show the `NOPASSWD` entry.
3.  `su - apache -s /bin/bash` (switch to apache user)
4.  `sudo /usr/bin/systemctl restart httpd` (this should run without asking for a password)

### 4. Practice Tasks

Yeh tasks aapko RHCE exam ki tarah real-world scenarios mein aapki skills test karne mein help karenge.

**Lab Setup:**
*   **Control Node:** Aapki Ansible workstation.
*   **Managed Nodes:** Minimum 2-3 CentOS/RHEL 8 servers. Make sure `podman` aur `firewalld` services available hon.
*   Ansible inventory file `hosts` mein apne managed nodes add karein.

#### Practice Task 1: System Rescue Simulation & Recovery

**Scenario:** Imagine karo aapne kuch galat kar diya hai `/etc/fstab` file mein, ya aapka root password corrupt ho gaya hai, aur system boot nahi ho raha hai. Aapko system ko recover karna hai.

**Steps:**
1.  **Simulate Error (on one managed node):**
    *   SSH into one of your managed nodes.
    *   `sudo vi /etc/fstab`
    *   Add a garbage line (e.g., `GARBAGE none none defaults 0 0`) at the end of the file.
    *   `sudo reboot`
2.  **Perform Rescue:**
    *   VM console (VirtualBox, VMware, cloud console) se server ko access karein.
    *   Boot loader mein `e` press karke kernel parameters edit karein.
    *   `linux` line ke end mein `rd.break` add karein.
    *   Ctrl+X se boot karein.
    *   Emergency shell mein `mount -o remount,rw /sysroot` karein.
    *   `chroot /sysroot` karein.
    *   `/etc/fstab` ko theek karein (garbage line hata dein).
    *   `passwd root` se root password reset karein (for practice).
    *   `touch /.autorelabel` (SELinux context fix ke liye, agar password reset kiya ho).
    *   `exit` from chroot.
    *   `exit` to continue boot.
3.  **Verify:** System successfully boot hona chahiye aur aap SSH se login kar paayein.

#### Practice Task 2: Advanced Web Stack Automation with Ansible & Podman

**Scenario:** Aapko ek multi-component web application deploy karna hai using Ansible aur Podman. Isme Nginx reverse proxy container, aur ek simple Flask app container hoga.

**Requirements:**
1.  Har managed node pe Podman install ho.
2.  Ek Nginx container `nginx-proxy` run ho jo host ke port `80` par listen kare.
3.  Nginx container ka configuration dynamic ho, jinja2 template se generate ho. Yeh template backend Flask app container ko proxy kareगा.
4.  Ek Flask app container `flask-app` run ho jo host ke *internal* port `5000` par listen kare (yaani only Nginx proxy usse access kar paaye). Flask app `Hello from Flask on {{ inventory_hostname }}!` text display kare.
5.  Flask app ke liye ek custom `Dockerfile` hona chahiye. Ansible us `Dockerfile` se image build karega.
6.  Firewall `http` service (`80/tcp`) ko allow kare.
7.  SELinux ko `enforcing` mode mein set kare.

**Hints:**
*   Nginx config template mein `upstream` block use karein.
*   Flask app ke liye `Dockerfile` mein `FROM python:3.9-slim`, `COPY`, `RUN pip install flask`, `CMD` use karein.
*   `community.general.podman_image` module `build` option ke saath use karein.
*   `ansible.posix.firewalld` module use karein.
*   `ansible.posix.selinux` module use karein.

#### Practice Task 3: Secure Remote Access & Monitoring with Ansible

**Scenario:** Aapko apne saare managed nodes par security ko enhance karna hai aur ek basic monitoring setup karna hai.

**Requirements:**
1.  Saare managed nodes par SSH access sirf key-based authentication se allow ho, password authentication disable ho.
2.  Aapki public SSH key (`~/.ssh/id_rsa.pub` on control node) saare managed nodes par `root` user ke authorized_keys mein add ho.
3.  Ek naya user `monitor_user` create karein jisse `systemctl status` command `NOPASSWD` ke saath run karne ki permission ho.
4.  SELinux `ssh_sysadm_enable` boolean ko enable karein (just for practice, though not strictly needed for key auth).
5.  Sabhi managed nodes par `tuned` service running aur enabled ho, aur profile `throughput-performance` set ho. (यह एक तरह का system monitoring/optimization tool है).

**Hints:**
*   `ansible.builtin.lineinfile` for `sshd_config` (Port, PasswordAuthentication, ChallengeResponseAuthentication, UsePAM).
*   `ansible.builtin.authorized_key` for adding public keys.
*   `ansible.builtin.user` for `monitor_user`.
*   `ansible.builtin.lineinfile` for `/etc/sudoers.d/monitor_user`.
*   `ansible.posix.seboolean` for `ssh_sysadm_enable`.
*   `ansible.builtin.yum` and `ansible.builtin.service` for `tuned`.
*   `ansible.builtin.command: tuned-adm profile throughput-performance` (or `ansible.builtin.shell`).

### 5. Pro Tips / Common Mistakes

**Pro Tips:**

*   **Idempotency is Key:** Hamesha apne playbooks ko idempotent banayein. Yani, kitni baar bhi run karo, system state wahi rahega aur unnecessarily changes nahi honge. Ansible modules inherently idempotent hote hain, but `command` ya `shell` use karte waqt dhyaan rakhein.
*   **Use Handlers Wisely:** Configuration file changes ke baad services ko restart karne ke liye `notify` aur `handlers` ka use karein. Yeh multiple changes ke baad sirf ek baar restart ensure karta hai.
*   **Test on Isolated Environments:** Real production servers par deploy karne se pehle, hamesha apne playbooks ko testing (dev/staging) environments par test karein.
*   **Version Control Everything:** Git use karein apne playbooks aur inventory files ko version control karne ke liye.
*   **Dry Runs (`--check --diff`):** Playbook ko `--check` flag ke saath run karein ye dekhne ke liye ki kya changes honge, bina actual changes kiye. `--diff` se aapko changes ka diff bhi dikh jayega.
*   **Vault for Secrets:** Passwords, API keys, aur sensitive information ko Ansible Vault use karke encrypt karein. Kabhi bhi plain text mein credentials commit na karein.
*   **Inventory Best Practices:** Group variables (`group_vars/`), host variables (`host_vars/`) use karein apne inventory ko organize karne ke liye.
*   **SELinux Contexts:** Jab bhi aap custom ports ya directories create karein jahan services data store karti hain, SELinux context ko `semanage` ya Ansible `seport`/`file` module se adjust karna na bhoolein. `restorecon -Rv /path/to/dir` is your friend.
*   **Podman - Rootless is Good:** Production mein, agar possible ho, toh Podman rootless containers use karein enhanced security ke liye. RHCE mein mostly rootful scenarios aate hain.
*   **Error Handling - Debugging:** Jab Ansible playbook fail ho, output ko carefully padhein. `--verbose` (`-vvv`) flag se detailed output milta hai jo debugging mein help karta hai.

**Common Mistakes to Avoid:**

*   **Not Testing Handlers:** Kabhi-kabhi handlers mein syntax errors ho sakte hain, ya woh service restart nahi kar paate. Make sure handlers bhi test ho.
*   **Hardcoding Values:** Playbooks mein hardcoded values se bachein. Variables (`vars/`, `group_vars/`, `host_vars/`) ka use karein.
*   **Ignoring SELinux:** SELinux ko `disabled` kar dena ek quick fix ho sakta hai, but production mein yeh security risk hai. Troubleshooting karte waqt `setenforce 0` (permissive mode) mein temporarily rakh sakte hain, but problem identify karke SELinux policies ko adjust karna hi sahi approach hai.
*   **Forgetting `become: yes`:** Root privileges ki zaroorat wali tasks ke liye `become: yes` (ya `become: true`) lagana bhoolna common mistake hai.
*   **Incorrect `firewalld` Zones/States:** `firewalld` rules apply karte waqt `zone` aur `state` (enabled/disabled, permanent/runtime) ka dhyaan rakhein. `permanent: yes` lagana na bhoolein agar reboot ke baad bhi rule chahiye. `firewall-cmd --reload` ya `firewalld` service restart karna bhi zaroori hai.
*   **Podman Container Name Collisions:** Jab `podman_container` module use karein, `name` parameter unique rakhein. Agar container already exist karta hai same name se aur aap `state: started` ya `restarted` use kar rahe ho, toh woh handle ho jayega. Lekin `state: present` ke saath conflicts ho sakte hain. `cleanup: yes` use karna bhi useful hai.
*   **Sudoers Syntax Errors:** `/etc/sudoers` file mein syntax error system ko break kar sakta hai (`visudo` command use karna is a must). Ansible `lineinfile` module mein `validate` option use karein.

---

### 7. Mock RHCE Test

Yeh mock test aapki preparation ko check karne ke liye designed hai. Each scenario will require you to combine multiple skills.

**Environment:**
*   **Control Node:** A RHEL/CentOS 8 system with Ansible installed.
*   **Managed Nodes:** Two RHEL/CentOS 8 systems (e.g., `node1.example.com`, `node2.example.com`). Ensure SSH access (password-less preferred) is set up from control node.

---

#### **Mock Test Scenario 1: Web Application Deployment & Security**

**Goal:** `node1.example.com` par ek containerized web application deploy karein aur usse secure karein.

**Tasks:**

1.  **Podman Installation & Configuration:**
    *   Ensure Podman is installed on `node1.example.com`.
    *   Ensure `podman.socket` is enabled and running for rootless containers (if you choose to attempt rootless, otherwise rootful is fine for exam).
2.  **Containerized Nginx Web Server:**
    *   Deploy an Nginx container named `web_app_nginx` on `node1.example.com`.
    *   This container should serve a simple `index.html` file with content: "Welcome to RHCE Web Server on `node1.example.com`".
    *   The `index.html` file should be mounted from a host path (e.g., `/var/www/html_content`) into the container.
    *   The Nginx container should listen on host port `8080`.
    *   Ensure the container automatically restarts if the host reboots.
3.  **Firewall Configuration:**
    *   Open port `8080/tcp` permanently on `node1.example.com` using `firewalld`.
    *   Ensure the default `http` (port `80`) and `https` (port `443`) services are *not* open, unless explicitly required.
4.  **SELinux Configuration:**
    *   Ensure SELinux is in `enforcing` mode.
    *   Adjust SELinux contexts so that Nginx (running as a container) can read content from `/var/www/html_content`. (Hint: `semanage fcontext` and `restorecon` or `ansible.posix.sefcontext` module).
5.  **User Management & Sudoers:**
    *   Create a new user `devops_admin` with a password of your choice.
    *   Grant `devops_admin` the ability to restart the `web_app_nginx` container (e.g., `podman restart web_app_nginx`) using `sudo` without needing a password.

**Verification Steps (Manual on `node1.example.com`):**
*   `sudo podman ps` (check container status)
*   `curl http://localhost:8080` (check web content)
*   `sudo firewall-cmd --list-all` (check port 8080)
*   `sestatus` and `ls -Z /var/www/html_content` (check SELinux context)
*   `sudo -l -U devops_admin` (check sudoers entry)
*   `su - devops_admin` then `sudo podman restart web_app_nginx` (test sudo)

---

#### **Mock Test Scenario 2: Centralized Service Management & Troubleshooting**

**Goal:** `node1.example.com` and `node2.example.com` par centralized time sync aur SSH security implement karein, saath hi ek challenging situation handle karein.

**Tasks:**

1.  **NTP Configuration:**
    *   On both `node1.example.com` and `node2.example.com`, configure `chronyd` to synchronize time with `0.rhel.pool.ntp.org` and `1.rhel.pool.ntp.org`.
    *   Ensure `chronyd` service is running and enabled.
2.  **SSH Hardening:**
    *   On both nodes, change the default SSH port from `22` to `22022`.
    *   Disable password authentication for SSH. Only public key authentication should be allowed.
    *   Your Ansible control node's public SSH key should be added to the `root` user's `authorized_keys` file on both managed nodes.
    *   Ensure `firewalld` is updated to allow SSH connections on the new port (`22022/tcp`) and remove the old port (`22/tcp`).
    *   Ensure `sshd` service restarts after changes.
3.  **Troubleshooting & Recovery:**
    *   **Simulate Problem:** On `node2.example.com`, intentionally set SELinux to `disabled` mode via `/etc/selinux/config` file, and then reboot. (This will simulate a scenario where SELinux is not in enforcing mode after a recovery).
    *   **Recovery Task:** Using Ansible, automatically detect if SELinux is `disabled` on `node2.example.com` and set it back to `enforcing` mode. This might require a reboot, which should also be automated by the playbook. (Hint: Use `ansible.posix.selinux` module and potentially `reboot` module). The playbook should be able to handle this.
4.  **Custom MOTD (Message of the Day):**
    *   On both nodes, create a custom `/etc/motd` file that displays the current hostname and "Managed by Ansible for RHCE".

**Verification Steps:**
*   After playbook run, try to SSH from control node to `node1.example.com` and `node2.example.com` on port `22022`.
*   On `node1` and `node2`, run `sudo chronyc sources` (check NTP sync).
*   On `node1` and `node2`, run `sudo firewall-cmd --list-all` (check firewall rules).
*   On `node2`, run `sestatus` (should be enforcing).
*   Login to each node, `/etc/motd` should show custom message.

---

**Evaluation Criteria for Mock Test:**

*   **Correctness:** Are all requirements met precisely?
*   **Idempotency:** Can the playbook be run multiple times without issues or unnecessary changes?
*   **Efficiency:** Is the playbook well-structured and uses appropriate modules (avoiding `shell` where a module exists)?
*   **Error Handling:** Does the playbook gracefully handle potential issues or is it brittle?
*   **Security:** Are security best practices followed (e.g., proper permissions, SELinux contexts)?

---

Good luck with your RHCSA and RHCE exams! Yeh topics aapko real-world production environments mein bhi bohot help karenge. Keep practicing!
