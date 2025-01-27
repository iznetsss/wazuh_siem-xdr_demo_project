# Windows 10 Agent: Detecting Malware Using YARA Integration

This guide demonstrates how to use YARA with Wazuh to detect malware in new or modified files on a Windows 10 endpoint. 

---

## **Requirements for the Endpoint (Installed on a VM)**

### Software Requirements:

1. **Python 3.X**
   - Install the launcher for all users.
   - Add Python 3.X to the system PATH during installation.

2. **Visual C++ Redistributable Package**
   - Ensure the appropriate version is installed based on your system architecture (x86 or x64).

For detailed instructions on the installation, refer to the [official Wazuh documentation](https://documentation.wazuh.com/current/proof-of-concept-guide/detect-malware-yara-integration.html).

---

## **Setup Steps**

1. **Integrate YARA with Wazuh**
   - Configure Wazuh to use YARA rules to monitor and detect malicious activity on endpoint files.
   - Ensure the YARA rules are up-to-date and relevant to your use case.

2. **Add BlackSuit Ransomware Rules**

   Following the guide [Detect and Respond to BlackSuit Ransomware with Wazuh](https://wazuh.com/blog/detect-and-respond-to-blacksuit-ransomware-with-wazuh/):

   - Edit the downloaded YARA rule file at `C:\Program Files (x86)\ossec-agent\active-response\bin\yara\rules\yara_rules.yar` and add the following BlackSuit ransomware rule:

     ```yara
     rule BlackSuit_ransomware {
        meta:
           description = "BlackSuit ransomware executable detection"
           author = "Aishat Motunrayo Awujola"
           reference = "https://github.com/Neo23x0/yarGen"
           date = "2024-10-03"
        hash1= "90ae0c693f6ffd6dc5bb2d5a5ef078629c3d77f874b2d2ebd9e109d8ca049f2c"
        strings:
           $x1 = "C:\\Users\\pipi-\\source\\repos\\encryptor\\Release\\encryptor.pdb" fullword ascii
           $s2 = "api-ms-win-core-synch-l1-2-0.dll" fullword wide
           $s3 = "C:\\Users\\Adm\\vcpkg\\packages\\openssl_x86-windows-static\\bin" fullword ascii
           $s4 = "C:\\Users\\Adm\\vcpkg\\buildtrees\\openssl\\x86-windows-static-rel\\providers\\implementations\\ciphers\\cipher_aes_hw_aesni.inc" ascii
           $s5 = "C:\\Users\\Adm\\vcpkg\\buildtrees\\openssl\\x86-windows-static-rel\\providers\\implementations\\ciphers\\cipher_aes_cts.inc" fullword ascii
           $s6 = "C:\\Users\\Adm\\vcpkg\\buildtrees\\openssl\\x86-windows-static-rel\\providers\\implementations\\macs\\blake2_mac_impl.c" fullword ascii
           $s7 = "get_payload_private_key" fullword ascii
           $s8 = "C:\\Users\\Adm\\vcpkg\\packages\\openssl_x86-windows-static\\lib\\engines-3" fullword ascii
           $s9 = "C:\\Users\\Adm\\vcpkg\\packages\\openssl_x86-windows-static" fullword ascii
           $s10 = "get_payload_public_key" fullword ascii
           $s11 = "C:\\Users\\Adm\\vcpkg\\buildtrees\\openssl\\x86-windows-static-rel\\crypto\\err\\err_local.h" fullword ascii
           $s12 = "C:\\Users\\Adm\\vcpkg\\buildtrees\\openssl\\x86-windows-static-rel\\providers\\implementations\\ciphers\\cipher_camellia_cts.inc" ascii
           $s13 = "C:\\Windows\\Sysnative\\bcdedit.exe" fullword wide
           $s14 = "C:\\Windows\\Sysnative\\vssadmin.exe" fullword wide
           $s15 = "error processing message" fullword ascii
           $s16 = "C:\\Users\\Adm\\vcpkg\\buildtrees\\openssl\\x86-windows-static-rel\\engines\\e_capi_err.c" fullword ascii
           $s17 = "AppPolicyGetProcessTerminationMethod" fullword ascii
           $s18 = "get_dh_dsa_payload_p" fullword ascii
           $s19 = "loader incomplete" fullword ascii
           $s20 = "get_payload_group_name" fullword ascii
        condition:
           uint16(0) == 0x5a4d and filesize < 7000KB and
           1 of ($x*) and 4 of them
     }
     ```

### Analysis of Yara BlackSuit ransomware rule: 

### Strings
Identifiable strings in BlackSuit ransomware executables include:

- **$x1**: PDB file path for debugging (e.g., `C:\\Users\\pipi-\\source\\repos\\encryptor\\Release\\encryptor.pdb`), indicating the developer's machine.
- **$s2**: DLL name for Windows thread synchronization (`api-ms-win-core-synch-l1-2-0.dll`).
- **s3–s12**: OpenSSL-related paths, used for encryption, e.g.:
  - `C:\\Users\\Adm\\vcpkg\\packages\\openssl_x86-windows-static\\bin`
  - `C:\\Users\\Adm\\vcpkg\\buildtrees\\openssl\\x86-windows-static-rel\\...`
- **s7, s10, s18, s20**: Encryption-related function names:
  - `get_payload_private_key`
  - `get_payload_public_key`
  - `get_dh_dsa_payload_p`
  - `get_payload_group_name`
- **s13, s14**: Windows system utility paths:
  - `C:\\Windows\\Sysnative\\bcdedit.exe`
  - `C:\\Windows\\Sysnative\\vssadmin.exe`
- **s15, s19**: Error messages:
  - `error processing message`
  - `loader incomplete`
- **$s17**: Windows API function name: `AppPolicyGetProcessTerminationMethod`.

### Conditions
Defines when the YARA rule triggers:

1. **PE file signature**: File must start with "MZ" (`0x5A4D`).
2. **File size**: Must be under 7000 KB.
3. **String matches**:
   - At least one string from `$x*` (e.g., `$x1`).
   - At least four strings from all listed in the `Strings` section.

### Rule Functionality
The YARA rule scans files for:
- **PE file signature and size limits.**
- **Matches with specified strings to identify BlackSuit ransomware executables.**

##
3. **Add BlackSuit Ransomware Rules on the Wazuh Server**

   - Create a file for the rules:

     ```bash
     touch /var/ossec/etc/rules/blacksuit_ransomware.xml
     ```

   - Add the following content to `/var/ossec/etc/rules/blacksuit_ransomware.xml`:

     ```xml
     <group name="BlackSuit, ransomware,">
       <!-- Ransomware execution -->
       <rule id="100011" level="12">
         <if_sid>61603</if_sid>
         <field name="win.eventdata.CommandLine" type="pcre2">(?i).*.exe\s+-name\s\d{32}$</field>
         <description>Possible BlackSuit ransomware executed.</description>
         <mitre>
           <id>T1059</id>
           <id>T1086</id>
         </mitre>
       </rule>

       <!-- Inhibit system recovery -->
       <rule id="100012" level="12">
         <if_sid>61603</if_sid>
         <field name="win.eventdata.CommandLine" type="pcre2">(?i)vssadmin.exe\\"\sDelete\sShadows\s\/All\s\/Quiet</field>
         <description>Volume shadow copy deleted using $(win.eventdata.originalFileName). Potential ransomware activity detected.</description>
         <mitre>
           <id>T1490</id>
           <id>T1059.003</id>
         </mitre>
       </rule>

       <!-- Ransom note file creation -->
       <rule id="100013" level="15" timeframe="100" frequency="2">
         <if_sid>61613</if_sid>
         <field name="win.eventdata.image" type="pcre2">\.exe</field>
         <field name="win.eventdata.targetFilename" type="pcre2">(?i)[C-Z]:.*.\\README.BlackSuit.txt</field>
         <description>The file $(win.eventdata.targetFilename) has been created in multiple directories. BlackSuit ransomware detected.</description>
         <mitre>
           <id>T1059</id>
         </mitre>
       </rule>
     </group>
     ```

---

## **Testing the Configuration**

To test the setup, download and scan the following malware samples:

1. **Sample Test Virus**
- URL: [Download Test Virus (Ikarus Test Virus)](https://www.ikarussecurity.com/en/private-customers/download-test-viruses-for-free/)

2. **Petya Ransomware**
-  URL: [Download Petya Sample (Github Link)](https://gist.github.com/vulnersCom/65fe44d27d29d7a5de4c176baba45759)

### Testing Procedure:

1. Download the test malware samples in a controlled environment (VM).
2. Ensure that Wazuh and YARA integration are actively monitoring the designated file paths.
3. Observe and log the detections triggered by the malware files.
4. Test the Active Response script to confirm automated actions are executed upon detection.

---

## **Notes and Recommendations**

- **Controlled Environment:** Perform all testing in an isolated Virtual Machine to avoid accidental infection of production systems.
- **Stay Updated:** Regularly update Wazuh, YARA, and their associated rules for optimal malware detection.
- **Documentation:** Refer to the [official Wazuh documentation](https://documentation.wazuh.com/current/proof-of-concept-guide/detect-malware-yara-integration.html) for further guidance.
- **Backup:** Create regular backups of the VM before performing tests to simplify recovery in case of errors.

---
