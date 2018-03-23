This Ansible role is my attempt to automate the deployment of [r10k](https://github.com/puppetlabs/r10k).

Please note, that this code has been tested in a lab environment only!

## What the role does

* Installs and configures r10k on the Puppet Server
* Installs and configures Gitolite on the server hosting the control repo
* Adds a system user on both servers to be used in r10k code management operations
* Sets up permissions and ssh key authentication
* Deploys a bash script for finishing the setup and adding the control repo

## Sample configuration

Configuring r10k on a node puppetserver and the control repo on a node controlreposerver.

Write a playbook:

```
---
- name: Configure r10k
  hosts: puppetserver, controlreposerver
  become: true
  gather_facts: true
  tasks:
  - include_role:
      name: ansible-puppetr10k
```

Set up the vars:

```
r10k_server: puppetserver
gitolite_server: controlreposerver
r10k_ip: 18.195.177.126
gitolite_ip: 18.195.26.8
```

Additional steps after applying the playbook:

Connect to the Puppet Server via SSH and login as the user created (git by default). Then initiate an SSH connection to the Gitolite Server in order to get the fingerprint added to the known_hosts.

```
su - git
ssh -T gitolite
```

Connect to the Gitolite Server and as root run the setup_r10k script that gets deployed when applying the role.

```
sudo -s
/usr/local/bin/setup_r10k
```

