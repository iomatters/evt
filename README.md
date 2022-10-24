This project includes Ansible harness to build, deploy and destroy a simple WebSocket service backed by PostgreSQL database.

# Requirements

 - Debian/Ubuntu, RHEL/CentOS
 - Python >= 3.8
 - Ansible >= 2.10.0
 - Docker
 - Docker-compose
 - Git

# How it works

### Server roles

 - **Ansible Master** - Linux server that has Ansible installed on it and is used for managing remote hosts or nodes.
 - **Target** -  Linux server which is used to run build jobs, store artifacts and run services .

You may want your testing machine to inherit all servers roles mentioned above.

### What it does

| # | Job |Host |Action|
|--|--|--|--|
| 1 |Install |All| Install Docker and python dependencies|
| 2 |Build |Target| Clone https://github.com/iomatters/bequant|
| 3 |Build |Target| Build the Endpoint Docker image|
| 4 |Deploy |Target| Start up Docker containers|
| 5 |Deploy |Target| Restore PostgreSQL dump|
| 6 |Deploy |Target| Verify containers are up and running|
| 7 |Deploy |Target| Print WS endpoint|
| 8 |Destroy |Target| Remove Docker containers and images|
| 9 |Destroy |Target| Clean up folders|

# Installation

### On Ansible Master node

Make sure to install Ansible.

    sudo apt update -y
    sudo apt-get install ansible -y
or

    sudo yum update -y
    sudo yum install ansible -y

Create Ansible user

    sudo useradd -m -d /home/ansible ansible
    sudo usermod -a -G sudo ansible

Make sure to have this line at /etc/sudoers. You may want to use another group upon your choice.

> %sudo  ALL=(ALL) NOPASSWD: ALL

Generate SSH keys

    sudo su - ansible
    ssh-keygen
    cat /home.ansible/ssh/id_rsa.pub >> /home/ansible/.ssh/authorized_keys

### On Target nodes

Create Ansible user

    sudo useradd -m -d /home/ansible ansible
    sudo usermod -a -G sudo ansible

Make sure to have this line at /etc/sudoers. You may want to use another group upon your choice.

> %sudo  ALL=(ALL) NOPASSWD: ALL

Copy and paste Ansible public key to /home/ansible/.ssh/authorized_keys

# Usage

Clone this repository.

    git clone https://github.com/iomatters/evt.git

### Configuration

Modify env/inventory to manage your target hosts.
Modify env/group_vars/all/main.yml to set the Endpoint version and PostgreSQL credentials.

### Run Install

    ansible-playbook -i env/inventory playbooks/docker.yml
### Run Build

    ansible-playbook -i env/inventory playbooks/endpoint.yml --tags build
### Run Deploy

    ansible-playbook -i env/inventory playbooks/endpoint.yml --tags deploy
### Run Destroy

    ansible-playbook -i env/inventory playbooks/endpoint.yml --tags destroy
