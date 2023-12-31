# AWS-DevOps-CI-CD-Project



## Introduction

The project revolves around integrating diverse AWS services like AWS CodeCommit for source code management, CodeBuild for artifact creation, and S3 for artifact storage. The deployment strategy involves CodeDeploy targeting AWS Elastic Beanstalk and an RDS database to support the application.
With this process, I've successfully completed the CI/CD part, showcasing how AWS managed services seamlessly orchestrate the deployment pipeline.

## Beanstalk

Elastic Beanstalk simplifies deployment by abstracting away the need for managing EC2 instances, load balancers, and auto scaling group, offering a streamlined setup for developers. It provides a quick pathway to deploy AWS applications without deep system administration or cloud operations knowledge.

The setup involves naming the application, selecting a platform (such as Tomcat), configuring environment variables, defining security groups, setting up auto-scaling parameters, and more. 

#### Auto Scaling Group
I configured the auto-scaling group to have a minimum of 2 instances and a maximum of 4.

![Auto scaling group](https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/b0c8ad15-95ea-4edf-93d2-7a224d2a13a5)

#### Deployment policy
I opted for a rolling strategy for the deployment policy to ensure updates are applied gradually, minimizing any potential downtime during deployments. This setup balances availability and resource utilization effectively within the application environment.

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
+ **Target Group Configuration:** Updated the health check of the target group in the Beanstalk environment to '/login', specific to the application ('vprofile'). I'd like to assure you that the load balancer settings and stickiness were enabled for seamless traffic management.

## Setting Up AWS CodeCommit for CI/CD Pipeline

The objective is to transition from GitHub to AWS CodeCommit, establishing a version-controlled environment similar to GitHub for the project.

**1. Creating CodeCommit Repository**
+ **Accessing CodeCommit:** Navigated to AWS CodeCommit via the AWS Management Console.
+ **Repository Creation:** Created a repository named 'V Profile' with necessary descriptions and tags, ensuring the AWS CodeGuru option was available for code analysis.

**2. IAM User and SSH Key Setup**
+ **IAM User Creation:** Established an IAM user with programmatic access and specific repository-level access policies.
+ **SSH Key Generation:** Generated SSH keys using SSH key-gen, uploaded the public key to AWS CodeCommit and configured the SSH config file for seamless access.

**3. Cloning Repository and Transitioning Code**
+ **Tested Access:** Ensured successful authentication using SSH to validate access to the CodeCommit repository.
+ **Transition from GitHub:** Switched the remote repository from GitHub to CodeCommit using git remote rm/add origin, checked out all branches, and pushed all changes to the CodeCommit repository.

**4. Pushing Branches and Tags**
+ **Pushing Branches:** Pushed all branches using git push origin with simple automation.
```
for i in `cat /tmp/branches`; do git checkout $i;done

```
+ **Pushing Tags:** Pushed tags using git push tags if applicable to sync all changes to the CodeCommit repository.

![Aws repo view](https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/6c4fe680-247e-4140-a9ec-45a23d333767)

## AWS CodeBuild Setup: Compile & Create Artifacts

**1. Accessing AWS CodeBuild:**

+ Navigated to AWS CodeBuild from the AWS Management Console.
+ Created a project named 'vprofile build'.
+ 
**2. Source Code Configuration:**
  
+ Selected the CodeCommit repository and specified the branch (vp-rem) to fetch the source code.

** 3. Environment Setup:**

+ Utilized AWS managed Ubuntu image as the operating system for the build environment.
+ Chose Linux as the environment type with specified compute sizes.
**4. Build Specification File Creation:**

+ Constructed a build spec file in YAML format for the build process.
``` yaml
version: 0.2

phases:
  install:
   runtime-versions:
      java: corretto8
  pre_build:
    commands:
      - apt-get update
      - apt-get install -y jq 
      - wget https://downloads.apache.org/maven/maven-3/3.8.8/binaries/apache-maven-3.8.8-bin.tar.gz
      - tar xzf apache-maven-3.8.8-bin.tar.gz
      - ln -s apache-maven-3.8.8 maven
      - sed -i 's/jdbc.password=admin123/jdbc.password=00000000000/' src/main/resources/application.properties
      - sed -i 's/jdbc.username=admin/jdbc.username=admin/' src/main/resources/application.properties
      - sed -i 's/db01:3306/00000000000000000000000000000000000:3306/' src/main/resources/application.properties
  build:
    commands:
      - mvn install
  post_build:
    commands:
       - mvn package
artifacts:
  files:
     - '**/*'
  base-directory: 'target/vprofile-v2'

```

+ Utilized phases such as install, pre_build, build, and post_build.
+ Included commands in each phase to install packages, perform pre-build tasks, execute the main build commands, and specify artifacts.

**5. Artifact Upload to S3:**

+ Specified an S3 bucket (specific region for my case, 'US West 1 region') for artifact upload.
+ Set up logging to stream logs to CloudWatch for easier monitoring.

![build and save in s3 bucket](https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/43b2de18-e76a-43ba-bd24-8a43129a0d39)

## Build, Deploy & CodePipeline Setup

**1. Build Verification**
+ I initiated the build job to verify its functionality by starting the build process and monitoring the different phases. While checking the CloudWatch logs, I ensured the successful completion of each phase, including any challenges faced during this process.

![succeeded buid job](https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/d6fc4f90-7ab2-4948-8fce-d5ab64c005ff)

**2. CodePipeline Creation**
+ To integrate services seamlessly, I created the CodePipeline, documenting the steps involved. I named the pipeline, configured advanced settings, selected the source code (CodeCommit), build provider (CodeBuild), deployment target (Beanstalk), etc. I verified each stage before the pipeline's creation.

![cicd-pipeline source- build- deploy](https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/c4cc733f-d03a-4962-b034-e96eb55be3b7)

**3. Deployment and Verification**
+ I successfully created the pipeline, triggering the build and deployment. With the deployment's completion, I verified the successful deployment status and conducted relevant application testing on Beanstalk to ensure functionality.

https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/fc71973e-de9c-445f-b17d-ef5bc6780d4a





