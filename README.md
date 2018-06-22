Ansible Role: mongodb 
======================================

[![Build Status](https://travis-ci.org/entercloudsuite/ansible-mongodb.svg?branch=master)](https://travis-ci.org/entercloudsuite/ansible-mongodb)
[![Galaxy](https://img.shields.io/badge/galaxy-entercloudsuite.mongodb-blue.svg?style=flat-square)](https://galaxy.ansible.com/entercloudsuite/mongodb)  

Installs mongodb on Ubuntu 16.04 (Xenial)

## Requirement

This role requires Ansible 2.4 or higher.

## Role Variables

The role defines most of its variables in `defaults/main.yml`:

## Example Playbook

Run with default vars:

    - name: Install MongoDB
      hosts: all

      pre_tasks:
        - name: install percona repo
          shell: wget https://repo.percona.com/apt/percona-release_0.1-4.$(lsb_release -sc)_all.deb && dpkg -i percona-release_0.1-4.$(lsb_release -sc)_all.deb
          args:
          creates: /etc/apt/sources.list.d/percona-release.list
          executable: /bin/bash
          chdir: /tmp

      roles:
        - role: ansible-mongodb
	  vars:
            mongodb_package: percona-server-mongodb-36
            mongodb_storage_dbpath: /var/lib/mongodb
            mongodb_version: "3.6"
            mongodb_net_bindip: "0.0.0.0"
            mongodb_pymongo_from_pip: true
            mongodb_user_update_password: "on_create"
            mongodb_manage_service: true
            mongodb_user: mongod
            mongodb_pidfile_path: /var/run/mongod.pid
            mongodb_net_maxconns: 65536
            mongodb_net_port: 27017
            mongodb_security_authorization: "enabled"
            mongodb_security_keyfile: /etc/mongodb-keyfile
            mongodb_processmanagement_fork: true
            mongodb_systemlog_destination: "file"
            mongodb_systemlog_logappend: true                                        
            mongodb_systemlog_path: /var/log/mongodb/{{ mongodb_daemon_name }}.log   
            mongodb_logrotate: true
            mongodb_set_parameters: { "enableLocalhostAuthBypass": "true", "authenticationMechanisms": "SCRAM-SHA-1,MONGODB-CR" }
            mongodb_daemon_name: mongod
            mongodb_logrotate_options:
              - compress
              - copytruncate
              - daily
              - dateext
              - rotate 7
              - size 10M
            mongodb_keyfile_content: |
              8pYcxvCqoe89kcp33KuTtKVf5MoHGEFjTnudrq5BosvWRoIxLowmdjrmUpVfAivh
              CHjqM6w0zVBytAxH1lW+7teMYe6eDn2S/O/1YlRRiW57bWU3zjliW3VdguJar5i9
              Z+1a8lI+LS9pWynbv9+Ao0aXFjSJYVxAm/w7DJbVRGcPhsPmExiSBDw8szfQ8PAU
              2hwRl7nqPZZMMR+uQThg/zV9rOzHJmkqZtsO4UJSilG9euLCYrzW2hdoPuCrEDhu
              Vsi5+nwAgYR9dP2oWkmGN1dwRe0ixSIM2UzFgpaXZaMOG6VztmFrlVXh8oFDRGM0
              cGrFHcnGF7oUGfWnI2Cekngk64dHA2qD7WxXPbQ/svn9EfTY5aPw5lXzKA87Ds8p
              KHVFUYvmA6wVsxb/riGLwc+XZlb6M9gqHn1XSpsnYRjF6UzfRcRR2WyCxLZELaqu
              iKxLKB5FYqMBH7Sqg3qBCtE53vZ7T1nefq5RFzmykviYP63Uhu/A2EQatrMnaFPl
              TTG5CaPjob45CBSyMrheYRWKqxdWN93BTgiTW7p0U6RB0/OCUbsVX6IG3I9N8Uqt
              l8Kc+7aOmtUqFkwo8w30prIOjStRrokxNsuK9KTUiPu2cj7gwYQ574vV3hQvQPAr
              hhb9ohKr0zoPQt31iTj0FDkJzPepeuzqeq8F51HB56RZKpXdRTfY8G6OaOT68cV5
              vP1O6T/okFKrl41FQ3CyYN5eRHyRTK99zTytrjoP2EbtIZ18z+bg/angRHYNzbgk
              lc3jpiGzs1ZWHD0nxOmHCMhU4usEcFbV6FlOxzlwrsEhHkeiununlCsNHatiDgzp
              ZWLnP/mXKV992/Jhu0Z577DHlh+3JIYx0PceB9yzACJ8MNARHF7QpBkhtuGMGZpF
              T+c73exupZFxItXs1Bnhe3djgE3MKKyYvxNUIbcTJoe7nhVMrwO/7lBSpVLvC4p3
              wR700U0LDaGGQpslGtiE56SemgoV
            
            mongodb_replication_replset: rs0
            mongodb_user_admin_name: admin
            mongodb_user_admin_password: AdminPassword
            mongodb_root_admin_name: root
            mongodb_root_admin_password: RootPassword
            mongodb_login_host: mongodb-0
            mongodb_replication_params:
              - { host_name: 10.6.0.43, host_port: "{{ mongodb_net_port }}", host_type: replica }
              - { host_name: 10.6.0.37, host_port: "{{ mongodb_net_port }}", host_type: replica }
              - { host_name: 10.6.0.44, host_port: "{{ mongodb_net_port }}", host_type: replica }

## Testing

Tests are performed using [Molecule](http://molecule.readthedocs.org/en/latest/).

Install Molecule or use `docker-compose run --rm molecule` to run a local Docker container, based on the [enterclousuite/molecule](https://hub.docker.com/r/fminzoni/molecule/) project, from where you can use `molecule`.

1. Run `molecule create` to start the target Docker container on your local engine.  
2. Use `molecule login` to log in to the running container.  
3. Edit the role files.  
4. Add other required roles (external) in the molecule/default/requirements.yml file.  
5. Edit the molecule/default/playbook.yml.  
6. Define infra tests under the molecule/default/tests folder using the goos verifier.  
7. When ready, use `molecule converge` to run the Ansible Playbook and `molecule verify` to execute the test suite.  
Note that the converge process starts performing a syntax check of the role.  
Destroy the Docker container with the command `molecule destroy`.   

To run all the steps with just one command, run `molecule test`. 

In order to run the role targeting a VM, use the playbook_deploy.yml file for example with the following command: `ansible-playbook ansible-mongodb/molecule/default/playbook_deploy.yml -i VM_IP_OR_FQDN, -u ubuntu --private-key private.pem`.  

## License

MIT
