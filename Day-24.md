Day 24: Swap, filesystem growth & shrink (xfs_growfs)
---
नमस्ते दोस्तों! Linux की इस धमाकेदार ट्रेनिंग क्लास में आपका स्वागत है! मैं आपका इंस्ट्रक्टर, और आज हम RHCSA और RHCE के एक बहुत ही महत्वपूर्ण और प्रैक्टिकल टॉपिक पर बात करेंगे: **"Swap, Filesystem Growth & Shrink (xfs_growfs)"**.

ये वो स्किल्स हैं जो आपको एक एडमिनिस्ट्रेटर के रूप में सर्वर की परफॉरमेंस को मैनेज करने और स्टोरेज को कुशलता से इस्तेमाल करने में मदद करेंगी. तैयार हैं? चलिए शुरू करते हैं!

---

## RHCSA + RHCE Training: Swap, Filesystem Growth & Shrink (xfs_growfs)

### 1. Concept Explanation (Hinglish)

चलिए समझते हैं इन कॉन्सेप्ट्स को आसान भाषा में:

#### a) Swap (स्वैप) - जब RAM कम पड़ जाए!

सोचिए, आपके कंप्यूटर या सर्वर की RAM (Random Access Memory) एक वर्किंग डेस्क है. आप जितनी चीजें उस डेस्क पर रखते हैं, उतनी तेजी से काम कर पाते हैं. लेकिन, अगर आपकी डेस्क भर जाए और आपको और चीजें रखनी हों? तब आप क्या करते हैं? आप कुछ कम ज़रूरी चीजों को डेस्क के नीचे एक दराज (Drawer) में रख देते हैं, ताकि जब ज़रूरत पड़े, तो उन्हें वापस डेस्क पर लाया जा सके.

Linux में, **Swap Space** भी कुछ ऐसा ही है. यह आपकी हार्ड ड्राइव या SSD का एक हिस्सा होता है जिसे RAM की तरह इस्तेमाल किया जाता है, लेकिन इसकी स्पीड RAM से बहुत धीमी होती है.

