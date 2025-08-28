# 📟 Windows CMD Cheat Sheet for Developers

## 🔧 Basic File & Directory Commands

| Command | Description |
|--------|-------------|
| `dir` | Lists files and folders in the current directory. |
| `cd [folder]` | Changes to the specified folder. Example: `cd projects` |
| `cd ..` | Moves up one directory level. |
| `cd /` or `cd \` | Goes to the root of the current drive. |
| `cls` | Clears the command prompt screen. |
| `mkdir [folder]` | Creates a new folder. Example: `mkdir test` |
| `rmdir [folder]` | Deletes an empty folder. |
| `del [file]` | Deletes a file. Example: `del index.html` |
| `copy [src] [dest]` | Copies files. Example: `copy file1.txt backup\file1.txt` |
| `move [src] [dest]` | Moves files. |

## ⚙️ System Info & Diagnostics

| Command | Description |
|--------|-------------|
| `ipconfig` | Displays IP address info. Useful for network issues. |
| `ping [hostname]` | Tests network connectivity. Example: `ping google.com` |
| `tasklist` | Lists running processes. |
| `taskkill /IM [name] /F` | Kills a process by name. Example: `taskkill /IM chrome.exe /F` |
| `systeminfo` | Shows detailed system information. |
| `echo %PATH%` | Displays the system `PATH` variable. Helpful for dev setup. |
| `set` | Lists environment variables. |

## 🔍 Navigation & Search

| Command | Description |
|--------|-------------|
| `tree` | Shows a directory structure as a tree. |
| `find "text" [filename]` | Searches for a string in a file. |
| `where [file]` | Shows the path of executables. Example: `where python` |

## 🛠️ Development-Related Commands

| Command | Description |
|--------|-------------|
| `code .` | Opens the current folder in VS Code (if installed). |
| `npm`, `npx` | Node.js package management commands. |
| `git` | Version control. Use `git --version` to check if installed. |
| `python` | Starts Python interpreter (if installed). |
| `javac`, `java` | Compile/run Java programs (if Java SDK is installed). |
| `dotnet` | .NET CLI for building and running .NET apps. |

## 🔄 Networking & Ports

| Command | Description |
|--------|-------------|
| `netstat -an` | Shows open network connections and listening ports. |
| `net use` | Maps network drives and lists shared resources. |
| `hostname` | Shows the computer's network name. |

## 🧪 File Inspection and Output

| Command | Description |
|--------|-------------|
| `type [file]` | Outputs contents of a file to the console. |
| `more [file]` | Views large files page by page. |
| `echo [text]` | Prints text to console. Example: `echo Hello World!` |
| `> [file]` | Redirects output to a file. Example: `echo Hello > hello.txt` |
| `>> [file]` | Appends to a file. |

## 🧠 Bonus Tips for Beginners

- Use **Tab** to auto-complete folder/file names.
- Use **arrow keys** to scroll through previous commands.
- Add `/?` to most commands for help. Example: `xcopy /?`
- CMD paths are **not case-sensitive**.

## 🧰 Want to Do More?

You can also explore:
- **PowerShell** (more powerful scripting than CMD)
- **Windows Terminal** (modern CLI with tab support)
- **WSL (Windows Subsystem for Linux)** if you want a Linux-like experience