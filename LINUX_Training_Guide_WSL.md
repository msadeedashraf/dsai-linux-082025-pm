# Linux Training Guide (WSL Edition)
**Author:** Sadeed  
**Audience:** Windows users (Beginners → Intermediate → Advanced)  
**Last updated:** 2025‑06‑22

> This course is hands-on. Every section includes short practices you can do immediately.
> We’ll start on Windows, install and understand WSL, then build solid Linux skills with Windows-friendly examples.

---

## Chapter 1 — WSL on Windows Pro: Install, Configure, Start/Stop, and Master the Basics

### 1.1 What is WSL (and why you’ll love it)?
Windows Subsystem for Linux (WSL) lets you run a real GNU/Linux environment on Windows without a full VM. You can open a Linux shell next to PowerShell, run Bash, apt, ssh, Docker, Python, and even GUI apps, while still using Windows tools and files.

**WSL 1 vs WSL 2 (fast facts):**
- **WSL 2** runs a real Linux kernel in a lightweight VM and offers *much faster* Linux‑filesystem I/O and full syscall compatibility. Great default choice for dev work.
- **WSL 1** uses a translation layer (no VM). It can be snappier for *cross‑filesystem* access (working directly under `/mnt/c`), but lacks some features (e.g., some container scenarios).  
- You can run both, side‑by‑side, and set your preferred default.


### 1.2 Prerequisites checklist (Windows Pro)
1. **Windows 10 version 2004+ (Build 19041+) or Windows 11.**  
   - Press `Win + R`, type `winver`, press Enter to check.
2. **Virtualization enabled in BIOS/UEFI.**  
   - Ensure Intel VT‑x / AMD‑V is on (look for “Virtualization Technology”).  
3. **Optional features** (WSL commands will auto‑enable these):  
   - Windows Subsystem for Linux  
   - Virtual Machine Platform (required for WSL 2)  

> **Note on Hyper‑V:** WSL 2 uses Windows’ hypervisor under the hood. You **do not** need to install the full **Hyper‑V role** to use WSL 2, but enabling it on Windows Pro is fine if you also use Hyper‑V VMs.


### 1.3 Easiest install (one command)
Open **PowerShell as Administrator** and run:
```powershell
wsl --install
```
- This enables required components and installs **Ubuntu** by default.  
- After a reboot, set your Linux **username** and **password** when prompted.

**Pick a different distro (optional):**
```powershell
wsl --list --online       # see available distros
wsl --install Debian      # example: install Debian
```

**Set WSL 2 as default for future installs:**
```powershell
wsl --set-default-version 2
```


### 1.4 Manual / offline install (if needed)
If policy blocks Microsoft Store or you’re on an older build, enable features manually in **elevated PowerShell**:
```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
Then reboot, install the **WSL kernel** package if prompted, and set default version:
```powershell
wsl --set-default-version 2
```


### 1.5 Start, stop, and status — the WSL “power trio”
**Check what’s installed and running:**
```powershell
wsl -l -v            # shows distros, versions, and state
wsl --status         # shows default distro, kernel, WSL version
```

**Start WSL in your Linux home directory:**
```powershell
wsl ~
```

**Shut down everything (all distros + the WSL 2 VM):**
```powershell
wsl --shutdown
```

**Stop a single distro (terminate):**
```powershell
wsl --terminate <DistroName>
# or:
wsl -t <DistroName>
```

**Update WSL to latest:**
```powershell
wsl --update
```


### 1.6 Where are “my files”? (Windows vs Linux mental model)
- Windows drive **C:\\** is mounted at **/mnt/c/** inside WSL.  
- Your Windows profile `C:\Users\<you>` is **/mnt/c/Users/<you>** in WSL.  
- Your Linux home (inside the distro) is **/home/<you>** (fastest place for projects on WSL 2).

**Quick map:**

| Windows (examples)                     | Linux (WSL) equivalent                   | Use / Notes |
|---------------------------------------|------------------------------------------|-------------|
| `C:\` / `D:\`                         | `/mnt/c` / `/mnt/d`                      | Windows drives mounted under `/mnt` by default. |
| `C:\Users\<you>`                      | `/mnt/c/Users/<you>`                     | Your Windows profile as seen from Linux. |
| `C:\Windows\System32`                 | `/usr/bin`, `/bin`, `/sbin`              | System binaries live under `/usr/bin` etc. |
| `C:\Program Files`, `Program Files (x86)` | `/usr`, `/opt`                         | Installed software and optional packages. |
| Global settings (Registry, etc.)      | `/etc`                                   | Text‑based configs, e.g., `/etc/ssh/ssh_config`. |
| Temp files                            | `/tmp`, `/var/tmp`                       | Temporary and variable data. |

> **Performance tip:** On WSL 2, keep your **Git repos and projects in the Linux home** (`/home/<you>`) for best speed; accessing large codebases directly under `/mnt/c` can be slower.


### 1.7 Configuring WSL (per‑distro & global)
**Per‑distro (`/etc/wsl.conf`)** — controls auto‑mount, networking, interop, default user, and (on Windows 11) boot behaviors. Example:
```ini
# /etc/wsl.conf
[automount]
enabled=true
root=/mnt/
options="metadata,umask=022,fmask=000"

