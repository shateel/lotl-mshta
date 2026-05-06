# 🖥️ lotl-mshta

> [!TIP]
> ### 📖 Technical Documentation & Instructions
> **Demonstrates mshta.exe → inline JS → PowerShell (-EncodedCommand) with a staged download from a remote URL and in-memory binary execution, including complex DLL functionality.**
> 
> **For a deep dive into the code and step-by-step instructions on how to run this project in your lab, visit the official article:**
> 
> URL: https://hackersterminal.com/how-mshta-spawns-powershell-and-executes-script-exe-dll-in-memory-from-remote-url/

---

## 🔍 Overview

This repository demonstrates a **fileless, living-off-the-land (LotL)** execution flow used in a controlled lab environment. The example shows how a small bootstrap launches an interpreter chain (**mshta → JavaScript → PowerShell**), fetches a staged task list at runtime, and executes actions — including in-memory execution of payloads — without writing those payloads to disk.

By leveraging native Windows binaries, this method avoids traditional disk-based detection, making it a critical study point for modern defense and detection engineering.

---

## 🛠 Execution Flow

The attack chain follows a three-stage **"Bootstrap, Fetch, and Execute"** pattern:

### 1. The Bootstrap
The entry point begins with `launch_script.ps1`, which initiates the chain:
* **MSHTA Host:** Starts `mshta.exe` with an inline JavaScript snippet.
* **JS-to-PowerShell:** The JavaScript engine spawns a new PowerShell process using a **Base64-encoded command** to evade simple command-line string monitoring.

### 2. Child PowerShell Execution
The newly spawned `child.ps1` operates as the primary orchestrator:
* **Staged Fetch:** It reaches out to a remote URL to download the logic (`child.ps1`) and the instruction set (`task.json`).
* **Dynamic Parsing:** It parses the JSON task list at runtime to determine the next steps.

### 3. Task Processing & In-Memory Execution
The final stage demonstrates the **fileless** nature of the technique:
* **Task Iteration:** The child script processes instructions defined in `task.json`.
* **Binary Execution:** Payloads like `HelloWorld.exe` and `SimpleDll.dll` are loaded into memory using **.NET Reflection**. 
* **Zero-Disk Footprint:** These binaries are never written to the file system, residing only in the volatile memory of the PowerShell process.

---

## 🛡️ Detection Opportunities

Security teams can leverage this project to test and refine detection logic for the following behaviors:

| Technique | Detection Strategy |
| :--- | :--- |
| **Process Blunting** | Monitor `mshta.exe` spawning `powershell.exe` with `-EncodedCommand`. |
| **Anomalous Lineage** | Detect unusual parent-child relationships (e.g., MSHTA as a parent to shells). |
| **Network Indicators** | Watch for PowerShell processes initiating outbound connections to unfamiliar remote URLs. |
| **Reflective Loading** | Look for in-memory .NET assembly loads (`[Reflection.Assembly]::Load`) in Event ID 4104. |

---

## ⚠️ Disclaimer

> [!IMPORTANT]
> **Educational and Defensive Research Only.**
> This repository is provided strictly for **educational and defensive security research purposes only**. Do not use this project for unauthorized access or malicious activity. The author assumes no liability for misuse. Always conduct testing in a segmented, authorized lab environment.

---
