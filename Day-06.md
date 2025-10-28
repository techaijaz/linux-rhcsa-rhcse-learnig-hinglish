Day 6: System documentation & basic troubleshooting (whoami, id, uptime)
---
Namaste students! Main aapka Linux instructor hoon, aur aaj hum ek bohot hi important topic pe baat karenge jo aapke RHCSA aur RHCE dono exams ke liye crucial hai – "System Documentation & Basic Troubleshooting". Hum kuch fundamental commands ko samjhenge jo aapko system ki health aur user information janne mein help karenge. Chaliye shuru karte hain!

---

## RHCSA + RHCE Training Lesson: System Documentation & Basic Troubleshooting (whoami, id, uptime)

### 1. Concept Explanation (Hinglish)

System documentation aur basic troubleshooting, ye do pillars hain ek successful Linux administrator ke liye.

*   **System Documentation:** Socho, aap ek nayi system set up karte ho ya existing system ko manage kar rahe ho. Agar aapko uski details (kaun logg-in hai, system kab se chal rahi hai, kitna load hai) pata na ho, toh kaam karna mushkil ho jayega. Documentation ka matlab hai, system ke baare mein important information collect karna aur use record karna. Ye aapko future troubleshooting, configuration changes, ya security audits mein bohot help karta hai. Jaise ek doctor patient ki history record karta hai, waise hi ek admin system ki details record karta hai. Ye aapko "kya hua tha?" aur "kya ho raha hai?" ka jawab dene mein madad karta hai.

*   **Basic Troubleshooting:** Jab bhi koi problem aati hai, hum seedhe advanced tools pe nahi jaate. Pehle kuch basic checks karte hain. Jaise car start nahi ho rahi toh pehle petrol check karte hain na? Waise hi Linux mein bhi, kuch simple commands hain jo aapko system ki current state aur environment ke baare mein quick insights dete hain. Ye first line of defense hai. Aaj hum jin commands ki baat karenge (`whoami`, `id`, `uptime`) wo isi basic troubleshooting ka hissa hain. Ye commands aapko initial context dete hain ki system kis haal mein hai aur kaun kya kar raha hai.

In short, "system documentation" is about knowing your system inside out, and "basic troubleshooting" is about quickly diagnosing initial issues with simple checks.

### 2. Key Linux Commands (Hinglish)

Chaliye, ab dekhte hain kuch bohot hi useful commands aur unka matlab:

#### `whoami`

*   **Kya karta hai?** Yeh command aapko batata hai ki aap currently kis user ke roop mein logged in ho aur commands execute kar rahe ho. `whoami` ka matlab hai "who am I?"
*   **Kyun useful hai?** Kabhi-kabhi, khaaskar jab aap `sudo` ya `su` commands use karte ho, aapko confusion ho sakti hai ki aap kis user account mein ho. Permissions aur access issues ko troubleshoot karte waqt ye command bohot kaam aata hai.

#### `id`

*   **Kya karta hai?** `id` command aapko current user ki ya kisi specified user ki detailed identity information dikhata hai. Isme user ID (UID), primary group ID (GID), aur saare supplementary groups jinka user member hai, shamil hote hain.
*   **Kyun useful hai?** Permissions bohot important hoti hain Linux mein. Agar koi file ya directory access nahi ho rahi, toh `id` command se aap check kar sakte ho ki us user ke paas necessary group memberships hain ya nahi. Ye security aur access control troubleshooting mein critical hai.

#### `uptime`

*   **Kya karta hai?** `uptime` command system ki kitni der se chal rahi hai (uptime), kitne users logged in hain, aur system ka load average dikhata hai.
*   **Kyun useful hai?**
    *   **Uptime:** Aapko pata chalta hai ki system kab reboot hui thi, ya kitni stable hai (agar bohot kam uptime hai, matlab baar-baar reboot ho rahi hai system).
    *   **Users:** Kitne users current system pe active hain, iska quick count mil jata hai.
    *   **Load Average:** Ye ek bohot important metric hai. Yeh batata hai ki pichle 1, 5, aur 15 minutes mein system pe kitna load tha. Load average basically "number of processes waiting for CPU or disk I/O" hota hai. High load average matlab system pe pressure zyada hai, aur low matlab system theek chal rahi hai. Ye performance aur resource monitoring ke liye critical hai.

#### Bonus: `man` & `info`

*   **Kya karte hain?** Ye commands Linux ke built-in documentation tools hain. Har command, utility, aur configuration file ke baare mein in-depth information provide karte hain.
*   **Kyun useful hain?** Jab bhi aapko kisi command ke options ya usage ke baare mein confusion ho, `man ` ya `info ` type karke aap details dekh sakte ho. RHCSA aur RHCE mein `man` pages ka use karna bohot common hai.

