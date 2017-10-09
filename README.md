Role Name
=========

MySQL v5.7 installation with optionnals user/replication configurations.

[![build status](https://gitrep.services.local/ansible-middlewares/ansible-mysql/badges/develop/build.svg)](https://gitrep.services.local/ansible-middlewares/ansible-mysql/commits/develop)

Requirements
------------

- Ubuntu Xenial

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Role Variables
--------------

    mysql_port: 3306
    mysql_bind_address: 127.0.0.1
    mysql_use_lvm: true
    mysql_vg_name: vg_vroot
    mysql_lv_name: lv_mysql
    mysql_lv_size: 10g
    mysql_relaylog_space_limit: "16G"
    mysql_key_buffer_size: "16M"
    mysql_general_log: "0"

    mysql_replication: false
    mysql_replication_gtid: true
    mysql_replication_mode: "mm"
    mysql_replication_binformat: "MIXED"
    mysql_replication_username: "repli_user"
    mysql_backup_username: "backup_user"
    mysql_app_username: "dbapp_user"

    ssl_mode: false
    ssl_certificate_files:
      - 'ca-key.pem'
      - 'ca.pem'
      - 'server-key.pem'
      - 'server-cert.pem'
      - 'private_key.pem'
      - 'public_key.pem'
      - 'client-key.pem'
      - 'client-cert.pem'

    #
    # variables in secret.yml
    #
    mysql_root_password: "rootpw"
    mysql_replication_password: "repli_pass"
    mysql_backup_password: "backup_pass"
    mysql_app_password: "dbapp_pass"
    
    ### App Databases and Users
    mysql_db:
      - name: 'dbapp'
        replicate: yes


Hosts example - Master/Slave Replication
------------

    [bddservers]
    192.168.229.150  mysql_srv_id=1  mysql_replication_role='master'
    192.168.229.200  mysql_srv_id=2  mysql_replication_role='slave' mysql_master_ip='192.168.229.150'


Example Playbook - Master/Slave Replication
----------------

Playbook example:

    - name: deploy mysql with Master/Slave replication
      hosts: bddservers
      become: yes
      vars:
        - mysql_replication: true
        - mysql_replication_mode: "ms"	## Master/Slave
        - mysql_replication_username: 'repl_user'
        - mysql_backup_username: "backup_user"
        - mysql_app_username: "myuser"
        - mysql_bind_address: "0.0.0.0"
        - mysql_key_buffer_size: "32M"
        - mysql_general_log: true

        - mysql_db:
          - name: 'dbapp'
            replicate: 'yes'

        ### Put in secrets.yml ###
        - mysql_root_password: "passwd"
        - mysql_replication_username: "repl_user"
        - mysql_replication_password: "passwd"
        - mysql_backup_username: "backup_user"
        - mysql_backup_password: "passwd"
        - mysql_app_username: "app_user"
        - mysql_app_password: "passwd"


      roles:
        - ansible-mysql



Hosts example - Master/Master Replication
------------

    [bddservers]
    192.168.229.150  mysql_srv_id=1  mysql_replication_role='master' mysql_master_ip='192.168.229.200'
    192.168.229.200  mysql_srv_id=2  mysql_replication_role='master' mysql_master_ip='192.168.229.150'


Example Playbook - Master/Master Replication
----------------

Playbook example:

    - name: deploy mysql with Master/Master replication
      hosts: bddservers
      become: yes
      vars:
        - mysql_replication: true
        - mysql_replication_mode: "mm"	## Master/Master
        - mysql_replication_username: 'repl_user'
        - mysql_backup_username: "backup_user"
        - mysql_app_username: "myuser"
        - mysql_bind_address: "0.0.0.0"
        - mysql_key_buffer_size: "32M"

        - mysql_db:
          - name: 'dbapp'
            replicate: 'yes'

         ### Put in secrets.yml ###
        - mysql_root_password: "passwd"
        - mysql_replication_username: "repl_user"
        - mysql_replication_password: "passwd"
        - mysql_backup_username: "backup_user"
        - mysql_backup_password: "passwd"
        - mysql_app_username: "app_user"
        - mysql_app_password: "passwd"

      roles:
        - ansible-mysql


Hosts example - Standalone
------------

    [bddservers]
    192.168.229.150


Example Playbook - Standalone installation
----------------

    - name: deploy mysql standalone
      hosts: bddservers
      become: yes
      vars:
        - mysql_replication: false
        - mysql_key_buffer_size: "32M"
        - mysql_db:
            - name: "dbapp"
              replicate: "no"

        ### Put in secrets.yml ###
        - mysql_root_password: "passwd"
        - mysql_backup_username: "backup_user"
        - mysql_backup_password: "passwd"
        - mysql_app_username: "app_user"
        - mysql_app_password: "passwd"


Playbook launch
---------------

    export ANSIBLE_HOST_KEY_CHECKING=False; ansible-playbook -i hosts playbook.yml --ask-pass --ask-become-pass --tags installation

Tags
----

- [installation] : MySQL installation
- [rollback] : does nothing yet
- [testing] : Unit testing for MySQL

License
-------

BSD

Author Information
------------------

BSO ISL
