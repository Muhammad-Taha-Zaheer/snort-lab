# snort-lab
Description: e.g. Snort installation &amp; testing lab

## 📘 Overview

This repository contains documentation, configurations, and screenshots from a practical **Snort Intrusion Detection and Prevention System (IDS/IPS)** lab.  
The purpose of this lab was to install, configure, and test **Snort** to detect malicious network activities such as **ICMP packet scans** and **SYN flood attacks**.

---

## 🎯 Objective

To demonstrate how Snort operates as both an **Intrusion Detection** and **Intrusion Prevention** system by:
- Installing Snort on Parrot OS.  
- Creating and testing **custom detection rules** using **Snorpy**.  
- Crafting and simulating **network attacks** using **Scapy** and **hping3**.  
- Observing Snort’s real-time alert generation on detected attacks.

---

## ⚙️ Tools & Environment

| Tool | Purpose |
|------|----------|
| **Snort** | Network IDS/IPS used for packet analysis and rule-based detection. |
| **Scapy** | Python-based packet crafting tool used to simulate attacks. |
| **Snorpy** | Web-based Snort rule generator for quick and accurate rule creation. |
| **hping3** | Network testing tool used to simulate SYN flood and DoS attacks. |
| **Parrot OS** | Target VM running Snort IDS/IPS. |
| **Kali Linux** | Attacker VM used for generating malicious traffic. |

---

## 🧩 Lab Steps Summary

### 1. Installation

```bash
sudo apt update
sudo apt install snort -y
snort --version
````

### 2. Configuration

Edit the main Snort configuration file:

```bash
sudo nano /etc/snort/snort.conf
```

* Set home network:

  ```
  ipvar HOME_NET 150.1.7.0/24
  ```
* Configure log directory:

  ```
  config logdir: /var/log/snort
  ```

### 3. Creating Custom Rules

Navigate to rules directory:

```bash
cd /etc/snort/rules
sudo nano icmp.rules
```

Example ICMP detection rule:

```
alert icmp any any -> 150.1.7.0/24 (msg:"ICMP Packet Found"; sid:10000001; rev:1;)
```

You can also use **Snorpy** to generate custom rule syntax via its web interface.

### 4. Running Snort in Console Mode

```bash
sudo snort -A console -q -u snort -g snort -c /etc/snort/snort.conf -i ens34
```

### 5. Attack Simulation

#### ICMP Test

From Kali Linux:

```bash
ping -c 2 150.1.7.104
```

➡️ Snort should trigger an alert: **"ICMP Packet Found"**

#### SYN Flood Attack (hping3)

```bash
sudo hping3 -S --flood -V -p 22 150.1.7.104
```

➡️ Snort logs and alerts on excessive SYN packets (potential DoS).

#### Scapy Test

```python
from scapy.all import *
send(IP(dst="150.1.7.104")/ICMP(), count=5)
```

➡️ Confirms Snort detection for crafted packets.

---

## 🧠 Observations

* Snort successfully detected all test cases (ICMP and SYN flood).
* Real-time alerts displayed in console output and logs (`/var/log/snort/`).
* Custom rules created with Snorpy worked as expected.
* Demonstrated how Snort can serve as both a learning and defensive tool in network security.

---

## 📂 Repository Structure

```
snort-lab/
│
├── README.md                 # This documentation
├── snort_lab.docx            # Full formatted lab report
├── snort_lab.pdf             # (Optional) Exported version
├── rules/
│   ├── icmp.rules            # Custom ICMP rule
│   └── local.rules           # Additional rules
└── screenshots/
    ├── snort_console.png
    ├── snorpy_rule.png
    ├── scapy_attack.png
    └── hping3_attack.png
```

---

## 📊 Results

| Attack Type           | Detection Status | Description                               |
| --------------------- | ---------------- | ----------------------------------------- |
| ICMP Ping             | ✅ Detected       | Triggered alert for ICMP packets          |
| SYN Flood             | ✅ Detected       | Detected excessive SYN packets via hping3 |
| Scapy Crafted Packets | ✅ Detected       | Confirmed detection of custom packets     |

---

## 🧩 Key Takeaways

* Snort effectively monitors and analyzes real-time network traffic.
* Custom rules can be fine-tuned for specific protocols or behaviors.
* Integration with **Snorpy** accelerates rule creation.
* Simulation with **Scapy** and **hping3** helps validate IDS performance.

---

## 🧠 Future Improvements

* Integrate Snort logs with **Wazuh SIEM** for centralized monitoring.
* Deploy Snort in **inline (IPS)** mode for real-time prevention.
* Add more complex attack patterns (ARP spoofing, DNS tunneling).

---

## 🧾 License

This repository is shared for **educational and non-commercial use**.
Feel free to reference, learn, or build upon the configurations provided.