*   **क्यों ज़रूरी है? (Why it's needed?):**
    *   जब आपकी फिजिकल RAM पूरी भर जाती है (या करीब-करीब भर जाती है), तो ऑपरेटिंग सिस्टम कुछ कम इस्तेमाल होने वाले डेटा को RAM से उठाकर Swap Space में मूव कर देता है. इसे "swapping out" कहते हैं.
    *   जब उस डेटा की फिर से ज़रूरत पड़ती है, तो उसे Swap Space से वापस RAM में लाया जाता है. इसे "swapping in" कहते हैं.
    *   यह सिस्टम को क्रैश होने से बचाता है जब RAM कम पड़ जाती है, और उन एप्लीकेशन्स को चलने देता है जिन्हें ज्यादा मेमोरी चाहिए होती है.
    *   कुछ एप्लीकेशन्स (जैसे डेटाबेस सर्वर) को चलाने के लिए Swap Space की मिनिमम अमाउंट की ज़रूरत होती है, चाहे RAM कितनी भी हो.

*   **कैसे काम करता है? (How it works?):**
    *   Swap Space या तो एक अलग डिस्क पार्टीशन हो सकता है (जिसे "swap partition" कहते हैं) या फिर एक रेगुलर फाइल (जिसे "swap file" कहते हैं).
    *   Linux kernel decide करता है कि कब और कितना डेटा swap किया जाना है, एक पैरामीटर `swappiness` के बेस पर.

#### b) Filesystem Growth (फाइलसिस्टम बढ़ाना) - जब स्टोरेज कम पड़ जाए!

आप एक दुकान चला रहे हैं और आपके पास 100 वर्ग फुट का एक कमरा है जिसमें आप सामान रखते हैं (ये आपका Filesystem है). अब आपकी दुकान बहुत अच्छी चल रही है और आपको और सामान रखने के लिए ज्यादा जगह चाहिए. तो आप क्या करेंगे? आप अपने कमरे का साइज़ बढ़ाएंगे, मान लीजिए 100 से 200 वर्ग फुट कर दिया.

Linux में, जब एक Filesystem (जैसे `/home`, `/var`, `/opt`) अपनी पूरी कैपेसिटी के करीब पहुंच जाता है और हमें उसमें और डेटा स्टोर करना होता है, तो हमें उस Filesystem का साइज़ बढ़ाना पड़ता है.

*   **क्यों ज़रूरी है? (Why it's needed?):**
    *   डेटा ग्रोथ (logs, user data, application data).
    *   नई एप्लीकेशन्स इंस्टॉल करना.
    *   परफॉर्मेंस इश्यूज से बचना (full filesystems can lead to instability).

*   **कैसे काम करता है? (How it works?):**
    *   Filesystem growth दो स्टेप्स में होता है:
        1.  **Underlying Block Device को बढ़ाना:** पहले उस फिजिकल स्टोरेज (जैसे डिस्क पार्टीशन या LVM Logical Volume) का साइज़ बढ़ाना जिस पर Filesystem बना है.
        2.  **Filesystem को बढ़ाना:** फिर, उस बढ़ी हुई जगह को Filesystem को बताना ताकि वह उसे इस्तेमाल कर सके. `xfs_growfs` कमांड यहीं काम आती है.
    *   **XFS:** Red Hat Enterprise Linux में XFS डिफ़ॉल्ट Filesystem है. यह ऑन-द-फ्लाई (यानी, बिना अनमाउंट किए) grow कर सकता है, जो इसका एक बड़ा फायदा है.

#### c) Filesystem Shrink (फाइलसिस्टम छोटा करना) - जब जगह फालतू हो!

वही दुकान वाला उदाहरण लेते हैं. अब आपका बिज़नेस थोड़ा धीमा हो गया और आपको उतने बड़े कमरे की ज़रूरत नहीं है. आपके पास 200 वर्ग फुट का कमरा है, लेकिन आपको सिर्फ 100 वर्ग फुट की जगह चाहिए. आप चाहेंगे कि बाकी की जगह किसी और काम के लिए इस्तेमाल हो जाए.

Linux में, कभी-कभी हमें Filesystem का साइज़ कम करने की ज़रूरत पड़ सकती है, ताकि खाली जगह को किसी और Filesystem या पर्पस के लिए इस्तेमाल किया जा सके.

*   **क्यों ज़रूरी है? (Why it's needed?):**
    *   रिसोर्स ऑप्टिमाइजेशन (बची हुई जगह को किसी और जगह असाइन करना).
    *   कम जगह वाले डिवाइसेस में डेटा माइग्रेट करना.

*   **XFS के साथ लिमिटेशन (Limitation with XFS):**
    *   **बहुत महत्वपूर्ण बात: XFS Filesystem को Shrink नहीं किया जा सकता!** यह एक "grow-only" Filesystem है.
    *   अगर आपको XFS Filesystem की जगह कम करनी है, तो इसका एक ही तरीका है:
        1.  Filesystem का सारा डेटा कहीं और बैकअप करें.
        2.  Filesystem को अनमाउंट करें.
        3.  उस Filesystem को बनाने वाले अंडरलाइंग ब्लॉक डिवाइस (पार्टीशन/LVM LV) को डिलीट/छोटा करें.
        4.  एक नया, छोटा Filesystem उस जगह पर क्रिएट करें.
        5.  बैकअप डेटा को नए Filesystem पर रिस्टोर करें.
    *   यह एक डिस्ट्रक्टिव प्रोसेस है और इसमें डेटा लॉस का रिस्क होता है अगर बैकअप सही से नहीं हुआ. इसलिए, XFS के साथ साइज़िंग करते समय बहुत सावधानी बरतनी चाहिए.

---

### 2. Key Linux Commands (Hinglish Explanations)

चलिए अब उन ज़रूरी कमांड्स को देखते हैं जो इन कॉन्सेप्ट्स से जुडी हैं:

#### a) Swap Related Commands:

*   `free -h`:
    *   **Explanation:** यह कमांड आपकी सिस्टम मेमोरी (RAM) और Swap Space के इस्तेमाल को दिखाती है. `-h` flag "human-readable" फॉर्मेट में आउटपुट देता है (जैसे GB, MB).
    *   **Hinglish:** यह बताएगा कि कितनी RAM और कितनी Swap कुल है, कितनी इस्तेमाल हो रही है और कितनी खाली है.
    *   **Example Output:**
        ```
        $ free -h
                      total        used        free      shared  buff/cache   available
        Mem:           3.7G        1.2G        1.0G         16M        1.5G        2.2G
        Swap:          2.0G          0B        2.0G
        ```

*   `swapon -s` / `cat /proc/swaps`:
    *   **Explanation:** ये दोनों कमांड्स सिस्टम पर एक्टिवेटिड Swap Spaces (चाहे वो पार्टीशन हों या फाइलें) की लिस्ट दिखाती हैं.
    *   **Hinglish:** ये बताएंगी कि कौन-कौन से स्वैप डिवाइस या फाइलें अभी सिस्टम में एक्टिव हैं और उनका साइज़ कितना है.
    *   **Example Output:**
        ```
        $ swapon -s
        Filename                Type        Size      Used    Priority
        /dev/dm-1               partition   2097148   0       -2
        ```
        ```
        $ cat /proc/swaps
        Filename                Type        Size      Used    Priority
        /dev/dm-1               partition   2097148   0       -2
        ```

*   `mkswap `:
    *   **Explanation:** यह कमांड एक डिस्क पार्टीशन या एक फाइल को Swap Space के लिए फॉर्मेट करती है. यह उसे एक विशेष सिग्नेचर देता है ताकि Linux उसे Swap के रूप में पहचान सके.
    *   **Hinglish:** किसी पार्टीशन या फाइल को स्वैप के रूप में इस्तेमाल करने के लिए तैयार करना.
    *   **Example:** `mkswap /dev/sdb1` (पार्टीशन के लिए) या `mkswap /swapfile` (फाइल के लिए).

*   `swapon `:
    *   **Explanation:** यह कमांड एक Swap Space को एक्टिवेट करती है. यह उसे सिस्टम में इस्तेमाल के लिए उपलब्ध कराती है.
    *   **Hinglish:** स्वैप को चालू करना ताकि सिस्टम उसे इस्तेमाल कर सके.
    *   **Example:** `swapon /dev/sdb1` या `swapon /swapfile`.

*   `swapoff `:
    *   **Explanation:** यह कमांड एक एक्टिव Swap Space को डीएक्टिवेट करती है. इसका मतलब है कि सिस्टम अब उस जगह को Swap के लिए इस्तेमाल नहीं करेगा. अगर उस Swap Space में डेटा है, तो उसे वापस RAM में मूव किया जाएगा.
    *   **Hinglish:** स्वैप को बंद करना.
    *   **Example:** `swapoff /dev/sdb1` या `swapoff /swapfile`.

*   `/etc/fstab` (Configuration File):
    *   **Explanation:** यह एक कॉन्फ़िगरेशन फाइल है जिसमें सिस्टम बूट होने पर कौन से Filesystems और Swap Spaces माउंट/एक्टिवेट होने चाहिए, इसकी जानकारी होती है.
    *   **Hinglish:** ये वो लिस्ट है जो बताती है कि जब आपका सिस्टम स्टार्ट होगा तो कौन-कौन से ड्राइव्स और स्वैप अपने आप चालू हो जाएंगे.
    *   **Example Entry for Swap:**
        ```
        /dev/dm-1   swap   swap   defaults   0   0
        /swapfile   swap   swap   defaults   0   0
        ```

*   `dd if=/dev/zero of= bs=1M count=`:
    *   **Explanation:** यह कमांड एक फाइल को बनाने और उसे ज़ीरो से भरने के लिए इस्तेमाल होती है. इसे अक्सर Swap File बनाने के लिए इस्तेमाल किया जाता है. `bs` ब्लॉक साइज़ है और `count` कितने ब्लॉक्स लिखने हैं.
    *   **Hinglish:** एक खाली फाइल बनाने के लिए, जिसे हम बाद में स्वैप फाइल के रूप में इस्तेमाल कर सकते हैं.
    *   **Example:** `dd if=/dev/zero of=/swapfile bs=1M count=2048` (यह 2GB की फाइल बनाएगा).

#### b) Filesystem Growth (`xfs_growfs`) Related Commands:

*   `df -h`:
    *   **Explanation:** यह कमांड mounted Filesystems पर उपलब्ध डिस्क स्पेस का इस्तेमाल दिखाती है.
    *   **Hinglish:** यह बताएगा कि आपकी माउंट की हुई ड्राइव्स (फाइलसिस्टम्स) पर कितनी जगह है, कितनी इस्तेमाल हो गई है और कितनी खाली है.
    *   **Example Output:**
        ```
        $ df -h
        Filesystem          Size  Used Avail Use% Mounted on
        /dev/mapper/rhel-root  10G  3.0G  7.1G  30% /
        /dev/sda1          1014M  290M  725M  29% /boot
        /dev/mapper/rhel-home  5.0G  50M  5.0G   1% /home
        ```

*   `lsblk` / `fdisk -l`:
    *   **Explanation:** ये कमांड्स आपके सिस्टम पर ब्लॉक डिवाइसेस (डिस्क, पार्टीशन, LVM volumes) की जानकारी दिखाती हैं. `lsblk` ज्यादा मॉडर्न और रीडेबल है.
    *   **Hinglish:** ये बताएंगी कि आपके पास कौन-कौन सी हार्ड ड्राइव्स हैं, उनके पार्टीशन कैसे बने हैं और उनके साइज़ क्या हैं.
    *   **Example Output (lsblk):**
        ```
        $ lsblk
        NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
        sda               8:0    0   20G  0 disk
        ├─sda1            8:1    0    1G  0 part /boot
        └─sda2            8:2    0   19G  0 part
          ├─rhel-root 253:0    0   10G  0 lvm  /
          ├─rhel-swap 253:1    0    2G  0 lvm  [SWAP]
          └─rhel-home 253:2    0    5G  0 lvm  /home
        ```

*   `lvextend -L +G /dev//`:
    *   **Explanation:** यह LVM (Logical Volume Manager) Logical Volume का साइज़ बढ़ाती है. यह फाइलसिस्टम को नहीं बढ़ाती, बस उसके नीचे के स्टोरेज को बढ़ाती है.
    *   **Hinglish:** अगर आप LVM इस्तेमाल कर रहे हैं, तो यह कमांड आपके "लॉजिकल वॉल्यूम" की जगह को बढ़ाएगी. `+G` बताता है कि कितनी जगह बढ़ानी है (जैसे `+5G` 5 गीगाबाइट बढ़ाना है).
    *   **Example:** `lvextend -L +5G /dev/rhel/home`

*   `xfs_growfs `:
    *   **Explanation:** यह कमांड एक XFS Filesystem को उसके अंडरलाइंग ब्लॉक डिवाइस की मैक्सिमम अवेलेबल साइज़ तक बढ़ाती है. यह कमांड Filesystem को ऑनलाइन (mounted) रहते हुए काम कर सकती है.
    *   **Hinglish:** यह कमांड जादू करती है! जब आपने नीचे के स्टोरेज (जैसे LVM LV) को बढ़ा दिया हो, तो `xfs_growfs` उस बढ़ी हुई जगह को XFS Filesystem को बताएगी ताकि वह उसे इस्तेमाल कर सके. आपको सिर्फ माउंट पॉइंट बताना है, `xfs_growfs` खुद ही पहचान लेगी कि किस डिवाइस पर है.
    *   **Example:** `xfs_growfs /home`

*   `pvdisplay`, `vgdisplay`, `lvdisplay`:
    *   **Explanation:** ये LVM की स्थिति देखने के लिए कमांड्स हैं.
        *   `pvdisplay`: Physical Volumes की जानकारी.
        *   `vgdisplay`: Volume Groups की जानकारी.
        *   `lvdisplay`: Logical Volumes की जानकारी.
    *   **Hinglish:** LVM के "बिल्डिंग ब्लॉक्स" (Physical Volumes, Volume Groups, Logical Volumes) को देखने के लिए. ये बताएंगे कि कौन सा वॉल्यूम कितना बड़ा है और कितनी जगह खाली है.

---

### 3. Step-by-Step Examples

चलिए कुछ रियल-वर्ल्ड सिनेरियोज़ देखते हैं!

**Scenario 1: Adding a 2GB Swap File**

मान लीजिए आपके सर्वर में Swap की कमी है और आप एक 2GB की Swap File जोड़ना चाहते हैं.

1.  **Check Current Swap Status:**
    ```bash
    free -h
    swapon -s
    ```
    (Note the current Swap total and used)

2.  **Create a 2GB File for Swap:**
    हम `/swapfile` नाम की एक फाइल बनाएंगे.
    ```bash
    sudo dd if=/dev/zero of=/swapfile bs=1M count=2048
    ```
    (This creates a 2048MB = 2GB file filled with zeros.)

3.  **Set Correct Permissions for the Swap File:**
    Swap file को केवल `root` यूजर ही एक्सेस कर पाए, यह ज़रूरी है ताकि कोई और उसे छेड़ न सके.
    ```bash
    sudo chmod 600 /swapfile
    ```

4.  **Format the File as Swap Space:**
    ```bash
    sudo mkswap /swapfile
    ```
    **Expected Output:**
    ```
    Setting up swapspace version 1, size = 2 GiB (2147479552 bytes)
    no label, UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

5.  **Activate the Swap File (Temporarily):**
    ```bash
    sudo swapon /swapfile
    ```

6.  **Verify New Swap Space:**
    ```bash
    free -h
    swapon -s
    ```
    (You should now see the 2GB swapfile listed and the total swap space increased.)

7.  **Make Swap Persistent Across Reboots (Add to `/etc/fstab`):**
    `vim` या `nano` से `/etc/fstab` फाइल को एडिट करें और सबसे नीचे यह लाइन जोड़ें:
    ```bash
    # Open /etc/fstab with a text editor
    sudo vi /etc/fstab
    ```
    **Add this line:**
    ```
    /swapfile   swap    swap    defaults     0 0
    ```
    (Save and close the file.)

8.  **Test the fstab entry (Optional but Recommended):**
    ```bash
    sudo swapon --show # Check active swaps
    sudo swapoff -a    # Deactivate all swap
    sudo swapon -a     # Activate all swap listed in fstab
    sudo swapon --show # Verify again
    ```
    अगर कोई एरर नहीं आता, तो आपका Swap File रिबूट के बाद भी एक्टिव रहेगा.

---

**Scenario 2: Expanding an XFS Filesystem on an LVM Logical Volume**

मान लीजिए आपके पास `/home` पर 5GB का XFS Filesystem है, जो एक LVM Logical Volume (`/dev/rhel/home`) पर बना है, और आप उसे 10GB और बढ़ाकर कुल 15GB करना चाहते हैं.

**Pre-requisites:**
*   आपके `Volume Group` (`rhel` in this example) में पर्याप्त Free Physical Extents (PEs) होने चाहिए. अगर नहीं, तो आपको पहले `pvcreate` और `vgextend` से एक नई डिस्क या पार्टीशन को Volume Group में जोड़ना होगा. (RHCE level task, we assume free space is available in VG for this example).

1.  **Check Current Filesystem Size and Usage:**
    ```bash
    df -h /home
    ```
    **Expected Output:**
    ```
    Filesystem              Size  Used Avail Use% Mounted on
    /dev/mapper/rhel-home   5.0G   50M  5.0G   1% /home
    ```

2.  **Check Current Logical Volume Size:**
    ```bash
    sudo lvdisplay /dev/rhel/home
    ```
    **Expected Output (relevant part):**
    ```
    --- Logical volume ---
    LV Path                /dev/rhel/home
    LV Name                home
    VG Name                rhel
    LV Size                5.00 GiB
    ...
    ```

3.  **Check Volume Group Free Space:**
    ```bash
    sudo vgdisplay rhel
    ```
    **Expected Output (relevant part):**
    ```
    --- Volume group ---
    VG Name               rhel
    VG Size               
    PE Size               4.00 MiB
    Total PE              
    Alloc PE / Size        / 
    Free  PE / Size       2560 / 10.00 GiB   # IMPORTANT: Make sure you have enough free space (e.g., 10GB here)
    ```

4.  **Extend the Logical Volume by 10GB:**
    `lvextend` कमांड का इस्तेमाल करके Logical Volume का साइज़ बढ़ाएंगे.
    ```bash
    sudo lvextend -L +10G /dev/rhel/home
    ```
    (Note: `+10G` means add 10GB to the current size. You can also use `-L 15G` to set the total size to 15GB.)
    **Expected Output:**
    ```
    Logical volume rhel/home successfully resized.
    ```

5.  **Verify New Logical Volume Size:**
    ```bash
    sudo lvdisplay /dev/rhel/home
    ```
    (The `LV Size` should now reflect the increased size, e.g., 15.00 GiB).

6.  **Extend the XFS Filesystem:**
    अब हम `xfs_growfs` का इस्तेमाल करके Filesystem को बढ़ाएंगे. चूंकि XFS ऑनलाइन grow कर सकता है, हम इसे माउंटेड रहते हुए चला सकते हैं.
    ```bash
    sudo xfs_growfs /home
    ```
    (आप चाहें तो डिवाइस का नाम भी दे सकते हैं, जैसे `sudo xfs_growfs /dev/rhel/home`, लेकिन माउंट पॉइंट देना ज़्यादा कॉमन और रिकमेंडेड है.)
    **Expected Output:**
    ```
    meta-data=/dev/mapper/rhel-home  isize=512    agcount=4, agsize=327680 blks
             =                       sectsz=512   attr=2, projid32bit=1
             =                       crc=1        finobt=1, sparse=1, rmapbt=0
             =                       reflink=1
    data     =                       bsize=4096   blocks=1310720, imaxpct=25
             =                       sunit=0      swidth=0 blks
    naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
    log      =internal log           bsize=4096   blocks=2560, version=2
             =                       sectsz=512   sunit=0 blks, lazy-count=1
    realtime =none                   extsz=4096   blocks=0, rtextents=0
    data blocks changed from 1310720 to 3932160 # This line confirms the growth
    ```

7.  **Verify New Filesystem Size:**
    ```bash
    df -h /home
    ```
    **Expected Output:**
    ```
    Filesystem              Size  Used Avail Use% Mounted on
    /dev/mapper/rhel-home    15G   50M   15G   1% /home # Filesystem size is now 15G
    ```
    बधाई हो! आपने सफलतापूर्वक एक XFS Filesystem का साइज़ बढ़ा दिया है.

---

### 4. Practice Tasks (Hands-On Lab)

इन टास्क को अपनी VM (Virtual Machine) में प्रैक्टिस करें.

**Task 1: Create and Manage a Swap File**

1.  अपने VM में मौजूदा Swap Space की जाँच करें (`free -h`, `swapon -s`).
2.  `/var/swaptest` नाम की एक 1GB की नई Swap File बनाएं.
3.  उस फाइल को Swap Space के लिए फॉर्मेट करें और ज़रूरी परमिशन दें.
4.  उस Swap File को एक्टिवेट करें.
5.  जांचें कि नई Swap File एक्टिव है और कुल Swap Space बढ़ गया है.
6.  `/etc/fstab` में एंट्री करके इसे परमानेंट बनाएं.
7.  `swapoff -a` और `swapon -a` चलाकर `fstab` एंट्री को टेस्ट करें.
8.  अब इस `/var/swaptest` Swap File को डीएक्टिवेट करें (`swapoff`).
9.  `/etc/fstab` से इसकी एंट्री हटा दें और फाइल को डिलीट कर दें (`rm /var/swaptest`).

**Task 2: Extend an XFS Filesystem on an LVM Logical Volume**

**Pre-requisite:** अपने VM में एक एक्स्ट्रा वर्चुअल डिस्क (`/dev/sdb`) 10GB या 20GB की जोड़ें (VMware/VirtualBox settings से).

1.  एक नया Physical Volume (`/dev/sdb`) बनाएं: `sudo pvcreate /dev/sdb`
2.  अपने मौजूदा Volume Group (e.g., `rhel`) में इस Physical Volume को जोड़ें: `sudo vgextend rhel /dev/sdb`
3.  `vgdisplay rhel` चलाकर देखें कि `Free PE / Size` बढ़ गया है.
4.  एक नया Logical Volume (`mylv`) 5GB का बनाएं, अपने `rhel` Volume Group के अंदर:
    `sudo lvcreate -L 5G -n mylv rhel`
5.  इस Logical Volume (`/dev/rhel/mylv`) पर एक XFS Filesystem बनाएं:
    `sudo mkfs.xfs /dev/rhel/mylv`
6.  एक माउंट पॉइंट (`/data`) बनाएं और Filesystem को माउंट करें:
    `sudo mkdir /data`
    `sudo mount /dev/rhel/mylv /data`
7.  `df -h /data` से इसके साइज़ की पुष्टि करें.
8.  अब इस `mylv` को 5GB और बढ़ाएं (कुल 10GB करें):
    `sudo lvextend -L +5G /dev/rhel/mylv`
9.  `xfs_growfs /data` चलाकर XFS Filesystem को बढ़ाएं.
10. `df -h /data` से पुष्टि करें कि Filesystem का साइज़ 10GB हो गया है.
11. `/etc/fstab` में एंट्री करके `/data` को परमानेंट माउंट करें.

---

### 5. Pro Tips / Common Mistakes

#### Pro Tips:

*   **Monitor Swap Usage:** `free -h`, `sar -S` (sysstat package) जैसे कमांड्स से रेगुलरली Swap Usage चेक करते रहें. अगर Swap का इस्तेमाल लगातार हाई है, तो यह इंडिकेट करता है कि आपके सिस्टम को और RAM की ज़रूरत है.
*   **`swappiness` Parameter:** Linux Kernel का एक पैरामीटर `swappiness` (वैल्यू 0 से 100 तक) डिसाइड करता है कि सिस्टम कितनी जल्दी RAM से डेटा को Swap में मूव करेगा.
    *   `swappiness=0`: Kernel तब तक Swap नहीं करेगा जब तक RAM पूरी तरह भर न जाए (सर्वर के लिए अच्छा).
    *   `swappiness=60` (Default): Kernel एक ऑप्टिमल बैलेंस बनाने की कोशिश करता है.
    *   आप इसे `cat /proc/sys/vm/swappiness` से चेक कर सकते हैं और `sysctl -w vm.swappiness=10` से बदल सकते हैं (परमानेंट के लिए `/etc/sysctl.conf` में `vm.swappiness = 10` जोड़ें).
*   **XFS Grow is Online:** XFS Filesystem को `xfs_growfs` कमांड से माउंटेड रहते हुए (ऑनलाइन) बढ़ाया जा सकता है, जो एक बड़ा एडवांटेज है क्योंकि इससे सर्विस डाउनटाइम नहीं होता.
*   **LVM is Your Friend:** प्रोडक्शन एनवायरनमेंट में LVM (Logical Volume Manager) का इस्तेमाल करना हमेशा बेहतर होता है, क्योंकि यह स्टोरेज मैनेजमेंट को बहुत फ्लेक्सिबल बनाता है (आप पार्टीशंस को आसानी से बढ़ा या घटा सकते हैं, स्नैपशॉट ले सकते हैं, आदि).

#### Common Mistakes to Avoid:

*   **XFS Shrink Myth:** **सबसे बड़ी गलती!** बार-बार याद दिला रहा हूँ: **XFS Filesystem को Shrink नहीं किया जा सकता.** अगर आप गलती से कोई XFS Filesystem बहुत बड़ा बना देते हैं और फिर उसे छोटा करना चाहते हैं, तो आपको डेटा बैकअप करके, Filesystem को डिलीट करके, नया Filesystem छोटे साइज़ का बनाकर, और डेटा रिस्टोर करके ही यह करना पड़ेगा. यह एक डिस्ट्रक्टिव और टाइम-कंज्यूमिंग प्रोसेस है. इसलिए, XFS बनाते समय साइज़ का ध्यान रखें.
*   **Forgetting to extend Logical Volume (LVM):** `xfs_growfs` चलाने से पहले, आपको यह सुनिश्चित करना होगा कि अंडरलाइंग LVM Logical Volume (या पार्टीशन) का साइज़ बढ़ गया है. अगर नहीं, तो `xfs_growfs` को बढ़ाने के लिए कोई जगह नहीं मिलेगी.
*   **Typo in `/etc/fstab`:** `fstab` में गलत एंट्री (गलत डिवाइस पाथ, गलत Filesystem टाइप, गलत माउंट ऑप्शन) सिस्टम को बूट होने से रोक सकती है. हमेशा `mount -a` या `swapon -a` चलाकर टेस्ट करें या रीबूट से पहले `systemctl daemon-reload` चलाकर `mount` यूनिट्स को रीलोड करें.
*   **Incorrect Permissions on Swap File:** Swap File पर अगर परमिशन `600` (या इससे भी स्ट्रिक्ट) नहीं है, तो `mkswap` या `swapon` काम नहीं करेगा और सिक्योरिटी रिस्क भी रहेगा.
*   **Not Verifying After Changes:** कोई भी स्टोरेज या Swap से जुड़ा बदलाव करने के बाद, हमेशा `df -h`, `free -h`, `swapon -s`, `lvdisplay` जैसे कमांड्स से वेरीफाई करें कि बदलाव सही से अप्लाई हो गए हैं.
*   **Not Enough Free Space in VG:** अगर आप LVM LV को बढ़ा रहे हैं, तो `vgdisplay` चलाकर पहले यह सुनिश्चित करें कि आपके Volume Group में पर्याप्त खाली जगह (Free PEs) है. अगर नहीं, तो आपको और फिजिकल स्टोरेज जोड़ना होगा.

---

तो दोस्तों, यह था हमारा "Swap, Filesystem Growth & Shrink (xfs_growfs)" पर RHCSA + RHCE का कंप्रिहेंसिव लेसन. मुझे उम्मीद है कि आपको यह सब अच्छे से समझ आ गया होगा. इन कमांड्स की प्रैक्टिस बहुत ज़रूरी है, क्योंकि ये हर Linux एडमिनिस्ट्रेटर की डेली लाइफ का हिस्सा हैं.

कोई सवाल हो तो पूछिए, happy learning!
