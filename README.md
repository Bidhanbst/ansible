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

# Application Deployment

The role has been created for deploying laravel application to Kubernetes Cluster.
```
ansible-galaxy init application_deployment
```

In this section, we break down the tasks required for deploying application on Kubernetes cluster. Please check task.yaml file on the role.

- Create a directory for cloning repository of an application if it doesn't exists.
- Clone a simple laravel application from the repository.
- Enable multi architecture docker build to run docker image on different CPU architecture and operating systems.
- Build the docker image and push it to the docker hub.
- Create  Persistent Volume that utilizes the NFS server installed in ansible host to persist the volume of database pod.
- Create Persistent Volume claim.
- Create Storage Class.
- Create Statefulset for database. We are using Statefulset for deploying postgres database. The environment variables are passed in the yaml file.
- Create Service to expose database to ClusterIP.
- Create deployment for an application. Make sure you are using the latest docker image from Docker Hub.
- Create NodePort service to expose application on NodePort.
- Create Horizontal Pod Autoscaler for an application which triggers the high CPU usage and increase replicas on demand.


After executing the playbook following changes will occur.
- The playbook execution will create one master and one worker node.
- The Kubernetes cluster will be ready to accept deployments.
- Ansible server will be configured as Remote Kubernetes Management server.
- NFS server will be installed on localhost.
- NFS client will be installed on worker nodes.
- Application will be deployed to the Kubernetes cluster.




