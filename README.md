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

*Notice* &mdash; No any additional configurations like inventory file or `ansible.cfg`.

## Generate private and public keys

```bash
ssh-keygen -t rsa -b 4096 -m PEM -f keyname
```

Replace `keyname` with your name (e. g. `myserver`). Enter passphrase if you want to (or skip this step).

This command will create two files: private key (without extension) and public key (ends with `.pub`).

## Run a command

1. Prepare the command with your arguments.

```bash
ansible-playbook \
    setup-ssh.yml -i 123.123.123.123, \
    -u root \
    --become \
    --ask-pass \
    --extra-vars "admin_username=admin admin_password=verysecretpassword public_key_path=/path/to/keyname.pub ssh_port=22222"
```

* Change IP `123.123.123.123` to your server's IP address (keep character "`,`").
* Replace `admin_user` value with YOURNAME (if needed).
* Replace `public_key_path` value with your public key path.
* Replace `ssh_port` value with any other allowed number (due to security reasons).

2. Run the command. Enter `root`'s password.

## Test it with a new connection

```bash
ssh -p 22222 -i /path/to/key admin@123.123.123.123
```