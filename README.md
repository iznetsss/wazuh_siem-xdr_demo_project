# Wazuh SIEM/XDR Demo Project

This project showcases an exploration of the open-source Wazuh SIEM/XDR solution. The aim was to understand its capabilities by creating and testing various scenarios for defense, attack detection, and logging.

## Scenarios Implemented

### 1. Detecting Malware
- **Objective**: Utilize YARA integration to detect malware.
- **Details**:
  - Additional YARA rules were implemented to detect BlackSuit ransomware.
  - Active response mechanisms were triggered to mitigate threats.

### 2. CIS Benchmark Compliance
- **Objective**: Address security misconfigurations using CIS benchmarks.
- **Details**:
  - Fix for **CIS 2.3.1.5** implemented via a custom PowerShell script.

### 3. SQL Injection Detection
- **Objective**: Identify SQL injection attempts on a web application.
- **Details**:
  - An Apache web application was used as the target.
  - Alerts were triggered after SQL injection attempts were logged.

---

## Technologies and Tools
- **Wazuh**: Open-source SIEM/XDR platform.
- **YARA**: Tool for pattern matching against malware.
- **PowerShell**: Scripted fixes for compliance.
- **Apache**: Web server for hosting the test application.

## Project Highlights
- Real-world use cases simulated for better understanding of Wazuh capabilities.
- Integration of multiple tools to demonstrate attack detection and automated responses.
- Hands-on approach to security monitoring and mitigation.

---

### Acknowledgments
Special thanks to the Wazuh community for providing extensive documentation and support. I am also deeply grateful for the opportunity to explore SIEM and XDR, learn new skills, and gain valuable knowledge along the way!

---
