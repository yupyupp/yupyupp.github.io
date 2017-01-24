# Intermediate Linux

[Stephen Haffner](https://haffner.me) (yupyupp)

PGP: 3258 3651 0512 d37e

<img height="auto" width="25%" src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tux.svg/2000px-Tux.svg.png"/>

---

## Things I assume
 - You have used the command line

<img src="http://i.imgur.com/QHbj2Ob.png"/>

*All beards were sourced from [linuxbeard.com](http://linuxbeard.com)*

---

## Order of things
1. Tips for working in the terminal
2. Deeper into the Linux System
3. List of linux tools and demos

---

### General Shell Usage
 - ^c, ^d, ^z fg/bg
 - tmux/screen
 - git
 - vi(m)/nano/emacs/ed
 - Pipes and redirects

---

# The almighty tab key

---

### SSH Config

```
#~/.ssh/config

# School
Host cse
    Hostname stdlinux.cse.ohio-state.edu
    User haffners
    X11Forwarding yes

# Personal
Host mx1 nyc1
    Hostname mx1.kd8zev.net
    User kd8zev

Host tempi
    Hostname tempi.kd8zev.net
    User yupyupp
```

---

#### SSH Keys
```bash
# Commands on local.host
ssh-keygen
#Generating public/private rsa key pair.
#Enter file in which to save the key (/home/yupyupp/.ssh/id_rsa):
#Enter passphrase (empty for no passphrase):
#Enter same passphrase again:
#Your identification has been saved in /home/yupyupp/.ssh/id_rsa.
#Your public key has been saved in /home/yupyupp/.ssh/id_rsa.pub.
#The key fingerprint is:
#SHA256:pD+I7FqUTm6cRX60pzSXKXCYYlQdF51fsoi8evJuVGU yupyupp@local.host
#The keys randomart image is:
#+---[RSA 2048]----+
#|    ......oo .   |
#       ...
#|  ...    *o      |
#+----[SHA256]-----+
ssh-copy-id user@remote.host
```

```
# ~/.ssh/authorized_keys on remote.host
ssh-rsa AAAAB3NzaC1y ... c2EAAAADAQABA yupyupp@stanchion.kd8zev.net
ssh-rsa AAAAB3NzaC1y ... AADAQABAAABAQ shaffner@OAR-NC130023.local
ssh-rsa AAAAB3NzaC1y ... 9VZjArr0su2wh yupyupp@deoch.kd8zev.net
ssh-rsa AAAAB3NzaC1y ... jrUkK9bfWNlze yupyupp@local.host
```

---

## The Linux System
 - GNU vs. Linux
 - Really likes files
 - Really likes trees
 - users/groups
 - X11 (server/client)

---

### Processes

<img width="70%" height="auto" src="https://c3.staticflickr.com/9/8236/8599814378_de071de408_b.jpg"/>

 - Can only ```fork()``` and ```execvp()``` *(replace)*
 - Are in a tree
 - Are controlled with signals

---

### Common Signals

 - ```SIGHUP``` - 1
 - ```SIGINT``` - 2
 - ```SIGKILL``` - 9
 - ```SIGTERM``` - 15
 - ```SIGCONT```
 - ```SIGSEV```

---

## Boot Process
<img src="https://upload.wikimedia.org/wikipedia/commons/7/76/Sievi_Boots_(2).jpg" width="%70" height="auto"/>

---

#### POST
<img src="https://upload.wikimedia.org/wikipedia/commons/9/92/POST_P5KPL.jpg" width="%70" height="auto"/>



POST = Power On Self Test

---

#### BIOS
<img src="https://upload.wikimedia.org/wikipedia/commons/0/05/Award_BIOS_setup_utility.png" width="%70" height="auto"/>


 - Search for the bootloader

---

#### BOOTLOADER/Grub
<img src="https://upload.wikimedia.org/wikipedia/commons/1/12/GRUB_screenshot.png" width="%100" height="auto"/>


 - The kernel to load
 - The partition to use for "/"
 - The initramfs to use

---

#### Kernel/Initramfs
<img src="http://jochen.hin.de/diverses/initramfs-tools-bug2.jpg" width="%50" height="auto"/>


 - Kernel starts in initrmafs
 - Mounts the true root

---

#### CHROOT and launch Init
<img src="https://2.bp.blogspot.com/-0l8tfOUEOmA/UNBxtmCeh9I/AAAAAAAADEU/UR3HCqcuNaE/s1600/Screenshot+-+181212+-+12:35:12.png" width="%100" height="auto"/>


 -  Chroots to the new root
 -  Launches the init process
 -  Init launches services and mounts disks

---

## Directory Hierarchy
*(aka this mess)*

```bash
/-+            # Everything!
  |-bin/       # Binaries (duh)
  |-boot/      # Things to start linux
  |-dev/       # Devices?
  |-etc/       # Et cetra? Um
  |-home/      # Where users live
  |-lib{64}/   # Libraries
  |-proc/      # Processes?
  |-root/      # But I thought "/" was root..
  |-sbin/      # Also binaries I guess..
  |-sys/       # System files
  |-tmp/       # Temporary files
  |-usr/       # User files! .. Wait, what about "/home"?
  |-var/       # Variable files! .. But how is it different from "/tmp"?
```

---

### Why  ```/```, ```/usr```, and ```/usr/local``` exist

```
/-+
  |-bin/
  |-sbin/
  |-etc/
  |-lib/
  |-lib64/
  |-usr-+
        |-bin/
        |-sbin/
        |-lib/
        |-lib64/
        |-local-+
                |-bin/
                |-sbin/
                |-lib/
                |-lib64/
```

*If it can be stored in all these places how do I know where to look for a program?*

---

### The answer: ```$PATH```

```bash
echo $PATH
# /home/yupyupp/bin/:/usr/local/bin:/usr/bin:/bin
su -c "echo $PATH"
# /usr/local/sbin:/usr/local/bin:/usr/local/sbin:/usr/sbin:/usr/bin:/sbin:/bin
```

 - List of directories to search for executables
 - Searches in order
 - Generally prioritize local versions over others
 - Similar concept for libraries and ```ld```

---

### Where config files are stored

```bash
/-+
  |etc-+
       |-httpd/
       |-mariadb/
       |-ssh/
       |-fstab      # Pronounced "ef-stab"
       |-passwd     # User account information
       |-shadow     # Password information
```

---

### Data Directories

```bash
/-+
  |-home-+
         |-yupyupp-+
                   |-Documents/
  |-usr-+
        |-share/
        |-local/share/
  |-var-+
        |-log/
        |-spool/mail
        |-www-data/
        |-mariadb/
```

---

### "Virtual" Directories

```bash
/-+
  |-dev-+
        |-tty0
        |-sda
        |-sda1
        |-zero
        |-urandom
  |-proc-+
         |-meminfo
         |-loadavg
  |-sys-+
        |-devices/batt0/level
  |-temp/   # Not quite a virutal directory, but usually in memory
```

---

## System Daemons
*(aka demons)*

<img width="70%" height="auto" src="http://1.bp.blogspot.com/-XkkBW_WRzWQ/UBqk3BQug-I/AAAAAAAAADc/1H-0diXsJOI/s1600/gargoyle%2Bmoon.jpg"/>

---

### General Info
 - Usually run as their own users
 - Config files in ```/etc```
 - Modifiable files and logs in ```/var```
 - Managed by the "init system" *(PID 1)*

---

### SysVInit
 - Runlevels:
    - 0: Halt
    - 1: Singleuser (root)
    - 2: Basic multiuser (no network)
    - 3: Normal multiuser
    - 4: Custom
    - 5: Multiuser with GUI
    - 6: Reboot
 - Big folders of scripts and links to scripts
 - Run in alphabetical order

---

### Usage
 - ```service NAME start/stop```
 - ```service NAME status```
 - ```chkconfig --level # NAME on/off```
 - ```init ##```


 - *Start/stop service*
 - *Tells you if service is running*
 - *Enable/disable for runlevel(s) #*
 - *Switch runlevel*

---

### SystemD
 - Replacing SysVInit
 - Uses ```.service``` files for data about services
 - Uses targets instead of runlevels
    - local-fs.target
    - remote-fs.target
    - networking.target
    - multi-user.target
    - graphical.target
 - "Manages" services more than SysVInit
    - Collects logs
    - Restarts failed services
    - Allows more fine-grained ordering

---

### Usage
 - ```systemctl start/stop NAME.service```
 - ```systemctl enable/disable NAME.service```
 - ```systemctl status NAME.service```
 - ```systemctl set-default NAME.target```
 - ```systemctl isolate NAME.target```
 - ```journalctl -eu NAME```


 - *Start/stop service*
 - *Enable/disable service*
 - *See status info about service and part of log*
 - *Change default target to boot to*
 - *Change to target*
 - *Display **E**rrors that NAME has printed*

---

### Common Services
 - sshd
 - apache/nginx
 - postgress/mariadb/mysql/redis
 - sendmail/postfix/exim/dovecot/spamassassin/amavisd


 - *OpenSSH Server*
 - *Web servers*
 - *Database servers*
 - *Mail-related servers*

---

## Installing software

<img width="70%" height="auto" src="http://screenshots.en.sftcdn.net/en/scrn/12000/12648/installshield-professional-3.jpg"\>

---

<img width="70%" height="auto" src="https://i.stack.imgur.com/jC7MY.png"\>

---

<img width="70%" height="auto" src="https://i.ytimg.com/vi/2mzBpO0sJPk/maxresdefault.jpg"\>

---

<img width="70%" height="auto" src="https://i.ytimg.com/vi/NQPoD5AT30g/hqdefault.jpg"\>

---

### Package Managers!

 - Debian: ```apt-get```
 - RedHat: ```yum```/```dnf``` (Fedora)
 - ArchLinux: ```pacman```
 - Gentoo: ```emerge```

---

### Repositories!

 - Giant collections of packages (.deb, .rpm, .tar.gz)
 - ```ppa://``` for Ubuntu
 - EPEL and RPMFusion for RedHat systems
 - Arch User Repository (AUR) for arch linux

---

### Compiling from source
 - Install to ```/usr/local/bin``` ```/usr/local/share```
 - Understand how [makefiles](https://www.gnu.org/software/make/manual/html_node/Introduction.html) work first
 - ```./configure```
 - ```make```
 - ```sudo make install```

---

## Linux Tools

<img width="70%" height="auto" src="https://upload.wikimedia.org/wikipedia/commons/d/d8/Huge_collection_of_tools_in_a_store_in_Chloride,_a_ghost_town_in_New_Mexico,_USA_-_July_2013.jpg"/>

---

### General Tools
 - man/apropos
 - find/locate
 - sed
 - awk
 - grep
 - sudo/su

---

#### System information
 - uname -a
 - df/du
 - ls{blk,pci,usb}
 - dstat

---

#### Process Management
 - kill/killall/pkill
 - pidof/pgrep
 - top/htop
 - ps
 - lsof
 - gdb
 - dtrace

---

#### File Manipulation
 - chown
 - chmod
 - touch
 - dd/pv
 - cat

---

### Networking Tools

 - ssh
 - telnet (and its super cool uses!)
 - nc
 - iptables/firewalld

---

#### Info about me:

 - ifconfig/ip addr
 - netstat
 - tcpdump
 - host

---

#### Info about others:

 - nslookup
 - dig
 - nmap
 - traceroute

---

### Security Tools
 - gpg
 - openssl
 - wireshark/tcpdump
 - macchanger
 - hashcat
 - john *the ripper*

---

<img height="auto" width="60%" src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tux.svg/2000px-Tux.svg.png"/>

