# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263) Building a Scalable WordPress Website with AWS EC2, RDS, and Apache Webserver.


## <a name="introduction">🤖 Introduction</a>

In this step-by-step tutorial, we guide you through the process of creating an EC2 instance, demonstrating how to effortlessly connect to it and install essential packages like Apache, MariaDB, and more. 

Learn how setup an RDS instance, establishing a seamless connection between your EC2 and RDS, and finally, installing WordPress for a fully functional website. 


## <a name="design">📐 Architecture Diagram</a>


![Blank diagram-18](https://github.com/user-attachments/assets/7a3bd865-7167-4b54-8628-e3f56a9f31f6)


## <a name="steps">☑️ Steps</a>

The procedure for deploying this architecture on AWS consists of the following steps:

* Step 1. [Create EC2 for WordPress](#create-ec2-for-wordpress)
* Step 2. [Connect to EC2 instance via Instance connect](#connect-ec2-to-instance)
* Step 3. [Connect to EC2 instance via SSH client (local Terminal)](#connect-ec2-to-ssh)
* Step 4. [Install Apache on Amazon Linux 2](#install-apache)
* Step 5. [Install PHP on Amazon Linux 2](#install-php)
* Step 6. [Install MariaDB on Amazon Linux 2](#instance-mariadb)
* Step 7. [Download the latest WordPress zip file](#download-wordpress)
* Step 8. [Database option on AWS for WordPress website](#wp-database)
* Step 9. [Create RDS instance for WordPress website](#rds-wp)
* Step 10. [Connect RDS and EC2 instance](#connect-rds-ec2)
* Step 11. [WordPress Installation on Amazon Linux 2 EC2 instance](#install-wp-on-ec2)

## <a name="create-ec2-for-wordpress">➡️ Step 1 - Create EC2 for WordPress</a>

You can launch an EC2 instance using the AWS Management Console as described in the following procedure.

To launch an instance:

1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
2. From the EC2 console dashboard, in the Launch instance pane, choose Launch instance.

![1](https://github.com/user-attachments/assets/6a4fbf1d-377e-473d-9ee0-448976625bc9)

3. Under Name and tags, for Name, enter a descriptive name for your instance `WP-instance`.

![screencapture-us-east-1-console-aws-amazon-ec2-home-2024-07-16-17_28_49](https://github.com/user-attachments/assets/1a4e6b48-b2a5-4d27-b5d0-78e574641e09)


4. Under Application and OS Images (Amazon Machine Image), Choose Quick Start, and then choose the operating system (OS) for your instance. For your first Linux instance, we recommend that you choose Amazon Linux.
5. From Amazon Machine Image (AMI), select `Amazon Linux 2 AMI`, that is marked Free Tier eligible.

![screencapture-us-east-1-console-aws-amazon-ec2-home-2024-07-16-17_28_49 copy](https://github.com/user-attachments/assets/ceaf67fa-b784-468c-978c-0d089d8029de)

6. Under Instance type, for Instance type, choose t2.micro, which is eligible for the Free Tier. 
7. Under Key pair (login), for Key pair name, choose an existing key pair or choose Create new key pair to create your first key pair.

![Screenshot 2024-07-22 at 12 36 01](https://github.com/user-attachments/assets/08865853-85fd-46b3-8a96-0ac68a8fda8c)

Note: If you choose Proceed without a key pair (Not recommended), you won't be able to connect to your instance using the methods described in this tutorial.

8. Enter Key pair name `wp-instance`, for Private key file format select .pem for use with OpenSSH.

![Screenshot 2024-07-22 at 12 40 40](https://github.com/user-attachments/assets/7fee1bf8-152e-4087-9e7c-c734179ef016)

9. Under Network settings, notice that we selected your default VPC, selected the option to use the default subnet in an Availability Zone that we choose for you, and configured a security group with a rule that allows connections to your instance from anywhere. For your first instance, we recommend that you use the default settings.

10. Make sure Auto-assign public IP is enabled, because we are creating the server in public subnet
11. Under Firewall (security groups), choose Create security group, enter security group name `WP-security-group`

![screencapture-us-east-1-console-aws-amazon-ec2-home-2024-07-16-17_28_49 copy 2](https://github.com/user-attachments/assets/215d34c6-b973-4428-95fb-229ebcc3c3aa)

12. Under Inbound Security Group Rules, let's add two more rules:
* The first one is `HTTP` Rule and allow it from `anywhere`, everyone can make HTTP request to  the server.
* The second one is `HPPTS` Rule and allow it from `anywhere`, everyone can make HTTPS request to  the server.

![screencapture-us-east-1-console-aws-amazon-ec2-home-2024-07-16-17_28_49 copy 3](https://github.com/user-attachments/assets/0044974a-25ff-4329-8647-a236a04391ad)


13. For the storage you can get 30 GB under free tier, i will just keep it 15 GB which is sufficient and click on launch instance.

![Screenshot 2024-07-22 at 13 42 19](https://github.com/user-attachments/assets/1c9c495a-62cd-4044-9269-471fb376951e)


## <a name="connect-ec2-to-instance">➡️ Step 2 - Connect to EC2 instance via Instance connect</a>

1. After the instance is created and its on the running state, choose instance ID, and click on Connect button. 

![connect-ec2](https://github.com/user-attachments/assets/7a03613e-2f50-4aff-b791-0e7070d3527e)

2. Under EC2 Instance Connect, click Connect

![connectec2-2](https://github.com/user-attachments/assets/336529a0-4913-438a-915e-036213536d49)

3. if you see the welcome message from the AWS command line, then you are successfully connected to the server.

![aws-command-line](https://github.com/user-attachments/assets/23070b03-c9a6-4b16-ad84-d9097d0a5444)

## <a name="connect-ec2-to-ssh">➡️ Step 3 - Connect to EC2 instance via SSH client (local Terminal)</a>

There is another way to Connect to EC2 instance which is via SSH client, local Terminal to connect to EC2 instance via SSH client.

1. Select the instance, and choose Connect button.
2. Choose `SSH client`, copy the SSH command to connect to EC2 instance, and paste it into your terminal.

![ssh](https://github.com/user-attachments/assets/f6b17555-1de0-46ac-bb9b-ae926a941d96)


3. Open your terminal in your computer and paste the SSH command (make you sure your key pair is saved in same folder as your local user in your computer).

4. Click `Yes` to continue then click Enter.

![terminal](https://github.com/user-attachments/assets/9db2dd0e-4874-4ff4-9fed-8ed1260da6a2)


## <a name="install-apache">➡️ Step 4 - Install Apache on Amazon Linux 2</a>

We will run following command to ensure that all packages on the system are up-to-date with the latest patches and versions.

```bash
sudo yum update -y
```

By running the following command, you install the Apache web server on your system, which can then be used to host websites and serve web content.

```bash
sudo yum install -y httpd
```

Let's start the Apache HTTP Server service by running the following command, By running this command, you initiate the Apache web server, enabling it to begin serving web content.

```bash
sudo service httpd start
```

To test it, go back to your EC2 console, copy the public IP of your EC2 instance and paste it in your browser.

![ec2-apache](https://github.com/user-attachments/assets/8f015343-36a2-49a2-8e67-c96c15bce417)

![apache](https://github.com/user-attachments/assets/c5348565-3c93-4f56-a45f-f55461c87a8b)






