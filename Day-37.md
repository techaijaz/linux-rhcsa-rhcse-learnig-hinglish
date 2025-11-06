Day 37: Ansible Vault (encryption)
---
Namaste, future Linux champions aur Ansible masters! Aaj hum ek bahut hi critical topic par discuss karenge jo aapke infrastructure automation ko secure banata hai: **Ansible Vault (Encryption)**. Yeh topic RHCSA aur RHCE dono exams ke liye crucial hai aur real-world mein iska usage har jagah hota hai jahan sensitive data ko manage karna hota hai.

Chaliye, shuru karte hain!

---

## **Ansible Vault (Encryption) - Comprehensive RHCSA + RHCE Training Lesson**

### 1. Concept Explanation (Hinglish)

Dekho doston, jab hum Ansible se servers manage karte hain, toh kai baar humein sensitive information (jaise database passwords, API keys, SSH private keys, certificates, etc.) use karni padti hai. Agar hum yeh data plain text mein apne playbooks ya variable files mein rakhenge, toh yeh ek bahut bada security risk hai. Koi bhi jiske paas aapke Ansible repository ka access hai, woh yeh credentials dekh sakta hai, aur yeh bilkul theek nahi hai.

Is problem ko solve karne ke liye Ansible ne introduce kiya **Ansible Vault**.

**Ansible Vault kya hai?**
Ansible Vault ek feature hai jo sensitive data ko encrypt karne ki functionality provide karta hai. Iski madad se aap apni files, variables ya strings ko encrypt kar sakte ho taki woh readable na rahein. Jab Ansible ko unko use karna hota hai, tab aapko ek password provide karna padta hai jisse Ansible unhe decrypt karta hai runtime par.

**Kyun chahiye Ansible Vault? (Why do we need it?)**
*   **Security:** Sabse main reason. Taaki aapke passwords, API keys, aur doosre confidential credentials koi bhi unauthorised person na dekh paaye.
*   **Compliance:** Bahut saari regulatory bodies aur security standards (jaise PCI DSS, HIPAA) demand karte hain ki sensitive data encrypted ho.
*   **Version Control:** Jab aap apna Ansible code Git jaise version control systems mein store karte ho, toh encrypted files safe rehti hain. Plain text files commit karna ek bahut badi galti hai.

**Ansible Vault kaise kaam karta hai? (How does it work?)**
Simple terms mein:
1.  Aap ek file banate ho jisme plain text data hota hai (e.g., `db_pass: mysecretpassword`).
2.  `ansible-vault encrypt` command se aap is file ko encrypt karte ho. Yeh aapko ek password set karne ke liye bolega.
3.  File encrypt hone ke baad, uska content scramble ho jaata hai aur readable nahi rehta.
4.  Jab aap koi playbook run karte ho jo us encrypted file ko use karta hai, tab Ansible aapko vault password enter karne ke liye prompt karega (ya aap password kisi file se provide kar sakte ho).
5.  Password milne par, Ansible us data ko memory mein decrypt karta hai, use karta hai, aur phir discard kar deta hai. Disk par data hamesha encrypted hi rehta hai.

**Kahan use kar sakte hain? (Where can it be used?)**
*   **Variables Files:** Sabse common use case. `group_vars/`, `host_vars/`, ya `vars/` directory mein separate vault files banana.
*   **Individual Variables/Strings:** Sirf ek particular variable ya string ko encrypt karna, poori file ko nahi.
*   **Entire Playbooks:** Kuch scenarios mein jahan poora playbook hi sensitive operations perform karta hai, aap poore playbook ko encrypt kar sakte ho.
*   **Static Files:** Agar aapko koi SSH private key ya certificate server par copy karna hai, toh aap use pehle vault se encrypt kar sakte ho.

Yeh aapke automation ko secure aur professional banata hai. Iske bina, aapka setup vulnerable ho sakta hai.

---

### 2. Key Linux Commands

