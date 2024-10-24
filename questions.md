# Questions

## Project overview

### 1. What is a virtual machine?
A VM can run programs and apps (like an OS) but it uses software allocated ressources instead of a physical computer: each VM on one machine get **virtual** components (RAM, Hard drive, etc) as portions or the physical hardware.

### 2. Why you chose Debian (over Rocky)?
- Debian is more user-firendly / easier to use
- Debian community (and help to get online) is bigger
- School's computers use Ubuntu, which is based on Debian. And so is my personal computer.

### 3. The basic difference between CentOS and Debian
- CentOS (downstream version of RedHat Entreprise Linux) moved to CentOS (upstream version of RHEL). Rocky was created as ""100% bug-for-bug compatible" with RHEL.
- CentOS/Rocky is for companies / Debian is for individuals\
**TODO: CONTINUE THIS**

### 4. The purpose of Virtual Machines
- Several VM can work on a single machine: it reduces cost and makes easier maintenance
- Each VM is totally isolated from others: it guaranties security

### 5. `aptitude` vs `apt`


### 6. What is APPArmor?


## Simple setup

- UFW service started right after boot: `sudo service ufw status`
- SSH service started right after boot: `sudo service ssh status`
- OS system: `$ uname -a`

## User
- Check that a user with login <42_login> is added and belong to groups "user42" and "sudo": ``
- Verify password policy rules:
  - Create a new user: ``
  - Assign it a password respecting subject rules:
  - Explain how rules as been applied: `Update of files 'aaa' and 'aaa'`
  - Create a group named "evaluating" & assign the new user to it & check:\
    `aaa`\
    `aaa`\
    `aaa`
  - Explain the advantages of this password policy: 

## Hostname and partitions

- Check hostname matches the format <42_login>: \
  `sudo logout`\
  `aaa`
- Modify hostname to the evaluator login & restart the machine & check:\
  `aaa`\
  `aaa`\
  `aaa`
- Restore machine to its original name: `aaa`
- Check that partitions match the structure in the subject: `aaa`
- What is LVM about and how it works: `aaa`

## SUDO

- Check sudo program is installed: `aaa`
- Assign the new user to "sudo" group" `Already done in a previous step`
- Explain the value and operation of sudo using example of your choice: `Aaa`
- Verify that "/var/log/sudo/" folder exists and has at least one file: `aaa`
- Check the content of the files in this folder (you should se a history of commands used with sudo): `aaa`
- Run a command via sudo & check that the log file was updated: `aaa`

## UFW

- Check "UFW" program is installed and works properly:\
  `aaa`\
  `aaa`
- What is UFW and the value of using it: `Aaa`
- List all the actives rules in UFW (a rule must exist for port 4242): `aaa`
- Add a new rule to open port 8080 & check is has been added to the list:\
  `aaa`\
  `aaa`
- Delete this new rule: `aaa`

## SSH

- Check SSH is installed and works properly:\
  `aaa`\
  `aaa`
- What is SSH and the value of using it: `Aaa`
- Verify that SSH only uses port 4242: `aaa`
- Use SSH to log in with newly created user: `aaa`
- Make sure we cannot use SSH with root: `aaa`

## Script monitoring

- Explain the monitoring.sh script: `Aaa`
- What is CRON: `Aaa`
- How the script was setup to run every 10 minutes & make it run every minutes instead (without modifying the script iteself: `Aaa`

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
