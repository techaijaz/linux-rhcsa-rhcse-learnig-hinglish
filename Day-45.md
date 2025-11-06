Day 45: Podman basics (containers, images, volumes)
---
Namaste, Linux Enthusiasts! Main aapka Linux Instructor, aur aaj hum Podman ki duniya mein dive karenge. Yeh topic RHCSA aur RHCE exams ke liye bahut crucial hai, aur real-world server management mein bhi Podman kaafi useful hai. Toh chaliye, shuru karte hain Podman ki basic concepts ko, containers, images aur volumes ko samjhenge!

---

## ������ RHCSA + RHCE Training Lesson: Podman Basics (Containers, Images, Volumes)

### Title: Podman Ki Pehli Udaan: Containers, Images aur Data Persistence!

**Duration:** Approx. 60-90 minutes (practical implementation ke saath)

**Target Audience:** Linux Admins seeking RHCSA/RHCE certification, Developers, DevOps Engineers.

**Prerequisites:** Basic Linux command-line knowledge, understanding of file systems, user management.

---

### 1. Concept Explanation (Hinglish)

Sabse pehle, yeh jaan lete hain ki Podman kya hai aur hum iski baat kyun kar rahe hain.

#### ������ Podman Kya Hai?

Podman basically ek **daemonless container engine** hai. "Daemonless" ka matlab hai ki Docker ki tarah ismein koi background service (daemon) continuously run nahi karti. Jab aap Podman command run karte ho, woh process chalegi aur uske baad end ho jayegi, security aur resource management ke liye yeh ek bada plus point hai.

*   **Daemonless:** Koi background daemon nahi. Commands directly containers ke saath interact karte hain.
*   **Rootless:** Podman ko aap as a normal user bhi run kar sakte ho, bina `sudo` ke. Isse security significantly improve hoti hai. Har user apne containers manage kar sakta hai, system-wide access ki zaroorat nahi.
*   **OCI Compliant:** Open Container Initiative (OCI) standards ko follow karta hai, jiska matlab hai ki aap Podman se create kiye gaye containers aur images dusre OCI-compliant runtimes (jaise runc, Kata Containers) ke saath bhi compatible honge.
*   **Docker Compatibility:** Podman ki commands Docker ki commands se kaafi similar hain, toh agar aapko Docker aata hai, Podman seekhna bahut easy hoga.

**Podman Kyun Use Karein?**
RHCSA aur RHCE mein Podman preferred container runtime hai Red Hat ecosystem mein. Security, resource efficiency aur native systemd integration iske kuch bade advantages hain.

#### ������ Containers Kya Hain?

Socho ki aapke paas ek application hai (jaise ek website, ya database). Is application ko run karne ke liye ek specific environment chahiye hota hai (jaise ek operating system, libraries, dependencies). Agar aap is application ko directly apne server par install karte ho, toh woh server par installed dusri applications ke saath conflict kar sakti hai.

**Containers** lightweight virtual machines jaise hote hain, jo applications ko **isolate** karke run karte hain.

*   **Isolation:** Har container ka apna separate file system, network aur process space hota hai. Ek container mein chal rahi application dusre container ki application ko affect nahi karti.
*   **Portability:** Container ko ek environment mein banao aur use kisi bhi dusre environment mein run karo jahan container runtime installed ho (chahe woh aapka laptop ho, cloud server ho ya data center). "Build once, run anywhere."
*   **Efficiency:** VMs ke comparison mein containers OS kernel ko host machine se share karte hain, isliye woh bahut lightweight aur fast start hote hain. Unhe poore OS ki copy ki zaroorat nahi hoti.
*   **Layers:** Containers image layers se bante hain, jo storage efficient banata hai.

#### ������️ Images Kya Hain?

Agar container application ka running instance hai, toh **Image** us application ka read-only blueprint ya template hai.

*   **Read-Only Templates:** Image ek static snapshot hoti hai, jismein application code, runtime, libraries, environment variables aur configuration files hote hain. Jab aap ek container run karte ho, Podman us image ka use karke ek naya writable layer create karta hai.
*   **Layers:** Images multiple layers se banti hain. Har layer ek specific change ko represent karti hai (jaise ek OS base, fir ek software installation, fir application code). Yeh layers share kiye ja sakte hain, jisse disk space bachta hai.
*   **Dockerfile:** Images ko generally `Dockerfile` naam ki text file se define kiya jaata hai. Ismein step-by-step instructions hoti hain ki image ko kaise build karna hai. (RHCE level par yeh important hai).

