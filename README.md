# ansible-monitoring
Ansible Playbook to manage some monitoring tools


## Requirements

* [Ansible](https://www.ansible.com/) - Tested with Ansible v2.3.1.0


## Simple flow to Configure Elasticsearch servers

**NOTE**: In our production environment, we provide to the elasticsearch servers a persistent storage device to store all we need.

1. Become ROOT:
   * ```sudo su -```

2. Retrieve GIT repository of the project:
   * ```apt-get install git```
   * ```cd /opt ; git clone https://github.com/malavolti/ansible-monitoring.git```

3. Create your ```.vault_pass.txt``` that contains the encryption password (this is needed ONLY when you use Ansible Vault):
   * ```cd /opt/ansible-monitoring```
   * ```openssl rand -base64 64 > .vault_pass.txt```

4. Create the right inventory file/files about your IdP servers by following the template provided:
   * ```inventories/development/development.ini``` for your development servers.
   * ```inventories/production/production.ini``` for your production servers.
   * ```inventories/test/test.ini``` for your test servers.

5. Create the monitoring configuration file by copying/filling the templates:
   * ```/opt/ansible-monitoring/#_environment_#/host_vars/FQDN.yml-template```

   into proper ```/opt/ansible-monitoring/#_environment_#/host_vars/FQDN.yml``` file.
   This file will provide to Ansible all variables needed to instance elasticsearch servers.

6. Encrypt the monitoring configuration files with Ansible Vault (Optional: this is needed ONLY when you need Ansible Vault):
   * ```cd /opt/ansible-monitoring```
   * ```ansible-vault encrypt inventories/#_environment_#/host_vars/FQDN.yml --vault-password-file .vault_pass.txt```

7. Execute this command to run Ansible on develoment inventory and to instance new virtual machine:
   * ```ansible-playbook site.yml -i inventories/development/development.ini --vault-password-file .vault_pass.txt```


## Useful Commands

```
--- development.ini ---
# Put here IP or FQDN of your Elasticsearch Servers
[Debian-Elasticsearch]
elasticsearch1.example.org
elasticsearch2.example.org
-----------------------
```

1. Test that the connection with the server(s) is working:
   * ```ansible all -m ping -i /opt/ansible-monitoring/inventories/#_environment_#/#_environment_#.ini -u debian```
   ("```debian```" is the user used to perform the SSH connection with the client to synchronize)

2. Get the facts from the server(s):
   * ```ansible HOST_NAME -m setup -i /opt/ansible-monitoring/inventories/#_environment_#/#_environment_#.ini -u debian```

   Examples:
      * without encrypted files:
         ```ansible HOST_NAME -m setup -i /opt/ansible-monitoring/inventories/#_environment_#/#_environment_#.ini -u debian```
      * with encrypted files:
         ```ansible HOST_NAME -m setup -i /opt/ansible-monitoring/inventories/#_environment_#/#_environment_#.ini -u debian --vault-password-file .vault_pass.txt```

      ("```.vault_pass.txt```" is the file you have created that contains the encryption password)

3. Encrypt files:
   * ```ansible-vault encrypt inventories/#_environment_#/host_vars/all.yml --vault-password-file .vault_pass.txt```
   * ```ansible-vault encrypt inventories/#_environment_#/host_vars/monitoring-client.yml --vault-password-file .vault_pass.txt```

4. Decrypt Encrypted files:
   * ```ansible-vault decrypt inventories/#_environment_#/host_vars/all.yml --vault-password-file .vault_pass.txt```
   * ```ansible-vault decrypt inventories/#_environment_#/host_vars/monitoring-client.yml --vault-password-file .vault_pass.txt```

5. View Encrypted files:
   * ```ansible-vault view inventories/#_environment_#/host_vars/all.yml --vault-password-file .vault_pass.txt```
   * ```ansible-vault view inventories/#_environment_#/host_vars/monitoring-client.yml --vault-password-file .vault_pass.txt```

## Authors

#### Original Author and Development Lead

* Marco Malavolti (marco.malavolti@gmail.com)