Yahan par kuch important `ansible-vault` commands hain jo aapko pata hone chahiye:

#### `ansible-vault create `
*   **English:** Creates a new encrypted file.
*   **Hinglish:** Is command se aap ek nayi file banate ho aur usko turant encrypt bhi kar dete ho. Jaise hi aap yeh command run karoge, yeh aapko vault password set karne ke liye kahega, aur phir us file mein content add karne ke liye ek editor open karega. Jab aap save karke exit karoge, toh content encrypted state mein file mein save ho jaayega.
*   **Example:** `ansible-vault create group_vars/all/secrets.yml`

#### `ansible-vault edit `
*   **English:** Edits an existing encrypted file.
*   **Hinglish:** Agar aapko ek existing encrypted file ke content ko change karna hai, toh yeh command use hota hai. Yeh aapko vault password enter karne ke liye prompt karega (agar specify nahi kiya gaya hai), phir file ko decrypt karke editor mein open karega. Jab aap changes karke save aur exit karoge, toh file dobara encrypt ho jaayegi naye content ke saath.
*   **Example:** `ansible-vault edit group_vars/all/secrets.yml`

#### `ansible-vault view `
*   **English:** Views the decrypted content of an encrypted file.
*   **Hinglish:** Yeh command kisi encrypted file ke content ko dekhne ke liye use hota hai, without editing it. Yeh aapko password poochega, decrypt karega, aur content ko `stdout` (terminal) par display kar dega.
*   **Example:** `ansible-vault view group_vars/all/secrets.yml`

#### `ansible-vault decrypt `
*   **English:** Decrypts an encrypted file, converting it back to plain text.
*   **Hinglish:** Agar aapko kisi file ko permanently decrypt karke plain text mein convert karna hai, toh is command ka use hota hai. Yeh file ko decrypted state mein save kar dega. **Caution:** Is command ko tabhi use karein jab aapko pata ho ki aap kya kar rahe hain, kyunki plain text file source control mein nahi jaani chahiye.
*   **Example:** `ansible-vault decrypt group_vars/all/secrets.yml`

#### `ansible-vault encrypt `
*   **English:** Encrypts a plain text file.
*   **Hinglish:** Agar aapke paas already ek plain text file hai jise aap encrypt karna chahte ho, toh yeh command use hota hai. Yeh aapko password set karne ke liye kahega aur file ko encrypt kar dega.
*   **Example:** `ansible-vault encrypt my_database_creds.yml`

#### `ansible-vault rekey `
*   **English:** Changes the password of an encrypted file.
*   **Hinglish:** Jab aapko kisi encrypted file ka password change karna ho, toh yeh command sabse best hai. Yeh aapko pehle current password poochega, aur phir naya password set karne ke liye kahega. Puraani file ko decrypt karke naye password se dobara encrypt kar deta hai.
*   **Example:** `ansible-vault rekey group_vars/all/secrets.yml`

#### `ansible-vault encrypt_string '' --name ''`
*   **English:** Encrypts a single string and outputs it in a format suitable for embedding directly into YAML files.
*   **Hinglish:** Yeh ek bahut useful command hai jab aapko kisi poori file ko encrypt nahi karna, balki sirf ek particular variable ki value ko encrypt karna hai. Yeh ek YAML block output karta hai jise aap seedha apni playbook ya vars file mein copy-paste kar sakte ho.
*   **Example:** `ansible-vault encrypt_string 'MySuperSecretDBPass' --name 'db_password'`

#### `ansible-playbook ... --ask-vault-pass`
*   **English:** Prompts for the vault password when running a playbook.
*   **Hinglish:** Jab aap koi playbook run kar rahe ho jisme encrypted data use ho raha hai, toh Ansible ko woh data decrypt karne ke liye password chahiye hota hai. ` --ask-vault-pass` flag use karne se Ansible aapko terminal par password enter karne ke liye prompt karega.
*   **Example:** `ansible-playbook deploy_app.yml --ask-vault-pass`

