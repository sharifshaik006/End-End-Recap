# Linux in Production Systems

Purpose: Deep understanding of Linux as the foundation of cloud, Kubernetes, and telecom infrastructure — including file system structure, essential commands, and production troubleshooting.

---

# 1. Simple Definition

Linux is an operating system that manages hardware and allows applications to run safely and efficiently.

Everything in cloud infrastructure ultimately runs on Linux.

---

# 2. Where Linux Sits in Architecture

Hardware
↓
Linux Kernel
↓
System Services (systemd, networking)
↓
Container Runtime (Docker/containerd)
↓
Kubernetes
↓
Application Pods

If Linux layer fails → everything above fails.

---

# 3. Linux Root File System Structure (Very Important)

The Linux filesystem starts at:


This is called the **root directory**.

Below are critical directories every DevOps engineer must know.

---

## 🔹 `/`
Root of the entire file system.

---

## 🔹 `/bin`
Essential user binaries.
Basic commands like:
- ls
- cp
- mv
- cat

---

## 🔹 `/sbin`
System binaries (administrative).
- ip
- reboot
- shutdown

---

## 🔹 `/etc`
Configuration files.

Examples:
- `/etc/ssh/sshd_config`
- `/etc/fstab`
- `/etc/hosts`
- `/etc/systemd/`

If system misbehaves → configs usually here.

---

## 🔹 `/var`
Variable data.

Very important subfolders:

- `/var/log` → logs
- `/var/lib` → app data (Docker, kubelet, DB)
- `/var/tmp`

Production debugging heavily involves `/var/log`.

---

## 🔹 `/home`
User directories.

---

## 🔹 `/root`
Home directory for root user.

---

## 🔹 `/usr`
User-installed software and libraries.

- `/usr/bin`
- `/usr/lib`
- `/usr/local`

---

## 🔹 `/opt`
Optional third-party software.

---

## 🔹 `/dev`
Device files.

Example:
- `/dev/sda`
- `/dev/null`

---

## 🔹 `/proc`
Virtual filesystem exposing process and kernel info.

Example:
- `/proc/cpuinfo`
- `/proc/meminfo`

---

## 🔹 `/sys`
Kernel and hardware information interface.

---

# 4. Essential Linux Commands (Production Critical)

---

## 📌 System Information

uname -a # Kernel version
hostname # Machine name
uptime # System load
whoami # Current user


---

## 📌 Process Management

ps aux # List processes
top # Real-time process usage
htop # Better top (if installed)
kill <pid> # Stop process
kill -9 <pid> # Force kill




Kubernetes pods are just Linux processes.

---

## 📌 Memory & CPU
free -m # Memory usage
top # CPU usage
vmstat # Memory statistics


---

## 📌 Disk & Storage

df -h # Disk usage
du -sh * # Folder size
lsblk # Block devices
mount # Mounted filesystems


Disk full → applications crash.

---

## 📌 Networking

ip a # Show IP addresses
ip route # Routing table
ss -tulnp # Listening ports
netstat -tulnp # Legacy port check
ping # Connectivity test
curl # Test HTTP endpoint
traceroute # Path to host



If pod cannot connect → check Linux networking first.

---

## 📌 Logs

journalctl -xe # System logs
journalctl -u nginx # Service logs
tail -f /var/log/syslog


Logs live mostly in `/var/log`.

---

## 📌 Services (systemd)

systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl enable nginx


Most production services managed by systemd.

---

## 📌 File Permissions

ls -l
chmod 755 file
chown user:group file


Permission format:



rwx r-x r-x


Owner | Group | Others

---

# 5. Linux User & Permission Model

Three levels:

- User
- Group
- Others

Permission types:

- Read (4)
- Write (2)
- Execute (1)

Example:



chmod 644 file.txt


= rw-r--r--

Security depends on proper permission design.

---

# 6. Linux and Containers

Containers use Linux kernel features:

- Namespaces (process isolation)
- cgroups (resource limits)
- Overlay filesystem
- iptables networking

Docker runs on Linux.

Kubernetes worker nodes are Linux servers.

If node CPU/memory exhausted → pods fail.

---

# 7. Linux in EKS Architecture

EKS Worker Node (Linux EC2 instance):

Runs:

- kubelet
- container runtime
- kube-proxy
- CNI plugin

Pods are Linux processes inside namespaces.

Everything is process + network + file system.

---

# 8. Production Failure Scenarios (Linux Level)

---

## Scenario 1: High CPU

Check:


top
ps aux --sort=-%cpu


Possible:
- Infinite loop
- Traffic spike

---

## Scenario 2: Out of Memory (OOMKilled)

Check:


dmesg | grep -i oom
free -m


Fix:
- Increase memory
- Set proper limits

---

## Scenario 3: Disk Full

Check:


