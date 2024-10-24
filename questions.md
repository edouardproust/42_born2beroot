# Questions

### Project overview

- What is a virtual machine?
`A VM can run programs and apps (like an OS) but it uses software allocated ressources instead of a physical computer: each VM on one machine get virtual components (RAM, Hard drive, etc) as portions or the physical hardware.`
- Why you chose Debian (over Rocky)?
  ```
  - Debian is more user-firendly / easier to use
  - Debian community (and help to get online) is bigger
  - School's computers use Ubuntu, which is based on Debian. And so is my personal computer.
  ```
- The basic difference between CentOS and Debian
  ```
  - CentOS/Rocky history: Initially a downstream version of RedHat Entreprise Linux, it moved to CentOS Stream (upstream version of RHEL). Rocky was created as "100% bug-for-bug compatible" with RHEL.
  - Audience: CentOS/Rocky is better for companies / Debian is better for individuals
  - Stability & Updates Debian is extensive open-source & balancing stability and updates / CentOS is stability focus & has a predictable release cycle based on a schedule (for comapanies)
  - They use different package type (Debian: .deb / CentOS: .rpm) and package managers (Debian: apt / CentOS: yum & dnf)
  ```
- The purpose of Virtual Machines:
  ```
  - Several VM can work on a single machine: it reduces cost and makes easier maintenance
  - Each VM is totally isolated from others: it guaranties security
  ```
- 'aptitude' vs 'apt': `'aptitude' enhances 'apt' and 'apt-get': more user-friendly interface (interactive navigation), better dependency resolution, advanced search capabilities, allows viewing and managing package states, allows to simulate actions before execution, and can mark packages for auto-removal.`
- What is APPArmor: `A security module in the Linux kernel that allows the system administrator to restrict the capabilities of a program.`

## Simple setup

- UFW service started right after boot: `sudo service ufw status`
- SSH service started right after boot: `sudo service ssh status`
- OS system: `$ uname -a`

## User
- Check that a user with login <42_login> is added and belong to groups "user42" and "sudo": `getent group <sudo|user42>`
- Verify password policy rules:
  - Create a new user: `sudo adduser <test>`
  - Assign it a password respecting subject rules: `Test creating passwords in order and check erro messages: 'test', 'test1', 'test1A', 'test1Abcde', 'tes1Abcdef', 'HelloBcn123'`
  - Explain how rules as been applied:
    ```
    $ sudo visudo -f /etc/sudoers.d/<config_global>
    $ sudo nano /etc/login.defs
    $ sudo chage -M <30> <root|eproust>
    $ sudo chage -m <2> <root|eproust>
    ```
  - Create a group named "evaluating" & assign the new user to it & check:\
    ```
    $ sudo addgroup evaluating
    $ sudo adduser <test> evaluating
    $ getent group evaluating
    $ sudo chage -l <test>
    ```
  - Explain the advantages of this password policy: **TODO**

## Hostname and partitions

- Check hostname format: `hostname`
- Modify hostname to the evaluator login & restart the machine & check:\
  ```
  $ sudo nano /etc/hostname # And update login
  $ sudo nano /etc/hosts # And update login
  $ sudo reboot
  $ hostname
  ```
- Check that partitions match the structure in the subject: `lsblk`
- What is LVM about and how it works:\
  ```
  - Logical Volume Management allows to allocate space on storage devices in a more dynamic / flexible than using traditional partioning on logical - partitions: no need to unmount the volume and backup files, delete and create a new partition.
  - It uses command line to update partitions size: 'lvcreate', 'lvresize', 'lvremove', etc.
  - We created an encrypted logical (traditional) partition, that we divided into LVM sub-partitions for more flexibility.`
  ```

## SUDO

- Check sudo program is installed: `dpkg -s sudo`
- Assign the new user to "sudo" group" `sudo adduser <test> sudo`
- Explain the value and operation of sudo using example of your choice: `TODO`
- Verify that "/var/log/sudo/" folder exists and has at least one file: `sudo ls /var/log/sudo`
- Check the content of the files in this folder (you should se a history of commands used with sudo), then run a command via sudo & check that the log file was updated:
  ```
  sudo cat /var/log/sudo
  sudo apt update
  sudo cat /var/log/sudo
  ```

## UFW

- Check "UFW" program is installed & works properly: `sudo service ufw status`
- What is UFW and the value of using it: `A firewall management tool that simplifies configuring iptables to control incoming and outgoing network traffic based on defined rules.`
- List all the actives rules in UFW (a rule must exist for port 4242): `sudo ufw status`
- Add a new rule to open port 8080 & check is has been added to the list: `sudo ufw allow 8080`
- Delete this new rule:
  ```
  $ sudo ufw status numbered
  $ sudo ufw delete <rule_num>
  $ sudo ufw status numbered
  $ sudo ufw delete <v6_rule_num>
  ```

## SSH

- Check SSH is installed and works properly & Verify that SSH only uses port 4242: `sudo service ssh status`
- What is SSH and the value of using it: `TODO`
- Use SSH to log in with newly created user: `ssh <test>@localhost -p 4243`
- Make sure we cannot use SSH with root: `ssh root@localhost -p 4243` + enter password 3 times

## Script monitoring

- Explain the monitoring.sh script: `Aaa`
- What is CRON: `CRON is a time-based job scheduler in Unix-like systems. It automates tasks by running scripts or commands at specified intervals (e.g., daily, weekly) based on defined schedules in a configuration file called crontab.`
- How the script was setup to run every 10 minutes & make it run every minutes instead: `sudo crontab -e -u root` + change the line into: `* * * * * /home/<user>/monitoring.sh | wall`
- Stop and restart the CRON job (without changing the code):
  ```
  sudo service cron status
  
  ```


## Bonus

- Partitions setup recheck:
- Check Wordpress check:\
  `In browser: 'http://localhost:1672/wordpress'`
  `aaa` (Only installing the services required by the subject)
- Check additional service installed:\
  `aaa`
  `aaa`
  `aaa`
- Explain what you chose this additional service (why it is useful): `aaa`
