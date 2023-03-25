# webdev-advanced
Providing the Linux Server Infrastructure for a 10-server university project for 21 other students.

# Prerequisites

## Servers
1. Login at [First steps](https://www.bw-cloud.org/de/erste_schritte) 
1. Apply for Servers [Ticketing BWcloud](https://bw-support.scc.kit.edu) - see ticket PDF files in this repo
1. Create security groups (prod, stage) in accordance with [VMWare Userguide](https://docs.vmware.com/de/VMware-Integrated-OpenStack/5.1/com.vmware.openstack.user.doc/GUID-2B5AE34D-7091-48D2-88B9-219C7EF32A3D.html)
1. Choose OS (default user for CentOS8 is centos) 
1. Create servers in the respective groups
- instance name: according to naming schema  
- description: respectively 
- number of instances: 1
- image: CentOS8
- instance variant: m1.tiny as requested in the initial ticket
1. Gather ed25519 public keys from users that need to log into the servers, ed25519 as it is safer than RSA see [Source](https://wiki.archlinux.org/title/SSH_keys#Ed25519)

## Ansible
1. On earch server there needs to be a user with sudo rights - for bwcloud by default this is the user "centos" who is a member of the wheel group with respective rights
1. Configure each server so ansible will work:
```
cd /etc/yum.repos.d/ 
sudo sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* 
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-* 
cd  
Sudo yum install -y python3  
curl https://bootstrap.pypa.io/pip/3.6/get-pip.py -o get-pip.py 
python3 get-pip.py 
rm get-pip.py 
```
Here is the short version:
```
cd /etc/yum.repos.d/; sudo sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*; sudo sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*; cd; sudo yum install -y python3; curl https://bootstrap.pypa.io/pip/3.6/get-pip.py -o get-pip.py; python3 get-pip.py; rm get-pip.py
```

# Set up the servers for the teams to work (CI/CD Pipeline from Gitlab)

## Set up user access
ansible-playbook user.yml -i staging -i production

## Basic setup for all servers
ansible-playbook -i production -i staging basic-setup.yml

## Setup for DB servers as MariaDB is running natively, compared to all other services running in containers
ansible-playbook -i production -i staging db-setup.yml

