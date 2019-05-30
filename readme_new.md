
#


## Install PIP, Python Boto:

$ apt-get install python-pip python-boto python-dev libffi-dev

We need python-boto to make Ansible is able to work AWS

## Install Ansible:
There are several way to install Ansible.  I choose the way that uses PIP to install Ansible.

$ pip install ansible
 
## AWS Access Key
Now, we need to add AWS Access Key to make Boto can access to your AWS account:


$ export AWS_ACCESS_KEY_ID="your_aws_access_key"
$ export AWS_SECRET_ACCESS_KEY="your_aws_secret_key"

execute the below command and then enter the access keys and secret access keys as below: 

sudo vi ~/.boto 

add below three lines in the above file, replace the ?? with access key and secret key values:


[Credentials]
aws_access_key_id = ??
aws_secret_access_key = ??


Ansible looks in several locations for your ansible.cfg:

– ansible.cfg in the current directory
– .ansible.cfg in your home directory
– /etc/ansible/ansible.cfg

[web]

[development]

[production]

[microservices]

[webservers]


4

For a user to use /apps/ansible/ansible.cfg you can either

Link ~/.ansible.cfg (in the home directory) to /apps/ansible/ansible.cfg, or
Set ANSIBLE_CONFIG (environment variable) to /apps/ansible/ansible.cfg




# Create VPC with Public/Private subnets and Multi Availability Zones

ansible-playbook -i ./inventory/ec2.py environment_create.yml

There are  3 different VPC topologies :

VPC with Public subnet only
VPC with Public/Private subnets
VPC with Public/Private subnets and Multi Availability Zones


In this project, we are going to create a VPC with two Availability Zones. In an AWS region, Availability Zones are physically separated, and their power supply are different, so that in the case of a power outage, not all zones are impacted. Pretty awesome.


![ARCHITECTURE](arch-diagram.png)


We will create 4 subnets (2 publics, 2 privates). We will create 2 Elastic IPs, and 2 NAT Gateways (one per AZ). We double the NAT Gateways so that each AZ can be completely independent.


What makes a subnet public is the fact that it has a route on its route table to the Internet Gateway. If no route is created for the Internet Gateway, then the subnet is private.


An Internet Gateway is a logical connection between an Amazon VPC and the Internet.

A NAT Instance is an Amazon EC2 instance configured to forward traffic to the Internet.

AWS introduced a NAT Gateway Service that can take the place of a NAT Instance. The benefits of using a NAT Gateway service are:

It is a fully-managed service -- just create it and it works automatically, including fail-over
It can burst up to 10 Gbps (a NAT Instance is limited to the bandwidth associated with the EC2 instance type)
However:

Security Groups cannot be associated with a NAT Gateway
You'll need one in each AZ since they only operate in a single AZ


# Prerequisites
Ansible : sudo pip install ansible
Boto : sudo pip install boto
AWS CLI : sudo pip install awscli





-   Create EC2 Keypair using local PublicKey
-   Create VPC
-   Create Public Subnet
-   Create Private Subnet 



 
 The main file of the playbook, playbook.yml
 all the variables we use in the playbook are listed in the aws.yml file :


# Run the playbook :

    $ ansible-playbook playbook.yml -i inventory -e @vars/aws_credentials.yml