#### ������ Volumes Kya Hain?

By default, container ke andar jo data generate hota hai, woh container ke saath hi delete ho jaata hai jab container remove hota hai. Par humein toh data ko **persistent** rakhna hai, right? Jaise database ka data, ya website ke user uploads. Yahin par **Volumes** kaam aate hain.

*   **Persistent Storage:** Volumes container ke lifecycle se independent hote hain. Aap container ko delete bhi kar do, volume mein stored data secure rahega.
*   **Data Sharing:** Volumes ko multiple containers ke beech share bhi kiya ja sakta hai.
*   **Types of Volumes:**
    *   **Named Volumes:** Podman khud manage karta hai ki data kahan store hoga host system par. Ye preferred method hai. (Example: `podman volume create mydata`)
    *   **Bind Mounts:** Aap manually host machine par kisi specific directory ko container ke andar mount karte ho. Yeh development ke liye useful hai. (Example: `-v /host/path:/container/path`)
    *   **tmpfs Mounts:** Volatile data ke liye, jo RAM mein store hota hai aur container stop hote hi delete ho jaata hai. Performance-sensitive ya sensitive data ke liye useful.

---

### 2. Key Linux Commands (Hinglish)

Ab hum Podman ke essential commands dekhenge jo containers, images, aur volumes ko manage karne ke liye use hote hain. Har command ka explanation Hinglish mein hoga.

```bash
# General Information
podman info                       # Podman environment ki details dikhata hai.
podman --version                  # Podman ka version batata hai.

# Image Management
podman search         # Container registries (jaise Docker Hub, Red Hat Registry) mein image search karta hai.
podman pull           # Ek image ko remote registry se download karke local storage mein lata hai.
                                  # Example: podman pull registry.access.redhat.com/ubi8/ubi
                                  # Example: podman pull docker.io/library/nginx
podman images                     # Locally available sabhi images ko list karta hai.
podman rmi      # Ek ya zyada images ko remove karta hai.
podman image prune                # Unused images (dangling images) ko remove karta hai.
podman inspect  # Ek image ki detailed configuration dikhata hai.

# Container Management
podman run                        # Ek naya container create aur run karta hai. Yeh sabse zyada use hone wala command hai.
    -it                           # Interactive pseudo-TTY allocate karta hai (interact karne ke liye).
    -d                            # Container ko background (detached) mode mein run karta hai.
    --name        # Container ko ek custom naam deta hai.
    -p : # Host machine ke port ko container ke port se map karta hai.
    --rm                          # Container stop hone ke baad automatically remove kar deta hai.
    --volume / -v : # Host directory ko container ke andar mount karta hai (bind mount).
    --volume / -v : # Named volume ko container ke andar mount karta hai.
    --network       # Container ko ek specific network se attach karta hai. (Advanced)
    --env / -e KEY=VALUE          # Environment variable set karta hai container ke andar.
podman ps                         # Running containers ko list karta hai.
podman ps -a                      # Sabhi containers (running aur stopped) ko list karta hai.
podman stop  # Ek ya zyada running containers ko gracefully stop karta hai.
podman start  # Ek stopped container ko start karta hai.
podman restart  # Ek container ko restart karta hai.
podman rm   # Ek ya zyada stopped containers ko remove karta hai. (-f for force remove running container)
podman exec -it   # Running container ke andar koi command execute karta hai.
                                                # Bahut useful hai container ke andar shell access karne ke liye.
podman logs  # Container ke stdout/stderr logs dikhata hai. (-f for follow logs)
podman inspect  # Ek container ki detailed configuration dikhata hai.
podman commit   # Running container ki current state se ek naya image banata hai. (RHCE level)
podman container prune            # Sabhi stopped containers ko remove karta hai.

# Volume Management
podman volume create   # Ek naya named volume create karta hai.
podman volume ls                    # Sabhi named volumes ko list karta hai.
podman volume inspect  # Ek specific volume ki detailed information dikhata hai.
podman volume rm       # Ek ya zyada volumes ko remove karta hai.
podman volume prune                 # Unused (dangling) volumes ko remove karta hai.

# System-wide Cleanup
podman system prune               # Sabhi unused containers, images, networks, aur volumes ko remove karta hai. Use with caution!
```

