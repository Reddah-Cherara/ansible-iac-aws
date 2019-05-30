# 

As an example your ~/.boto file should contain something along the lines of:

[profile personal]
aws_access_key_id=key
aws_secret_access_key=secret

https://docs.ansible.com/ansible/latest/user_guide/intro_dynamic_inventory.html#inventory-script-example-aws-ec2


At this point go ahead and run the playbook just to ensure Ansible is properly connecting to AWS and able to manage security groups:

AWS_PROFILE=personal ansible-playbook -i inventory/ec2.py playbook.yml

MacBook-Cherara-Reddah:inventory rcherara$ ./ec2.py --list

{
  "_meta": {
    "hostvars": {}
  }
}
MacBook-Cherara-Reddah:inventory rcherara$


https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.py


# Create VPC with Public/Private subnets and Multi Availability Zones

In this project, we are going to create a VPC with two Availability Zones. In an AWS region, Availability Zones are physically separated, and their power supply are different, so that in the case of a power outage, not all zones are impacted. Pretty awesome.

We will create 4 subnets (2 publics, 2 privates). We will create 2 Elastic IPs, and 2 NAT Gateways (one per AZ). We double the NAT Gateways so that each AZ can be completely independent.

![ARCHITECTURE](arch-diagram.png)



-   Create EC2 Keypair using local PublicKey
-   Create VPC
-   Create Public Subnet
-   Create Private Subnet 

# Prerequisites
Ansible : sudo pip install ansible
Boto : sudo pip install boto
AWS CLI : sudo pip install awscli

 
 The main file of the playbook, playbook.yml
 all the variables we use in the playbook are listed in the aws.yml file :


# Run the playbook :

    $ ansible-playbook playbook.yml -i inventory -e @vars/aws_credentials.yml




https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Scenario2.html


![ARCHITECTURE](nat-gateway-diagram.png)


Perform `make install` to install dependencies

```bash
make install
```

Create an aws keypair using your ~/.ssh/id_rsa.pub public key

```bash
make keypair/create
```


I will use 3 different topologies :

VPC with Public subnet only
VPC with Public/Private subnets
VPC with Public/Private subnets and Multi Availability Zones










- name: Create new nat gateway and allocate new EIP if a nat gateway does not yet exist in the subnet AZ-A Public.
ec2_vpc_nat_gateway:
state: present
subnet_id: "{{ public_subnet_az1_id }}"
wait: yes
region: "{{ aws_region }}"
if_exist_do_not_create: true
register: my_nat_gateway_az1

- name: Set Nat Gateway ID in variable [AZ-1]
set_fact:
nat_gateway_az1_id: "{{ my_nat_gateway_az1.nat_gateway_id }}"

- name: Create new nat gateway and allocate new EIP if a nat gateway does not yet exist in the subnet AZ-B Public.
ec2_vpc_nat_gateway:
state: present
subnet_id: "{{ public_subnet_az2_id }}"
wait: yes
region: "{{ aws_region }}"
if_exist_do_not_create: true
register: my_nat_gateway_az2

- name: Set Nat Gateway ID in variable [AZ-2]
set_fact:
nat_gateway_az2_id: "{{ my_nat_gateway_az2.nat_gateway_id }}"





temp 
- name: Create EC2 load balancer {{ aws_webservers }}
  local_action:
        module: ec2_elb_lb
        name: elb-{{ TAG_ENVIRONMENT }}-{{ aws_webservers }}
        state: present
        region: "{{ aws_region }}"
        cross_az_load_balancing: "yes"
        subnets: '"{{ my_public_subnet_a }}","{{ my_public_subnet_b }}"'
        instance_ids: '"{{ ec2instance_frontend_b}}"'
        health_check:
            ping_protocol: http
            ping_port: 80
            ping_path: "/ping"
            response_timeout: 5
            interval: 30
            unhealthy_threshold: 2
            healthy_threshold: 2
        listeners:
          - protocol: http
            load_balancer_port: 80
            instance_port: 80
  register: aws-frontend-elb



      access_logs:
      interval: 5 # minutes (defaults to 60)
      s3_location: "{{ bucketname }}"
      s3_prefix: "{{ prefix }}"



# Download the ansible ec2 inventory script, make it executable
$ pwd
~/github/cassandra-image/

$ wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.py -O ansible-ec2/ec2.py

$ chmod +x ansible-ec2/ec2.py

$ wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.ini -O ansible-ec2/ec2.ini
After you download it, you can start using it

# Add instance ec2 pem file (key pair)
$ ssh-add ~/.ssh/cloudurable-us-west-2.pem
Then we can ping the EC2 instance with the ansible ping module as follows:

Pass dynamic list to ansible use user centos
$ ansible -i ansible-ec2/ec2.py  tag_Name_cassandra_node  -u centos  -m ping
54.218.113.95 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
The -i option passes a script that will generate a JSON inventory list



tasks:
    - name: Clone the github repo
      git:
        repo: https://github.com/adichiru/cloudformation-ansible-PoC.git
        version: master
        dest: /tmp/remove-me/

    - name: Check if the index.html file exists
      stat:
        path: /tmp/remove-me/website/index.html
      register: stat_result

    - name: Copy the index.html file to the Document Root
      copy:
        src: /tmp/remove-me/website/index.html
        dest: /mnt/var/www/html/
      when: stat_result.stat.exists == True

    - name: Setting up ownership and permissions
      become: yes
      become_user: root
      become_method: sudo
      file:
        path: /mnt/var/www/html/index.html
        owner: apache
        group: apache
        mode: 0640

Internet Gateway

An Internet Gateway is a logical connection between an Amazon VPC and the Internet. It is not a physical device. Only one can be associated with each VPC. It does not limit the bandwidth of Internet connectivity. (The only limitation on bandwidth is the size of the Amazon EC2 instance, and it applies to all traffic -- internal to the VPC and out to the Internet.)