Ansible Playbook to Install Icinga2, IcingaWeb2, PostgreSQL, Apache2
====================================================================

There is a Vagrantfile to spin up a Virtualbox Ubuntu 14.04 VM to use the playbook against.

Dependencies used:
* Mac OSX El Capitan
* Ansible 2.0
* Vagrant 1.8.1
* Virtualbox 4.3.34

The following playbook utilizes a dynamic inventory source, but the Python script simply parses a static inventory file, hosts.yaml
```
ansible-playbook ./deploy_icinga2.yml -i inventory.py
```

CLI Steps used to create the Icinga IcingaWeb2 Server
=====================================================

Install Icinga2 Server:
-----------------------

```    
add-apt-repository ppa:formorer/icinga
sudo apt-get update
sudo apt-get install icinga2
sudo apt-get install postgresql
sudo apt-get install icinga2-ido-pgsql
sudo icinga2 feature enable ido-pgsql
sudo icinga2 feature enable command
sudo service icinga2 restart
sudo wget -O – http://packages.icinga.org/icinga.key | apt-key add –
sudo add-apt-repository 'deb http://packages.icinga.org/ubuntu icinga-trusty main'
sudo apt-get update
sudo apt-get install icingaweb2
addgroup --system icingaweb2
usermod -a -G icingaweb2 www-data
icingacli setup config directory --group icingaweb2;
chown -R www-data /etc/icingaweb2/
vi /etc/php5/apache2/php.ini #change date.timezone to "America/Denver"
sudo apt-get install php5-json php5-gd php5-imagick php5-pgsql php5-intl php5-ldap php5-mysql
service apache2 restart
```

Create user and db for the wizard details at <host>/icingaweb2/setup:
---------------------------------------------------------------------
```
cd /tmp
sudo -u postgres psql -c "CREATE ROLE postgresuser WITH LOGIN PASSWORD 'pw'"; # the first connection details for icingaweb2
sudo -u postgres createdb -O postgresuser icinga_users # the first connection details for icingaweb2
cat /etc/icinga2/features-enabled/ido-pgsql.conf # use the ido db settings, name, user, password and use for the 2nd connection details
```

Create Token for wizard:
------------------------
```
icingacli setup token create 
```

Wizard
======

IcingaWeb2 Database Connection 1
--------------------------------
See the ```deploy_icinga2.yml``` file for changing the Icinga postgres user/role and database details.


![database 1]: https://raw.githubusercontent.com/chrisjalinsky/ansible-icinga2/master/wizard_images/database_connection1.png "Icingaweb2 Database details created in Ansible playbook"


IcingaWeb2 Database Connection 2
--------------------------------
The second database validation uses the details in the ```/etc/icinga2/features-enabled/ido-pgsql.conf``` file, which is output at the end of the ansible playbook as well.


![database 2]: https://raw.githubusercontent.com/chrisjalinsky/ansible-icinga2/master/wizard_images/database_connection2.png "Icing IDO Database details created in features file"

IcingaWeb2 Successful Installation 1
------------------------------------
![success 1]: https://raw.githubusercontent.com/chrisjalinsky/ansible-icinga2/master/wizard_images/icingaweb2_success1.png "Installation success 1"


IcingaWeb2 Successful Installation 2
------------------------------------
![success 2]: https://raw.githubusercontent.com/chrisjalinsky/ansible-icinga2/master/wizard_images/icingaweb2_success2.png "Installation success 2"