# AWS-DevOps-CI-CD-Project



## Introduction

The project revolves around integrating diverse AWS services like AWS CodeCommit for source code management, CodeBuild for artifact creation, and S3 for artifact storage. The deployment strategy involves CodeDeploy targeting AWS Elastic Beanstalk and an RDS database to support the application.
With this process, I've successfully completed the CI/CD part, showcasing how AWS managed services seamlessly orchestrate the deployment pipeline.

## Beanstalk

Elastic Beanstalk simplifies deployment by abstracting away the need for managing EC2 instances, load balancers, and auto scaling group, offering a streamlined setup for developers. It provides a quick pathway to deploy applications on AWS without deep knowledge of system administration or cloud operations.

The setup involves naming the application, selecting a platform (such as Tomcat), configuring environment variables, defining security groups, setting up auto-scaling parameters, and more. 

#### Auto Scaling Group
I configured the auto-scaling group to have a minimum of 2 instances and a maximum of 4.

![Auto scaling group](https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/b0c8ad15-95ea-4edf-93d2-7a224d2a13a5)

#### Deployment policy
For the deployment policy, I opted for a rolling strategy to ensure updates are applied gradually, minimizing any potential downtime during deployments. This setup aims to balance availability and resource utilization effectively within the application environment.

![Deployment policy](https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/9b9ee5b7-ec71-49a7-9494-5b59b19502e5)

## RDS & Application Setup on AWS Elastic Beanstalk

This section outlines the setup process to establish connectivity between the application running on Elastic Beanstalk and the RDS database, ensuring a functional and healthy application environment.

**Step 1: Creating RDS Instance**
+ **Creating RDS:** Using the AWS RDS service, I created a MySQL instance through the console, ensuring it was on the free tier for cost considerations.
+ **Configuring Security:** Adjusted security groups to allow connectivity between the RDS instance and the Beanstalk environment by enabling inbound traffic on port 3306 from the Beanstalk security group.

**Step 2: Initializing the Database**
+ **SSH to Beanstalk:** Accessed the Beanstalk instance via SSH to validate connectivity to the RDS by deploying the DB SQL file.
+ **Establishing Connection:** Installed MySQL client and connected to the RDS from the Beanstalk instance to deploy the SQL file successfully, ensuring connectivity between Beanstalk and RDS.

![Processes](https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/9d5c6689-816b-4ae2-8926-20d32778e76c)



**Step 3: Updating Health Checks**
+ **Target Group Configuration:** Updated the health check of the target group in the Beanstalk environment to '/login', specific to the application ('vprofile'). Ensured the load balancer settings and stickiness were enabled for seamless traffic management.

## Setting Up AWS CodeCommit for CI/CD Pipeline

The objective is to transition from GitHub to AWS CodeCommit, establishing a version-controlled environment similar to GitHub for the project.

**1. Creating CodeCommit Repository**
+ **Accessing CodeCommit:** Navigated to AWS CodeCommit via the AWS Management Console.
+ **Repository Creation:** Created a repository named 'V Profile' with necessary descriptions and tags, ensuring the AWS CodeGuru option was available for code analysis.

2. IAM User and SSH Key Setup
IAM User Creation: Established an IAM user with programmatic access and specific repository-level access policies.
SSH Key Generation: Generated SSH keys using SSH key-gen, uploaded the public key to AWS CodeCommit, and configured the SSH config file for seamless access.
3. Cloning Repository and Transitioning Code
Tested Access: Ensured successful authentication using SSH to validate access to the CodeCommit repository.
Transition from GitHub: Switched the remote repository from GitHub to CodeCommit using git remote rm/add origin, checked out all branches, and pushed all changes to the CodeCommit repository.
4. Pushing Branches and Tags
Pushing Branches: Pushed all branches using git push origin with automation.
```
for i in `cat /tmp/branches`; do git checkout $i;done

```
Pushing Tags: Pushed tags using git push tags if applicable to sync all changes to the CodeCommit repository.
