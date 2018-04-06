## puppet_r10k

This Ansible role is my attempt to automate the deployment of [r10k](https://github.com/puppetlabs/r10k).

Please note, that this code has been tested in a lab environment only!

What the role does:
* Installs and configures r10k on the Puppet Server
* Installs and configures Gitolite on the server hosting the control repo
* Adds a system user on both servers to be used in r10k code management operations
* Sets up permissions and ssh key authentication
* Deploys a bash script for finishing the setup on the Gitolite server. 

## Requirements

SSH key pair used for the authentication need to be found in ~/.ssh. By default, the key name should be git. If using another name, the defaults have to be adjusted accordingly. 

```
ssh-keygen -f ~/.ssh/git -N ''
```

## Role Variables

In vars, hostnames and IP addresses of the r10k and Gitolite servers need to be set. For example:

```
r10k_server: puppetserver
gitolite_server: controlreposerver
r10k_ip: 18.195.177.126
gitolite_ip: 18.195.26.8
```

In defaults, there should not be a need to alter the values, unless a user and/or SSH key named 'git' is already present on the target server.

## Dependencies

None

## Example Playbook

```
---
- name: Configure r10k
  hosts: puppetserver, controlreposerver
  become: true
  gather_facts: true
  tasks:
  - include_role:
      name: ansible-role-puppet_r10k
```

## Additional steps after applying the playbook.

1. Connect to the Puppet Server via SSH.

2. Login as the user created (git by default). Then initiate an SSH connection to the Gitolite Server in order to get the fingerprint added to the known_hosts.

```
su - git
ssh -T gitolite
```

3. Connect to the Gitolite Server and as root run the setup_r10k script that gets deployed when applying the role.

```
sudo -s
/usr/local/bin/setup_r10k
```

## License

BSD