[network]
generateResolvConf=true

[interop]
enabled=true
appendWindowsPath=true

[user]
default=<your-linux-username>

[boot]
# Enable systemd on newer WSL
systemd=true
```

**Global (`%UserProfile%\.wslconfig`)** — controls the WSL 2 VM (memory, CPUs, swap, GUI apps, networking). Example:
```ini
# C:\Users\<You>\.wslconfig
[wsl2]
memory=6GB             # cap RAM (optional)
processors=4           # limit vCPUs (optional)
swap=2GB
localhostForwarding=true
guiApplications=true   # WSLg (GUI apps) on by default on Win 11
```
> After changing config, run `wsl --shutdown` so settings take effect on next launch.


### 1.8 Running Linux GUI apps
On Windows 11 (and newer Windows 10 builds), WSL can run GUI apps (Wayland/X11) with WSLg. Try:
```bash
sudo apt update && sudo apt install -y x11-apps
xeyes     # simple GUI demo
```
GUI apps show up like normal Windows windows (Alt‑Tab, Start‑menu, etc.).


### 1.9 Essential WSL command reference
```powershell
wsl --help                      # full help
wsl --list --online             # show available distros
wsl --list --verbose            # list installed distros + state + WSL version
wsl --set-default-version 2     # default to WSL 2
wsl --set-version Ubuntu 2      # convert a specific distro
wsl --distribution Ubuntu        # run a specific distro
wsl --user root                  # run as a specific user
wsl --shutdown                   # stop all distros + the VM
wsl --terminate Ubuntu           # stop a single distro
wsl --export Ubuntu ubuntu.tar   # backup a distro
wsl --import UbuntuNew C:\WSL ubuntu.tar   # import a distro
wsl --mount <DiskPath>           # attach/mount a physical disk
wsl --unmount <DiskPath>         # unmount a disk
wsl --status                     # show kernel, default distro, etc.
wsl --update                     # update the WSL package/kernel
```


### 1.10 Case study — “Full‑stack dev on a Windows laptop”
**Scenario:** You’re a .NET/Node dev on Windows who needs Linux tooling (Docker, npm, Python).  
**Goal:** Put your web app in `~/apps/web`, run services in WSL, edit in VS Code, and test from Windows browsers.

1) Create a project dir in Linux home:
```bash
mkdir -p ~/apps/web && cd ~/apps/web
```
2) Initialize a Node app and run a simple server:
```bash
sudo apt update && sudo apt install -y nodejs npm
npm init -y
npm install express
printf "const e=require('express')(); e.get('/',(_,r)=>r.send('Hello WSL')); e.listen(3000);" > server.js
node server.js
```
3) In Windows, visit **http://localhost:3000**.  
4) Open the folder in **VS Code** and use the “WSL” remote to edit files (keeps them in Linux for best speed).


### 1.11 Case study — “Data science on WSL”
**Scenario:** You use Windows Office apps but want Linux Python tools.  
**Goal:** Create an isolated Python env and run Jupyter.  
```bash
sudo apt update && sudo apt install -y python3-pip python3-venv
python3 -m venv ~/.venvs/ds
source ~/.venvs/ds/bin/activate
pip install jupyterlab pandas matplotlib
jupyter lab --no-browser --port 8888
```
Open the provided localhost URL in your Windows browser. Save notebooks under `~/projects` (Linux), not `/mnt/c`, for speed.


### 1.12 Practice: WSL fundamentals (15–30 min)
1. Open PowerShell (Admin) and run: `wsl --install` (skip if already installed).  
2. After reboot, run: `wsl -l -v`, `wsl --status`, `wsl --version`.  
3. Create a folder in your Linux home `~/play`, then create a file and list it:
   ```bash
   mkdir -p ~/play && cd ~/play
   echo "hello from WSL" > note.txt
   ls -la
   ```
4. From Windows, open the same folder via Explorer:  
   - In WSL: `explorer.exe .` (opens Windows File Explorer to your current Linux directory).
5. Edit `.wslconfig` (Windows side) to set `memory=4GB`, then `wsl --shutdown` and relaunch your distro.  
6. Stop just your distro: `wsl -t <YourDistroName>` and verify with `wsl -l -v`.  
7. Export your distro to a backup tar and re‑import it to a new name.


---

## Chapter 2 — Files, Paths & Navigation (Windows analogies)

### 2.1 Moving around
```bash
pwd           # print working directory
ls -la        # detailed list
cd /etc       # change directory
cd ~          # back to home
```

**Windows analogy:** `cd`, `dir`, `where` ↔ `cd`, `ls`, `which`.  
**Path separators:** `\` (Windows) vs `/` (Linux).  
**Absolute vs relative paths:** `C:\Users\You\Desktop` ↔ `/home/you/Desktop`.

### 2.2 Creating, removing, copying
```bash
mkdir -p ~/lab/logs
touch ~/lab/notes.txt
cp ~/lab/notes.txt ~/lab/notes.bak
rm ~/lab/notes.bak
mv ~/lab/logs ~/lab/archive-logs
```

### 2.3 Practice (10–15 min)
- Make `~/lab/photos`, copy any image from Windows into it using Explorer (`\\wsl.localhost\<YourDistro>\home\<you>\lab\photos`).  
- From Linux, `ls -lh photos` and `file photos/<name>` to inspect.  
- Create a `~/bin` folder and add it to your `PATH` in `~/.bashrc`.


---

## Chapter 3 — Packages & Updates

### 3.1 Debian/Ubuntu (apt)
```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y git curl htop build-essential
```

### 3.2 Fedora (dnf) & Arch (pacman) quick refs
```bash
# Fedora
sudo dnf check-update
sudo dnf install tree

