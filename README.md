# Simple Ansible playbook for initial SSH setup

I use this scenario to quickly setup new Virtual Servers, which are delivered with the root and password access by default.

What the playbook does:

* Disables `root` user.
* Disables login via password (only with private key).
* Adds a new user (e. g. `admin`) with full `sudo` access (`sudo` with password).
* Changes default SSH port `22` to any other.

## Requirements

* Ansible (tested on version 2.8.1)
* Root's password to destination server
* Server's distro must support `sudo` command.

*Notice* &mdash; No any additional configurations like inventory file or `ansible.cfg`.

## Generate private and public keys

```bash
ssh-keygen -t rsa -b 4096 -m PEM -f keyname
```

Replace `keyname` with your name (e. g. `myserver`). Enter passphrase if you want to (or skip this step).

This command will create two files: private key (without extension) and public key (ends with `.pub`).

## Run a command

1. Download the playbook.

```bash
wget https://raw.githubusercontent.com/neverwet/ansible-ssh-setup/master/setup-ssh.yml -o setup-ssh.yml
```

2. Prepare the command with your arguments.

```bash
ansible-playbook \
    setup-ssh.yml -i 123.123.123.123, \
    -u root \
    --become \
    --ask-pass \
    --extra-vars "admin_username=admin admin_password=verysecretpassword public_key_path=/path/to/keyname.pub ssh_port=22222"
```

* Change IP `123.123.123.123` to your server's IP address (keep character "`,`").
* Replace `admin_user` value with your name (if needed).
* Replace `admin_password` value with your password (use a strong password). This password used for `sudo` command.
* Replace `public_key_path` value with your public key path.
* Replace `ssh_port` value with any other allowed number (due to security reasons).

3. Run the command. Enter `root`'s password.

Example output:

```bash
PLAY [Setup SSH] ***************************************************************

TASK [Gathering Facts] *********************************************************
ok: [123.123.123.123]

TASK [Add a new user named admin] **********************************************
changed: [123.123.123.123]

TASK [Add admin user to the sudoers] *******************************************
changed: [123.123.123.123]

TASK [Deploy SSH key] **********************************************************
changed: [123.123.123.123]

TASK [Disable password authentication] *****************************************
changed: [123.123.123.123]

TASK [Change default SSH port] *************************************************
changed: [123.123.123.123]

TASK [Disable Root Login] ******************************************************
changed: [123.123.123.123]

RUNNING HANDLER [restart ssh] **************************************************
changed: [123.123.123.123]

PLAY RECAP *********************************************************************
123.123.123.123               : ok=8    changed=7    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

## Test it with a new connection

```bash
ssh -p 22222 -i /path/to/key admin@123.123.123.123
```

And run `sudo su` with your password for testing root access.