---

### 3. Step-by-Step Examples (Hinglish)

Chaliye ab in commands ko real-world scenarios mein use karna seekhte hain.

**Scenario 1: Ek Simple Nginx Web Server Container Run Karna**

Hum ek Nginx web server container run karenge aur use apne host machine se access karenge.

**Step 1: Nginx Image Search aur Pull Karna**
Pehle dekhenge Nginx image available hai ya nahi aur fir usko download karenge.

```bash
# Image search karo (optional, but good practice)
podman search nginx

# Nginx image pull karo Docker Hub se (ya Red Hat Registry se agar UBI based chahiye)
# Docker Hub se pull karte waqt 'docker.io/library/' prefix optional hai, Podman use khud resolve kar leta hai.
podman pull nginx:latest
```
Output kuch aisa dikhega:
```
Trying to pull docker.io/library/nginx:latest...
Getting image source signatures
Copying blob d95801c40b79 done
...
Storing signatures
```
Ab `podman images` se verify kar sakte hain ki image download ho gayi hai.
```bash
podman images
```
Output:
```
REPOSITORY                 TAG         IMAGE ID      CREATED      SIZE
docker.io/library/nginx    latest      e5a065f0535e  4 days ago   143 MB
```

**Step 2: Nginx Container Run Karna**
Ab hum Nginx container ko run karenge aur uske port 80 ko host ke port 8080 par map karenge.

```bash
# Container ko detached mode (-d) mein run karo, ek naam do (--name mynginx),
# aur host port 8080 ko container port 80 se map karo (-p 8080:80).
podman run -d --name mynginx -p 8080:80 nginx:latest
```
Output:
```
5f4d3c2e1b... (yeh container ID hai)
```

**Step 3: Verify Karna aur Access Karna**
Check karo ki container running hai ya nahi.

```bash
podman ps
```
Output:
```
CONTAINER ID  IMAGE                           COMMAND               CREATED        STATUS            PORTS                 NAMES
5f4d3c2e1b    docker.io/library/nginx:latest  /docker-entrypoint.sh ... 5 seconds ago Up 3 seconds ago  0.0.0.0:8080->80/tcp  mynginx
```
Ab apne web browser mein `http://localhost:8080` (ya `http://:8080`) par navigate karo. Aapko Nginx ka default "Welcome to nginx!" page dikhna chahiye.

**Step 4: Container Logs Dekhna**
Container ke logs dekhne ke liye:

```bash
podman logs mynginx
```
Aapko Nginx ke access logs aur error logs dikhenge.

**Step 5: Container ke Andar Jaana (`exec`)**
Agar aapko running container ke andar koi command run karni hai, ya uska shell access karna hai:

```bash
podman exec -it mynginx bash
```
Ab aap container ke andar ho, jahan aap `ls`, `pwd`, `cat /etc/nginx/nginx.conf` jaise commands run kar sakte ho. Exit karne ke liye `exit` type karo.

**Step 6: Container Stop aur Remove Karna**
Jab kaam ho jaaye, container ko stop aur remove kar do.

```bash
podman stop mynginx
podman rm mynginx
```
`podman ps -a` se verify kar sakte ho ki container ab nahi hai.

---

**Scenario 2: Data Persistence with Named Volumes (Nginx Custom Page)**

Ab hum ek named volume ka use karke Nginx container mein custom content serve karenge.

**Step 1: Ek Named Volume Create Karna**
Pehle ek volume banayenge jahan hum apni custom HTML file rakhenge.

```bash
podman volume create mynginx_data
```
Verify karo volume create ho gaya hai:
```bash
podman volume ls
```
Output:
```
DRIVER      VOLUME NAME
local       mynginx_data
```