# Arch
sudo pacman -Syu
sudo pacman -S tree
```

### 3.3 Practice
- Install `ripgrep` (`rg`) and `jq`.  
- Use `rg -n "TODO" -S ~/apps` to search recursively.  
- Use `jq` to pretty‑print a JSON file.


---

## Chapter 4 — Text & the Shell

### 4.1 Pipes & redirection
```bash
cat /etc/passwd | cut -d: -f1 | sort | uniq | tee users.txt
```
### 4.2 Grep, less, head/tail, wc
```bash
grep -ni "ssh" /etc/ssh/sshd_config
head -20 /var/log/dpkg.log
tail -f /var/log/syslog
wc -l users.txt
```
### 4.3 Editors: nano & vim
```bash
nano users.txt
vim users.txt
```

### 4.4 Practice
- Use `curl -s https://example.com | wc -c` to count bytes.  
- Build a one‑liner that shows the **top 5** most common shell in `/etc/passwd`.


---

## Chapter 5 — Users & Permissions

### 5.1 Who am I? Groups?
```bash
whoami
id
groups
```
### 5.2 File modes
```bash
touch demo && ls -l demo
chmod u+x demo
chown $USER:$USER demo
```

### 5.3 Practice
- Create a `team` group and a shared dir with group‑write permissions under `~/shared`.  
- Explain how this compares to NTFS ACLs on Windows.


