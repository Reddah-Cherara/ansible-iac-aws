# Create AWS VM with key pair to use ad ansbible machine to run IaC 

Lunch VM on AWS and open ssh port and create new key pair.

Connect to this machine usering this command :

$ sudo ssh -i "ansible-ohio-rcherara-key-pair.pem" ubuntu@ec2-18-223-107-121.us-east-2.compute.amazonaws.com

install this package on this machine :

# Step 1 — Installing Ansible

Install and Configure Ansible on Ubuntu 18.04

$ sudo apt update
$ sudo apt install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt update
$ sudo apt install ansible






- git
- python3
- ansible

