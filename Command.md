# Volatility 3 Command Reference for Memory Analysis

Volatility 3 is a powerful open-source memory forensics framework for extracting digital artifacts from volatile memory (RAM) samples. It is used by incident responders and forensic analysts to investigate compromised systems.

## Installation and Setup

Before using Volatility 3, you need to install it. It's recommended to use a Python virtual environment.

1.  **Clone the repository**:
    ```bash
    git clone https://github.com/volatilityfoundation/volatility3.git
    cd volatility3
    ```

2.  **Install dependencies**:
    ```bash
    pip install -r requirements.txt
    ```

3.  **Run Volatility 3**:
    You can run Volatility 3 directly from the cloned directory.

## Basic Usage and Key Concepts

The general syntax for Volatility 3 commands is:

```bash
python3 vol.py -f <memory_dump> <plugin.command> [options]
```

*   `-f <memory_dump>`: Specifies the path to the memory dump file. This is a mandatory argument for most commands.
*   `<plugin.command>`: Refers to the specific plugin and command you want to run (e.g., `windows.pslist`, `linux.pstree`).
*   `[options]`: Additional arguments specific to the command.

### Identifying the Operating System and Profile

Volatility 3 often automatically identifies the operating system and suggests a profile. However, you can explicitly specify it or list available profiles.

*   **Suggest a profile (recommended first step)**:
    ```bash
    python3 vol.py -f <memory_dump> kdbgscan
    ```
    This command helps in identifying the correct profile for your memory dump.

*   **List available plugins and commands**:
    ```bash
    python3 vol.py --info
    ```

## Essential Volatility 3 Commands

Here's a list of commonly used Volatility 3 commands, categorized for easier navigation:

### Process Analysis

These commands help in understanding the processes that were running on the system.

1.  **windows.pslist**:
    -   **What it does**: Lists all the active processes in the memory dump.
    -   **Why it matters**: Provides a quick overview of running applications and system processes, similar to Task Manager.
    ```bash
    python3 vol.py -f <memory_dump> windows.pslist
    ```

2.  **windows.pstree**:
    -   **What it does**: Displays a tree structure of processes, showing parent-child relationships.
    -   **Why it matters**: Crucial for identifying suspicious processes and their origins, as malware often spawns from legitimate processes.
    ```bash
    python3 vol.py -f <memory_dump> windows.pstree
    ```

3.  **windows.cmdline**:
    -   **What it does**: Shows the command-line arguments used to start each process.
    -   **Why it matters**: Reveals how processes were launched, which can expose suspicious parameters or scripts.
    ```bash
    python3 vol.py -f <memory_dump> windows.cmdline
    ```

4.  **windows.dlllist**:
    -   **What it does**: Lists all DLLs (Dynamic Link Libraries) loaded by a specific process or all processes.
    -   **Why it matters**: Helps identify the resources a process is using. Malicious DLLs or unexpected DLLs loaded by legitimate processes can indicate compromise.
    ```bash
    python3 vol.py -f <memory_dump> windows.dlllist -p <PID>
    ```
    (Replace `<PID>` with the Process ID you want to inspect)

### Network Analysis

These commands focus on network connections and artifacts.

1.  **windows.netscan**:
    -   **What it does**: Scans for active and recently closed network connections.
    -   **Why it matters**: Essential for identifying command-and-control (C2) communication, data exfiltration, or other network-based malicious activities.
    ```bash
    python3 vol.py -f <memory_dump> windows.netscan
    ```

### Code and Injection Analysis

Commands to detect hidden or injected code within processes.

1.  **windows.malfind**:
    -   **What it does**: Detects hidden or injected code within process memory, often used by malware to evade detection.
    -   **Why it matters**: A primary tool for identifying code injection, rootkits, and other stealthy malware techniques.
    ```bash
    python3 vol.py -f <memory_dump> windows.malfind
    ```

### Registry Analysis

Commands for examining the Windows Registry.

1.  **windows.printkey**:
    -   **What it does**: Prints the contents of a specified registry key.
    -   **Why it matters**: Allows examination of system configuration, user activity, persistence mechanisms, and malware configurations stored in the registry.
    ```bash
    python3 vol.py -f <memory_dump> windows.printkey --key "Microsoft\Windows\CurrentVersion\Run"
    ```
    (Replace `--key` with the desired registry path)

### File System and Handle Analysis

Commands related to files and process handles.

1.  **windows.handles**:
    -   **What it does**: Lists open handles (files, registry keys, events, etc.) for each process.
    -   **Why it matters**: Shows what resources a process is interacting with, which can reveal access to sensitive files or malicious registry modifications.
    ```bash
    python3 vol.py -f <memory_dump> windows.handles -p <PID>
    ```

2.  **windows.dumpfiles**:
    -   **What it does**: Dumps various file types from memory, including executables, DLLs, and configuration files.
    -   **Why it matters**: Extracts files for further static or dynamic analysis, helping to recover important artifacts.
    ```bash
    python3 vol.py -f <memory_dump> windows.dumpfiles --dump-dir=<output_directory>
    ```
    (Replace `<output_directory>` with your desired output directory)

### Security Identifier (SID) Analysis

1.  **windows.getsids**:
    -   **What it does**: Lists Security Identifiers (SIDs) associated with each process.
    -   **Why it matters**: Helps in identifying the user context and permissions under which processes are running, critical for understanding privilege escalation or impersonation.
    ```bash
    python3 vol.py -f <memory_dump> windows.getsids
    ```

## Important Notes

*   **`<memory_dump>`**: Always replace this placeholder with the absolute or relative path to your memory dump file (e.g., `/path/to/memory.raw`).
*   **`<output_directory>`**: For commands that dump files, replace this with the path to an existing directory where you want the output files to be saved (e.g., `/home/user/forensics/dumped_files`).
*   **Profiles**: While Volatility 3 often auto-detects, sometimes you might need to specify a profile using `--profile=<profile_name>` if auto-detection fails or is incorrect.
*   **Linux/Mac Support**: Volatility 3 also supports Linux and macOS memory analysis with their respective plugins (e.g., `linux.pslist`, `mac.pslist`). The general command structure remains similar.

This guide provides a starting point for using Volatility 3. For more detailed information and advanced usage, refer to the official Volatility Foundation documentation.