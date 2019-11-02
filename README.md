# Deploying a Hadoop cluster for Test purposes using AWS EC2, Docker and Cloudera Quickstart

[![Project Status: Concept – Minimal or no implementation has been done yet, or the repository is only intended to be a limited example, demo, or proof-of-concept.](https://www.repostatus.org/badges/latest/concept.svg)](https://www.repostatus.org/#concept)

Some of you asked me to make a gist that helps beginners with `Hadoop`.
Is `Hadoop` going to die as many claim?<br>
If yes, then let's run a `Hadoop` cluster before it's too late :)!
<br>
This gist will help you launch a `Hadoop` cluster easily.<br>
We'll be using `AWS` as `Compute` and `Storage` platform. We'll also use `Docker` in order to launch `Cloudera QuickStart`.<br>
At the end of this gist, you'll have a `Hadoop` cluster up and running for basic purposes. I recommend you to use a regular or enterprise version of `Cloudera` for `dev` and `prod` purposes.

<hr>
1. Create an account on `AWS` and log into `AWS Management Console`.<br>

## 1. SECURITY - Setting up our Security Group
Bypass this step if you already have a Security Group on AWS<br>
Go to Services > EC2, in NETWORK AND SECURITY, click on Security Groups > Create Security Group<br>
Security group name: Hadoop<br>
Description: Hadoop-Admins-SG<br>
VPC: select default VPC<br>
Security Group Rules (Inbound and Outbound): allow `SSH`, `HTTP`, `HTTPS` from anywhere.<br>
Click on Create.<br>
<hr>
[![isaac-arnault-AWS.png](https://i.postimg.cc/NfQcCmyB/isaac-arnault-AWS.png)](https://postimg.cc/JtYvGyc2)
<hr>
Go to Services, in Security, Identity and Compliance section, click on `IAM`.<br>

Click on Users > Add user and configure as follows:
<hr>
[![isaac-arnault-aws-19.png](https://i.postimg.cc/DfBjFXj2/isaac-arnault-aws-19.png)](https://postimg.cc/9zwtYrHS)
<hr>
Click on Next: Permissions > Add user to group > Create group > Group Name: hadoop_admins<br>

Search for `EC2`: select `AmazonEC2FullAccess`, Search for `IAM`: select `AmazonIAMFullAccess`
<hr>
[![isaac-arnault-AWS-20.png](https://i.postimg.cc/gJBZtmsP/isaac-arnault-AWS-20.png)](https://postimg.cc/vgfTcRCP)
<hr>
[![isaac-arnault-aws-21.png](https://i.postimg.cc/qqhC9SD6/isaac-arnault-aws-21.png)](https://postimg.cc/8fG5vKST)
<hr>
In `IAM` go to `Roles` > Create role > click on EC2 > Next: Permissions > select AdministratorAccess
<hr>
[![isaac-arnault-aws-22.png](https://i.postimg.cc/wTxvP738/isaac-arnault-aws-22.png)](https://postimg.cc/dDXwZQ84)
<hr>
Key: name > Value: hadoop-cluster > Next: Review > Role name: AdminAccess > Create role. By clicking on `IAM`, you can have a summary of the role you've created.
<hr>
[![isaac-arnault-AWS-23.png](https://i.postimg.cc/W1jQSk4g/isaac-arnault-AWS-23.png)](https://postimg.cc/fJ22RkmR)
<hr>
At this stage you should have a user, a group and a role attached to your `AWS` account before proceeding to step 2. <br>

  > <br>Please note</b>: having all check marks on `IAM` green is great, but it is not mandatory by `AWS`.<br>
  <hr>
[![isaac-arnault-AWS-18.png](https://i.postimg.cc/N0jkjgdW/isaac-arnault-AWS-18.png)](https://postimg.cc/CR9qvVTN)
<hr>
## 2. INSTALLATION - Setting up our EC2 instance
Go to Services > EC2, click on Launch Instance.<br>
Select `Ubuntu server 18.04 LTS` as AMI.
<hr>
[![isaac-arnault-AWS-hadoop.png](https://i.postimg.cc/KjqgVF52/isaac-arnault-AWS-hadoop.png)](https://postimg.cc/LgPXYc7C)
<hr>
Choose a `t2.xlarge` instance type. Choosing a lower instance may lead to latency.
<hr>
[![isaac-arnault-hadoop-2.png](https://i.postimg.cc/Sxpyh3tF/isaac-arnault-hadoop-2.png)](https://postimg.cc/6yYsVjXY)
<hr>
Click on Configure Instance Details and tune as follows:<br>

Number of instances: 1 > IAM role: AdminAccess > Next: Add Storage, set storage size to 30 Gibibytes.
<hr>
[![isaac-arnault-aws-24.png](https://i.postimg.cc/9FxGN3mb/isaac-arnault-aws-24.png)](https://postimg.cc/ykZgkbS3)
<hr>
Next: Add tags > Key: name, Value: hadoop-cluster > Next: Configure Security Group > select an existing security group:<br>
choose the one you've created with the above commands. You can also select your default security group.<br>

Review and Launch > Launch.<br>

You'll be prompted by AWS to create a Key Pair file, create a new key pair file and Download it.<br>

Save it on a repository called <b>hadoop</b>:<br>

. mkdir hadoop

Go to Services > EC2, wait for your instance to be running and for the health checks to pass.<br>

When your instance is running, select your instance name, and click "Connect".<br>

Copy the provided link, e.g : ssh -i "MyKeyPairFile.pem" ubuntu@ec2-8-90-136-200.compute-1.amazonaws.com<br>

Open your Terminal and go the the repository where you've stored the Key Pair file.<br>

Perform as follows:

. chmod 400 MyKeyPairFile.pem

Now execute the given ssh command by your EC2 instance:

. ssh -i "MyKeyPairFile.pem" ubuntu@ec2-3-90-136-245.compute-1.amazonaws.com

You are now logged into your EC2 instance's terminal and ready to install Docker and Cloudera Quickstart.<br>

. sudo apt-get remove docker docker-engine docker.io
. curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
. sudo apt-key fingerprint 0EBFCD88
. sudo add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/ubuntu \ $(lsb_release -cs) \ stable"
. sudo apt-get update
. apt-cache madison docker-ce
. sudo apt install docker.io
. sudo systemctl start docker
. sudo systemctl enable docker
. docker --version
. sudo su
. docker images
. docker ps
. docker pull cloudera/quickstart:latest
. docker run -m 4G --memory-reservation 2G --memory-swap 8G --hostname=quickstart.cloudera --privileged=true -t -i -v $(pwd):/CDH --publish-all=true -p8888 -p8088 cloudera/quickstart /usr/bin/docker-quickstart
<hr>
[![isaac-arnault-AWS-24.png](https://i.postimg.cc/bvp5PDS8/isaac-arnault-AWS-24.png)](https://postimg.cc/xXp5ydPF)
<hr>
If all services are launched on your EC2 Terminal, open your web browser and type the following :

`my-EC2-instance-DNS:32768`

You should land to the login form, use `cloudera / cloudera` as login and password.

Here you go! You can now start using `Hadoop` for testing purposes.
<hr>
[![isaac-arnault-AWS-cloudera.png](https://i.postimg.cc/dQhZ65NP/isaac-arnault-AWS-cloudera.png)](https://postimg.cc/WtP4bwPX)
<hr>
`my-EC2-instance-DNS:32769` for cluster overview
<hr>
[![isaac-arnault-hadoop-cloudera.png](https://i.postimg.cc/BQNpFYjW/isaac-arnault-hadoop-cloudera.png)](https://postimg.cc/dLkGP9Rn)
<hr>
You can install other applications directly from the panel and have your cluster ready for action!
<hr>
[![isaac-arnault-cloudera-CDH.png](https://i.postimg.cc/FF6sKmxw/isaac-arnault-cloudera-CDH.png)](https://postimg.cc/DmqKC9Hc)
<hr>
## Author

* **Isaac Arnault** - Helping devs install Hadoop in a more effective way, cheaply, effortlessly and timelessly.
