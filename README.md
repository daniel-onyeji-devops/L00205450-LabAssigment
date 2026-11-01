# CloudFormation Lab Assignment

**Student:** Daniel Onyeji  
**Student Number:** L00205450  
**Academic Year:** 2026–2027

## 1. Introduction

This assignment demonstrates how I used AWS CloudFormation to create and manage infrastructure using YAML templates. I completed two setups: an S3 static website and a three-tier architecture containing a web server, an application server and a MySQL database.

I used AWS Academy Learner Lab, Visual Studio Code and GitHub during the assignment.

## 2. Part A1(a) – Static Website Using Amazon S3

I created `templates/website.yaml` to deploy an S3 bucket for hosting static website files.

The website includes `index.html` and `error.html`. The homepage displays my name and student number.

Initially, the S3 bucket was private. I investigated using CloudFront, but the AWS Academy environment restricted some permissions. To complete the lab, I configured public read access for the website files and successfully tested the website using its S3 website endpoint.

For a production website, I would prefer CloudFront with a private S3 origin and HTTPS rather than publicly readable S3 objects.

## 3. Part A1(b) – Three-Tier Architecture

I created `templates/three-tier.yaml` to deploy a three-tier AWS architecture.

### Web Tier

An Amazon EC2 instance runs Nginx in a public subnet. It serves the homepage and forwards `/api/` requests to the application server.

### Application Tier

A second EC2 instance runs a Python HTTP application in a private subnet. The application listens on port 8080 and can be accessed through Nginx.

### Database Tier

Amazon RDS runs a MySQL database in private subnets. The database is not publicly accessible, and its security group permits MySQL traffic from the application server's security group.

### Testing

I successfully deployed the CloudFormation stack and confirmed `CREATE_COMPLETE`.

I opened the public web server address and verified that the homepage displayed my name and student number.

I also tested `/api/` and confirmed that Nginx forwarded the request to the Python application.

The RDS MySQL instance showed `Available`. I verified the database subnet group and security group configuration. A successful application-to-database login has not yet been demonstrated.

## 4. Problems Encountered and Improvements

Several problems occurred during deployment.

First, my application server failed because `t3.micro` was not supported in the Availability Zone automatically selected by AWS. I fixed this by explicitly setting the web and application subnets to `us-east-1a`.

Second, RDS failed when I enabled automatic password management through AWS Secrets Manager because the lab did not permit the required secret creation. I changed the template to accept the database password as a CloudFormation parameter with `NoEcho: true`.

I also encountered YAML indentation problems while adding the Python application. I corrected these and validated the template and Python syntax locally.

Using CloudFormation made it easier to recreate the infrastructure after the lab environment was reset.

## 5. Part B – Best Practices

**Security:** I separated public and private subnets and restricted application and database access using security groups. The database was not exposed to the internet. I used a password parameter rather than hardcoding the database password.

**Scalability:** The architecture separates the three tiers so they can be scaled independently. In production, an Application Load Balancer and Auto Scaling groups could improve availability and support increased traffic.

**Performance:** Nginx handles incoming web requests and forwards application requests to Python. For a larger workload, I would add caching, monitoring and more application instances.

**Maintainability:** I used separate CloudFormation templates, meaningful resource names and Git version control. YAML templates make infrastructure easier to review, update and recreate.

## 6. Tools and AI Assistance

I used AWS Academy Learner Lab to deploy and test resources, VS Code to edit YAML and Python code, and GitHub for version control.

I also used OpenAI ChatGPT to help explain AWS concepts, troubleshoot CloudFormation errors, correct YAML indentation, understand three-tier networking and review security practices.

Examples of prompts I used included:

- How do I create an AWS three-tier architecture using CloudFormation?
- How can I configure Nginx to forward requests to a private Python application?
- Why did my EC2 instance fail to launch in an Availability Zone?
- How can I use a CloudFormation `NoEcho` parameter instead of hardcoding an RDS password?
- How do I validate YAML and embedded Python syntax?

I adapted and tested the suggested code in my own AWS Academy environment. Changes included configuring subnet Availability Zones, separating security groups, implementing an Nginx reverse proxy and using a database password parameter.

## 7. Conclusion

This lab helped me understand how infrastructure as code can automate AWS deployments. I gained practical experience with S3 hosting, EC2, VPC networking, Nginx, Python, RDS MySQL, CloudFormation troubleshooting and GitHub.

The most useful lesson was learning how to investigate deployment errors, change the configuration and redeploy successfully.