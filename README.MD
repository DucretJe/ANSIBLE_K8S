![](https://www.mvps.net/docs/wp-content/uploads/2019/06/kubernetes.png)

[TOC]

#### Features

- Initialize the differents nodes and installs K8s on debian OS (tested on Debian 9)
- Create one master node
- All worker nodes join the Master
- Uses Calico for network

# Installation

### Inventory

In order to make it works you need to populate the inventory with 2 groups:

- Master
- Workers

The playbook will also install server metrics on all pods. 

#### Master

Obviously there can be only one master in this group.
The host must be joinable by the host running ansible (can be himself)

Must be operating a Debian OS.

We recommand to use a fresh install.
**Warning**: The swap on this host will be disactivated and its partition deleted in the process.

The master will be initialized with Calico for the network.

#### Workers

I recommand to use IPs and not DNS names because the hostname of the worker will be changed.
There can be as many worker as wanted in this group.
The hosts must be joinable by the host running ansible (can be himself)

Must be operating a Debian OS.

We recommand to use a fresh install.
**Warning**: The swap on these hosts will be disactivated and its partition deleted in the process.
**Warning**: If a worker has already joined a master, ansible won't try to join the new one.
**Warning**: The worker's hostname will be changed in order to be sure it is unique and avoiding an error while joining the Master (there can't be 2 nodes with the same name)

#### Variables

To make it work the inventory must contain the following variables:

- *ansible_connection *: see ansible documentation.
- *ansible_user*: indicate the user that will be used in the installation process (it is the user that will be able to run the k8s commands and the config file will be installed in his home repository)
- * ansible_password*: It's the user's password
- *  ansible_become_password* : It's the "sudo" password
- * ansible_python_interpreter* : It's the path of the python interpreter on the hosts

If those variables are different on each host, you need to specify it in the inventory.

##### Example of Inventory

---
    all:
        hosts:
        children:
            master:
                hosts:
                    192.168.1.59
            nodes:
                hosts:
                    192.168.1.48
        vars:
            ansible_connection: ssh
            ansible_user: kubeuser
            ansible_password: {{ SSH_PASSWORD }}
            ansible_become_password: {{ SSH_ROOT }}
            ansible_python_interpreter: /usr/bin/python

### Secrets

This playbook uses secrets for the passwords so you have to create a file named secrets.yml containing your passwords and then converting to a secret with ansible vault.

Please consult the ansible documentation for this part.

#### Example of secret file
---
	SSH_PASSWORD: "mysuperpassword"
	SSH_ROOT: "myrootpassword"

### Resources

https://www.ansible.com/
https://kubernetes.io/
