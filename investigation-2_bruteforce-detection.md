# Investigation 2 – SSH Brute Force Attack Analysis  
*Project:* Linux Log Analysis & Threat Detection  
*Investigator:* Alex Ojo  
*Date:* Dec 03, 2025  
*Tools Used:* Hydra, journalctl, SSH, Kali Linux  

---

## 1. Summary  
A simulated brute-force attack was launched against the SSH service running on Kali Linux using the *Hydra* automation tool.  
The system logs (journalctl) captured repeated failed authentication attempts, all targeting the *root* account from IP *10.0.2.15*.  

This behavior strongly matches typical brute-force attacks seen in real-world environments, where attackers attempt thousands of passwords in a short window.

---

## 2. Evidence Collected (Journal Logs)

Sample logs observed:


Dec 03 03:13:56 kali sshd-session[64489]: Failed password for root from 10.0.2.15 port 43938 ssh2
Dec 03 03:13:58 kali sshd-session[64746]: Failed password for root from 10.0.2.15 port 48036 ssh2
Dec 03 03:14:06 kali sshd-session[64738]: Failed password for root from 10.0.2.15 port 48079 ssh2
Dec 03 03:14:11 kali sshd-session[64756]: Failed password for root from 10.0.2.15 port 48044 ssh2


### Extracted Indicators

- *Username targeted:* root  
- *Attacker IP:* 10.0.2.15  
- *Attack method:* high-frequency password attempts  
- *Source ports constantly changing* → automated tool  
- *Short attack duration: ~15 seconds*

---

## 3. Attack Pattern Analysis

### Attack Type  
Brute-force SSH authentication attack.

### Attacker Information  
| Field | Value |
|-------|--------|
| Attacker IP | 10.0.2.15 |
| Target Account | root |
| Tool Used | Hydra |
| Protocol | SSH (Port 22) |

### Behavior Observed  

| Indicator | Description |
|-----------|-------------|
| Rapid failed logins | Confirms brute-force activity |
| Target = root | High-value admin account |
| Rotating source ports | Hydra parallel threads |
| No successful login | System remained secure |

---

## 4. Attack Timeline

| Time (EST) | Event |
|------------|-------|
| *03:13:56* | First failed login attempt |
| *03:14:11* | Final captured attempt |
| *~15 seconds* | Total attack window |

Hydra attempted *thousands of login combinations* in a very short period.

---

## 5. Log Analysis Commands Used

### View all failed SSH login attempts:
bash
sudo journalctl | grep "Failed password"


### Count number of failed attempts:
bash
sudo journalctl | grep "Failed password" | wc -l


### Check SSH service logs:
bash
sudo journalctl -u ssh


---

## 6. Root Cause

The system allowed brute-force attempts due to:

- SSH service enabled  
- Root login permitted  
- No rate limiting in place  
- No Fail2Ban / IPS protection  
- Password authentication still enabled  

---

## 7. Security Recommendations

### 1.Disable SSH root login  
Edit /etc/ssh/sshd_config:


PermitRootLogin no


Restart SSH:

bash
sudo systemctl restart ssh


### 2. Enable Fail2Ban  
Automatically blocks repeated failed logins:

bash
sudo apt install fail2ban


### 3. Use SSH Key Authentication  
Disable password login:


PasswordAuthentication no


### 4. Rate-limit SSH  
Using UFW:

bash
sudo ufw limit ssh


### 5. Optional: Change SSH Port  
(Not a primary security measure, but reduces noise.)

---

## 8. Screenshots Included
<img width="767" height="472" alt="ssh_status" src="https://github.com/user-attachments/assets/50fb4829-f19a-431e-a9d4-520436b04a71" />
<img width="862" height="471" alt="attacker_ip" src="https://github.com/user-attachments/assets/7136c6b3-e1cb-4fd8-bf6f-713e0cb7ab82" />
<img width="1791" height="480" alt="hydra_attack_output" src="https://github.com/user-attachments/assets/7ac6bd59-2afc-4303-9c15-e43268b0cdde" />
<img width="816" height="473" alt="journal_failed_passwords" src="https://github.com/user-attachments/assets/d7761274-c078-4e05-97b7-153fbe5945a4" />



---

## 9. Final Assessment

The system successfully detected high-volume brute-force activity targeting SSH.  
Although no credentials were compromised, the attack reinforces the importance of:

- Hardening authentication  
- Disabling risky accounts (root login)  
- Implementing automated blocking  
- Monitoring authentication logs regularly  

This investigation demonstrates your capability in:

- Log analysis  
- SSH configuration  
- Attack simulation  
- SOC-style incident reporting  

---

*End of Report*
