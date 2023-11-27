# AWS-DevOps-CI-CD-Project



## Introduction

The project revolves around integrating diverse AWS services like AWS CodeCommit for source code management, CodeBuild for artifact creation, and S3 for artifact storage. The deployment strategy involves CodeDeploy targeting AWS Elastic Beanstalk and an RDS database to support the application.
With this process, I've successfully completed the CI/CD part, showcasing how AWS managed services seamlessly orchestrate the deployment pipeline.

## Beanstalk

Elastic Beanstalk simplifies deployment by abstracting away the need for managing EC2 instances, load balancers, and auto scaling group, offering a streamlined setup for developers. It provides a quick pathway to deploy applications on AWS without deep knowledge of system administration or cloud operations.

The setup involves naming the application, selecting a platform (such as Tomcat), configuring environment variables, defining security groups, setting up auto-scaling parameters, and more. 

##### Auto Scaling Group
I configured the auto-scaling group to have a minimum of 2 instances and a maximum of 4.
![Auto scaling group](https://github.com/ataullahkhanrifat/AWS-DevOps-CI-CD-Project/assets/89423331/b0c8ad15-95ea-4edf-93d2-7a224d2a13a5)

