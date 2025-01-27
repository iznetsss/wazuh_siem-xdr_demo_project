# CIS Benchmark Fix for Test ID 15513

## Overview

By default, the system fails the CIS Benchmark test with ID **15513**. This document provides instructions to resolve the issue and ensure compliance with the CIS Benchmark requirement.

## Prerequisites

- Administrative privileges on the system.
- PowerShell access.

## Steps to Fix

1. **Set Execution Policy**  
   Before executing the fix, you need to set the execution policy in PowerShell to allow the script to run. Open PowerShell as an administrator and run the following command:

   ```powershell
   Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
   ```
2. **Execute the Fix Script**
After setting the execution policy, run the provided fix script. Ensure the script is located at the specified path. Execute the following command in PowerShell:

```powershell
& "C:\Users\vboxuser\Desktop\15513-fix.ps1
```
- Once the script has executed successfully, reboot the system to apply the changes.

## Verification
After rebooting, the system should now comply with the CIS Benchmark requirement 2.3.1.5.
 
- This fix is specific to CIS Benchmark test ID 15513 and addresses compliance with section 2.3.1.5.

### Acknowledgments
- The original large script was taken from https://github.com/eneerge/CIS-Windows-Server-2022