---

## Chapter 6 — Processes, Services & systemd on WSL

### 6.1 What’s running?
```bash
ps aux | head
top            # or: htop
```
### 6.2 Signals
```bash
sleep 1000 &  # background
kill %1       # or: kill -TERM <pid>
```
### 6.3 systemd on modern WSL
- Enable in `/etc/wsl.conf`:  
  ```ini
  [boot]
  systemd=true
  ```
- Then `wsl --shutdown` and re‑open the distro.  
- `systemctl status` should work (services under WSL).

**Practice:** write and enable a simple systemd user service (e.g., a Python HTTP server).


---

## Chapter 7 — Networking (Windows cross‑checks)

### 7.1 IPs, DNS, ports
```bash
hostname -I
ip a
ss -tulpn        # listening sockets
```
**Windows cross‑check:** Use `netstat -ano` or Task Manager → Performance → Open Resource Monitor.

### 7.2 Localhost interop
- Services started in WSL 2 are reachable from Windows at **localhost** (default).  
- Quick test:
  ```bash
  cd ~/apps/web && python3 -m http.server 8080
  ```
  Then browse **http://localhost:8080** in Windows.

### 7.3 Practice
- Start an SSH server in WSL and connect from Windows’ `ssh`.  
- Map the result of `hostname -I` in WSL to `ipconfig` in Windows.


---

## Chapter 8 — Scripting Essentials

### 8.1 Variables, loops, functions
```bash
#!/usr/bin/env bash
set -euo pipefail

greet() { echo "Hello, $1"; }

for name in Alice Bob Charlie; do
  greet "$name"
done
```
Make it executable with `chmod +x script.sh` and run `./script.sh`.

### 8.2 Practice
- Write a backup script that tars `~/projects` to `~/backups/projects-$(date +%F).tar.gz`.  
- Write a log filter that prints only lines containing “ERROR” from a mixed log.


---

## Chapter 9 — Troubleshooting & Power Tips

- `wsl --update` regularly.  
- After editing `.wslconfig` or `wsl.conf`, **restart WSL**: `wsl --shutdown`.  
- If DNS breaks, recreate `/etc/resolv.conf` or set `generateResolvConf=false` and manage it yourself.  
- Use `wsl --export` to back up distros before risky changes.  
- Prefer storing active repos in Linux home for speed; copy outputs to `/mnt/c` when needed.


---

## Appendix A — Quick Windows ⇆ Linux Command Pairs

| Do this in Windows | Do this in Linux |
|---|---|
| `dir` | `ls -la` |
| `cd` | `cd` |
| `copy`, `xcopy`, `robocopy` | `cp`, `rsync` |
| `type` | `cat`, `less` |
| `findstr` / `Select-String` | `grep` |
| `where` | `which`, `type -a` |
| `tasklist` / `Get-Process` | `ps`, `top`, `htop` |
| `ipconfig` / `Get-NetIPConfiguration` | `ip a`, `hostname -I` |
| `sc`, `Get-Service` | `systemctl` |


---

## Appendix B — Memory hooks (how to remember key WSL commands)

- **Shutdown All:** *“Big red button”* → `wsl --shutdown`  
- **Terminate One:** *“t for terminate”* → `wsl -t Ubuntu`  
- **List Verbose:** *“l v = list verbose”* → `wsl -l -v`  
- **Set Default Version:** *“sdv”* → `wsl --set-default-version 2`  
- **Export/Import:** *“backup/restore”* → `wsl --export`, `wsl --import`  

Happy hacking! Keep one PowerShell and one Linux terminal open side‑by‑side while you practice.