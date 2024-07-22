# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263) Building a Scalable WordPress Website with AWS EC2, RDS, and Apache Webserver.


## <a name="introduction">🤖 Introduction</a>

In this step-by-step tutorial, we guide you through the process of creating an EC2 instance, demonstrating how to effortlessly connect to it and install essential packages like Apache, MariaDB, and more. 

Learn how setup an RDS instance, establishing a seamless connection between your EC2 and RDS, and finally, installing WordPress for a fully functional website. 


## <a name="design">📐 Architecture Diagram</a>


![Blank diagram-18](https://github.com/user-attachments/assets/7a3bd865-7167-4b54-8628-e3f56a9f31f6)


## <a name="steps">☑️ Steps</a>

The procedure for deploying this architecture on AWS consists of the following steps:

* [Create EC2 for WordPress](Create-EC2-for-WordPress)
* [Connect to EC2 instance via Instance connect](connect-ec2-to-instance)
* [Connect to EC2 instance via SSH client / local Terminal](connect-ec2-to-ssh)
* [Install Apache on Amazon Linux 2](install-apache)
* [Install PHP on Amazon Linux 2](install-php)
* [Install MariaDB on Amazon Linux 2](instance-mariadb)
* [Download the latest WordPress zip file](download-wordpress)
* [Database option on AWS for WordPress website](wp-database)
* [Create RDS instance for WordPress website](rds-wp)
* [Connect RDS and EC2 instance](connect-rds-ec2)
* [WordPress Installation on Amazon Linux 2 EC2 instance](install-wp-on-ec2)

## <a name="Create-EC2-for-WordPress">➡️ Create EC2 for WordPress</a>

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



