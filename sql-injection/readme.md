# Wazuh SQL Injection Detection

This guide demonstrates how to configure and test **SQL Injection detection** on a Wazuh-enabled setup using an Ubuntu VM with Apache2 as the web server. The steps below outline how to deploy, test, and verify SQL Injection alerts.

## Prerequisites
1. **Ubuntu 22.04 VM**: Installed and running in VirtualBox with Apache2 configured as a web server.
2. **Wazuh Server**: Deployed and accessible to receive alerts.
3. **Wazuh Agent**: Installed on the Ubuntu VM and configured to forward logs to the Wazuh server.
4. **Host PC**: Used to simulate an SQL Injection attempt.

## Setup Instructions
Follow the official Wazuh documentation to set up web attack monitoring:
[Wazuh SQL Injection Guide](https://documentation.wazuh.com/current/proof-of-concept-guide/detect-web-attack-sql-injection.html)

### Steps to Test SQL Injection Detection

1. **Ensure Apache2 is Running on Ubuntu VM**
   - Check the status of Apache2:
     ```bash
     sudo systemctl status apache2
     ```
   - If not running, restart Apache2:
     ```bash
     sudo systemctl reload apache2
     ```

2. **Verify Host-to-VM Connectivity**
   - From the host PC, ensure the VM is reachable:
     ```powershell
     ping IP-ADDRESS-OF-APACHE
     ```

3. **Simulate an SQL Injection Attempt**
   - Use PowerShell on the host PC to execute a simulated SQL Injection:
     ```powershell
     Invoke-WebRequest -Uri "http://IP-ADDRESS-OF-APACHE/users/?id=SELECT+*+FROM+users" -Method Get
     ```

4. **Verify Apache2 Logs**
   - On the Ubuntu VM, check that the Apache2 log contains the SQL Injection request:
     ```bash
     sudo cat /var/log/apache2/access.log
     ```

5. **Check for Wazuh Alert**
   - Confirm that the Wazuh server received an alert for the SQL Injection attempt.
   - Log in to the Wazuh Dashboard and check for an alert titled **"SQL Injection attempt"**.

---

## Notes
- Ensure the Wazuh agent on the Ubuntu VM is properly configured to forward logs to the Wazuh server.
- If no alert is generated, double-check the Wazuh rules and ensure the appropriate modules (e.g., **mod_security**) are enabled in Apache2.


## Resources
- [Wazuh Documentation](https://documentation.wazuh.com/)
- [Apache2 Official Documentation](https://httpd.apache.org/)
- [SQL Injection Testing with Wazuh](https://documentation.wazuh.com/current/proof-of-concept-guide/detect-web-attack-sql-injection.html)