#### `ansible-playbook ... --vault-password-file `
*   **English:** Provides the vault password from a file.
*   **Hinglish:** ` --ask-vault-pass` har baar password type karne ke liye theek hai, lekin automation mein ya CI/CD pipelines mein yeh practical nahi hai. Iski jagah aap ek text file bana sakte ho jisme sirf vault password ho, aur us file ka path ` --vault-password-file` flag ke saath provide kar sakte ho. **Bahut important:** Is password file ko bahut carefully manage karein aur kabhi bhi version control mein commit na karein. Permissions (`chmod 400`) restrict karna na bhoolein.
*   **Example:** `ansible-playbook deploy_app.yml --vault-password-file ~/.ansible/vault_pass.txt`

#### `ansible.cfg` mein `vault_password_file` set karna
*   **English:** Configure the default vault password file in `ansible.cfg`.
*   **Hinglish:** Agar aapko har baar ` --vault-password-file` flag nahi dena, toh aap apne `ansible.cfg` file mein `[defaults]` section mein `vault_password_file` parameter set kar sakte ho. Ansible automatically us file ko use karega password ke liye.
*   **Example:**
    ```ini
    # ansible.cfg
    [defaults]
    inventory = inventory.ini
    remote_user = devops
    vault_password_file = ~/.ansible/vault_pass.txt
    ```

---

### 3. ������ Step-by-Step Examples

Chaliye kuch real-world scenarios mein in commands ko apply karke dekhte hain.

**Setup:**
Pehle hum ek basic Ansible setup bana lete hain.

```bash
mkdir ansible_vault_lesson
cd ansible_vault_lesson
mkdir group_vars
mkdir playbooks
touch inventory.ini
```

**`inventory.ini` content:**
```ini
[webservers]
server1 ansible_host=192.168.1.10
server2 ansible_host=192.168.1.11

[databases]
dbserver ansible_host=192.168.1.20
```
(Note: Replace IP addresses with your actual lab IPs or use `localhost` for testing).

#### Example 1: Encrypting a complete variable file

Aapke paas ek file hai jisme database credentials hain. Aapko is file ko encrypt karna hai.

**Step 1: Plain text variable file banayein.**
`group_vars/databases/secrets.yml` file banayein aur usme database credentials likhein:
```yaml
# group_vars/databases/secrets.yml
db_username: admin_user
db_password: MySecretDbPass123!
```

**Step 2: File ko encrypt karein.**
```bash
ansible-vault encrypt group_vars/databases/secrets.yml
```
Output:
```
New vault password:     <--- Yahan strong password enter karein
Confirm new vault password: <--- Password re-enter karein
Encryption successful
```
Ab `cat group_vars/databases/secrets.yml` karein. Aapko encrypted content dikhega, readable nahi:
```
$ANSIBLE_VAULT;1.1;AES256
66323635393433363339663737333065326537383236373836376536643665383533613031306362...
```

**Step 3: Encrypted content ko view karein.**
```bash
ansible-vault view group_vars/databases/secrets.yml
```
Output:
```
Vault password:         <--- Yahan apna vault password enter karein
db_username: admin_user
db_password: MySecretDbPass123!
```
Ab content decrypted form mein dikh raha hai terminal par, jo ki theek hai.

**Step 4: Playbook mein encrypted variables ko use karein.**
`playbooks/db_setup.yml` file banayein:
```yaml
# playbooks/db_setup.yml
---
- name: Configure Database
  hosts: databases
  become: yes
  vars_files:
    - ../group_vars/databases/secrets.yml # Yeh encrypted file hai

  tasks:
    - name: Show database username (for demonstration, avoid in prod)
      debug:
        msg: "Database Username: {{ db_username }}"

    - name: Use database password for a task (e.g., create user)
      # In a real scenario, this would be a module like community.mysql.mysql_user
      # For demonstration, we just show the variable.
      # NEVER output sensitive data in debug in production.
      debug:
        msg: "Using password: {{ db_password }} for user {{ db_username }}"
      no_log: true # Is task ke output ko logs mein na dikhaye, for security
```

