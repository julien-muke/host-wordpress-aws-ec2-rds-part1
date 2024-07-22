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

<img width="602" alt="1" src="https://github.com/user-attachments/assets/6a4fbf1d-377e-473d-9ee0-448976625bc9">
