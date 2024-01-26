# Assignment:
Deploy Nginx container into AWS EC2 with ECS (manually).

Following are the steps on how to deploy nginx container into aws ec2 with ecs (manually)



**Steps to create a Cluster:**

-First login to aws account.
-Then go to ECS.
-Click on create cluster.
-Write name of cluster e.g "my-test".
-Choose default VPC and default subnets. (you can choose any)
-On "Infrastructure" select "Amazon EC2 instances".
-Select "Create new ASG".
-Select "Amazon Linux 2" in "Operating system/Architecture".
-Select "t2.micro" on "EC2 instance type".
-Give value to "Desired capacity" e.g Minimum "1" and Maximum "2".
-Click on "Create Cluster".



**Steps to create a Task Definition:**

-Go to ECS.
-Enable or switch to "old console" or "old ECS experience".
-Click on "Create new task definition".
-Select "Launch type" e.g "EC2".
-Write name in "Task definition name" e.g "my-test-task-2".
-Enter value "512" in "Task memory(MiB).
-Enter value "256" in "Task CPU(unit).
-Click on "Add container".
-Write "nginx" in Container name.
-Write "nginx:latest" in "Image."
-Navigate to "Port mappings".
-Give value "80" to "Host port".
-Give value "80" to "Container port".
-Navigate to "Log configuration" and tick the "Auto-configure CloudWatch Logs".
-Click on "Create".



**Steps to create a Service:**

-Navigate to ECS.
-Click on your cluster.
-Navigate to bottom "Service" and click on "Create".
-Select "existing cluster".
-On "Compute options", select "Launch type".
-Select Launch type "EC2".
-On "Application type", select "Service.
-On "Family" select "my-test-task-2".
-On "Revision", select "1(LATEST)".
-Write name "test" on "Service name".
-Click on "Create".



**Steps for verification of Nginx deployment:**

-Navigate to EC2 console.
-Click on your created instance.
-Navigate to "Public Ipv4 address" and copy the ip address.
-Paste that ip address into your browser (if needed, write ":8080" with ip address e.g ip-address:8080) and press enter.

**Note:** You will see an Nginx webpage.

**NOTE:** If you don't have access to port 8080 on browser, then go to "Security groups". Click on your security group and edit the inbound rules. On "inbound rules" give permissions to port 80, by entering value "80" in "Port range", select "TCP" in "Protocol" and select any desired source e.g "0.0.0.0/0" in "Source". And for "Outbound rules" everything should be allowed e.g give values in Port range "All", in Protocol "All", in Destination "0.0.0.0/0".   

