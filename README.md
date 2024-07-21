- This repository tries to set up an LDAP server on a host machine and utilizes the LDAP user federation of Keycloak to manage users and let MariaDB users authenticate via the defined users in the LDAP. 
- The Ansible is used to set up components of this testing lab.
  - It is assumed that one needs to install the components on a Debian-based operating system to keep the implementation simple.
  - The LDAP server is set up on a machine's host to reduce the setup complexity in the beginning.
    - The OpenLDAP package installation and reconfiguration process with `apt` and `dpkg-reconfigure` usually uses an interactive UI. The default deb package configurations are kept in [`./roles/ldap/files/debconf-slapd.conf`](./roles/ldap/files/debconf-slapd.conf) and set during the installation step to automate the configuration noninteractively.
    - The `slappasswd` utility can be used to generate a password for `userPassword`. The utility is part of the `slapd` package in Ubuntu 20.04.
  - A MariaDB server configured in a Docker container to authenticate its users via PAM (Pluggable Authentication Module) plugin. In this case, LDAP is used.
    - The secret of `root` and `user02` users of the MariaDB service are encrypted in [`./group_vars/secret/db.enc`](./group_vars/secret/db.enc).
    - To run its playbook, one can use `ansible-playbook --ask-vault-pass ./playbooks/setup_test_db.yml`.
    - The secret is `test`.
  - The Keycloack and its back-end database are set up via a docker compose file. The LDAP user federation and its mapper are also created in this step.
    - A Nginx is also set up to expose the keycloak panel for a specified domain.
    - A TLS certificate is issued using [`acme.sh`](https://github.com/acmesh-official/acme.sh) for the specified domain.
  - In this setup, users must be created with the same name in MariaDB and LDAP to log in to the MariaDB CLI.
    - New created and registered users will sync in LDAP automatically. In addition, one need to create same user win PAM authentication in the MariaDB service.
      - One solution to automate this process could be a cron job to sync LDAP and MariaDB users.
      - Anotherone could be, let manage users whth Ansible and create users in LDAP and MariaDB at same time.
- This setup still needs development to be production ready, and some considerations must be accounted for.
