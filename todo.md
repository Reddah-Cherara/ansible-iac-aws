- gatway tag name and present don't work 


    tags:
      Name: "{{ aws_tags_frontend.Name}}"
      Role: "public"
      Created By: "{{ aws_tags_frontend.Author}}"

    tags:
      Name: "{{ aws_tags_frontend.Name}}"
      Role: "public"
      Created By: "{{ aws_tags_frontend.Author}}"

      solution 

      - name: Create Subnet(s) in VPC - DMZ
  ec2_vpc_subnet:
    state: present
    vpc_id: "{{ vpc_id }}"
    region: "{{ vpc_region }}"
    az: "{{ item.az }}"
    cidr: "{{ item.subnet }}"
    resource_tags:
       Name: "{{ item.name }}"
       Role: "{{ role_tag }}"
       Team: "{{ team_tag }}"
       Product Area: "{{ product_area_tag }}"
       Portfolio: "{{ portfolio_tag }}"
       Created By: "{{ created_by }}"
  with_items: "{{ dmz_subnet_az }}"
  register: new_dmz_subnets

- name: Set facts for Subnet - DMZ
  set_fact:
    subnet_dmz_id: "{{ subnet_dmz_id | default({}) | combine({ item.subnet.tags.Name: item.subnet.id }) }}"
  loop: "{{ new_dmz_subnets.results }}"

- debug:
    var=subnet_dmz_id
    
- create backend instance with jar of webservice in  specifique port
- create db

- name: ping all hosts
  command: ansible -i hosts all -m ping -u ubuntu


    'Are you sure you want to continue connecting (yes/no\)\?':'yes'
    yes | 

- todo

$ eval `ssh-agent -s`  # start ssh agent
$ ssh-add -D <ssh key path>  # delete ssh key

- name: add our public key to the authorized_keys file on remote machine of zone a 2
  expect:
    command: ssh-copy-id -f ubuntu@{{ item.public_ip }}
    ignore_errors: yes
    responses:
        'want to continue connecting': 'yes'
  with_items:           "{{ ec2instance_frontend_a.instances }}"
- name: add our public key to the authorized_keys file on remote machine of zone b 2
  expect:
    command: ssh-copy-id -f ubuntu@{{ item.public_ip }}
    ignore_errors: yes
    responses:
        'want to continue connecting': 'yes'
  with_items:           "{{ ec2instance_frontend_b.instances }}"


  #    - name: copy the content of the web site
#      copy:
#        src: ./files/static-site-src/
#        dest: /home/ubuntu/static-site