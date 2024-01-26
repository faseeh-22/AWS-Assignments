# Assignment: 
Create an ansible playbook and through that playbook, access your own AWS account and through existing ECS cluster create a task definition and run a service.
Following are the steps to complete the assignment.



## Prerequisites:

**AWS Account:** Ensure you have an active AWS account with appropriate permissions to create ECS resources.

**Ansible Installed:** Ensure Ansible is installed on the machine where you plan to run this playbook.

**AWS CLI Installed:** The AWS CLI must be installed and configured on the machine where Ansible is running.



## Playbook Content:

```
---
- name: Deploy ECS Task and Service with EC2
  hosts: localhost
  gather_facts: False
  vars:
	aws_access_key: "5"
	aws_secret_key: "5"
	aws_region: "us-east-1"
	ecs_cluster_name: "DevCluster-1"
	ecs_task_definition_name: "ansible-task-definition"
	ecs_service_name: "ansible-ecs-service"
	ecs_container_name: "ansible_nginx"
	ecs_container_image: "nginx:latest"
	task_definition: null
  tasks:
	- name: Create ECS Cluster
  	command: "aws ecs create-cluster --cluster-name {{ ecs_cluster_name }}"
  	environment:
    	AWS_ACCESS_KEY_ID: "{{ aws_access_key }}"
    	AWS_SECRET_ACCESS_KEY: "{{ aws_secret_key }}"
    	AWS_DEFAULT_REGION: "{{ aws_region }}"
  	ignore_errors: yes  # Ignore errors if the cluster already exists
	- name: Create ECS Task Definition
  	ecs_taskdefinition:
    	region: "{{ aws_region }}"
    	family: "{{ ecs_task_definition_name }}"
    	network_mode: awsvpc
    	aws_access_key: "{{ aws_access_key }}"
    	aws_secret_key: "{{ aws_secret_key }}"
    	execution_role_arn: "arn:aws:iam::5:role/ecsTaskExecutionRole"
   	## task_role_arn: "arn:aws:iam::5:role/ecsTaskRole"
    	containers:
      	- name: "{{ ecs_container_name }}"
        	image: "{{ ecs_container_image }}"
        	portMappings:
          	- containerPort: 80
        	memoryReservation: 100
        	memory: 250
        	cpu: 100
    	state: present
  	register: task_definition
	- name: Register ECS Task Definition
  	debug:
    	var: task_definition
	- name: Create ECS Service
  	ecs_service:
    	region: "{{ aws_region }}"
    	cluster: "{{ ecs_cluster_name }}"
    	service: "{{ ecs_service_name }}"
    	task_definition: "{{ ecs_task_definition_name }}"
    	launch_type: EC2
    	desired_count: 1  # Set the desired count to 1 to start one task
    	wait: yes
    	deployment_configuration:
      	maximum_percent: 150
      	minimum_healthy_percent: 100
    	network_configuration:
      	subnets:
        	- "subnet-0"
        	- "subnet-0"
        	- "subnet-0"
        	- "subnet-0"
        	- "subnet-0"
        	- "subnet-0"
      	security_groups:
        	- "sg-0"
    	state: present
  	register: service_result
	- name: Register ECS Service
  	debug:
    	var: service_result
```


## Playbook Structure:

The Ansible playbook is structured as follows:

Name: Deploy ECS Task and Service with EC2

Hosts: localhost

Gather Facts: False

**Variables:**
     
   **aws_access_key:** AWS access key for authentication
    	
   **aws_secret_key:** AWS secret key for authentication
   
   **aws_region:** AWS region where ECS resources will be created
     
   **ecs_cluster_name:** Name of the existing ECS cluster
     
   **ecs_task_definition_name:** Name of the ECS task definition to be created
     
   **ecs_service_name:** Name of the ECS service to be created
     
   **ecs_container_name:** Name of the ECS container within the task
     
   **ecs_container_image:** Docker image for the ECS container
     
   **task_definition:** Variable to store the ECS task definition details

**Tasks:**

   **Create ECS Cluster:**
        	
   **Command:** aws ecs create-cluster --cluster-name {{ ecs_cluster_name }}
         
   **Purpose:** Creates an ECS cluster with the specified name.
         
   **Note:** Ignore errors if the cluster already exists.

   **Create ECS Task Definition:**
   
   **Module:** ecs_taskdefinition
         
   **Purpose:** Defines the ECS task with specified configurations, including container details.
         
   **Note:** The task definition is registered, and its details are stored in the task_definition variable.

   **Register ECS Task Definition:**
        	
   **Module:** debug
         
   **Purpose:** Displays the details of the registered ECS task definition.
         
   **Note:** This step is for informational purposes.

   **Create ECS Service:**
        	
   **Module:** ecs_service
         
   **Purpose:** Launches an ECS service using the defined task definition within the specified ECS cluster.
         
   **Note:** The service details are stored in the service_result variable.

   **Register ECS Service:**
   
   **Module:** debug
         
   **Purpose:** Displays the details of the registered ECS service.
         
   **Note:** This step is for informational purposes.



## Execution Steps:

 **1.Set AWS Credentials:**
    	Ensure that AWS credentials (aws_access_key and aws_secret_key) are set as environment variables or configured in the AWS CLI.

 **2.Run the Ansible Playbook:**
    	Execute the following command in the terminal:
```
    	ansible-playbook <playbook_filename.yml>
```
 
Replace <playbook_filename.yml> with the actual filename of your Ansible playbook.

	
**3.Review Output:**
    	After the playbook execution, review the output to check for any errors or confirm the successful deployment.



## Additional Notes:

Ensure that the specified ECS cluster (ecs_cluster_name) and IAM roles (ecsTaskExecutionRole and ecsTaskRole) exist in the AWS account.
Modify the variables in the playbook as needed, such as the Docker image (ecs_container_image), security groups, subnets, etc.