**Step 2: Volume ke Andar Content Dalna**
Ab is volume mein custom `index.html` file banani hai. Hum `podman mount` aur `podman unmount` ka use karke volume ko temporary host system par mount kar sakte hain.

```bash
# Volume ko temporary mount karo host system par
sudo podman mount mynginx_data
```
Output mein ek path dikhega, jaise `/var/lib/containers/storage/volumes/mynginx_data/_data`. Is path ko copy kar lo.

```bash
# Ab is path par jao aur apni custom index.html file create karo
# Replace  with the path you got from 'podman mount'
sudo sh -c "echo '
Welcome to My Custom Nginx Page from Podman Volume!
' > /index.html"
```
**Important Note for Rootless Podman:** Agar aap rootless Podman use kar rahe ho, `podman mount` command normally without `sudo` chalegi, aur mount path aapke user ke home directory mein hoga (e.g., `~/.local/share/containers/storage/volumes/mynginx_data/_data`).
Agar `podman mount` rootless mein kaam na kare (kuch systems par), toh aap bind mount ka bhi use kar sakte ho for this step, ya fir `podman run -v mynginx_data:/temp_volume ...` karke container ke andar se `echo` kar sakte ho. For simplicity, we are using `podman mount` here.

```bash
# Volume ko unmount karo (bahut important hai!)
sudo podman unmount mynginx_data
```

**Step 3: Nginx Container Run Karna Volume ke Saath**
Ab container ko run karo, aur `mynginx_data` volume ko Nginx ke default web root directory `/usr/share/nginx/html` par mount karo.

```bash
podman run -d --name mynginx_persistent -p 8081:80 \
    -v mynginx_data:/usr/share/nginx/html \
    nginx:latest
```
Yahan humne host port 8081 use kiya hai, taaki agar 8080 already busy ho, toh conflict na ho.

**Step 4: Verify Custom Content**
Ab `http://localhost:8081` (ya `http://:8081`) par browse karo. Aapko apni custom message "Welcome to My Custom Nginx Page from Podman Volume!" dikhni chahiye.

**Step 5: Container aur Volume Cleanup**

```bash
podman stop mynginx_persistent
podman rm mynginx_persistent

# Volume ko remove karna
podman volume rm mynginx_data
```
**Pro Tip:** `podman rm` container ko remove karta hai, volume ko nahi. Volume ko alag se `podman volume rm` se delete karna padta hai.

---

### 4. Practice Tasks (Hinglish)

Ye kuch practical exercises hain jo aap try kar sakte ho apni understanding ko improve karne ke liye:

1.  **Apache HTTPD Server Run Karo:**
    *   `httpd` (Apache) image pull karo.
    *   Ek container run karo jahan `httpd` host ke port 8080 par accessible ho.
    *   `httpd` server ke logs check karo.
    *   Container ko stop aur remove karo.

2.  **CentOS/Fedora Container Explore Karo:**
    *   `podman pull ubi8/ubi` (Red Hat Universal Base Image) ya `podman pull centos` (CentOS Stream) ya `podman pull fedora` karo.
    *   `podman run -it --name myos  bash` command se container ke andar ek interactive shell access karo.
    *   Container ke andar `ls /`, `df -h`, `cat /etc/os-release` jaise commands run karke uska environment explore karo.
    *   Container se `exit` karo.
    *   Container ko remove karo.

3.  **MariaDB (MySQL) Database Container with Persistent Data:**
    *   `mariadb` image pull karo.
    *   Ek named volume create karo `mariadb_data` naam se.
    *   `mariadb` container ko run karo. Environment variable `MYSQL_ROOT_PASSWORD` set karna mat bhoolna (`-e MYSQL_ROOT_PASSWORD=mysecretpassword`).
    *   `mariadb_data` volume ko container ke `/var/lib/mysql` directory par mount karo.
    *   Container ko background (`-d`) mein run karo.
    *   `podman ps` se verify karo ki container running hai.
    *   Container ko stop karo aur remove karo (par volume ko remove mat karna abhi).
    *   Ab same command se ek naya `mariadb` container run karo, using the *same* `mariadb_data` volume.
    *   Check karo ki purana data abhi bhi available hai ya nahi (normally database ko restart karne par data lost nahi hota).
    *   Jab kaam ho jaaye, dono container aur volume ko clean up karo.

