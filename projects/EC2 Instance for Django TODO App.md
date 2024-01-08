Creating AWS EC2 Instance for Django TODO Application Deployment

### **Title:**
Creating an AWS EC2 Instance for Django TODO Application Deployment

### **Aim:**
Provision an AWS EC2 ubuntu instance to host the Django TODO application.

### **Why:**
Demonstrate understanding and capability in deploying Django applications in virtualized environments. 
This serves as a hands-on experience in preparation for DevOps roles and real-world deployment scenarios.

### **Tools:**
- AWS Account
- GitBash
- GitHub
- Python3, pip3, Django

### **Pre-requisite:**
- Django project with source code hosted on GitHub.
- AWS Account

### **Part 1: Create and Launch EC2 Instance**

#### Sign in to AWS Console:
Open your web browser and navigate to the AWS Management Console.
Sign in with your AWS account credentials.

#### Access EC2 Dashboard:
In the AWS Management Console, 
search for and select "EC2" under the "Find Services" search bar.

#### Launch Instance:
Click on the "Instances" in the EC2 Dashboard.
Click "Launch Instance" to start the instance creation wizard.

#### Choose Amazon Machine Image (AMI):
Select an Ubuntu AMI (e.g., Ubuntu Server 20.04 LTS free tier).


#### Configure Instance Details:
Set the number of instances to launch (e.g., 1).
Optionally, configure advanced settings like network, IAM role, etc.

#### Select Key Pair:
Choose an existing key pair or create a new one. 
Create a new key pair 
name "aws-login" and 
select the .pem type.
Click "Download Key Pair" and 
save the private key (.pem file) to a secure location.

#### Add Storage:
Configure the storage settings for your instance. 
The default settings are usually sufficient for a simple setup.


#### Add Tags (Optional):
Add a tag to your instance for better identification. 
Name it "todo-app-deploy."

#### Configure Security Group:
Create a new security group or select an existing one (e.g., default).

Click "Launch Instances."

#### View Instances:

Click on "Instances" in the left sidebar. This will show you a list of all your instances.
#### Select the Launched Instance:

Find the instance you just created in the list. 
You can identify it by the name (e.g., "todo-app-deploy") or any tags you may have added.
Click on Instance ID and note the public IP

### **Part 2: SSH into EC2**

#### Access EC2 Instance:
Once the instance is launched, go back to the EC2 Dashboard.
Find your instance and note the "Public IPv4 address."

#### Connect to EC2 Instance using Git Bash:
Open Git Bash or your preferred terminal.
Navigate to the directory where the private key is stored, for example, the Downloads folder:
bash
Copy code
cd Downloads

#### List and Confirm Key Pair:
List all items in the directory and search for the AWS login file to confirm the name (e.g., aws-login.pem):
bash
Copy code
```
ll | grep aws-login
```

#### SSH into EC2 Instance:
Use the following command to connect to your EC2 instance. 
Replace <pem file with absolute path> with the absolute path to your AWS login PEM file, 
and <EC2 Public IP> with the actual Public IP of your EC2 instance:
bash
ssh -i /absolute/path/to/aws-login.pem ubuntu@<EC2 Public IP>

(username and public ip you can get from aws console in ec2 dashboard, mostly it will be ubuntu )

now  the cmd becomes 
```
"ssh -i aws-login.pem ubuntu@<EC2 Public IP>"
```
#### Handling Login Error:
If you encounter a login error, it might be due to the SSH rule not being added to the security group of the EC2 instance.
Navigate to the AWS Console, go to the EC2 Dashboard, and find your instance.

#### Edit Inbound Rules:
Under the "Security" tab, click on the security group associated with your instance.

#### Add SSH Rule:
In the inbound rules, add a rule to allow SSH traffic:
Type: "All traffic"
Port Range: "22"
Source: "Anywhere" (This allows SSH access from any IP).
Save the changes.

#### Retry SSH Connection:

Go back to Git Bash and retry the SSH connection command:
```
"ssh -i aws-login.pem ubuntu@<EC2 Public IP>"
```
You should now be able to connect to your EC2 instance without encountering a login error.

### **Part 3: Deploy TODO Web App**

#### User Management:
Create a New User and Grant Permissions:
After connecting to the EC2 instance, switch to the root user:
```
sudo su -
```

#### Add a new user named "srihari":
```
adduser srihari
```
Set the password for the new user (e.g., Complex@001)


#### Switch to the newly created user:
```
su - srihari
```
It is always good to update the VM before performing any deployment activities,
```
sudo apt update
```
Note: If you receive the error "srihari is not in the sudoers file. This incident will be reported," 
exit the user session:
```
exit
```

#### And then, as the root user, add the user to the sudo group:
```
sudo usermod -aG sudo srihari
```

#### Switch back to the "srihari" user:
```
su - srihari
```

#### Environment Setup :
Update System:
```
sudo apt update
```
Install Essential Packages:
```
sudo apt-get install python3 python3-pip git
```

#### Clone the Django Project:
```
git clone https://github.com/Srihari-001/TODO-Project.git
```
move to project folder
```
cd TODO-Project/
```
#### Install Virtual Environment and Dependencies:
Install Virtual Environment for dev Environment
```
sudo apt-get install python3-venv
```
name the env as "venv"
```
python3 -m venv venv
```
activate the Virtual Environment
```
source venv/bin/activate
```
Always remember to install requirements inside the Virtual Environment
```
pip install -r requirements.txt
```

#### Database Configuration and Migration:
```
python3 manage.py migrate
```

#### Server Deployment:
Run Django's Development Server:
```
nohup python3 manage.py runserver 0.0.0.0:8000 &
```
Note: Using nohup allows the server to run in the background even after the terminal session is closed.

### **Part 4: Verification and Cleanup**

#### Access the Django Application:
Open a web browser and navigate to the Django application using the EC2 instance's public IP 
and the specified port (e.g., http://<EC2 Public IP>:8000/).

#### Post Deployment Checks:
Terminate Server Process (Optional):

If needed, terminate the Django server process by identifying the process ID (PID) using the following command:
```
ps aux | grep runserver
```
#### Terminate the process using the kill command:
```
kill -9 <PID1> <PID2>
```
where PID1 and PID2 are Parent and child process ID's of manage.py runserver cmds

#### Check nohup.out for Logs:

Check the nohup.out file for logs or error messages:
```
rm nohup.out
```

####  Cleanup Steps (Optional):
If you created an additional user (e.g., "srihari") for deployment purposes, 
and you want to remove this user along with associated files:

#### Identify and kill any processes started by the user "srihari" using:
```
ps aux | grep srihari
```
Terminate the identified processes using 
```kill -9 <PID>.
```

#### Remove the user "srihari" and its home directory:
```
sudo userdel -r srihari
```

#### Terminate EC2 Instance (Optional):
If the deployment is successful and verified, and you no longer need the instance, 
you can terminate it to avoid unnecessary charges.
In the AWS EC2 Dashboard, select the instance.
Click "Actions," and choose "Instance State" -> "Stop" to stop the instance (if needed).
Once stopped, click "Actions," and choose "Instance State" -> "Terminate."
Confirm termination.

### ***Conclusion:***
Successfully completed the deployment of the Django TODO application on an AWS EC2 instance, 
including instance creation, SSH access, application deployment, verification, and cleanup. 
This process provides a structured approach to hosting Django applications on AWS. 
The additional cleanup steps help ensure a tidy environment and can be performed based on your specific needs.