### 3. Step-by-Step Examples (Hinglish)

Chaliye, ab in commands ko practically dekhte hain.

**Scenario 1: Ek naya user apni identity check kar raha hai.**

Maana ki aapne abhi-abhi ek nayi user account banaya hai ya kisi server pe login kiya hai aur aap confirm karna chahte ho ki aap kaun se user ho aur aapke paas kaunsi permissions hain.

**Step 1: Check your current username.**

```bash
whoami
```
**Expected Output:**
```
rheluser
```
**Explanation:** `whoami` ne aapko bataya ki aap `rheluser` ke roop mein kaam kar rahe ho. Ye sabse pehla confirmation hai.

**Step 2: Check detailed user ID and group memberships.**

```bash
id
```
**Expected Output:**
```
uid=1000(rheluser) gid=1000(rheluser) groups=1000(rheluser),10(wheel),27(sudo)
```
**Explanation:**
*   `uid=1000(rheluser)`: Aapki User ID (UID) 1000 hai, aur username `rheluser` hai. Har user ka ek unique UID hota hai.
*   `gid=1000(rheluser)`: Aapki Primary Group ID (GID) 1000 hai, aur group name bhi `rheluser` hai (by default, user create hone pe uska primary group usi naam ka banta hai).
*   `groups=1000(rheluser),10(wheel),27(sudo)`: Aap `rheluser` ke alawa `wheel` aur `sudo` groups ke bhi member ho. `wheel` group typically systems mein administrative privileges provide karta hai (like using `sudo`). Ye bohot important information hai, khaaskar jab aapko pata karna ho ki aapko `sudo` access hai ya nahi.

**Scenario 2: System Administrator system health check kar raha hai.**

Aap ek server ke admin ho aur aapko jaldi se system ki overall health aur activity check karni hai.

**Step 1: Check system uptime and load average.**

```bash
uptime
```
**Expected Output:**
```
 10:30:05 up 2 days, 14:22,  3 users,  load average: 0.05, 0.10, 0.12
```
**Explanation:**
*   `10:30:05`: Current system time.
*   `up 2 days, 14:22`: System 2 din 14 ghante 22 minute se chal rahi hai (bina reboot hue). Ye ek achhi uptime hai, matlab system stable hai.
*   `3 users`: Teen users current system pe logged in hain.
*   `load average: 0.05, 0.10, 0.12`: Yeh pichle 1 minute, 5 minutes, aur 15 minutes ka load average hai.
    *   **Interpretation (RHCE perspective):** Load average core count se compare kiya jaata hai. Agar aapke server pe 4 CPU cores hain, toh load average 4.0 tak theek maana ja sakta hai. Agar ye consistently 4.0 se upar hai, toh system pe pressure zyada hai. Yahan `0.05`, `0.10`, `0.12` bohot low values hain, matlab system pe filhaal koi khaas load nahi hai, it's idle.

**Scenario 3: Troubleshooting ek permission issue (using `id` indirectly).**

Aapka ek user `devuser` ek particular shared directory `/var/www/html` ko access nahi kar pa raha, jiska owner `apache` group hai.

**Step 1: Switch to `devuser` (ya `sudo` se run karein) aur `id` command chala ke dekhein.**

```bash
su - devuser
id
```
**Expected Output (agar access nahi mil raha):**
```
uid=1001(devuser) gid=1001(devuser) groups=1001(devuser)
```
**Explanation:** Is output mein `apache` group kahi bhi mention nahi hai. Iska matlab hai ki `devuser` `apache` group ka member nahi hai, aur isliye us shared directory ko access nahi kar pa raha jiska ownership `apache` group ke paas hai.

**Step 2: Admin `devuser` ko `apache` group mein add karega.**

```bash
sudo usermod -aG apache devuser
```
**Step 3: User ko dobara login karna hoga ya session refresh karna hoga (new groups apply hone ke liye). Phir se `id` chala ke dekhein.**

```bash
su - devuser
id
```
**Expected Output (access milne ke baad):**
```
uid=1001(devuser) gid=1001(devuser) groups=1001(devuser),48(apache)
```
**Explanation:** Ab `devuser` `apache` group ka member hai. Ab wo shared directory ko access kar payega (agar directory permissions `g+rw` hain). Ye `id` command ka practical use case hai permission troubleshooting mein.

### 4. Practice Tasks (Hinglish)

Ye kuch practice tasks hain jo aapko in commands pe hath saaf karne mein madad karenge:

1.  **Task 1: Apni Pehchaan Jaanien (Know Your Identity)**
    *   Apne Linux system pe login karein.
    *   Terminal open karein.
    *   Pehle `whoami` command run karein aur output note karein.
    *   Phir `id` command run karein aur output ko carefully analyze karein. Dekhein aapke UID, GID, aur kaun-kaun se groups mein aap member ho.

2.  **Task 2: System Ki Halat Check Karein (Check System Status)**
    *   `uptime` command run karein.
    *   Output mein se current time, system uptime, number of users, aur teeno load average values ko note karein.
    *   Load average values ka kya matlab hai, ise apni bhasha mein explain karne ki koshish karein. (Hint: kya system busy hai ya free?)

3.  **Task 3: Naye User Ki Jaanch (Inspect a New User)**
    *   `sudo adduser testuser` command se ek naya user `testuser` create karein. (`sudo passwd testuser` se password set karna na bhoolein)
    *   `sudo id testuser` command chala ke `testuser` ki identity details check karein.
    *   Ab `su - testuser` command se `testuser` mein switch karein.
    *   `testuser` ke roop mein `whoami` aur `id` commands chala ke dekhein aur compare karein jo output aapne `sudo id testuser` se dekha tha. Kya koi difference hai?

4.  **Task 4 (RHCE Level): Load Average Pe Nazar (Monitoring Load Average)**
    *   Ek terminal mein `uptime` command run karte rahein (har 5-10 seconds mein).
    *   Doosre terminal mein, ek CPU intensive process start karein. Jaise `yes > /dev/null` (Ctrl+C se rok sakte hain) ya `stress -c 1` (Agar `stress` installed nahi hai toh `sudo yum install stress` ya `sudo apt install stress` se install karein).
    *   Observe karein ki `uptime` ke output mein load average values kaise badal jaati hain. Jab process ko stop karenge, tab load average wapis kaise normal hota hai, ise note karein.

### 5. Pro Tips / Common Mistakes (Hinglish)

Yahan kuch extra tips aur aisi galtiyan hain jinse aapko bachna chahiye:

*   **Pro Tips:**
    *   **`man` Pages Ka Use Karein:** Hamesha `man ` ka use karein options aur detailed information ke liye. Jaise `man uptime` ya `man id`. Ye aapki sabse badi madadgaar hai.
    *   **Output Ko Record Karein:** Jab bhi aap troubleshooting kar rahe ho, commands ka output kisi file mein redirect kar lo (`command > output.txt`). Ye documentation ka part hai aur future reference ke liye useful hai.
    *   **Load Average Ko Sahi Samjhen:** Load average ko sirf numbers mein na dekhein. Use CPU cores ki quantity se relate karein. Agar aapke server mein 2 CPU cores hain aur load average consistently 2.0 se upar hai, toh system pe pressure hai.
    *   **`w` Command:** `uptime` se zyada detailed user information ke liye, `w` command use kar sakte hain. Ye dikhata hai kaun logged in hai, kya terminal use kar raha hai, kitni der se idle hai aur kaunsi command chala raha hai.
    *   **Context Matters:** Hamesha yaad rakhein ki `whoami`, `id`, `uptime` sirf ek snapshot dete hain. Aapko unke outputs ko current system ke context mein analyze karna hoga.

*   **Common Mistakes:**
    *   **Load Average Ko Misinterpret Karna:** Sirf high load average dekh ke ghabra na jaayen. Hamesha use system ke CPU cores ke context mein analyze karein.
    *   **User Switch Karne ke Baad `id` Check Na Karna:** Jab aap `su` ya `sudo -i` se user switch karte ho, toh apni current identity confirm karne ke liye `whoami` ya `id` chalana bhool jaate hain. Ye bohot common mistake hai jisse permission errors ho sakte hain.
    *   **`man` Pages Ko Ignore Karna:** Naye users aksar `man` pages ko ignore karte hain aur seedhe Google pe search karte hain. `man` pages authentic aur system-specific information provide karte hain.
    *   **`sudo` Ka Galat Istemal:** Jab koi command `Permission denied` error de, toh har baar `sudo` lagana solution nahi hota. Pehle `id` se apni group membership check karein, ho sakta hai aapko us group mein add hona ho.
    *   **Not Documenting Observations:** Troubleshooting ke dauraan jo observations aap karte ho, unhe note na karna. Future mein same problem aane par ya kisi aur ko explain karte waqt ye bohot mushkil ho sakta hai.

---

Mujhe ummeed hai ki yeh comprehensive lesson aapko "System Documentation & Basic Troubleshooting" ke in fundamental commands ko samajhne mein madad karega. Yaad rakhiye, practice makes perfect! In commands ko regularly use karein aur inke outputs ko analyze karna seekhein. All the best for your RHCSA and RHCE journey!