4.  **Podman Cleanup:**
    *   Sabhi running containers ko stop karo.
    *   Sabhi stopped containers ko remove karo.
    *   Sabhi unused images ko remove karo.
    *   Sabhi unused volumes ko remove karo. (Hint: `podman system prune` could be useful, but understand its impact first).

---

### 5. Pro Tips / Common Mistakes (Hinglish)

Kuch important tips aur common mistakes jinse aap bach sakte ho:

**Pro Tips:**

*   **Rootless Podman ko Prefer Karo:** Always try to run Podman as a non-root user (`podman` vs `sudo podman`). Yeh security ke liye best practice hai. Agar `sudo podman` use kar rahe ho, toh `podman images` aur `podman ps` ki output `sudo podman images` aur `sudo podman ps` se different hogi, kyuki woh root user ke containers aur images dikhayega.
*   **Systemd Integration (RHCE Focus):** RHCE mein `podman generate systemd` command bahut important hai. Isse aap running container ke liye ek systemd service unit file generate kar sakte ho. Fir aap `systemctl enable --now ` se container ko system boot par automatically start kar sakte ho aur `systemctl` commands se manage kar sakte ho.
*   **Image Tagging:** Images pull karte waqt `nginx:latest` jaise tags use karna theek hai, par production environments mein specific version tags (`nginx:1.21.4`) use karna better hota hai, taaki unexpected updates se bacha ja sake.
*   **Networking:** Podman default bridge network use karta hai. `podman run -p` port forwarding ke liye hai. Advanced networking ke liye `podman network create` aur `podman network connect` explore kar sakte ho.
*   **`podman inspect`:** Yeh command ek container ya image ki detailed configuration JSON format mein dikhata hai. Debugging aur advanced setups ke liye bahut useful hai.
*   **Resource Limits:** RHCE mein aapko container ke CPU aur memory usage ko limit karna bhi aana chahiye (`--cpu-shares`, `--memory`).

**Common Mistakes aur Unse Kaise Bachein:**

*   **Container Stop/Remove Karna Bhool Jaana:** Naye users aksar containers run karke unhe stop ya remove karna bhool jaate hain. Isse system resources waste hote hain. Regularly `podman ps -a` check karo aur unneeded containers ko `podman rm` karo. `podman run --rm` use karna agar container ka kaam khatam hone par use automatically remove karna ho.
*   **Data Loss due to No Volumes:** Agar aapne `podman run` karte waqt volume attach nahi kiya aur container ko remove kar diya, toh container ke andar ka saara data lost ho jaayega. Persistent data ke liye hamesha volumes use karo.
*   **Port Conflicts:** Agar aap `podman run -p 8080:80` karte ho aur host par already port 8080 koi aur service use kar rahi hai, toh container start nahi hoga. Error message par dhyaan do (`Address already in use`). Host par ek available port choose karo (jaise `8081`, `8000`, etc.).
*   **Incorrect Volume Paths:** Bind mounts (`-v /host/path:/container/path`) mein agar `/host/path` exist nahi karta ya `/container/path` galat hai, toh container ke andar data access nahi hoga. Always double-check paths. Named volumes mein yeh problem kam hoti hai kyuki Podman path khud manage karta hai.
*   **Running `podman` as root and non-root interchangeably:** Jaise upar bataya, root aur non-root users ke liye Podman ka apna separate storage hota hai. Agar aapne `sudo podman pull nginx` kiya hai, toh `nginx` image sirf root user ke Podman storage mein hogi. Non-root user `podman images` karega toh use `nginx` image nahi dikhegi. Image ko non-root user ke liye bhi pull karna padega.
*   **Not Cleaning Up Dangling Resources:** `podman images -f dangling=true` aur `podman volume ls -f dangling=true` se unused images aur volumes ko identify kar sakte ho. `podman system prune` ek strong command hai, but use carefully, especially in production.

---

Umeed hai ki yeh comprehensive lesson aapko Podman, containers, images aur volumes ki basic concepts ko samajhne mein helpful raha hoga. Practise karte rahiye, kyuki "Practice makes perfect"! All the best for your RHCSA/RHCE journey! Jai Hind!
