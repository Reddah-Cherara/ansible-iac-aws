
MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ssh-add -l
2048 SHA256:/FuCEsj79nlIgG/GVaVi8Oyjyas4Et51Ft7zTDWuzg8 ./files/rcherara-provision-development-key.pem (RSA)
MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ssh-add -D
All identities removed.
MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ssh-add -l
The agent has no identities.
MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ansible-playbook -v -i ./hosts  environment_create.yml

ansible-playbook -i ./hosts  environment_create.yml



ansible all --list-hosts -i ./hosts
ansible ec2hosts --list-hosts -i ./hosts

MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ansible all --list-hosts -i ./hosts
 [WARNING] Ansible is being run in a world writable directory (/Users/rcherara/Projects-AWS/rcherara-aws-ansible-new), ignoring it as an ansible.cfg source. For more information see https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
  hosts (3):
    35.174.5.210
    54.175.126.48
    127.0.0.1

MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ansible ec2hosts --list-hosts -i ./hosts
 [WARNING] Ansible is being run in a world writable directory (/Users/rcherara/Projects-AWS/rcherara-aws-ansible-new), ignoring it as an ansible.cfg source. For more information see https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
  hosts (2):
    35.174.5.210
    54.175.126.48
MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$


ansible-playbook -i hosts -s -u ubuntu my_playbook.yaml

To ssh to our remote servers, we need ssh key. So, let's create it:

MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/rcherara/.ssh/id_rsa):
/Users/rcherara/.ssh/id_rsa already exists.
Overwrite (y/n)? yes
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/rcherara/.ssh/id_rsa.
Your public key has been saved in /Users/rcherara/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:Kndnz/66C+bE4/wgI/Ek6TrsA+ctQT/alI/DQcfscGA rcherara@MacBook-Cherara-Reddah
The key's randomart image is:
+---[RSA 2048]----+
|                 |
|      E          |
|     . +         |
|    . o.=        |
|   . o+*S        |
|  . o.**..       |
|   =.B=== X      |
|    B+*o.@ *     |
|   .o+ .  +oO=.  |
+----[SHA256]-----+
MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$



When we create an EC2 instance, AWS provides us a key and we can use it to access the instance. However, here we'll put our public key (/home/rcherara/.ssh/id_rsa.pub) for "rcherara" user into the /home/ubuntu/.ssh/authorized_keys file :


To place the public key at our remote host, instead of logging into that machine, we can use ssh-copy-id. But before we doing it, "ssh-agent" should be running on our control (most likely local machine):


Probably, we may need to add ec2 key-pair (*.pem) into the agent:
MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ssh-add ./files/rcherara-provision-development-key.pem
Identity added: ./files/rcherara-provision-development-key.pem (./files/rcherara-provision-development-key.pem)
 

MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ssh-add -l
2048 SHA256:Ijw7Mzkar/uvngRadaY4ZnaNKFhYgmBO3hxUh6WYAws ./files/rcherara-provision-development-key.pem (RSA)
MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$

MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ssh-add -D
All identities removed.


Now we'll be able to add our public key to the authorized_keys file on remote machine:

MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ssh-copy-id -f ubuntu@35.174.5.210

Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'ubuntu@35.174.5.210'"
and check to make sure that only the key(s) you wanted were added.

MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ssh-copy-id -f ubuntu@54.175.126.48

Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'ubuntu@54.175.126.48'"
and check to make sure that only the key(s) you wanted were added.

MacBook-Cherara-Reddah:rcherara-aws-ansible-new rcherara$ ansible -i hosts all -m ping -u ubuntu
 [WARNING] Ansible is being run in a world writable directory (/Users/rcherara/Projects-AWS/rcherara-aws-ansible-new), ignoring it as an ansible.cfg source. For more information see https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
127.0.0.1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
35.174.5.210 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
54.175.126.48 | SUCCESS => {
    "changed": false,
    "ping": "pong"



- remove 

# ============================================================
- name: Add the newly created EC2 instance(s) to the local host group (located inside the directory)
  local_action: lineinfile 
                dest="./hosts" 
                regexp={{ item.public_ip }} 
                insertafter="[launched]" line={{ item.public_ip }}
  with_items: "{{ ec2instance_frontend_a.instances }}"
- name: Add the newly created EC2 instance(s) to the local host group (located inside the directory)
  local_action: lineinfile 
                dest="./hosts" 
                regexp={{ item.public_ip }} 
                insertafter="[launched]" line={{ item.public_ip }}
  with_items: "{{ ec2instance_frontend_b.instances }}"



- name: add host of zone a to inventory
  add_host: name={{ item.public_ip }} groups=webservers
  with_items: ec2instance_frontend_a.instances
- name: add host of zone b to inventory
  add_host: name={{ item.public_ip }} groups=webservers
  with_items: ec2instance_frontend_b.instances

    - name: Add new instance to host group
      add_host:
        hostname: "{{ item.public_ip }}"
        groupname: launched
      loop: "{{ ec2.instances }}"

- remove 
   [launched]

[dbservers]

[ec2hosts]


pip install pexpect
