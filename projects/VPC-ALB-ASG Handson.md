# Creating a Resilient VPC for Production Environment

To enhance resiliency, deploy servers across two availability zones using an auto-scaling group 
and an application load balancer. For additional security, place servers in private subnets, 
receiving requests through the load balancers. Enable internet access for servers 
using NAT gateways deployed in both availability zones.

## Architecture Diagram:

![img.png](img.png)

### Step 1: Create VPC and Subnets
#### Create VPC:
Create a new VPC with two public and two private subnets.

Select create vpc and more

#### Availability Zones (AZ):
Ensure servers are deployed in two availability zones.

#### NAT Gateways:
Deploy a NAT gateway in each availability zone.

#### S3 Endpoint:
S3 Endpoint not required

click on launch

### Step 2: Launch EC2 Instances using Auto Scaling Group
#### Launch EC2 Instances:
Navigate to EC2 Dashboard
Select "Auto Scaling Groups" and 
click "Create Auto Scaling Group."
Name: "Prod-Architecture-Project."
Click "Create Launch Template"

#### Create a new launch template:
Name: "Prod-Project."

AMI: Ubuntu (Free Tier).

Instance Type: t2.micro.

Key Pair: "aws-login.pem."
Security Group: "prod-project-sg" (previously created during VPC).

Network: Default VPC.

Inbound Rules: Allow SSH (Port 22) and TCP (Port 8000).

Click "Create Auto Scaling Group"

#### Back in the Auto Scaling Group creation:
Template: Select the recently created template "Prod-Project."

Network: Choose the recently created VPC.

Subnets: Choose only 2 private subnets to deploy the instances.
Load Balancer: None.

Scaling: Set desired capacity to 2, min to 1, and max to 3. Keep scaling policy as none.

Launch Auto Scaling Group:

Click "Next" and review the configurations.

Click "Launch Auto Scaling Group."

Verify EC2 Instances:

Confirm that two EC2 instances are created in the EC2 Dashboard within private subnets without public IPs.

* NOTE: Since EC2 have no public IP, to ssh into these instance which are in private subnets, 
we need bastion host (another EC2 VM with public IP within the same VPC)

### Step 3: Create Bastion Host
#### Launch Bastion Host:
Name: bastion host

AMI: Ubuntu (Free Tier)

Key Pair: aws-login

Security Group: Allow SSH (Port 22)

Type: t2.micro

Network : vpc recently created

subnet : public

Assign public ip:enable

key pair:aws-login

security group:create with ssh port-22 open

launch instance

* local terminal ---(SSH using pem)---> bastion host ---(SSH using pem)---> EC2 in private subnet .
* So to perform ssh from bastion to private subnet ec2, we need pem key inside the bastion vm folders

#### SSH into Bastion Host:
navigate to pem key downloads folder

```
realpath aws-login.pem
```
copy from local downloads to home/ubuntu folder of bastion VM

```
scp -i /c/Users/srisr/downloads/aws-login.pem /c/Users/srisr/downloads/aws-login.pem ubuntu@3.239.247.151:/home/ubuntu
```

now you log into bastion host:
```
ssh -i aws-login.pem ubuntu@<bastion public ip>
```

check for aws-login file and its permissions:
```
ll | grep pem
```
if you don't have exec permission on that file, use below cmd
```
chmod 600 aws-login.pem
```

SSH again into Private EC2 Instances:
```
ssh -i aws-login.pem ubuntu@<EC2 private ip>
```
#### Deploy Sample App:
```
vi index.html
```
click i

paste below sample html code
```
<!DOCTYPE html>
<html>
<body>

<h1>Deploying Sample App on First Private subnet EC2 Instance</h1>

</body>
</html>
```

click ESC

Type ' :wq '

now cmd
```
python3 -m http.server 8000
```

In similar way, SSH again into another private subnet EC2 instance and create index.html and run server using python cmd

Note: Now Sample apps are running on both EC2 instances which are in private subnets, to access them over internet,
Application Load balancer in between these 2 instances in that VPC.

### Step 4: Create Application Load Balancer (ALB)
navigate to EC2 dashboard

on left side, scroll dow to Load Balancer

click Application Load Balancer create

name: prod-project-ALB

Internet facing enabled 

In network settings,
	
select vpc created

select public subnet1

select public subnet2

sg:created one 

create new target group,

name=prod-project-TG

port=8000

default

click next

select instances (both private subnet ec2)

port=8000 

click include as pending

click create

refresh ALB 

in drop down, select recently created 'prod-project-TG'

let remaining fields be default

click create ALB

view LB

copy DNS link and browse in browser

i will find the sample app and after few refreshes,
you will find Load Balancer will redirect requests to both instances.

### Conclusion:
Successfully deployed a resilient VPC for a production environment, 
including two availability zones, private subnets, NAT gateways, 
and an application load balancer. 
Implemented a bastion host for secure SSH access 
and verified application accessibility through the load balancer. 
This documentation provides a step-by-step guide for creating 
a production-ready AWS infrastructure.



