# Linux System Administration Lab

Hands-on practice of core Linux system administration tasks on Ubuntu 24.04, completed in a browser-based Killercoda Ubuntu playground (root access, temporary environment).

**Author:** Eric Clark · Chicago, IL · [LinkedIn](https://www.linkedin.com/in/eric-clark-817b3b70/)

---

## Skills practiced

| Area | What I did | Key commands |
| --- | --- | --- |
| Files and navigation | Moved around the file system, created folders and files | `pwd`, `ls -l`, `cd`, `mkdir`, `cat` |
| Users and groups | Created a user and a group, added the user to the group | `useradd`, `groupadd`, `usermod`, `id` |
| Permissions | Locked down a file and changed its ownership | `chmod`, `chown` |
| Patching | Checked for updates, identified security updates, applied a patch | `apt update`, `apt list --upgradable`, `apt install --only-upgrade` |
| Software | Installed and verified a package | `apt install`, `dpkg -l` |
| Backups | Created and verified a compressed backup | `tar -czf`, `tar -tzf` |
| Monitoring | Checked uptime, load, disk and memory | `uptime`, `df -h`, `free -h` |
| Networking | Tested connectivity and DNS, reviewed listening ports | `ping`, `ss -tuln`, `ip a`, `ip route` |

---

## Lab 1: Files and navigation

```bash
whoami
pwd
mkdir lab
cd lab
echo "Hello from Eric" > notes.txt
cat notes.txt
cd ..
ls -l
```

**Result:** Logged in as `root` in `/root`. Created `/root/lab` and `notes.txt`, then read the file back.

**What I learned:** reading `ls -l` output. In `drwxr-xr-x 2 root root ... lab`, the `d` marks a directory, and `rwxr-xr-x` gives the permissions for owner, group and everyone else.

---

## Lab 2: Users, groups and permissions

```bash
useradd -m tech1
id tech1
groupadd qc
usermod -aG qc tech1
id tech1
cd lab
ls -l notes.txt
chmod 600 notes.txt
ls -l notes.txt
chown tech1:qc notes.txt
ls -l notes.txt
```

**Result:**

- New user: `uid=1001(tech1) gid=1001(tech1) groups=1001(tech1)`
- After adding to the group: `groups=1001(tech1),1002(qc)`
- File permissions, step by step:

| Step | `ls -l notes.txt` |
| --- | --- |
| Before | `-rw-r--r-- 1 root root` |
| After `chmod 600` | `-rw------- 1 root root` |
| After `chown tech1:qc` | `-rw------- 1 tech1 qc` |

**Troubleshooting note:** running `groupadd qc` a second time returned `groupadd: group 'qc' already exists`. The error confirmed the group was already in place; nothing needed to be fixed.

---

## Lab 3: Security updates and software installation

```bash
apt update
apt list --upgradable
apt install -y tree
tree /root
apt install --only-upgrade -y sudo
dpkg -l sudo tree
```

**Result:**

- `apt list --upgradable` showed packages with pending updates. Those marked `noble-security` are security updates for Ubuntu 24.04 (codename Noble), including `sudo`, `systemd` and `xz-utils`.
- Applied the security update to `sudo` and verified it with `dpkg -l`:

```
ii  sudo  1.9.15p5-3ubuntu5.24.04.4  amd64  Provide limited super user privileges to specific users
ii  tree  2.1.1-2ubuntu3.24.04.2     amd64  displays an indented directory tree, in color
```

`ii` means the package is installed correctly.

**Troubleshooting note:** running `tree` before installing it returned `Command 'tree' not found, but can be installed with: apt install tree`. Installing the package fixed it.

---

## Lab 4: Backups

```bash
cd ~
tar -czf lab-backup.tar.gz lab
ls -lh lab-backup.tar.gz
tar -tzf lab-backup.tar.gz
```

**Result:** Created `lab-backup.tar.gz` (184 bytes). Listing its contents without extracting confirmed the backup held `lab/` and `lab/notes.txt`.

**What I learned:** a backup isn't finished until it's verified.

---

## Lab 5: Server health check

```bash
uptime
df -h
free -h
```

**Result:**

| Check | Reading | Assessment |
| --- | --- | --- |
| Uptime and load | Up 1 hour 17 minutes, load average 0.00 | Server idle |
| Disk (`/dev/vda1` on `/`) | 19G total, 5.5G used, 13G free (30%) | Healthy; would watch above 80–90% |
| Memory | 1.9G total, 481M used, 1.4G available; swap 0B used | No memory pressure |

---

## Lab 6: Networking

```bash
ip a
ip route
cat /etc/resolv.conf
ping -c 4 8.8.8.8
ping -c 4 google.com
ss -tuln
```

**Result:**

- Server address: `172.30.1.2` on interface `enp1s0`.
- `ping 8.8.8.8`: 4 sent, 4 received, 0% packet loss, about 12 ms. Internet reachable by IP.
- `ping google.com`: resolved to `142.250.217.14`, 4 received, 0% loss. DNS working.
- `ss -tuln` showed the listening services:

| Port | Address | Service | Exposure |
| --- | --- | --- | --- |
| 22/tcp | `0.0.0.0` and `[::]` | SSH (remote login) | Open to the network |
| 53 | `127.0.0.53`, `127.0.0.54` | Local DNS resolver | This machine only |
| 68/udp | `172.30.1.2` | DHCP client | Gets the server's IP address |
| 40200–40305/tcp | various | Playground platform services | Platform-specific |

**What I learned:** troubleshooting connectivity step by step. If pinging an IP works but pinging a name fails, the problem is DNS. Also, `0.0.0.0` means a service is open to the network, while `127.0.0.x` means only the local machine can use it, which matters for security reviews.

---

## Screenshots

Add terminal screenshots to a `screenshots/` folder and link them here:

- `screenshots/lab2-permissions.png`
- `screenshots/lab3-security-update.png`
- `screenshots/lab4-backup.png`
- `screenshots/lab5-health-check.png`
- `screenshots/lab6-networking.png`

---

## Next steps

- Schedule automatic backups with `cron`
- Manage services with `systemctl` and read logs with `journalctl`
- Practice on Red Hat Enterprise Linux (RHEL) commands (`dnf`, `firewalld`)
- Configure a basic firewall
