# Simulating and Detecting DWVA Attacks Using Splunk

This demonstrates **offensive** (attacks on DVWA) and **defensive** (detection in Splunk) capabilities in a virtual lab. We simulate common web application vulnerabilities and monitor attack traffic using Splunk.

---

## 🧪 Lab Setup Overview

- **Attacker VM**: Kali Linux (external to victim) executing DVWA attacks over shared LAN.
- **Victim VM**:  Kali Linux running DVWA (on a host IP), with Splunk Universal Forwarder installed.
- **Log Server: Splunk Enterprise server, indexing logs from the victim.

---

## 🎯 Goals

1. Deploy DVWA on a non-localhost IP to allow remote testing.
2. Execute multiple DVWA-based attacks (SQLi, XSS, LFI, etc.) using Kali.
3. Collect web and system logs via Splunk Universal Forwarder.
4. Detect and visualize each attack in Splunk dashboards and search queries.
5. Document each attack: objectives, steps, and detection screenshots.

---

---

## ⚙️ Setup Instructions

See detailed guides in the `setup/` directory:
- **`vm_setup.md`**: Configures kali VM with DVWA on a LAN IP.
- **`splunk_setup.md`**: Installs Splunk Enterprise and Universal Forwarder, configures data inputs (web logs, auth logs, custom DVWA logs).

---

## 🚀 Attack Simulations

Each attack is documented in `attacks/`, with:

- **Objective**  
- **Tools used** (e.g., Burp Suite, curl, browser)
- **Execution steps**
- **Expected output on DVWA**
- **Splunk detection** (search commands and screenshots)
- **PCAP or log snippets** (if applicable)

Examples:
- `01_sql_injection.md`
- `02_xss.md`
- `...`
- `14_final_attack.md`

---

## 📊 Detection in Splunk

- **Search queries** and **dashboard panels** show successful detection of each attack.
- Screenshots are stored in `screenshots/` and embedded in markdown files.
- Splunk reports on attacker IP, timestamp, and payload signatures.

---

## ✅ Key Outcomes

- DVWA served remotely and attacked successfully from a separate host.
- Splunk Universal Forwarder reliably sent logs to Splunk Enterprise.
- 14 DVWA attacks were detected and visualized in Splunk.
- Demonstrated red-team (attack) vs blue-team (detection) capabilities.

---


