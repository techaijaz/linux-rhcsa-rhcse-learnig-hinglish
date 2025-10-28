Day 10: Access Control Lists (ACLs)
---
वाह! Access Control Lists (ACLs) Linux permissions ka ek bahut hi powerful feature hai, jo standard UGO (User, Group, Other) permissions ki limitations ko overcome karta hai. Chalo, ise Hinglish mein detail mein samajhte hain, RHCSA aur RHCE dono levels ko cover karte hue.

---

## RHCSA + RHCE Training Lesson: Access Control Lists (ACLs)

**Topic:** Access Control Lists (ACLs) - Advanced File Permissions
**Level:** RHCSA & RHCE
**Language:** Hinglish

### Table of Contents

1.  **Introduction: Why ACLs?** (ACLs ki zaroorat kyun?)
2.  **Prerequisites/Quick Recap** (Zaroori baatein)
3.  **RHCSA Level: Understanding & Implementing Basic ACLs**
    *   3.1. What are ACLs? (ACLs kya hain?)
    *   3.2. Checking ACL Support (Filesystem check)
    *   3.3. Viewing ACLs: `getfacl`
    *   3.4. Setting ACLs: `setfacl` - User, Group, Mask
    *   3.5. Understanding the `mask` (Mask ko samajhna)
    *   3.6. Default ACLs for Directories (Directories ke liye default ACLs)
    *   3.7. Removing ACLs (`setfacl -x` & `setfacl -b`)
    *   3.8. Backing Up and Restoring ACLs
4.  **RHCE Level: Advanced ACLs & Troubleshooting**
    *   4.1. ACL Precedence (ACLs ki priority)
    *   4.2. Complex Default ACL Scenarios (Advanced inheritance)
    *   4.3. Troubleshooting ACL Issues (Mask, Default ACLs)
    *   4.4. ACLs with Special Permissions (SUID, SGID, Sticky Bit)
    *   4.5. Best Practices & Security Considerations
5.  **Practice Tasks**
    *   5.1. RHCSA Practice Tasks
    *   5.2. RHCE Practice Tasks
6.  **Summary & Key Takeaways**

---

### 1. Introduction: Why ACLs? (ACLs ki zaroorat kyun?)

Standard Linux permissions (User, Group, Other - UGO) bahut simple aur effective hain. Aap ek file ke owner ko, uske primary group ko, aur baki sabko permissions de sakte hain. Lekin, kabhi-kabhi yeh kaafi nahi hota.

**Example Scenario:**
Aapka ek `project_data` directory hai.
*   `john` (owner) ko full access chahiye.
*   `developers` group ke members ko read/write access chahiye.
*   `qa_team` group ke members ko sirf read access chahiye.
*   `mary` naam ki user ko bhi read/write access chahiye, jo `developers` group mein nahi hai.
*   Baaki sabko koi access nahi chahiye.

Standard UGO permissions ke saath, aap `john` ko, `developers` group ko, aur `other` ko permissions de sakte hain. Lekin `qa_team` aur `mary` ko specifically target karna mushkil ho jata hai. Ya toh `other` mein sabko read access de do (jo security risk hai) ya `mary` ko `developers` group mein add karo (jo uske role ke khilaf ho sakta hai).

**Solution:** Yahin par **Access Control Lists (ACLs)** kaam aate hain! ACLs aapko specific users ya specific groups ko file ya directory par additional permissions dene ki suvidha dete hain, UGO permissions ke upar. Iska matlab hai, aap `mary` ko `developers` group mein add kiye bina usko read/write access de sakte hain, aur `qa_team` group ko read-only access de sakte hain.

---

### 2. Prerequisites/Quick Recap (Zaroori baatein)

Is lesson ko shuru karne se pehle, aapko standard Linux file permissions (rwx, octal modes), `chmod`, `chown`, `chgrp`, aur `umask` ki basic understanding honi chahiye.

*   `r` (read): 4
*   `w` (write): 2
*   `x` (execute): 1
*   `ls -l` output ko samajhna.
*   `umask` ka concept (new files/directories par default permissions kaise apply hoti hain).

---

### 3. RHCSA Level: Understanding & Implementing Basic ACLs

#### 3.1. What are ACLs? (ACLs kya hain?)

