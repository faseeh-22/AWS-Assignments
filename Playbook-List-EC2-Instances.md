# Assignment: 
Create an ansible playbook and through that playbook, list EC2 instances.

Following are the steps to complete the assignment.



**Steps:**

- Install Required Ansible Roles: You'll need the boto library for AWS integration. Install it using Ansible Galaxy, a role and playbook manager for Ansible:

- pip install boto boto3

- AWS configure

```
AWS Access Key ID [None]: Your_AWS_Access_Key

AWS Secret Access Key [None]: Your_AWS_Secret_Key

Default region name [None]: us-east-1

Default output format [None]: json
```

- ansible-galaxy collection install community.aws   (NOTE: use this command in case of error, if you use "aws.community" module)

- cd ~/.aws (here you can check and config the following files: "config", "credentials")

- Write the following contents, to create the playbook:

```	
---
- name: List EC2 Instances
  hosts: localhost
  gather_facts: no
  tasks:
	- name: List EC2 instances
  	community.aws.ec2_instance_info:
    	region: us-east-1
  	register: ec2_instances

	- name: Display EC2 instances
  	debug:
    	var: ec2_instances.instances
```

- Save this playbook and run it on terminal.




