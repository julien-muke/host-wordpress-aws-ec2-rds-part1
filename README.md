# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263) How to Deploy WordPress Website on AWS using EC2, RDS, ALB and more (Part1).


![Blank diagram-19](https://github.com/user-attachments/assets/1d4fa91d-2efd-46f8-b8c4-bbd834f4dbfc)

<div align="center">

<h3 align="center">How to Deploy WordPress Website on AWS using EC2</h3>

   <div align="center">
     Build this hands-on demo step by step with my detailed tutorial on <a href="http://www.youtube.com/@julienmuke/videos" target="_blank"><b>Julien Muke</b></a> YouTube. Feel free to subscribe 🔔!
    </div>
</div>

## 🚨 Tutorial

This repository contains the steps corresponding to an in-depth tutorial available on our YouTube
channel, <a href="http://www.youtube.com/@julienmuke/videos" target="_blank"><b>Julien Muke</b></a>.

If you prefer visual learning, this is the perfect resource for you. Follow my tutorial to learn how to build projects
like these step-by-step in a beginner-friendly manner!

<a href="https://youtu.be/HcqDrF-k7bc?si=vc-ViXgOG4B7IOEl?feature=shared" target="_blank"><img src="https://github.com/sujatagunale/EasyRead/assets/151519281/1736fca5-a031-4854-8c09-bc110e3bc16d" /></a>

## <a name="introduction">🤖 Introduction</a>

Deploying a WordPress website on AWS involves leveraging various AWS services to ensure scalability, reliability, and security. In this guide, we will walk you through the process of setting up a WordPress site using Amazon EC2 for hosting, Amazon RDS for database management, and Amazon Application Load Balancer (ALB) for efficient traffic distribution.

Additionally, we will utilize Amazon Route 53 for domain name management and DNS routing. This comprehensive approach not only simplifies the deployment process but also equips your WordPress site with the robust infrastructure needed to handle varying levels of traffic, ensuring optimal performance and uptime.


## 📐 Architecture Diagram Overview

* Users will request to open WordPress website, that request will be received by Route 53 which is a domain Management Service in AWS.
* We will use Route 53 to host DNS entries of the website's domain.
* Route 53 will send request to Application Load Balancer (ALB), it handles distribution of the traffic, if you have multiple instances of the same website, it will handle all the incoming requests. 
* ALB also support SSL certificate through AWS Certificate Manager, we will use it to issue a new SSL certificate for our domain name and ALB will apply that SSL certificate and send request to EC2 instance.
* EC2 instance is a virtual server where we will install all the needed packages to run WordPress  and create files of our WordPress website.
* We will make EC2 and RDS accessible to public source and enforce security via Security Group rules.
* We will create an EC2 instance which will be our Virtual Server and RDS instance which will be used for Database Hosting.

## <a name="tech-stack">⚙️ Tech Stack</a>

- Amazon EC2
- Amazon Application Load Balancer (ALB)
- Amazon RDS
- Amazon Route 53
- AWS Certificate Manager
- WordPress


## <a name="steps">☑️ Steps</a>

The procedure for deploying this architecture on AWS consists of the following steps:

* Step 1. [Create EC2 for WordPress](#create-ec2-for-wordpress)
* Step 2. [Connect to EC2 instance via Instance connect](#connect-ec2-to-instance)
* Step 3. [Connect to EC2 instance via SSH client (local Terminal)](#connect-ec2-to-ssh)
* Step 4. [Install Apache on Amazon Linux 2](#install-apache)
* Step 5. [Install PHP & MariaDB on Amazon Linux 2](#install-php-mariadb)
* Step 6. [Download the latest WordPress zip file](#download-wordpress)
* Step 7. [Create RDS instance for WordPress website](#rds-wp)
* Step 8. [Connect RDS and EC2 instance](#connect-rds-ec2)
* Step 9. [WordPress Installation on Amazon Linux 2 EC2 instance](#install-wp-on-ec2)

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


## <a name="install-php-mariadb">➡️ Step 5 - Install PHP & MariaDB on Amazon Linux 2</a>

By running this command, you install PHP 8.2 on your Amazon Linux system.

```bash
sudo amazon-linux-extras install php8.2 -y
```

Next, let's install MariaDB, by running following command, you install MariaDB 10.5, which is a popular open-source relational database management system, on your Amazon Linux system.

```bash
sudo amazon-linux-extras install mariadb10.5 -y
```

Let's start the MariaDB service, by running the following command, you start the MariaDB database server, enabling it to accept connections and handle database operations.

```bash
sudo systemctl start mariadb
```

As you can see below the database is running.

![cmd](https://github.com/user-attachments/assets/8cf7ce96-10d0-4db1-b5db-540aedddc3ec)


Let's access the home directory `/home/ec2-user`, enter `cd ~` and `pwd` present home directory. 

![home-user](https://github.com/user-attachments/assets/5eeb3865-f573-4ae1-863a-3b28d869dc09)


## <a name="download-wordpress">➡️ Step 6 - Download the latest WordPress zip file</a>

Let's download latest WordPress files from  their official website.

By running the following command, you download the latest WordPress package to your current directory. This package can then be extracted and used to set up a WordPress site.


```bash
wget https://wordpress.org/latest.tar.gz
```

After the wordpress is download, run `ls` to see the wordpress file `latest.tar.gz` then run `tar -xzf latest.tar.gz` to unzip the file, lastly run `ls` to see the WordPress folder with all the necessary WordPress files.

![cmd-3](https://github.com/user-attachments/assets/69b41341-44c0-4cdc-8b52-8d28a7b52ce5)



## <a name="rds-wp">➡️ Step 7 - Create RDS instance for WordPress website</a>


WordPress needs a database to run properly, we need to create a database before we the installation of WordPress. 

Note: For the database you have two options:
* Option 1. You can create a database locally on EC2 instance and connect it locally via local host.
* Option 2. You can create RDS instance and host your database on it (it's recommend to keep the database separate on RDS instance for durability and security reasons).

Amazon RDS DB instances simplify the deployment and management of relational databases, enabling you to focus on your application development and business logic rather than database maintenance tasks.

To create a DB instance:

 1. Sign in to the AWS Management Console and open the Amazon RDS console at https://console.aws.amazon.com/rds/.
 2. In the upper-right corner of the Amazon RDS console, choose the AWS Region in which you want to create the DB instance. (make sure your AWS Region is the same as your EC2 instance).
 3. In the navigation pane, choose Databases.
 4. Choose Create database, then choose Standard create.
 5. For Engine type, choose MySQL.
 6. For Version, choose the engine version.

 ![RDS-us-east-1](https://github.com/user-attachments/assets/bc2a208b-847b-4b3e-a2e9-40c62b3c305c)

7. Select Free Tier so we don't get unexpected  monthly bill for our database instance.

![RDS-us-east-1 copy](https://github.com/user-attachments/assets/dad49041-fb77-407a-8088-9b0d2e6b21c2)

8. Type a name for your DB instance `wp-database`
9. Type a login ID for the master user of your DB instance `admin`
10. Ender Credentials management, select `Self managed` to create your own password or have RDS create a password
that you manage.
11. Choose `Auto generate password` Amazon RDS can generate a password for you, or you can specify your own password.
12. For Instance confiquration, select `db.t3.micro`

![RDS-us-east-1 copy 2](https://github.com/user-attachments/assets/35cb92e1-30dc-46ed-9f6b-007c25db7bcc)

13. For the Allocated storage, enter the minimum value of `20 GiB`

![RDS-us-east-1 copy 3](https://github.com/user-attachments/assets/e2d06e5d-d3b1-4507-af98-92548eefffd6)


14. In the Connectivity section, make sure to select same VPC as of your EC2 instance and default submit group.
15. For Public Access we want to keep it private for security select `No`
16. In the Connectivity section under VPC security group (firewall), if you select Create new, a VPC security group is created with an inbound rule that allows your local computer's IP address to access the database. Enter VPC security group name `rds-sg`

![RDS-us-east-1 copy 4](https://github.com/user-attachments/assets/24df58e2-4902-4622-bad8-6c12991375c1)

17. Enter initial database name `wp_database`
18. It's a good practice to encrypt your RDS instance I will select default key.

![RDS-us-east-1 copy 5](https://github.com/user-attachments/assets/114cbb9e-3faa-41e7-bc37-15f2566731f4)

Note: AWS provide automated Backup Service which is additional cost. Once you are in production, it's recommend to use it, you can keep database backup up to last 35 days but for this demo I will disable Backup.

19. Enable deletion protection so it can protect your instance from accidental deletion.
20. Click Create database.

Note: Based on the options we selected you can see estimated monthly cost, the Amazon RDS Free Tier is available to you for 12 months. Each calendar month, the free tier will allow you to use the Amazon RDS resources listed below for free:

![RDS-us-east-1 copy 6](https://github.com/user-attachments/assets/239e1adc-a372-4a63-adda-cccfb664e523)

21. It will take about 2 to 5 minutes to create the database, once you see status is `available` click on **view connection details** to see  RS credentials, copy host username and password of the database somewhere safe as you will needed for database connection from our EC2 instance.

![RDS](https://github.com/user-attachments/assets/acd614fd-280b-405b-b938-aa630e846009)



## <a name="connect-rds-ec2">➡️ Step 8 - Connect RDS and EC2 instance</a>

In this sttep, we need to add an inbound rule to RDS Security Group to allow connect to RDS, it's to make sure we can access database from our EC2 instance. 

* Open RDS and go to Security Group, we can see inbound is only allowed from one IP.

![sg1](https://github.com/user-attachments/assets/681d3645-8ce2-49bd-8486-e7ad7e7da3ad)

* We have created two security groups, one for EC2 and one for RDS instance.
* Copy Security Group ID of EC2 instance `wp-security-group`, select RDS Security Group `rds-sg`, select inbound rules and click on EDIT inbound rules.

![indound-rule](https://github.com/user-attachments/assets/d3f131b8-e73f-419e-9729-0ccbc7ed405f)

* we will add rule, select type `MYSOL/Aurora`, Port is auto selected, then paste the ID of EC2 Security Group and add description click on Save rules.


![indound-rule-2](https://github.com/user-attachments/assets/71e56576-b189-4714-a0c6-d9ace515983d)


**Now let's check if these rules are workingopen** 

* EC2 instance dashboard and click on connect. 
* we will just use EC2 instance connect method.

![ec2-connect](https://github.com/user-attachments/assets/18f12bac-e1e2-4f45-8f7a-b68bd9432e1d)


* To quickly verify the connection to database, run the following command:

In my case:

```bash
mysql -h wp-database.cr2g2a04mnil.us-east-l.rds.amazonaws.com -u admin -p
```

In you case (get from connection details to your database wp-database):

```bash
mysql -h wp-database.YOUR-ENDPOINT -u YOUR-USERNAME -p YOUR-PASSWORD
```
Note: Whenever you are typing your password, will not see it writing any text this is a Linux security feature, just paste your password hit Enter.  

* If you see welcome message and my SQL terminal  then you are successfully able to connect to RDS instance.
* To check if the database is created run the following command

```bash
 show databases;
 ```

 * As we can see below `wp_database` is already present, we have successfully connect our database. 


![cmd](https://github.com/user-attachments/assets/184ef34a-1a45-4f2d-a28d-bc04d6d476c0)

 Next we  can start the process of Wordpress installation.


## <a name="install-wp-on-ec2">➡️ Step 9 - WordPress Installation on Amazon Linux 2 EC2 instance</a>

To install WordPresson on Amazon Linux 2 EC2 instance:

1. The following command copies all files and subdirectories from the wordpress directory to the `/var/www/html/` directory, using superuser privileges to ensure the operation has the necessary permissions. This is typically used when setting up a WordPress website on a web server.


```bash
sudo cp -r wordpress/* /var/www/html/
```

2. Run the following command to add the `ec2-user` to the apache group on a Linux system.

```bash
sudo usermod -a -G apache ec2-user
```

3. By running the following command, you changes the ownership of the `/var/www` directory and all its contents to the user ec2-user and the group apache. This is often done to ensure that the `ec2-user` has the necessary permissions to manage web files and that the apache group has the appropriate access to these files, facilitating web server operations.


```bash
sudo chown -R ec2-user:apache /var/www
```

4. The following command is typically used in web server environments to ensure proper permissions for web server processes and users, facilitating collaboration and proper access control.


```bash
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
```

5. The following command is used in a Unix-like operating system (such as Linux) to change the permissions of all files within the /var/www directory. The command finds all files within the `/var/www` directory and sets their permissions to `0664`.

```bash
find /var/www -type f -exec sudo chmod 0664 {} \;
```

6.  Let's move to HTML folder by running

```bash
cd /var/www/html
```

7. As we can see below, we have `wp-config-sample.php` file, we will create a copy of this file and name it `wp-config.php`.  This file contains configuration of WordPress website.


![cmd4](https://github.com/user-attachments/assets/3b164752-b00b-4708-b4f5-152bc8681126)

8. Now let's edit `wp-config.php` file by running `nano wp-config.php`

* On the `wp-config.php` we need to change database parameters so WordPress can connect to our database
* Enter your database name, username and password, then paste host of the RDS instance as shown below.
* Once you are done editing hit Control + X to exit.

![edit-rds](https://github.com/user-attachments/assets/89e6eb41-5746-4291-bf7f-8d1a69c9f7a7)

* To check the changes we made run the command `cat wp-config.php`. As you can see below our file is modified with our database information.

![edit-rds2](https://github.com/user-attachments/assets/7e2546b2-431f-458b-b83e-345233c1e748)

* Next, we need to allow WordPress to use permalinks in Apache server, we will edit a parameter in Apache configuration file.
To do that, we need to use the following command to open config file in VIM Editor.

```bash
sudo vim /etc/httpd/conf/httpd.conf
```

* Change the `AllowOverride None` to `AllowOverride All`

Note: To type in VIM Editor, first press `i` key to get into insert mode, then you can  
type when you want, to quit from VIM Editor press `Escape(Esc)` key then write `:wq` and press Enter.


![edit-rds3](https://github.com/user-attachments/assets/c6d3d20b-ddda-4aad-b401-3f4c2247282b)

* To make sure everything is saved and new settings are applied, we need to restart Apache server with restart command:

```bash
sudo systemctl restart httpd
```

* Go back EC2 instance console, and click on public IP of the instance.

Note: If you click on the public IP link, sometimes you might get the error connection, that is not an actual server error that is your browser doing `https` request rather than `http` request, just remove s at the end and press enter.

![ip](https://github.com/user-attachments/assets/ae13aa35-6eb2-4d34-8311-08ae8895d8d3)

As you can see below we have the WordPress Welcome Page

* Fill in the information, enter your Site Title, Username, Password and Your Email, then click Install WordPress.

![wp1](https://github.com/user-attachments/assets/e071afd2-b211-496f-a299-7bcbeb602217)


* Now you have successfully installed WordPress on your server.

![wp2](https://github.com/user-attachments/assets/cbc58b58-ef46-4e57-b277-c7b510c6fd14)

* To login enter your email and password and click on login.

![wp3](https://github.com/user-attachments/assets/d562bc2d-a17c-4d84-9ec5-b5e89cebf604)

* It will open the WordPress Admin Panel to manage contents of your website.

![wp4](https://github.com/user-attachments/assets/149d1f31-b95f-4937-a3df-34e403caa48c)


In conclusion, deploying a WordPress website on AWS using EC2, RDS and other AWS services provides a highly scalable, reliable, and secure solution for hosting your website. By following the steps outlined in this guide, you have set up a robust infrastructure that can efficiently handle traffic fluctuations, ensure data integrity, and deliver a seamless user experience. 

This AWS Hands-On tutorial has two parts, this is [PART 01](), Stay tuned for [PART 02] where we will cover Amazon Application Load Balancer (ALB) for efficient traffic distribution and Amazon Route 53 for domain name management and DNS routing.

## 💰 Cost

All services used are eligible for the AWS Free Tier. However, charges will incur at some point so it's recommended that you shut down resources after completing this tutorial.