ACLs ek fine-grained permission mechanism hai jo aapko standard UGO permissions ke alawa, individual users aur groups ke liye extra permissions define karne ki facility deta hai. Jab `ls -l` mein aapko permissions ke baad ek `+` sign dikhe, toh samajh lena ki us file/directory par ACLs apply ho rahe hain.

**Example:**
```bash
$ ls -l my_file.txt
-rw-rw-r--+ 1 user1 group1 0 Jan  1 10:00 my_file.txt
             ^
             | -- This '+' indicates ACLs are present.
```

#### 3.2. Checking ACL Support (Filesystem check)

Modern Linux filesystems jaise `ext4` aur `XFS` default roop se ACLs support karte hain. Puraane systems ya custom configurations mein `fstab` entry mein `acl` option add karna pad sakta tha, lekin ab yeh usually automatically enabled hota hai.

**How to check:**
`mount` command ki output mein dekh sakte hain ki `acl` option enable hai ya nahi:
```bash
$ mount | grep " / "
/dev/mapper/rhel-root on / type xfs (rw,relatime,seclabel,attr2,inode64,logbufs=8,logbsize=32k,noquota)
                                                              ^
                                                              |-- ACL is typically supported by default.
                                                                  You won't always see 'acl' explicitly listed,
                                                                  especially with modern filesystems where it's implicit.
```
Agar `acl` option explicitly dikhta hai, toh theek hai. Agar nahi bhi dikhta, toh bhi `ext4` aur `XFS` mein ACLs by default enable hote hain.

#### 3.3. Viewing ACLs: `getfacl`

Kisi file ya directory par applied ACLs ko dekhne ke liye `getfacl` command use karte hain.

**Syntax:** `getfacl `

**Example:**
```bash
# Ek file banate hain
$ touch test_acl_file.txt
$ ls -l test_acl_file.txt
-rw-rw-r--. 1 user1 user1 0 Jan  1 10:00 test_acl_file.txt

# Iski default ACLs dekhte hain
$ getfacl test_acl_file.txt
# file: test_acl_file.txt
# owner: user1
# group: user1
user::rw-              # Owner (user1) ko rw- (read, write)
group::rw-             # Owning group (user1) ko rw- (read, write)
other::r--             # Others ko r-- (read)
```