**Step 5: Playbook run karein.**
```bash
ansible-playbook playbooks/db_setup.yml --ask-vault-pass
```
Output:
```
Vault password:         <--- Yahan apna vault password enter karein

PLAY [Configure Database] ******************************************************

TASK [Gathering Facts] *********************************************************
ok: [dbserver]

TASK [Show database username (for demonstration, avoid in prod)] **************
ok: [dbserver] => {
    "msg": "Database Username: admin_user"
}

TASK [Use database password for a task (e.g., create user)] *******************
ok: [dbserver] => {
    "msg": "Using password: VALUE_SPECIFIED_IN_NO_LOG_PARAMETER for user admin_user"
}

PLAY RECAP *********************************************************************
dbserver                   : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
Notice `no_log: true` ne password ko `VALUE_SPECIFIED_IN_NO_LOG_PARAMETER` dikhaya, jo ki production ke liye best practice hai.

#### Example 2: Encrypting a single string within a playbook/vars file

Kabhi-kabhi aapko poori file nahi, balki sirf ek particular variable ki value encrypt karni hoti hai.

**Step 1: Single string ko encrypt karein.**
Aapko ek `nginx_admin_password` variable banana hai jo encrypted ho.
```bash
ansible-vault encrypt_string 'nginx_pass_123!' --name 'nginx_admin_password'
```
Output:
```
New vault password:
Confirm new vault password:
!vault |
          AES256_GCM@openssh.com
          383236373737356230623237303031626435386638643864333966393539343361663737
          ... (long encrypted string) ...
          333539343936613936653331393630636636663539653835363065353165383935613337
nginx_admin_password: !vault |
          AES256_GCM@openssh.com
          383236373737356230623237303031626435386638643864333966393539343361663737
          ... (long encrypted string) ...
          333539343936613936653331393630636636663539653835363065353165383935613337
```
Yeh output YAML format mein hai. Copy this output.

**Step 2: Is encrypted string ko apni vars file mein add karein.**
`group_vars/webservers/main.yml` file banayein aur ismein paste karein:
```yaml
# group_vars/webservers/main.yml
# Other webserver variables can go here
web_port: 80

# Encrypted variable for nginx admin password
nginx_admin_password: !vault |
          AES256_GCM@openssh.com
          383236373737356230623237303031626435386638643864333966393539343361663737
          ... (long encrypted string) ...
          333539343936613936653331393630636636663539653835363065353165383935613337
```

**Step 3: Playbook mein use karein.**
`playbooks/web_setup.yml` file banayein:
```yaml
# playbooks/web_setup.yml
---
- name: Configure Webservers
  hosts: webservers
  become: yes

  tasks:
    - name: Show web port
      debug:
        msg: "Webserver is on port {{ web_port }}"

    - name: Configure Nginx with admin password (demonstration)
      debug:
        msg: "Configuring Nginx with admin password: {{ nginx_admin_password }}"
      no_log: true # Again, avoid logging sensitive data
```

**Step 4: Playbook run karein.**
```bash
ansible-playbook playbooks/web_setup.yml --ask-vault-pass
```
Output (similar to previous example, with password hidden by `no_log`):
```
Vault password:

PLAY [Configure Webservers] ****************************************************

TASK [Gathering Facts] *********************************************************
ok: [server1]
ok: [server2]

TASK [Show web port] ***********************************************************
ok: [server1] => {
    "msg": "Webserver is on port 80"
}
ok: [server2] => {
    "msg": "Webserver is on port 80"
}

TASK [Configure Nginx with admin password (demonstration)] *********************
ok: [server1] => {
    "msg": "Configuring Nginx with admin password: VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
}
ok: [server2] => {
    "msg": "Configuring Nginx with admin password: VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
}

PLAY RECAP *********************************************************************
server1                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
server2                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