df -h
du -sh /var/log/*


Logs often fill disk.

---

## Scenario 4: Service Not Listening

Check:


ss -tulnp
systemctl status service


---

## Scenario 5: Node NotReady in Kubernetes

Check:


systemctl status kubelet
journalctl -u kubelet
top
df -h


Often resource exhaustion.

---

# 9. Linux Security Basics

- Disable password SSH
- Use SSH keys
- Restrict root login
- Configure firewall (ufw / iptables)
- Keep packages updated
- Remove unused services

Linux security = cluster security.

---

# 10. Troubleshooting Framework (Bottom-Up)

Always debug in this order:

1. Hardware
2. CPU / Memory
3. Disk
4. Network
5. Service
6. Application

Do not jump directly to Kubernetes without checking Linux.

---

# 11. Quick Production Cheat Sheet

- `/etc` = config
- `/var/log` = logs
- `df -h` = disk
- `free -m` = memory
- `top` = CPU
- `ss -tulnp` = open ports
- `systemctl status` = service health
- `journalctl` = system logs


ln == link (this is a command )

Linux Folder Structure
----------------------
/root --> directory for root user
/bin --> binaries, while system is starting these binaries will load
/boot --> When linux system starts, Linux starts boot loading from here
/dev = devices --> hard disk, keyboard, devcices
/etc = extra configuration = /etc/nginx
/home = Linux stores all users in home location
/lib = operating system libraries
/media = cd or dvd drives
/mnt = you can mount other disks
/opt = optional = if you are installing any package manually, prefer opt location
/proc = pid info and processor info
/sbin = admin commands
/tmp = it is temp, not an important
/usr = user commmands, libraries, documents
/var = variables.. mainly log files

Linux is the runtime backbone of:

- Cloud
- Kubernetes
- Telecom
- DevOps
- Containers

If you master Linux deeply,
everything above becomes easier to reason about.


Linux all commands
--- linux uses forward slashes /

Pwd-- Present working directory
ssh-keygen -t rsa -b 4096 -f <file-name>
absolute path starts from / (root)
relative path refers to current working directory
ls --- listing subdirectories
ls -l-- long format
ls -la--- all files including hidden
ls -lt..... latest files
ls -ltr----starts from old files
ls -lr.... reverse alphabetical order

CRUD --> Create Read Update Delete
touch-- to create a file
cat-- to read the content in the file
cat > sample enter text and press enter ctrl+d
will create file with the content
cat > --- replace old content
cat >> ---- append/add new content to the file
rm <file-name> remove file
rm -r --- r is recursive which mean delete inside the file aswell
rm -rf --- force remove
rmdir--- remove the directory empty fail to remove if it has files
mkdir--- creates a directory

copy
cp <source-file> <destination-path>
move

mv <source-file> <destination-path>

grep
used to search word in the file
 grep <word-to-find> <file-name>
 grep -i 

find 
used to find the files/directories
examples for find command:


find <path> <options> <actions>

find .  -iname  example.txt
This searches for files named example.txt in the current directory and its subdirectories.

curl--- to get text directory in terminal
wget --- to download softwares, files and folders

cut-- used to get fragments of text using delimiter
cut -d ":" -f1
space is called as delimiter

awk-- used to process text.. usually to divide text in column format

awk -F "/" '{print $1F}

head and tail-- first and last 10 lines of the files


Editors

vi and vim

vim is advanced version of vi

vim <file-name>
:q-- quit
:!q-- force quit without saving
:wq-- write and quit and save
:set nu---- numbers
:set hlsearch-- highlights the search
:noh--- no highlights
:/ <word-to-find>---- word to find from top
:? <word-to-find>--- finds from the bottom
:%d ---- delete everything
:s/new/NEW--- replace the word new to NEW in the cursor line
:%s/new/NEW--replace every line but only first occurence
:%s/new/NEW/g--- replace in the whole line
:%s<word-to-find>/<replace-word>gi--- replaces everywhere all occurences in the file

There are Two modes in this Esc mode
insert mode

Permissions

Read-R------4
write-W-----2
Execute-X---1
4+2+1==== 7
User/Owner---U 
group--g 
others-----o
u+g+o=== 7
chmod ----- change permissions
ch o+X--- Giving permissions to others
ch 761--- giving permissions to others to execute


User-Management

/etc/passwd-- will have user details
id user-group ---group details
getent group--- group info


usermod--- modifies the user
usermod -g or -aG <group-name> <user-name>
usermod -g developer sharif
sharif is the primary user changed from sharif to developer
usermod -aG testing sharif
sharif is added to the secondary group not removed from any other groups
useradd-- add/create the user
userdel--- removes the user

in linux there is only one primary group you can delete the primary group but can change or modify the primary group

whenever you create a user by default linux will create a group on that user

while you want to delete the user if you delete the user directly the group created by default will not be deleted

so you need to change the primary group to his own group
and then delete the user

change ownership of the user

chown -R sharif:shaik .ssh

Package management

one package depends on other package

yum list all
yum list installed

service management

0-65535=====65536 ports which means services are available
systemctl start,status,enable,disable,restart,stop

enable-- helps service to start automatic after reboot or restart of servers




---

End of Document