**`getfacl` output explanation:**
*   `# file: `: Jis file/directory ki ACLs dekh rahe hain.
*   `# owner: `: File ka owner.
*   `# group: `: File ka primary group.
*   `user::permissions`: File owner ki permissions (same as UGO's 'User' part).
*   `group::permissions`: Owning group ki permissions (same as UGO's 'Group' part).
*   `other::permissions`: Others ki permissions (same as UGO's 'Other' part).
*   `user::permissions`: Specific user ke liye permission entry.
*   `group::permissions`: Specific group ke liye permission entry.
*   `mask::permissions`: Yeh sabse important hai! Yeh maximum effective permissions define karta hai jo named users, named groups, aur owning group ko mil sakti hain (details aage).
*   `default:user::permissions`, `default:group::permissions`, etc.: Directories ke liye default ACLs, jo naye banne wale files/directories ko inherit hoti hain.

#### 3.4. Setting ACLs: `setfacl` - User, Group, Mask

`setfacl` command ka use ACL entries add, modify, ya remove karne ke liye hota hai.

**Common Options:**
*   `-m` (modify): Existing ACL entries ko modify karne ya nayi add karne ke liye.
*   `-x` (remove): Specific ACL entry ko remove karne ke liye.
*   `-b` (remove all): Saari ACL entries ko remove karne ke liye.
*   `-R` (recursive): Directories aur uske contents par recursively apply karne ke liye.
*   `-d` (default): Directory par default ACL entry set karne ke liye.

**3.4.1. Adding Permissions for a Specific User:**

Let's say `user2` ko `test_acl_file.txt` par read/write (`rw`) access chahiye.

```bash
# Agar user2 exist nahi karta, toh create kar lo:
# sudo useradd user2
# sudo passwd user2

$ setfacl -m u:user2:rw test_acl_file.txt
```
Ab check karo `getfacl` se:
```bash
$ getfacl test_acl_file.txt
# file: test_acl_file.txt
# owner: user1
# group: user1
user::rw-
user:user2:rw-         # User2 ko rw- access mil gaya
group::r--             # Dhyan do! Owning group ki permission kam ho gayi
                       # aur ek mask entry automatically add ho gayi hai.
mask::rw-              # Mask (explained next)
other::r--
```
Aur `ls -l` se bhi dekho:
```bash
$ ls -l test_acl_file.txt
-rw-rw-r--+ 1 user1 user1 0 Jan  1 10:00 test_acl_file.txt
           ^
           |-- '+' sign confirms ACLs are present.
```

**Important Note:** Jab aap pehli baar kisi file par ACL add karte hain, toh `setfacl` automaticlly ek `mask` entry create karta hai. Ye mask UGO permissions ke group field (`group::`) se apni initial value leta hai, aur jo effective permissions `user:user2:rw-` ke liye dikh rahi hain, woh is mask se limited hoti hain.

**3.4.2. Adding Permissions for a Specific Group:**

Suppose `dev_group` ko `test_acl_file.txt` par read-only (`r`) access chahiye. (Pehle `dev_group` bana lo, agar nahi hai toh: `sudo groupadd dev_group`)

```bash
$ setfacl -m g:dev_group:r test_acl_file.txt
$ getfacl test_acl_file.txt
# file: test_acl_file.txt
# owner: user1
# group: user1
user::rw-
user:user2:rw-
group::r--
group:dev_group:r--    # dev_group ko r-- access
mask::rw-
other::r--
```

#### 3.5. Understanding the `mask` (Mask ko samajhna)

`mask` entry ACLs ka ek bahut hi crucial component hai.
*   **Purpose:** `mask` defines the maximum permissions that can be granted to *named users*, *named groups*, and the *owning group* (`group::`).
*   **Effective Permissions:** Kisi bhi named user, named group, ya owning group ki final permissions (`effective` permissions) uski apni permission aur `mask` permission ka intersection (AND operation) hoti hain.
    *   `effective_perm = actual_perm AND mask_perm`
*   **Default Behavior:** Jab aap `setfacl -m` se pehli baar koi additional entry add karte hain, toh `setfacl` automatically `mask` ko owning group (`group::`) ki permissions par set kar deta hai.

**Example:**
Earlier, we had:
```
user::rw-
user:user2:rw-
group::r--             # Owning group's permission
group:dev_group:r--
mask::rw-              # Current mask
other::r--
```
*   `user2` ke liye: Requested `rw-`, Mask `rw-`. Effective `rw-`.
*   `dev_group` ke liye: Requested `r--`, Mask `rw-`. Effective `r--`.
*   `owning group` (`user1`) ke liye: Original `rw-`. Mask `rw-`. Effective `rw-`.

Ab suppose, aap chahte hain ki `user2` ko sirf read access mile, chahe uski entry `rw-` ho. Aap `mask` ko change kar sakte hain:

```bash
$ setfacl -m m:r test_acl_file.txt
$ getfacl test_acl_file.txt
# file: test_acl_file.txt
# owner: user1
# group: user1
user::rw-
user:user2:rw-         # Actual permission
group::r--
group:dev_group:r--
mask::r--              # Mask changed to r--
other::r--

# Notice the effective permissions (getfacl output mein directly nahi dikhti,
# lekin logic aise kaam karta hai):
# user:user2: effective: r-- (rw- AND r--)
# group::     effective: r-- (r-- AND r--)
# group:dev_group: effective: r-- (r-- AND r--)
```
Iska matlab hai, `user2` ko `rw-` permission hote hue bhi, mask `r--` hone ki wajah se, usko effective read-only access milega.

**`mask` aur `group::` permission ka relation:**
`ls -l` command output mein jo group permission dikhti hai (`-rw-**r**--+`), woh `mask` ki permission hoti hai, `group::` ki actual permission nahi. Yeh confusing ho sakta hai, isliye hamesha `getfacl` hi final truth batata hai.

#### 3.6. Default ACLs for Directories (Directories ke liye default ACLs)

Default ACLs bahut useful hain jab aap chahte hain ki ek directory mein nayi banne wali files aur sub-directories automatically specific ACLs inherit karein. Yeh sirf directories par apply hote hain.

**Syntax:** `setfacl -m d:u:username:perm directory_name` (for default user ACL)
`setfacl -m d:g:groupname:perm directory_name` (for default group ACL)
`setfacl -m d:m:perm directory_name` (for default mask ACL)

**Example:** Ek shared directory banate hain `shared_proj`. Hum chahte hain ki `user2` ismein nayi files create kar sake, aur `dev_group` ke members bhi.

```bash
$ mkdir shared_proj
$ chmod 770 shared_proj # Owner aur Owning group ko full access
$ setfacl -m d:u:user2:rw,d:g:dev_group:rw,d:m:rwx shared_proj
# Explanation:
# d:u:user2:rw    -> Nayi files ko user2 ke liye rw
# d:g:dev_group:rw -> Nayi files ko dev_group ke liye rw
# d:m:rwx         -> Default mask bhi set kar diya

$ getfacl shared_proj
# file: shared_proj
# owner: user1
# group: user1
user::rwx
group::rwx
other::---
default:user::rwx
default:user:user2:rw-
default:group::rwx
default:group:dev_group:rw-
default:mask::rwx
default:other::---
```
Ab, `shared_proj` directory ke andar kuch create karo (as `user1` or `user2`):

```bash
$ cd shared_proj
$ touch new_file_by_user1.txt
$ mkdir new_dir_by_user1

$ getfacl new_file_by_user1.txt
# file: new_file_by_user1.txt
# owner: user1
# group: user1
user::rw-
user:user2:rw-         # Inherited from default ACLs
group::rwx             # Inherited owning group permission (rwx, then modified by mask)
group:dev_group:rw-    # Inherited from default ACLs
mask::rwx              # Inherited from default ACLs
other::---

$ getfacl new_dir_by_user1
# file: new_dir_by_user1
# owner: user1
# group: user1
user::rwx
user:user2:rw-
group::rwx
group:dev_group:rw-
mask::rwx
other::---
default:user::rwx      # Default ACLs for new_dir_by_user1 itself
default:user:user2:rw-
default:group::rwx
default:group:dev_group:rw-
default:mask::rwx
default:other::---
```
**Important:** Jab default ACLs inherit hote hain, toh file permissions umask aur default ACLs ke beech ke intersection se decide hoti hain. Directories ke liye, default ACLs khud bhi inherit hote hain.

#### 3.7. Removing ACLs (`setfacl -x` & `setfacl -b`)

*   **Removing a specific entry:** `setfacl -x u:user2 test_acl_file.txt` (removes `user2`'s entry)
*   **Removing all entries:** `setfacl -b test_acl_file.txt` (removes all ACLs including `mask`, aur `+` sign bhi hat jayega `ls -l` se).

**Example:**
```bash
$ setfacl -x u:user2 test_acl_file.txt
$ getfacl test_acl_file.txt
# user:user2:rw- entry will be gone, and mask will adjust.

$ setfacl -b test_acl_file.txt
$ getfacl test_acl_file.txt
# Only owner, group, other entries will remain. No mask, no specific user/group entries.
# ls -l will no longer show the '+' sign.
```

#### 3.8. Backing Up and Restoring ACLs

ACLs ko backup aur restore karna important hai, especially migration ya disaster recovery scenarios mein.

**Backup:**
```bash
# Ek directory aur uske andar ki files/dirs ke ACLs ko backup karna:
$ getfacl -R /path/to/directory > acls_backup.txt

# Example:
$ getfacl -R shared_proj > shared_proj_acls.txt
```

**Restore:**
```bash
# ACLs ko restore karna:
$ setfacl --restore=acls_backup.txt

# Example:
$ setfacl --restore=shared_proj_acls.txt
```
**Note:** `cp -a` command bhi ACLs ko preserve karta hai jab files/directories copy ki jaati hain.

---

### 4. RHCE Level: Advanced ACLs & Troubleshooting

RHCE exam mein ACLs ke complex scenarios, troubleshooting aur unki underlying working ko samajhna zyada zaroori hota hai.

#### 4.1. ACL Precedence (ACLs ki priority)

Jab ek user kisi file ko access karne ki koshish karta hai, toh Linux kernel permissions ko is order mein check karta hai:

1.  **Owner ACL entry (`user::` or `user:username:`):**
    *   Agar user file ka owner hai, toh `user::` permission check hoti hai.
    *   Agar user ke liye koi specific `user:username:` entry hai, toh woh check hoti hai. (The most specific entry wins *among named entries*).
    *   **Prioritization:** Specific `user:username` entry has precedence over `user::` if the user is *not* the owner but has a specific entry. If the user *is* the owner, then `user::` is the primary, but `user:username:` can *still* exist for the owner (though it's redundant and usually `user::` takes effect). *Correction:* If a user has *both* `user::` (as owner) and `user:username:` (as a named user), the `user::` entry for the owner takes precedence. If the user is *not* the owner, but has a `user:username:` entry, that entry is used.

2.  **Named User ACL entry (`user:username:`):** Agar user file ka owner nahi hai, lekin uske liye ek specific `user:username:` entry hai, toh woh check hoti hai. Is entry ki permissions `mask` se limited hoti hain.

3.  **Named Group ACL entry (`group:groupname:`):** Agar user kisi aise group ka member hai jiske liye specific `group:groupname:` entry hai, toh us group ki entry check hoti hai. Ek user multiple groups ka member ho sakta hai, toh jis group ki entry sabse broad access deti hai (aur mask se limited hoti hai) woh apply hoti hai. Is entry ki permissions bhi `mask` se limited hoti hain.

4.  **Owning Group ACL entry (`group::`):** Agar user file ka owner nahi hai, uske liye koi specific `user:username:` entry nahi hai, aur woh kisi named group entry ka member nahi hai, lekin woh file ke owning group (`group::`) ka member hai, toh yeh entry check hoti hai. Is entry ki permissions bhi `mask` se limited hoti hain.

5.  **Others ACL entry (`other::`):** Agar upar mein se koi bhi match nahi karta (yani user na owner hai, na specific user/group mein hai, na owning group mein hai), toh `other::` entry apply hoti hai. `other::` entry `mask` se affect nahi hoti.

**Rule of Thumb:**
*   **Most Specific Wins:** Agar user directly match karta hai (as owner, or specific user), toh woh ACL apply hogi.
*   **Mask Limits:** Named user, named group, aur owning group ki permissions hamesha `mask` se limited hoti hain. `other` ko `mask` affect nahi karta.

#### 4.2. Complex Default ACL Scenarios (Advanced inheritance)

RHCE level par aapko aise scenarios mil sakte hain jahan multiple users aur groups ke liye default ACLs set karni padti hain aur unka behavior samajhna hota hai.

**Scenario:** Ek `shared_docs` directory hai.
*   `admin_user` (owner) ko full access.
*   `content_editors` group ko read/write access.
*   `reviewers` group ko read-only access.
*   Nayi files jo is directory mein banti hain, un par bhi yahi rules apply hone chahiye.

```bash
# 1. Directory banao
$ sudo mkdir /shared_docs
$ sudo chown admin_user:admin_group /shared_docs # admin_group bhi bana lo

# 2. Users aur Groups banao
# sudo useradd editor1
# sudo useradd reviewer1
# sudo groupadd content_editors
# sudo groupadd reviewers
# sudo usermod -aG content_editors editor1
# sudo usermod -aG reviewers reviewer1

# 3. Default ACLs set karo
$ sudo setfacl -m u:admin_user:rwx /shared_docs  # Owner permissions, though it's already there
$ sudo setfacl -m g:content_editors:rw,g:reviewers:r /shared_docs

$ sudo setfacl -m d:u:admin_user:rwx,d:g:content_editors:rw,d:g:reviewers:r,d:m:rwx /shared_docs
# Explanation:
# d:u:admin_user:rwx     -> Default for admin_user
# d:g:content_editors:rw -> Default for content_editors group
# d:g:reviewers:r        -> Default for reviewers group
# d:m:rwx                -> Default mask (very important for inherited permissions)

$ getfacl /shared_docs
# ... (output will show all default entries)
```
Ab agar `editor1` log in karke `shared_docs` mein koi file banata hai:
```bash
# As editor1
$ touch /shared_docs/my_report.txt
$ getfacl /shared_docs/my_report.txt
# file: my_report.txt
# owner: editor1
# group: editor1
user::rw-                           # editor1's own permissions (umask applied)
user:admin_user:rwx                 # Inherited from default
group::rwx                          # Owning group (editor1's primary group), mask se limited
group:content_editors:rw-           # Inherited
group:reviewers:r--                 # Inherited
mask::rwx                           # Inherited
other::---
```
Yahan `editor1` (as owner of `my_report.txt`) ko `rw-` mila (kyunki `umask` ne execute permission hata di). `admin_user`, `content_editors`, aur `reviewers` ko default ACLs ke hisaab se permissions mili, jo `mask::rwx` se limited hain.

#### 4.3. Troubleshooting ACL Issues (Mask, Default ACLs)

RHCE level par aapko ACL issues diagnose karne hote hain.

**Common Issues:**

1.  **"Effective permissions are less than expected" (Mask problem):**
    *   **Symptom:** User ko `rw-` permission di, lekin woh `r--` hi kar pa raha hai. `ls -l` mein group permission bhi `r--` dikh rahi hai.
    *   **Diagnosis:** `getfacl` output dekho. Agar `mask::` entry user/group ki actual permission se kam hai, toh wahi reason hai.
    *   **Solution:** `setfacl -m m:rwx file` ya `setfacl -m m:6 file` (jo bhi max permission chahiye).

    ```bash
    # Example: User2 ko rw permission deni hai, lekin sirf r mil rahi hai
    $ touch issue_file.txt
    $ setfacl -m u:user2:rw issue_file.txt
    $ getfacl issue_file.txt
    # ...
    # user::rw-
    # user:user2:rw-
    # group::r--          <- Initial mask took its value from here
    # mask::r--           <- Mask is r--
    # other::r--

    # User2 try karega write karna toh denied hoga
    # Solution: Mask change karo
    $ setfacl -m m:rw issue_file.txt
    $ getfacl issue_file.txt
    # ...
    # mask::rw-           <- Mask is now rw-
    # Now user2 will have rw- effective permission.
    ```

2.  **"New files/directories are not inheriting ACLs" (Default ACLs missing/incorrect):**
    *   **Symptom:** Parent directory par default ACLs set kiye hain, lekin naye banne wale items par woh apply nahi ho rahe.
    *   **Diagnosis:** `getfacl directory_name` command se dekho, kya `default:` entries exist karti hain aur sahi hain?
    *   **Solution:** `setfacl -m d:u:user:perm` ya `d:g:group:perm` se default ACLs ko sahi se set karo. Yaad rakho, default ACLs `mask` (`d:m:`) se bhi limited hote hain.

3.  **"ACLs not applying at all" (Filesystem or syntax error):**
    *   **Symptom:** `setfacl` command error de raha hai, ya `ls -l` par `+` sign nahi aa raha.
    *   **Diagnosis:**
        *   Filesystem ACL support check karo (`mount` command).
        *   `setfacl` command syntax double check karo.
        *   Error messages ko dhyan se padho.

#### 4.4. ACLs with Special Permissions (SUID, SGID, Sticky Bit)

Special permissions (SUID, SGID, Sticky bit) aur ACLs ek saath coexist kar sakte hain.

*   **SUID/SGID:** Agar koi file SUID/SGID bit set hone ke karan ek specific user/group ki identity se run hoti hai, toh ACLs us user/group ki permissions ke hisaab se access ko further restrict kar sakte hain. ACLs SUID/SGID ke fundamental behavior ko change nahi karte, bas unke context mein access ko fine-tune kar sakte hain.
*   **Sticky Bit:** `Sticky bit` (directories par) user ko sirf apni files delete karne ki permission deta hai, chahe user ke paas directory par `w` permission ho. ACLs is behaviour ko override nahi karte. Agar `userA` ko `dir` par `rw` access hai through ACL, aur `dir` par sticky bit set hai, toh `userA` sirf apni files delete kar payega, dusre users ki nahi.

Essentially, special permissions define *who* executes, *who* owns new files, or *who* can delete. ACLs define *what* permissions (rwx) that *who* has on the files.

#### 4.5. Best Practices & Security Considerations

*   **Use when necessary:** ACLs powerful hain, lekin unhe tabhi use karo jab standard UGO permissions kaafi na hon. Zyada complex ACLs management ko mushkil bana sakte hain.
*   **Principle of Least Privilege:** Hamesha minimum permissions do jo user/group ko apna kaam karne ke liye chahiye.
*   **Documentation:** Complex ACL configurations ko document karna bahut zaroori hai.
*   **Auditing:** `getfacl -R` ka use karke regularly permissions ko review karo.
*   **Backup:** Filesystem backups ke saath-saath, ACLs ka bhi backup rakho.
*   **Consistency:** Directories par default ACLs set karte waqt consistent raho.
*   **Test:** Hamesha change karne ke baad different users se test karo ki permissions sahi se apply ho rahi hain ya nahi.

---

### 5. Practice Tasks

Chalo, ab kuch practical tasks karte hain. `user1` (aapka current user), `user2`, aur `admin_user` banayein, aur `dev_group`, `qa_group` groups banayein.

**Setup (Run as root/sudo):**
```bash
sudo useradd user2 -m
sudo passwd user2 <> /home/user1/my_data.txt # Should fail (Permission denied)
exit

# As user1:
setfacl -x u:user2 my_data.txt
getfacl my_data.txt

# As user2:
su - user2
cat /home/user1/my_data.txt # Should fail now
exit
```

**Task 2: Group ACL and Mask**
1.  Apne home directory mein `project_files` naam ki directory banao.
2.  `dev_group` ko `project_files` par read/write (`rw`) access do.
3.  `qa_group` ko `project_files` par read-only (`r`) access do.
4.  Verify karo ki `user2` (`dev_group` ka member) `project_files` mein file bana sakta hai, lekin `admin_user` (`qa_group` ka member) nahi bana sakta.
5.  `project_files` par mask ko `r--` par set karo.
6.  `user2` se try karo `project_files` mein write karna. Kya hota hai? Explain.

**Commands for Task 2:**
```bash
# As user1:
cd ~
mkdir project_files
chmod 770 project_files # Initial permissions
getfacl project_files

setfacl -m g:dev_group:rw project_files
setfacl -m g:qa_group:r project_files
getfacl project_files

# As user2:
su - user2
touch /home/user1/project_files/dev_file.txt # Should work
exit

# As admin_user:
su - admin_user
touch /home/user1/project_files/qa_file.txt # Should fail
exit

# As user1:
setfacl -m m:r project_files # Set mask to r--
getfacl project_files
ls -l project_files # Observe group permission change

# As user2:
su - user2
echo "test" > /home/user1/project_files/dev_file.txt # Should fail (even though dev_group has rw originally)
exit
# Explanation: Mask ne effective permissions ko r-- tak limit kar diya.
```

**Task 3: Default ACLs**
1.  Apne home directory mein `shared_data` naam ki directory banao.
2.  `shared_data` par default ACLs set karo, taki:
    *   `user2` ko nayi files par `rw` access mile.
    *   `dev_group` ko nayi files par `rw` access mile.
    *   `qa_group` ko nayi files par `r` access mile.
    *   Default mask `rwx` ho.
3.  `shared_data` ke andar `test_doc.txt` aur `test_subdir` banao (as `user1`).
4.  `getfacl` use karke verify karo ki `test_doc.txt` aur `test_subdir` par ACLs sahi se inherit hue hain.

**Commands for Task 3:**
```bash
# As user1:
cd ~
mkdir shared_data
chmod 770 shared_data # Initial permissions

setfacl -m d:u:user2:rw,d:g:dev_group:rw,d:g:qa_group:r,d:m:rwx shared_data
getfacl shared_data # Verify default entries

cd shared_data
touch test_doc.txt
mkdir test_subdir
cd ..

getfacl shared_data/test_doc.txt
getfacl shared_data/test_subdir
```

#### 5.2. RHCE Practice Tasks

**Task 4: Complex Shared Project Setup & Troubleshooting**
Aapko ek `/srv/projectX` directory set up karni hai jahan:
*   Owner `admin_user` aur group `dev_group` ho.
*   `admin_user` aur `dev_group` ko full control ho (rwx).
*   `user2` (jo `dev_group` ka member hai) ko bhi full control ho.
*   `qa_group` ko sirf read access ho.
*   Nayi files aur directories jo ismein banegi, un par bhi yahi permissions apply honi chahiye.
*   `other` ko koi access nahi hona chahiye.

**Steps:**
1.  `/srv/projectX` directory banao aur sahi owner/group set karo.
2.  Initial ACLs aur default ACLs set karo.
3.  `user2` se `file_by_user2.txt` banao is directory mein.
4.  `admin_user` se `file_by_admin.txt` banao is directory mein.
5.  `qa_group` (via `admin_user` switch to `admin_user` which is in `qa_group`) se `qa_test.txt` banane ki koshish karo.
6.  **Troubleshooting:** Agar `user2` ko full control nahi mil raha, ya `qa_group` read nahi kar pa raha, toh `getfacl` use karke diagnose karo aur theek karo. (Hint: `umask` aur mask ka role.)

**Commands for Task 4:**
```bash
# As root/sudo:
mkdir -p /srv/projectX
chown admin_user:dev_group /srv/projectX
chmod 2770 /srv/projectX # SGID set for dev_group new files, and 770 for rwx

# Initial getfacl to see base permissions
getfacl /srv/projectX

# Set specific user ACL for admin_user (redundant but good for practice)
setfacl -m u:admin_user:rwx /srv/projectX

# Set ACLs for dev_group (already owning group, but explicit is fine)
setfacl -m g:dev_group:rwx /srv/projectX

# Set ACLs for qa_group
setfacl -m g:qa_group:r /srv/projectX

# Set default ACLs
setfacl -m d:u:admin_user:rwx,d:u:user2:rwx,d:g:dev_group:rwx,d:g:qa_group:r,d:m:rwx /srv/projectX
# d:u:user2:rwx is critical here for user2 to inherit full control, as user2 is not the owner by default for new files.

getfacl /srv/projectX

# Test as user2
su - user2
touch /srv/projectX/file_by_user2.txt
ls -l /srv/projectX/file_by_user2.txt
getfacl /srv/projectX/file_by_user2.txt
echo "User2 can write" > /srv/projectX/file_by_user2.txt # Should work
exit

# Test as admin_user
su - admin_user
touch /srv/projectX/file_by_admin.txt
ls -l /srv/projectX/file_by_admin.txt
getfacl /srv/projectX/file_by_admin.txt
echo "Admin can write" > /srv/projectX/file_by_admin.txt # Should work
# Try to create a file as admin_user (who is in qa_group) but expected to fail
touch /srv/projectX/qa_test_create.txt # This should fail for qa_group if not owner.

# As admin_user, try to read file_by_user2.txt (should work)
cat /srv/projectX/file_by_user2.txt
exit

# If any test fails, go back to getfacl and analyze the mask and default entries.
# Pay attention to umask when files are created!
# For user2 to have rwx on new files it creates, it needs d:u:user2:rwx.
# Also, the system's umask can affect the initial permissions of files, which then interact with ACLs.
# A user's umask setting is usually 0002 or 0022.
# File permissions = (Default from ACLs OR (777 - umask for directory, 666 - umask for file)) AND ACL mask.
```

**Task 5: Backup and Restore**
1.  `/srv/projectX` ke saare ACLs ko `/root/projectX_acl_backup.txt` mein backup karo.
2.  `/srv/projectX` se saare ACLs remove karo.
3.  Verify karo ki `+` sign `ls -l` output se hat gaya hai.
4.  Backup file se ACLs ko restore karo.
5.  Verify karo ki ACLs wapas aa gaye hain.

**Commands for Task 5:**
```bash
# As root:
getfacl -R /srv/projectX > /root/projectX_acl_backup.txt
ls -l /srv/projectX # Note the '+'
setfacl -Rb /srv/projectX # Remove all ACLs recursively
ls -l /srv/projectX # '+' should be gone
getfacl /srv/projectX # Verify no ACLs

setfacl --restore=/root/projectX_acl_backup.txt
ls -l /srv/projectX # '+' should be back
getfacl /srv/projectX # Verify ACLs are restored
```

---

### 6. Summary & Key Takeaways

*   **ACLs standard UGO limitations ko solve karte hain:** Specific users/groups ko granular permissions dene ke liye.
*   **`ls -l` mein `+` sign:** Indicates ACLs are present.
*   **`getfacl`:** ACLs dekhne ke liye.
*   **`setfacl`:** ACLs modify, add, remove karne ke liye.
    *   `-m u:user:perm`: User ACL add/modify.
    *   `-m g:group:perm`: Group ACL add/modify.
    *   `-m m:perm`: Mask ACL add/modify.
    *   `-d`: Default ACLs for directories.
    *   `-x`: Specific entry remove.
    *   `-b`: All ACLs remove.
    *   `-R`: Recursive.
*   **`mask`:** Maximum effective permissions jo named users, named groups, aur owning group ko mil sakti hain. `mask` ko hamesha dhyan se set karo.
*   **Default ACLs:** Directories mein naye items ko permissions inherit karne ke liye.
*   **Precedence:** Most specific ACL entry wins. `other` ko `mask` affect nahi karta.
*   **Troubleshooting:** `getfacl` ka output carefully analyze karo, especially `mask` entries.

ACLs ek powerful tool hai. Jab unhe sahi se use kiya jaye, toh woh aapke system ki security aur flexibility ko bahut badha sakte hain. Practice karte raho, aur concepts ko clear rakho! All the best for your RHCSA/RHCE journey!