#### Example 3: Using a vault password file

Har baar `--ask-vault-pass` use karna tedious ho sakta hai. Ek password file use karte hain.

**Step 1: Vault password file banayein.**
Ek simple text file banayein jismein sirf aapka vault password ho.
```bash
echo "MyVaultStrongPassword123!" > ~/.ansible/vault_pass.txt
```
**Important:** File permissions restrict karein! Koi bhi is file ko read na kar paaye aapke alawa.
```bash
chmod 400 ~/.ansible/vault_pass.txt
```

**Step 2: Playbook run karte waqt password file specify karein.**
Ab aap apne playbooks ko ` --vault-password-file` flag ke saath run kar sakte hain:
```bash
ansible-playbook playbooks/db_setup.yml --vault-password-file ~/.ansible/vault_pass.txt
ansible-playbook playbooks/web_setup.yml --vault-password-file ~/.ansible/vault_pass.txt
```
Ab Ansible automatically password file se read kar lega, aur aapko manual entry ki zaroorat nahi padegi.

**Step 3 (RHCE): `ansible.cfg` mein `vault_password_file` set karna.**
Agar aapko yeh flag bhi nahi specify karna, toh `ansible.cfg` mein set kar sakte hain.
Apne project directory mein `ansible.cfg` file banayein:
```ini
# ansible.cfg
[defaults]
inventory = inventory.ini
remote_user = devops
# Apne system ke vault password file ka path dein
vault_password_file = ~/.ansible/vault_pass.txt
```
Ab, aap sirf `ansible-playbook playbooks/db_setup.yml` run kar sakte hain, aur Ansible automatically `ansible.cfg` se `vault_password_file` ka path utha lega.

#### Example 4: Rekeying a vault file

Aapko apne `group_vars/databases/secrets.yml` file ka password change karna hai.

**Step 1: Rekey command chalayein.**
```bash
ansible-vault rekey group_vars/databases/secrets.yml
```
Output:
```
Vault password:         <--- Enter your CURRENT vault password
New vault password:     <--- Enter your NEW vault password
Confirm new vault password: <--- Re-enter NEW vault password
Rekey successful
```
Ab yeh file naye password se encrypted hai. Agar aap apne `vault_pass.txt` ka password bhi change kar rahe hain, toh us file ko bhi update karna na bhoolein.

---

### 4. Practice Tasks (Lab Tasks)

Yeh kuch practical exercises hain jinhe aap apne lab environment mein try kar sakte hain:

1.  **Task 1: MySQL User Password Encryption**
    *   Ek naya encrypted variable file banao (`group_vars/all/mysql_creds.yml`) jismein `mysql_root_password` aur `mysql_app_user_password` variables store hon.
    *   In variables ko encrypt karo (`ansible-vault create`).
    *   Ek playbook (`playbooks/mysql_users.yml`) banao jo `mysql_app_user` create kare aur uska password encrypted file se uthaye. Make sure to use `no_log: true` for the password.
    *   Playbook ko run karo using `--ask-vault-pass`.

2.  **Task 2: API Key Encryption**
    *   Assume aap ek `aws_access_key` aur `aws_secret_key` ko manage kar rahe ho.
    *   `ansible-vault encrypt_string` command ka use karke `aws_secret_key` ko encrypt karo.
    *   Encrypted string ko kisi `host_vars/specific_host/cloud_creds.yml` file mein add karo.
    *   Ek simple playbook banao jo is key ko use karne ka simulate kare (`debug` module ke saath).
    *   Playbook run karo using a dedicated `vault_pass.txt` file (make sure its permissions are `400`).

3.  **Task 3: Playbook Encryption (Advanced)**
    *   Ek chhota sa playbook (`playbooks/sensitive_task.yml`) banao jo kuch sensitive operations karta ho (e.g., kisi file ko delete karna ya service stop karna).
    *   Is poore playbook file ko encrypt karo (`ansible-vault encrypt`).
    *   Koshish karo is encrypted playbook ko run karne ki. Aapko `--ask-vault-pass` ya ` --vault-password-file` use karna padega.
    *   Playbook ko view karo using `ansible-vault view`.

