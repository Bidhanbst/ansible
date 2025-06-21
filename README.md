# Introduction

In this guide, we will walk through the steps to deploy a Kubernetes cluster on Ubuntu 24.04 and deploy simple laravel application using Ansible roles.

## Prerequisites

Before we begin, ensure you have the following:
 - A stable internet connection
 - A control node with Ansible installed (preferably Ubuntu 24.04)
 - At least two Ubuntu 24.04 nodes (one master and one worker)
 - SSH access to all nodes from the control node.
 - Establish a user account with sudo(root) privileges on all nodes.

### Add the following entries to the /etc/hosts file on each node
```
192.168.130.175    control  # Ansible Control Node
192.168.130.176    master
192.168.130.177    worker1
```

## Install Kubernetes using Ansible

### Setting up your workspace

- Create user account with sudo privileges

```
sudo adduser sysadmin
sudo usermod -aG sudo sysadmin
```

### Setting up your environment

First, update your package lists and install Ansible on your control node only.
```
sudo apt install ansible -y
```
Next, set up SSH keys to allow passwordless access to your nodes. Generate an SSH key pair if you haven't already
```
ssh-keygen -t rsa -b 4096 -C "test@example.com"
```
Now that you've created a public key on your control node, copy that key to each of your nodes.
```
ssh-copy-id your_privileged_user@your_master_node_ip_address
ssh-copy-id your_privileged_user@your_worker_node_ip_address
```

### Define your Inventory file

Create an inventory file to list your nodes. This file, typically named hosts or inventory, should look like this.
```
vim hosts
```
Then copy and paste the following entries(below).
```
[master_nodes]
master  ansible_host=192.168.130.176

[worker_nodes]
worker1 ansible_host=192.168.1.177

[all:vars]
ansible_python_interpreter=/usr/bin/python3
```

### Create Ansible Roles

Now, let's create Ansible roles for the Kubernetes master, network and worker nodes.

- Role for Kubernetes master 
```
ansible-galaxy init kubernetes_master
```
- Role for Kubernetes worker
```
ansible-galaxy init kubernetes_worker
```
- Role for Kubernetes network
```
ansible-galaxy init kubernetes_network
```

Add the entries on respective file.

### Create a Playbook to Run the Roles
Create a playbook to run these roles. Create a file named site.yml
```
vim site.yml
```

## Playbook Execution

Now we will proceed with executing the playbook using the following command.
```
ansible-playbook -i hosts site.yml -K
```

After executing the playbook following changes will occur.
- The playbook execution will create one master and one worker node.
- The Kubernetes cluster will be ready to accept deployments.
- NFS server will be installed on localhost.
- NFS client will be installed on worker nodes.
- Application will be deployed to the Kubernetes cluster.




