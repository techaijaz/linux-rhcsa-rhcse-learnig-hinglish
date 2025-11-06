Day 46: Ansible & containers (playbooks for containerized services)
---
Namaste, future Linux Gurus! Aaj hum ek bahut hi important aur powerful topic par baat karenge: **"Ansible & Containers (playbooks for containerized services)"**. RHCSA aur RHCE exams ke liye aur modern IT infrastructure manage karne ke liye yeh topic bohot zaroori hai. Hum sikhenge ki kaise Ansible ki madad se hum containers ko automate kar sakte hain.

---

## RHCSA + RHCE Training: Ansible aur Containerized Services

### 1. Concept Explanation (Ansible aur Containers ko Samajhna)

Chaliye, sabse pehle concepts ko Hinglish mein samajhte hain:

*   **Ansible Kya Hai?**
    Ansible ek open-source IT automation tool hai. Simple terms mein, yeh aapko complex tasks ko automate karne mein help karta hai, jaise server provisioning, configuration management, application deployment, aur continuous delivery.
    *   **Agentless:** Iska matlab hai ki aapko target machines par koi special software (agent) install karne ki zaroorat nahi padti. Yeh SSH use karta hai communication ke liye.
    *   **Declarative:** Aap Ansible ko batate hain ki aapko *kya state* chahiye (e.g., "Nginx installed hona chahiye aur running hona chahiye"), na ki *kaise us state tak pahunchna hai*. Ansible khud decide karta hai ki kya steps lene hain.
    *   **YAML:** Iske playbooks (jo ki automation instructions hain) YAML (YAML Ain't Markup Language) mein likhe jaate hain, jo ki human-readable aur easy-to-understand hota hai.

*   **Containers Kya Hain?**
    Containers lightweight, standalone, executable packages hote hain jinmein ek application aur us application ko run karne ke liye sab kuch shamil hota hai — code, runtime, system tools, system libraries, settings.
    *   **Isolation:** Har container ek doosre se aur host system se isolated hota hai. Iska matlab hai ki ek container ke andar ki dependencies ya issues doosre containers ya host ko affect nahi karte.
    *   **Portability:** Container images kisi bhi environment mein run ho sakte hain jahan container runtime (jaise Podman ya Docker) installed ho. "Build once, run anywhere."
    *   **Efficiency:** VMs ke comparison mein containers bohot lightweight hote hain kyunki woh host OS ke kernel ko share karte hain.
    *   **Popular Runtimes:** Red Hat ki preferred container runtime **Podman** hai, jo Docker-compatible hai lekin daemonless hai (yani bina kisi background service ke chalta hai, jo security aur simplicity ke liye accha hai). Docker bhi widely used hai.

*   **Ansible aur Containers ko Kyun Combine Karein?**
    Jab hum Ansible aur containers ko saath use karte hain, toh hum containers ki management ko automate kar sakte hain. Socho:
    *   **Container Deployment Automation:** Aapko bar-bar `podman run` commands manually type nahi karne padenge. Ek Ansible playbook likho, aur woh aapke containers ko deploy, start, stop ya remove kar dega across multiple servers.
    *   **Reproducibility:** Aapke container deployments hamesha consistent rahenge. Ek hi playbook ko kitni bhi baar run karo, result same hoga (idempotency).
    *   **Infrastructure as Code (IaC) for Containers:** Aapki container infrastructure ko code ke roop mein define kar sakte hain (YAML files mein), jo version control (Git) ke saath track kiya ja sakta hai.
    *   **Efficiency aur Scalability:** Ek ya 100 servers par containers deploy karna, Ansible ke saath utna hi easy hai.

*   **Ansible Playbooks for Containers:**
    Ansible mein hum special modules use karte hain jo container runtimes (jaise Podman ya Docker) ke saath interact karte hain. RHCSA/RHCE ke context mein, `community.general.podman_image` aur `community.general.podman_container` modules bohot important hain.
    *   `podman_image`: Is module se hum container images ko pull, build, aur remove kar sakte hain.
    *   `podman_container`: Is module se hum containers ko create, start, stop, restart, aur remove kar sakte hain.

### 2. Key Linux Commands (Mahatvapurna Linux Commands)

Yahan kuch important commands hain jo is topic se related hain, Hinglish explanations ke saath:

#### Ansible Related Commands:

*   `ansible --version`
    *   **Karam:** Ansible ka version check karta hai.
    *   **Hinglish:** Is command se aapko pata chalega ki aapke system par Ansible ka kaunsa version install hai.
*   `ansible-playbook `
    *   **Karam:** Ek Ansible playbook ko execute karta hai.
    *   **Hinglish:** Aapne jo automation steps `playbook.yml` file mein likhe hain, unko run karne ke liye yeh command use hota hai.
*   `ansible-playbook  --check`
    *   **Karam:** Playbook ka dry-run karta hai, changes apply nahi karta.
    *   **Hinglish:** Sirf yeh dekhne ke liye ki playbook kya-kya changes karega, actual mein unhe apply kiye bina. Bohot useful hai debugging aur testing ke liye.
*   `ansible-playbook  --diff`
    *   **Karam:** Changes ka detailed diff dikhata hai.
    *   **Hinglish:** `--check` ke saath ya uske bina, yeh command aapko batayega ki kaunsi files ya configurations mein kya changes honge.
*   `ansible-galaxy collection install community.general`
    *   **Karam:** `community.general` collection install karta hai.
    *   **Hinglish:** `podman_image` aur `podman_container` jaise modules core Ansible ka part nahi hain. Yeh `community.general` collection mein milte hain. Is command se aap un modules ko apne system par availabe banate hain.

#### Container (Podman) Related Commands:

*   `podman info`
    *   **Karam:** Podman environment ki information display karta hai.
    *   **Hinglish:** Isse aapko Podman version, storage driver, registries, aur anya configuration details milte hain.
*   `podman search `
    *   **Karam:** Public registries (jaise Docker Hub, quay.io) par images search karta hai.
    *   **Hinglish:** Agar aapko koi specific image chahiye (jaise `nginx` ya `ubuntu`), toh usko search karne ke liye yeh use karte hain.
*   `podman pull `
    *   **Karam:** Ek container image ko registry se download karta hai.
    *   **Hinglish:** Is command se aapko remote repository se image apne local system par mil jaati hai. Jaise `podman pull registry.access.redhat.com/ubi8/nginx-120`.
*   `podman images`
    *   **Karam:** Local system par available saari container images ko list karta hai.
    *   **Hinglish:** Yeh dekhne ke liye ki aapke paas kaun-kaunsi images download ya build ki hui hain.
*   `podman run [options]  [command]`
    *   **Karam:** Ek naya container create aur start karta hai ek image se.
    *   **Hinglish:** Yeh container commands ka "swiss army knife" hai. Options mein `-p` (port mapping), `-d` (detached mode), `--name` (container ka naam), `-v` (volume mapping) jaise bohot kuch aata hai.
*   `podman ps`
    *   **Karam:** Running containers ko list karta hai. `--all` ya `-a` flag ke saath stopped containers bhi dikhata hai.
    *   **Hinglish:** Kaun-kaunse containers abhi chal rahe hain, yeh dekhne ke liye.
*   `podman stop `
    *   **Karam:** Ek running container ko gracefully stop karta hai.
    *   **Hinglish:** Container ko band karne ke liye.
*   `podman rm `
    *   **Karam:** Ek stopped container ko remove karta hai.
    *   **Hinglish:** Jab container ka kaam ho jaaye toh usko system se hatane ke liye.
*   `podman rmi `
    *   **Karam:** Local system se ek container image ko remove karta hai.
    *   **Hinglish:** Images jinhe ab use nahi karna hai, unko delete karne ke liye.
*   `podman build -t  .`
    *   **Karam:** Current directory mein maujood `Dockerfile` se ek naya container image build karta hai. `-t` se tag specify karte hain.
    *   **Hinglish:** Jab aapko apni custom application ka image banana ho, toh yeh command use hota hai.

### 3. Step-by-Step Examples (Kadam-ba-Kadam Udaharan)

Hum ek Ansible control node se remote target machine par containers deploy karenge. Make sure aapka Ansible control node set up hai aur target machine par Podman installed hai aur SSH access hai.

**Prerequisites:**
1.  **Ansible Control Node:** Jahaan se aap Ansible commands chalayenge.
2.  **Managed Node (Target Host):** Jahaan containers deploy honge. Is par Podman installed hona chahiye.
3.  **Inventory File:** Control node par ek `inventory.ini` file banayein:
    ```ini
    [webservers]
    your_target_ip_or_hostname ansible_user=your_username ansible_ssh_private_key_file=/path/to/your/ssh_key

    [all:vars]
    ansible_python_interpreter=/usr/bin/python3
    ```
    *Replace `your_target_ip_or_hostname`, `your_username`, aur `/path/to/your/ssh_key` apni actual details se.*
4.  **Community.general Collection Install Karein:** Control node par yeh command chalayein:
    ```bash
    ansible-galaxy collection install community.general
    ```

---

#### Example 1: Nginx Container Deploy Karna

Is example mein, hum Ansible ka use karke ek Nginx web server container deploy karenge.

1.  **`nginx_deploy.yml` playbook banayein:**
    ```yaml
    ---
    - name: Deploy Nginx Container
      hosts: webservers
      become: true # Podman commands ko root privileges chahiye hote hain
      tasks:
        - name: Pull Nginx image
          community.general.podman_image:
            name: registry.access.redhat.com/ubi8/nginx-120
            tag: latest
            state: present
          # Ubi8-nginx image Red Hat registry se aati hai. Ya aap simple 'nginx' bhi use kar sakte hain agar Docker Hub access hai.

        - name: Run Nginx container
          community.general.podman_container:
            name: my-nginx-web
            image: registry.access.redhat.com/ubi8/nginx-120:latest
            state: started # Ensure container is running
            ports:
              - "80:80" # Host port 80 ko container port 80 se map karein
            restart_policy: "always" # Agar container stop ho toh automatically restart ho
            # network: "host" # Agar host network use karna ho

        - name: Verify Nginx container status
          community.general.podman_container_info:
            name: my-nginx-web
          register: nginx_container_status

        - name: Display Nginx container IP
          ansible.builtin.debug:
            msg: "Nginx container '{{ nginx_container_status.containers[0].Name }}' is running with IP: {{ nginx_container_status.containers[0].NetworkSettings.IPAddress }}"
          when: nginx_container_status.containers | length > 0 and nginx_container_status.containers[0].NetworkSettings.IPAddress is defined
    ```

2.  **Playbook Run Karein:**
    ```bash
    ansible-playbook -i inventory.ini nginx_deploy.yml
    ```

3.  **Verification:**
    *   Target host par `podman ps` chalakar dekhein. Aapko `my-nginx-web` container running dikhna chahiye.
    *   Apne browser mein `http://your_target_ip_or_hostname` par navigate karein. Aapko Nginx welcome page dikhega.

---

#### Example 2: Custom Web App Container Banana aur Deploy Karna

Is example mein, hum ek simple custom HTML page ka container image banayenge aur phir use Ansible se deploy karenge.

1.  **Ek naya directory banayein aur usmein files create karein:**
    ```bash
    mkdir mywebapp
    cd mywebapp
    ```

2.  **`index.html` file banayein:**
    ```html
    
    
    
    
        
    
    

        
Hello from My Custom Web App!

        
This is a containerized service deployed with Ansible.


    

    
    ```

3.  **`Dockerfile` banayein:**
    ```dockerfile
    # mywebapp/Dockerfile
    FROM registry.access.redhat.com/ubi8/nginx-120:latest
    LABEL maintainer="Your Name "
    COPY index.html /opt/app-root/src/index.html
    # Nginx default configuration already serves from /opt/app-root/src
    # EXPOSE 80
    CMD ["nginx", "-g", "daemon off;"]
    ```
    *   **Explanation:**
        *   `FROM`: Base image set karta hai (Red Hat UBI 8 Nginx).
        *   `COPY`: Hamari `index.html` file ko container ke andar `/opt/app-root/src/index.html` par copy karta hai.
        *   `CMD`: Container start hone par kya command run ho.

4.  **`custom_web_app_deploy.yml` playbook banayein (parent directory mein):**
    ```yaml
    ---
    - name: Build and Deploy Custom Web App Container
      hosts: webservers
      become: true
      tasks:
        - name: Build custom web app image
          community.general.podman_image:
            name: my-custom-webapp
            tag: v1.0
            build:
              path: "{{ playbook_dir }}/mywebapp" # Dockerfile aur context ka path
              pull: yes # Base image pull kare
            state: present
          # build ke liye context path specify karna zaroori hai

        - name: Ensure old custom web app container is stopped and removed (for clean redeploy)
          community.general.podman_container:
            name: my-custom-webapp-instance
            state: absent # Remove the container if it exists
            force: true # Force stop before removing

        - name: Run custom web app container
          community.general.podman_container:
            name: my-custom-webapp-instance
            image: my-custom-webapp:v1.0
            state: started
            ports:
              - "8080:80" # Host port 8080 ko container port 80 se map karein
            restart_policy: "always"
    ```
    *   **Important:** `path: "{{ playbook_dir }}/mywebapp"` yeh batata hai ki `Dockerfile` aur uske related files kahan hain. `playbook_dir` ek special Ansible variable hai jo current playbook ki directory ko point karta hai.

5.  **Playbook Run Karein:**
    ```bash
    ansible-playbook -i inventory.ini custom_web_app_deploy.yml
    ```

6.  **Verification:**
    *   Target host par `podman images` chalakar dekhein. Aapko `my-custom-webapp:v1.0` image dikhni chahiye.
    *   Target host par `podman ps` chalakar dekhein. Aapko `my-custom-webapp-instance` container running dikhna chahiye.
    *   Apne browser mein `http://your_target_ip_or_hostname:8080` par navigate karein. Aapko "Hello from My Custom Web App!" message dikhega.

---

#### Example 3: Container State Manage Karna (Stop/Remove)

Ab hum dekhenge ki kaise Ansible se containers ko stop aur remove kar sakte hain.

1.  **`cleanup_container.yml` playbook banayein:**
    ```yaml
    ---
    - name: Stop and Remove Specific Container and its Image
      hosts: webservers
      become: true
      tasks:
        - name: Stop and remove Nginx container (my-nginx-web)
          community.general.podman_container:
            name: my-nginx-web
            state: absent # Stop and remove the container
            force: true # Force stop if it's not responding gracefully

        - name: Remove Nginx image (ubi8/nginx-120)
          community.general.podman_image:
            name: registry.access.redhat.com/ubi8/nginx-120
            tag: latest
            state: absent # Remove the image

        - name: Stop and remove custom web app container (my-custom-webapp-instance)
          community.general.podman_container:
            name: my-custom-webapp-instance
            state: absent
            force: true

        - name: Remove custom web app image (my-custom-webapp)
          community.general.podman_image:
            name: my-custom-webapp
            tag: v1.0
            state: absent
    ```

2.  **Playbook Run Karein:**
    ```bash
    ansible-playbook -i inventory.ini cleanup_container.yml
    ```

3.  **Verification:**
    *   Target host par `podman ps -a` aur `podman images` chalakar dekhein. Saare specified containers aur images remove ho chuke honge.
    *   Browser mein `http://your_target_ip_or_hostname` aur `http://your_target_ip_or_hostname:8080` check karein. Pages accessible nahi hone chahiye.

---

### 4. Practice Tasks (Abhyas Karya)

Yahan kuch practical exercises hain jinhe aap try kar sakte hain apni skills improve karne ke liye:

1.  **Task 1: MariaDB Container Deploy Karein.**
    *   Ek Ansible playbook likhein jo target host par `mariadb` container deploy kare.
    *   Container ka naam `my-database` rakhein.
    *   Host port `3306` ko container port `3306` se map karein.
    *   Environment variable `MYSQL_ROOT_PASSWORD` set karein (e.g., `MY_SECRET_PASSWORD`).
    *   Ensure container `started` state mein hai aur `restart_policy: always` hai.
    *   Deploy karne ke baad, target host par `podman ps` aur `podman logs my-database` se verify karein.

2.  **Task 2: Persistent Volume ke saath Redis Container Deploy Karein (RHCE Level).**
    *   Redis ek in-memory data store hai, lekin iska data persistent bhi ho sakta hai.
    *   Ek Ansible playbook likhein.
    *   `redis` container deploy karein.
    *   Ek Podman **volume** create karein (e.g., `redis_data_volume`).
    *   Is volume ko container ke andar `/data` path par mount karein.
    *   `podman_container` module mein `volumes` parameter use hoga.
    *   Hint: `volumes: - "redis_data_volume:/data"`
    *   Verify karein ki volume ban gaya hai (`podman volume ls`) aur container running hai (`podman ps`).

3.  **Task 3: Container Health Check Add Karein (RHCE Level).**
    *   Pichle `my-custom-webapp` example ko modify karein.
    *   `podman_container` module mein `healthcheck` parameters add karein.
    *   Example healthcheck: `healthcheck: { test: ["CMD", "curl", "-f", "http://localhost"], interval: "5s", timeout: "3s", retries: 3 }`
    *   Playbook run karein aur deploy hone ke baad target host par `podman inspect my-custom-webapp-instance` ya `podman healthcheck run my-custom-webapp-instance` se health status check karein.

---

### 5. Pro Tips aur Common Mistakes (Vishesh Salah aur Samanya Galtiyan)

#### Pro Tips (Vishesh Salah):

*   **Idempotency:** Hamesha Ansible playbooks ko idempotent banane ki koshish karein. Matlab, playbook ko kitni bhi baar run karein, woh system ki state ko sirf required state tak hi laaye, bina unnecessary changes kiye. `state: present`, `state: started`, `state: absent` ka sahi use karein.
*   **Use `name` for Containers:** Containers ko meaningful `name` dein (`podman_container` module mein `name` parameter). Isse unhe manage karna, stop karna, remove karna easy ho jaata hai.
*   **`--check` aur `--diff` ka Use:** Playbooks ko production mein run karne se pehle hamesha `--check` aur `--diff` flags ka use karein. Isse aapko pata chal jaata hai ki kaunse changes apply honge aur kya impact hoga.
*   **`ansible-lint`:** Apne playbooks ko `ansible-lint` tool se validate karein. Yeh best practices enforce karta hai aur common mistakes ko pakadta hai.
*   **`community.general` Collection:** Yaad rakhein ki `podman_` modules `community.general` collection ka part hain. Hamesha ensure karein ki yeh collection control node par installed ho.
*   **`become: true`:** Container operations (jaise `podman pull`, `podman run`) typically root privileges maangte hain. Isliye, apne playbooks mein `become: true` use karna na bhoolein.
*   **Ansible Vault:** Sensitive information (jaise database passwords, API keys) ko playbooks mein hardcode na karein. Uske liye Ansible Vault ka use karein.
*   **`listen_addresses` aur `ports`:** Agar aap containerized service ko bahar se access karna chahte hain, toh uske application ko `0.0.0.0` par listen karna chahiye, na ki sirf `127.0.0.1` par. Aur `ports` parameter ko sahi se map karein.

#### Common Mistakes (Samanya Galtiyan):

*   **YAML Indentation Errors:** YAML files mein indentation bohot important hai. Thodi si bhi galti se playbook fail ho jaayega. Hamesha 2 spaces ka indentation use karein.
*   **Missing `become: true`:** Podman commands root privileges se chalte hain. Agar aapne `become: true` nahi lagaya toh access denied errors milenge.
*   **Collection Not Installed:** Agar `podman_image` ya `podman_container` module "not found" error deta hai, toh iska matlab hai ki `community.general` collection install nahi hai.
*   **Port Conflicts:** Agar aap host machine par ek port map kar rahe hain jo pehle se hi kisi aur service ya container dwara use kiya ja raha hai, toh `port already in use` error aayega. Ensure karein ki host port available ho.
*   **Not Managing Container State Correctly:** Sirf `state: present` se container create aur start ho jaata hai, lekin agar woh beech mein stop ho jaaye toh automatically restart nahi hoga. `state: started` aur `restart_policy: always` ka use karein. `state: absent` se stop aur remove dono hote hain.
*   **Incorrect Image Name/Tag:** Image name ya tag mein spelling mistake ya galat version specify karne se `image not found` error aa sakta hai.
*   **Context Path for `build`:** Jab `podman_image` module se image build karte hain, toh `build.path` parameter ko `Dockerfile` aur uske related files ki directory ka sahi path dena bohot zaroori hai.

---

Umeed hai yeh comprehensive lesson aapko Ansible aur containers ke concepts, commands, aur practical implementation ko samajhne mein madad karega. RHCSA aur RHCE exams ke liye aur real-world scenarios mein automation ke liye yeh skills bohot valuable hain! Happy Automating!
