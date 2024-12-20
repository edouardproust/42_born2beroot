# born2beroot

## 1. Install VirtualBox


1. [Download the minimal image ("netinst") of Debian](https://www.debian.org/distrib/netinst) + choose "amd64"
2. Create VirtualBox:\
   `$ sudo apt update && sudo apt upgrade -y && sudo apt install virtualbox -y`\
   `$ virtualbox &` (To run VirtualBox. `&` is for opening it in the background)\
   In VirtualBox UI:
   - Click "New"
   - Name and locate it, select .iso file, check "Skip Unattended Installation" + click "Next"
   - Set hardware (default) + Virtual Hard disk size (30GB) + Click "Finish"
3. OS installation:
   - Select the VM newly created and click "Start"
   - Choose "Graphical install", then set language, location, keyboard type and locals
   - Set hostname, root and create the new user as detailled in `credentials.txt`
   - [Create partitions manually](https://github.com/gemartin99/Born2beroot-Tutorial/blob/main/README_EN.md#81--manual-partition), install no dependencies & install GRUB

*Start a VM: `VBoxManage startvm "<born2beroot>"`*\
*Stop a VM: `VBoxManage controlvm "<born2beroot>" poweroff`*

## 2. Install sudo & add users / groups

Reboot the VM, enter encryption key, login as <42_login>, **then:**\
`$ su` (then enter root password))\
`# apt update && apt upgrade && apt install sudo`\
`# sudo adduser <eproust> <sudo>`\
`# sudo addgroup <user42>`\
`# sudo adduser <eproust> <user42>`\
Maybe need to **reboot** to see the changes: `sudo reboot`\
**Check groups and users:**\
`$ getent passwd <eproust>` (file `/etc/passwd`)\
`$ getent group <sudo|user42>` (file `/etc/group`)

## 3. SSH

1. **Install SSH:** `$sudo apt install openssh-server -y` *(Verify installation: `$ sudo service ssh status`)*
2. **Edit SSH parameters:**\
`$ sudo nano /etc/ssh/sshd_config` (Uncomment & update this lines: `Port 4242` + `PermitRootLogin no`)\
`$ sudo nano /etc/ssh/ssh_config` (Uncomment & update this line: `Port 4242`)
3. **Verify SSH Port:**\
`$ sudo service ssh restart`\
`$ sudo service ssh status`
4. **Port forwarding 4242 > 4243** (if port 4242 is already taken): `VirtualBox` > `select VM` > `Settings` > `Network` > `Advanced` > `Port Forwarding` > Create new rule: `Name: SSH, Protocol: TCP, Host IP: (leave empty), Host: 4243, Guest IP: (leave empty), Guest port: 4242`
	
## 4. UFW (Uncomplicated Firewall)
	
`$ sudo apt install ufw`\
`$ sudo ufw enable`\
`$ sudo ufw allow <4242>` *(Verify: `$ sudo ufw status`)*\
**/!\ Continue this tuto on your computer terminal:** `$ ssh <eproust>@localhost -p 4243`
	
## 5. Update sudo config

`$ sudo visudo -f /etc/sudoers.d/<config_global>` + add this inside (`$ man sudoers` + scroll to `SUDOERS OPTIONS`): 
```bash
Defaults  passwd_tries=3
Defaults  badpass_message="Wrong password you stupid!"
Defaults  logfile="/var/log/sudo/general_log"
Defaults  log_input, log_output
Defaults  iolog_dir="/var/log/sudo"
Defaults  requiretty
Defaults  secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```
*Using `visudo` to edit the file is recommended: it check syntax before saving*\
*Verify syntax errors: `$ sudo chmod 0440 /etc/sudoers.d/<config_global> && sudo visudo -c`*

## 6. Strong password policy
	
`$ sudo nano /etc/login.defs` + update lines (`man login`):
```
PASS_MAX_DAYS 30
PASS_MIN_DAYS 2
PASS_WARN_AGE 7
```

Apply these rules to existing users too (`man chage`):\
`$ sudo chage -M <30> <root|eproust>`\
`$ sudo chage -m <2> <root|eproust>`\
*(`$ sudo chage -W <7> <root|eproust>`)*\
`$ sudo chage -l <root|eproust>` # To verify the change

Add more options for password in /etc/pam.d/common:\
`$ sudo apt install libpam-pwquality -y`\
`$ sudo nano /etc/pam.d/common-password` + update the line as follow (`man pam_pwquality`):
```
password   requisite   pam_pwquality.so retry=3 minlen=10 ucredit=-1 dcredit=-1 lcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
```

## 7. CRON script

`$ sudo nano </home/eproust/>monitoring.sh` and save the script below\
`sudo chmod +x </home/eproust/>monitoring.sh` (to make it executable)\
`$ sudo crontab -e -u root` (To add a new CRON job)\
At the bottom of the file, add this line:\
`*/10 * * * * /home/eproust/monitoring.sh | wall` (Piped into `wall` to broadcast to all users)

```bash
#!/bin/bash
arch=$(uname -a)
cpu=$(lscpu | awk '/Socket\(s\)/ {print $2}')
vcpu=$(lscpu | awk '$1 == "CPU(s):" {print $2}')
ram_used=$(free --mega | awk '$1 == "Mem:" {printf "%.2f", $3 / 1000}')
ram_total=$(free --mega | awk '$1 == "Mem:" {printf "%.2f", $2 / 1000}')
ram_used_percent=$(awk -v a=$ram_used -v b=$ram_total 'BEGIN {printf "%.2f", a / b * 100}')
disk_used=$(df -BM | awk '$1 ~ "/dev/" {tot += $3} END {printf "%.2f", tot / 1000}')
disk_total=$(df -BM | awk '$1 ~ "/dev/" {tot += $2} END {printf "%.2f", tot / 1000}')
disk_used_percent=$(awk -v a=$disk_used -v b=$disk_total 'BEGIN {printf "%.2f", a / b * 100}')
cpu_load=$(vmstat | tail -1 | awk '{printf "%.1f", 100 - $15}')
last_boot=$(who -b | awk '{print $(NF - 1), $NF}')
lvm=$(if lsblk | grep -q "lvm"; then echo "yes"; else echo "no"; fi)
tcp=$(ss -t | grep -c "ESTAB")
user_log=$(who -u | awk '{print $1}' | sort -u | wc -l)
ipv4=$(hostname -I | awk '{print $1}')
mac=$(ip addr | awk '/link\/ether/ {print $2}')
sudo_cmds=$(journalctl _COMM="sudo" | grep "COMMAND=" | wc -l)

echo "#Architecture: $arch
#CPU physical: $cpu
#vCPU: $vcpu
#Memory Usage: $ram_used/${ram_total}GB ($ram_used_percent%)
#Disk Usage: $disk_used/${disk_total}GB ($disk_used_percent%)
#CPU load: ${cpu_load}% 
#Last boot: $last_boot
#LVM use: $lvm
#Connexions TCP: $tcp ESTABLISHED
#User log: $user_log
#Network: IP $ipv4 ($mac)
#Sudo: $sudo_cmds commands"
```

## 8. Install Wordpress
	
**Download & setup WP:**\
`$ sudo mkdir /var/www/html` (Location for websites on Linux systems)\
`$ sudo apt install wget -y`\
`$ cd /tmp && wget https://wordpress.org/latest.tar.gz`\
`$ tar -xf latest.tar.gz && rm latest.tar.gz`\
`$ sudo mv wordpress /var/www/html/`\
`$ sudo chown -R www-data:www-data /var/www/html/wordpress/` (Give ownership to www-data: user made for)\
`$ sudo chmod -R 755 /var/www/html/wordpress/` (Give full permissions to the owner)

**Setup server** (Lighttpd):\
`$ sudo apt install lighttpd -y` *(Verify: `$ sudo service lighttpd status`)*\
`$ sudo ufw allow 80 && sudo ufw status` (Firewall white-listing)\
Enable fast-cgi module:\
`$ sudo apt install -y php php-cgi php-mysql`\
`$ sudo lighty-enable-mod fastcgi-php`\
`$ sudo service lighttpd force-reload`

**Setup database** (MariaDB):\
`$ sudo apt install mariadb-server -y` *(Verify: `$ sudo service mariadb status`)*\
`$ sudo mysql_secure_installation` + Answer the questions (in order: n, n, Y, Y, Y, Y)\
Setup tables:\
`$ sudo mariadb` + write these commands:
```
> CREATE DATABASE wordpress_db; # Verify: `> SHOW DATABASES;`
> GRANT ALL ON wordpress_db.* TO '<your_login_here>'@'localhost' IDENTIFIED BY '<your_passwd_here>'; # Verify: `> SELECT User, Host FROM mysql.user;`
> exit;
```

**Last steps:**\
Add port fowarding: 1672 > 80 for example\
In browser: http://localhost:1672/wordpress (NOT https) and finish setup:\
- database: wordpress_db
- username: <42_login>
- password: 12345

## 9. Extras
    
`sudo apt update && sudo apt upgrade -y`

Add system clock syncronization:\
`$ sudo apt install systemd-timesyncd`\
`$ timedatectl` (To check time sync is on)\
(If not activated: `$ sudo systemctl <start|enable> systemd-timesyncd`)

Add netcat & test TCP connexions:\
`$ sudo apt install tmux netcat`\
`$ tmux # to open tmux`. Ctrl+B then " to open a second terminal horizontaly. Ctrl+B then % for vertical. Ctrl+B then Arrows to switch between terminals.\
In terminal #1: `$ watch ss -t | grep -c "ESTAB"` (To visualize TCP estbalished connexions)\
In terminal #2: `$ nc -l 1234` (To open port 1234 and set a network). Then type "Hello"+Enter (for example) to send this to Terminal #1.\
In terminal #3: `$ nc localhost 1234` (To connext to serveur localhost)\

## 10. Get VB signature

`$ cd ~/Projects/born2beroot/`\
`$ sha1sum <born2beroot.vdi> > ~/Projects/born2beroot/signature.txt`
