# ansible-monitoring

Ansible Playbook to manage some monitoring tools (tested on Debian 9 "Stretch" servers):
   * [Kibana](https://www.elastic.co/products/kibana)
   * [Elasticsearch](https://www.elastic.co/products/elasticsearch)
   * [Rsyslog](https://www.rsyslog.com/)
   * [Rsnapshot](http://rsnapshot.org/)
   * [Check_MK](http://mathias-kettner.com/index.html)

## Requirements

* [Ansible](https://www.ansible.com/) - Tested with Ansible v2.4.0.0

## HOWTO configure the monitoring servers with this ansible playbook

**NOTE**: In our production environment, we provide elasticsearch/rsyslog servers with a persistent storage device to store all we need.

1. Become ROOT:
   * `sudo su -`

2. Retrieve GIT repository of the project:
   * `apt-get install git`
   * `cd /opt ; git clone https://github.com/[malavolti|ConsortiumGARR|GEANT]/ansible-monitoring.git`
   * `cd /opt/ansible-monitoring ; git clone https://github.com/[malavolti|ConsortiumGARR|GEANT]/ansible-monitoring-inventories inventories`

3. Create your `.vault_pass.txt` that contains the encryption password (this is needed ONLY when you use Ansible Vault):
   * `cd /opt/ansible-monitoring`
   * `openssl rand -base64 64 > .vault_pass.txt`

4. Create the right inventory file/files about your servers by following the templates provided:
   * `inventories/development/development.ini` for your development servers.
   * `inventories/production/production.ini` for your production servers.
   * `inventories/test/test.ini` for your test servers.

5. Create the monitoring configuration file/files by following the templates provided(example for 'production' environment):
   * `/opt/ansible-monitoring/inventories/production/host_vars/checkmk.example.org.yml`
   * `/opt/ansible-monitoring/inventories/production/host_vars/elasticsearch1.example.org.yml`
   * `/opt/ansible-monitoring/inventories/production/host_vars/kibana.example.org.yml`
   * `/opt/ansible-monitoring/inventories/production/host_vars/data-backups.example.org.yml`
   * `/opt/ansible-monitoring/inventories/production/host_vars/logs.example.org.yml`

   These files will provide to Ansible all variables needed to instance each server.

6. Check the "`inventories/files`" directory to understand what files each server needs and provide them.

7. Encrypt the monitoring configuration files with Ansible Vault (Optional: this is needed ONLY when you need Ansible Vault).
   Example for 'production' environment:
   * `cd /opt/ansible-monitoring`
   * `ansible-vault encrypt inventories/production/host_vars/FQDN.yml --vault-password-file .vault_pass.txt`

8. Execute this command to run Ansible on production inventory and configure your servers:
   * `ansible-playbook site.yml -i inventories/production/production.ini --vault-password-file .vault_pass.txt`

## Useful Commands

1. Test that the connection with the server(s) is working:
   * `ansible all -m ping -i /opt/ansible-monitoring/inventories/#_environment_#/#_environment_#.ini -u debian`
   ("`debian`" is the user used to perform the SSH connection with the client to synchronize)

2. Get the facts from the server(s):
   * `ansible HOST_NAME -m setup -i /opt/ansible-monitoring/inventories/#_environment_#/#_environment_#.ini -u debian`

   Examples:
      * without encrypted files:
        `ansible HOST_NAME -m setup -i /opt/ansible-monitoring/inventories/#_environment_#/#_environment_#.ini -u debian`
      * with encrypted files:
        `ansible HOST_NAME -m setup -i /opt/ansible-monitoring/inventories/#_environment_#/#_environment_#.ini -u debian --vault-password-file .vault_pass.txt`

      ("`.vault_pass.txt`" is the file you have created that contains the encryption password)

3. Encrypt files:
   * `ansible-vault encrypt inventories/#_environment_#/host_vars/FQDN.yml --vault-password-file .vault_pass.txt`

4. Decrypt Encrypted files:
   * `ansible-vault decrypt inventories/#_environment_#/host_vars/FQDN.yml --vault-password-file .vault_pass.txt`

5. View Encrypted files:
   * `ansible-vault view inventories/#_environment_#/host_vars/FQDN.yml --vault-password-file .vault_pass.txt`

## Authors

#### Original Author and Development Lead

* Marco Malavolti (marco.malavolti@gmail.com)
