# mail-server

This repository contains the scripts, configuration files, and Ansible playbooks for my email server setup. After running my own email service for seven years and redoing the setup multiple times, I've decided to document everything here to streamline maintenance.

Feel free to read, copy, use, and suggest improvements.

## Configuration

Ansible is used for configuration. The playbooks use a `vars.yml` file for settings. This file contains sensitive information, so it is not included in the repository. Below is an example of its structure:

```yml
---
ssh_public_key: "ssh-rsa AAAAB3...ak4EsUU="
mx1_domains:
  - mx1.pikami.org
mx1_mail_domain: "mx1.pikami.org"
mail_domains:
  - pikami.net
  - pikami.org
mail_users:
  - user: bob@pikami.org
    password: Password123
    virtuals:
      - "bob@pikami.net"
      - "bob.coolman@pikami.net"
  - user: alice@pikami.org
    password: Password123
    virtuals:
      - "alice@pikami.net"
```

The hosts are taken from the `inventory.yml` file:

```yml
all:
  hosts:
    mx1:
      ansible_host: 51.158.215.227
```

## Environment setup

Install python on remote hosts:

```sh
# replace "mx1" with server name
ansible -m raw -i inventory.yml -u root -a "pkg_add python%3.8" mx1
```

Install required ansible collections:

```sh
ansible-galaxy collection install ansible.posix
ansible-galaxy collection install community.general
```

Run playbooks

```sh
# replace "01-initial_setup.yml" with the playbook you want to run
ansible-playbook -i inventory.yml 01-initial_setup.yml
```

Current ansible playbooks:

- 01-initial_setup.yml
  - applies available system patches
  - upgrades all installed packages
  - installs nano, curl and git
  - disables ssh password logins
  - adds ssh public key
- 02-ssl.yml - generates ssl certificates and adds a renew cron job
- 03-mail.yml - installs and configures dovecot and opensmtpd