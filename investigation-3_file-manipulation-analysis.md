# Investigation 3 – Unauthorized File Manipulation & Script Behavior Analysis  
*Project:* Linux Forensics & Shell Script Analysis  
*Investigator:* Alex Ojo  
*Date:* Dec 03, 2025  
*Tools Used:* Bash, Cat, Echo, RM, Chmod, Custom Shell Script  

---

## 1. Summary  
This investigation focuses on analyzing suspicious file manipulation and evaluating the behavior of a custom shell script that responds differently based on the username provided.  
This is similar to how SOC analysts inspect:

- Unauthorized file creation  
- Unexpected file deletion  
- Malicious conditional logic inside scripts  
- Privilege-based script execution  

The activity was performed inside Kali Linux using standard Linux utilities.

---

## 2. Evidence – File Creation, Modification, and Deletion  
Commands executed:

### Creating a file:
bash
echo Good day to you > hello.txt


### Viewing file contents:
bash
cat hello.txt


### Deleting the file:
bash
rm hello.txt


### Appending new content:
bash
echo Hello World! >> hello.txt
echo Good day to you >> hello.txt


These behaviors are consistent with:

Legitimate user activity OR  
Malware/scripts trying to overwrite logs or mask activity  

Screenshots provided clearly show the sequence of file creation, deletion, and modification.

---

## 3. Analysis of Suspicious Script (ifelseifelse.sh)

The script:

bash
#!/bin/bash
if [ ${1,,} = alex ]; then
        echo "Oh, you're the boss here. Welcome!"
elif [ ${1,,} = help ]; then
        echo "Just enter your username, duh!"
else
        echo "I don't know who you are. But you're not the boss of me!"
fi


### Key Findings  

| Behavior | Meaning |
|---------|---------|
| Converts input to lowercase | Prevents case-bypass |
| Hard-coded username check | Common in privilege scripts & malware |
| Rejects unknown users | Could restrict access to specific threat actors |
| Displays different messages | Script contains logic branching based on identity |

### Script Output Evidence

Using various inputs:


./ifelseifelse.sh ALEX
“Oh, you're the boss here. Welcome!”

./ifelseifelse.sh help
“Just enter your username, duh!”

./ifelseifelse.sh test
“I don't know who you are. But you're not the boss of me!”


This confirms the script responds differently based on input — a pattern commonly seen in:

- Admin-access scripts  
- Rogue automation scripts  
- Obfuscated malware droppers  

---

## 4. Forensic Interpretation  
### Indicators of Intended Behavior  
- Script only grants “boss” access to the username alex
- Any unauthorized users are rejected  
- Could be used to create hidden backdoors or privilege escalations  

### Indicators of Local File Manipulation  
- File created, deleted, and re-created multiple times  
- Contents overwritten using redirection (>)  
- Appended new lines using (>>)  
- This is consistent with testing OR log tampering techniques  

---

## 5. Recommendations  

### 1. Monitor shell history  
bash
cat ~/.bash_history


### 2. Restrict unauthorised script execution  
bash
chmod -x suspicious_script.sh


### 3. Verify file integrity  
Use checksums to detect unauthorized changes:

bash
sha256sum filename


### 4. Prevent unauthorized script creation  
Enable auditing:

bash
sudo auditctl -w /home/kali -p war -k user_scripts


### 5. Review permissions  
Ensure least-privilege access:

bash
ls -l



---

## 6. Screenshots to Include
![file_creation png](https://github.com/user-attachments/assets/d9d596c1-ea9b-4938-8763-4ed222b37b84)
<img width="1920" height="1080" alt="file_append png" src="https://github.com/user-attachments/assets/1651e0c0-eaa3-415d-ad18-97629aabbfba" />
<img width="1920" height="1080" alt="file_deletion" src="https://github.com/user-attachments/assets/97ae5950-481d-4dfa-9bc8-913862d1ed44" />
![script_editing png](https://github.com/user-attachments/assets/5946492b-c40d-4aaf-8643-2c2afb4f9143)
![script_execution_outputs png](https://github.com/user-attachments/assets/5cf9981d-3fd3-4adb-b47a-ae9eb4c0a638)

---


