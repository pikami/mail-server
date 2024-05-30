# mail-server

This repository contains the scripts, configuration files, and Ansible playbooks for my email server setup. After running my own email service for seven years and redoing the setup multiple times, I've decided to document everything here to streamline maintenance.

Feel free to read, copy, use, and suggest improvements.

## Ansible configurations

Available roles:

- firewall - configures firewall
- initial-setup
  - applies available system patches
  - upgrades all installed packages
  - installs nano and curl
  - disables ssh password logins
  - adds ssh public key
- mail-primary - installs and configures opensmtpd with dovecot and sive
- mail-secondary - installs and configures opensmtpd as a backup mail receiver
- prometheus-exporters - installs prometheus exporters
- ssl - generates ssl certificates and adds renew cron job
- vpn - configures wireguard vpn

Available playbooks:

- primary-mail.yml - sets up a primary mail server, uses `vars-mx1.yml` for configuration
- secondary-mail.yml - sets up a secondary mail server, uses `vars-mx2.yml` for configuration

The hosts are taken from the `inventory.yml` file:

```yml
all:
  hosts:
    mx1:
      ansible_host: <primary mail server ip>
    mx2:
      ansible_host: <secondary mail server ip>
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
# replace "secondary.yml" with the playbook you want to run
ansible-playbook -i inventory.yml secondary.yml
```