4.  **Task 4: Vault Password File Management**
    *   Ek naya vault password file (`my_project_vault_pass.txt`) banao.
    *   Apne `ansible.cfg` file ko modify karo taaki woh is naye password file ko use kare.
    *   Ek existing encrypted file (`group_vars/all/secrets.yml` jaisa koi file) ka password change karo using `ansible-vault rekey`. Make sure to update your `my_project_vault_pass.txt` file accordingly.
    *   Verify ki sab kuch naye password ke saath theek se kaam kar raha hai.

---

### 5. Pro Tips / Common Mistakes

**Pro Tips:**

*   **Separate Vault Files:** Har group ya host ke liye separate vault files maintain karna ek good practice hai. For example: `group_vars/webservers/secrets.yml` aur `group_vars/databases/secrets.yml`.
*   **Permissions on Password File:** `vault_pass.txt` ko hamesha `chmod 400` permissions dein (`read-only` for owner only). Isse koi aur user is file ko read nahi kar paayega.
*   **Never Commit Password File:** `vault_pass.txt` ya koi bhi plaintext password file kabhi bhi apne Git repository mein commit na karein. ` .gitignore` ka use karein.
*   **Strong, Unique Passwords:** Apne vault ke liye strong aur unique passwords use karein. Multiple vaults ke liye different passwords use karna bhi ek good practice hai (though managing multiple vault IDs can get complex, RHCE level).
*   **`no_log: true`:** Jab bhi aap passwords ya other sensitive data display ya handle kar rahe ho, `no_log: true` ko task level par use karna na bhoolein. Yeh logs mein sensitive data ko print hone se rokta hai.
*   **Vault IDs (RHCE Level):** Jab aapke paas multiple encrypted files hon aur har ek ka password alag ho, tab aap `ansible-vault encrypt --vault-id ` use kar sakte ho. Phir `ansible-playbook --vault-id @prompt` ya ` --vault-id @` ka use karke correct password supply kar sakte ho. Yeh advance usage hai but useful for large setups.
*   **Integration with CI/CD:** CI/CD pipelines mein, vault passwords environment variables (`ANSIBLE_VAULT_PASSWORD`) ke through ya secret management tools (HashiCorp Vault, AWS Secrets Manager, Azure Key Vault) se inject kiye jaate hain.

**Common Mistakes to Avoid:**

*   **Committing plaintext secrets:** Sabse badi galti. Kabhi na karein.
*   **Weak Vault Passwords:** Easily guessable passwords se aapki encryption ineffective ho jaati hai.
*   **Incorrect `vault_password_file` permissions:** Agar permissions bahut open hain, toh koi bhi password padh sakta hai.
*   **Forgetting Vault Password:** Agar aap apna vault password bhool gaye, toh encrypted data recover karna impossible ho sakta hai. So, password ko secure tarike se manage karein.
*   **Using `ansible-vault decrypt` casually:** Agar aapne kisi file ko decrypt kar diya aur phir bhool gaye use dobara encrypt karna, toh woh plain text mein reh jaayegi.
*   **Not using `no_log: true`:** Debugging ke time passwords console par dikh sakte hain, jo ki security risk hai. Always use `no_log: true` for sensitive tasks.
*   **Not differentiating between `encrypt` and `encrypt_string`:** `encrypt` poori file ko karta hai, `encrypt_string` sirf ek particular value ko YAML mein embed karne ke liye. Apni requirement ke hisaab se sahi command choose karein.

---

Umeed hai ki yeh comprehensive lesson aapko Ansible Vault ko acche se samajhne aur use karne mein helpful hoga. Remember, security is paramount in automation! Practice karte rahiye, aur koi bhi doubt ho toh poochte rahiye.
