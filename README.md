# Infrastructure As A Code /Terraform Accelerator/ 2-Tier or 3-Tier App Deployment using Terraform

## Table of content

   * [Solution Overview](#solution-overview)
   * [General Information](#general-information)
   * [Infrastructure](#infrastructure)
      * [Project Infrastructure](#project-infrastructure)
      * [Infrastructure Architecture](#infrastructure-architecture)
        * [Infrastructure considerations due to demo proposals](#infrastructure-considerations-due-to-demo-proposals)
      * [CI/CD Architecture](#cd-architecture)
      * [Prerequisites](#prerequisites)
      * [Usage](#usage)
   * [Application Code](#application-code)
     * [Client app](#client-app)
     * [Server app](#server-app)
   * [Cleanup](#cleanup)
   
   

## Solution Overview
This repository contains Terraform code to deploy a two-tier/three-tier application. It shows how AWS resources can be used to build an infrastructure that eases the method of app deployment by giving the choice to either create the needed resources from the scratch itself or the functionality to attach pre-defined resources which already exists.

## General Information
The project has been divided into two parts:
- Sample Application: The code for running Application
            client/server: The code is just for demonstration process.
- Infrastructure: It contains the Terraform code to deploy the needed AWS resources for the solution.

## Infrastructure
The Infrastructure folder contains the terraform code to deploy the AWS resources. The Modules folder has been created to store the Terraform modules used in this project. The Templates folder contains the different configuration files needed within the modules. The Terraform state is stored locally in the machine where you execute the terraform commands, but feel free to set a Terraform backend configuration like an AWS S3 Bucket or Terraform Cloud to store the state remotely. The AWS resources created by the script are detailed below:

- AWS Networking Resources
- 2 ECR Repositories
- 1 ECS Cluster
- 2 ECS Services
- 2 Task definitions
- 4 Autoscaling Policies + Cloudwatch Alarms
- 2 Application Load Balancer (Public facing)
- IAM Roles and policies for ECS Tasks, CodeBuild, CodeDeploy and CodePipeline
- Security Groups for ALBs and ECS tasks
- 2 CodeBuild Projects
- 2 CodeDeploy Applications
- 1 CodePipeline pipeline
- 2 S3 Buckets (1 used by CodePipeline to store the artifacts and another one used to store assets accessible from within the application)
- 1 Dynamodb table
- 1 RDS
- 1 SNS topic for notifications

### Project Infrastructure

The following diagram represents the Infrastructure of the following Repository.

<p align="center">
  <img src="Documentation_Images/capstone_project_diagram.png"/>
</p>

## Infrastructure Architecture
The following diagram represents the Infrastructure architecture being used for deployment of Application:

<p align="center">
  <img src="Documentation_Images/Infrastructure_architecture.png"/>
</p>

### Infrastructure considerations due to demo proposals
There are some values which has been hardcoded and some variables with some default values which were set according to the demonstrated app requirement.

Feel free to change or to replace the values dynamically.

Feel fre to create a subscriptor for the SNS topic created by this code, in order to get informed of the status of each finished CodeDeploy deployment.

### CI/CD Architecture
    The following diagram represents the CI/CD architecture being used for deployment of Application:

<p align="center">
  <img src="Documentation_assets/CICD_architecture.png"/>
</p>

### Prerequisites
There are general steps that you must follow in order to launch the infrastructure resources.

Before launching the solution please follow the next steps:

1) Install Terraform, use Terraform v0.13 or above. You can visit [this](https://releases.hashicorp.com/terraform/) Terraform official webpage to download it.
2) Configure the AWS credentials into your machine (~/.aws/credentials). You need to use the following format:

```shell
    [AWS_PROFILE_NAME]
    aws_access_key_id = Replace_with_the_correct_access_Key
    aws_secret_access_key = Replace_with_the_correct_secret_Key
```

3) Generate a GitHub token. You can follow [this](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) steps to generate it.

### Usage

**1.** Fork this repository and create the GitHub token granting access to this new repository in your account.

**2.** Clone this repository and change the directory to the appropriate one:

```bash
cd Infrastructure/
```

**3.** Run Terraform init to download the providers and install the modules

```shell
terraform init 
```
**4.** Run the terraform plan command, feel free to use a tfvars file to specify the variables.
You need to set at least the followign variables:
+ **aws_profile** = according to the profiles name in ~/.aws/credentials
+ **aws_region** = the AWS region in which you want to create the resources
+ **environment_name** = a unique name used for concatenation to give place to the resources names
+ **github_token** = your GitHub token, the one generated a few steps above
+ **repository_name** = your GitHub repository name
+ **repository_owner** = the owner of the GitHub repository used
+ **vpc_choice** =  "yes" if you want to create a new vpc otherwise enter "vpc-id"
+ **s3_choice** = "yes" if you want to create a new s3 bucket otherwise enter "s3-bucket-name"
+ **db_type** = The type of database you want to create.Enter "dynamodb" for DynamoDB, "RDS" for RDS, "both" for both databases. 

```shell
terraform plan -var aws_profile="your-profile" -var aws_region="your-region" -var environment_name="your-env" -var github_token="your-personal-token" -var repository_name="your-github-repository" -var repository_owner="the-github-repository-owner"
```

Example of the previous command with replaced dummy values:

```shell
terraform plan -var aws_profile="demo-deployment" -var aws_region="us-east-1" -var environment_name="demo-deployment-env" -var github_token="your-personal-token" -var repository_name="your-github-repository" -var repository_owner="the-github-repository-owner"
```
 
**5.** Review the terraform plan, take a look at the changes that terraform will execute:

```shell
terraform apply -var aws_profile="your-profile" -var aws_region="your-region" -var environment_name="your-env" -var github_token="your-personal-token" -var repository_name="your-github-repository" -var repository_owner="the-github-repository-owner"
```

**6.** Once Terraform finishes the deployment, you can see the resouces created in your AWS Management Console.

## Application Code

### Client app

The Client folder contains the code to run the frontend. You can add your frontend application code over here.

Edit the Infrastructure/Templates/buildspec.yml accordingly.

### Server app

The Server folder contains the code to run the backend. You can add your backend application code over here.

Edit the Infrastructure/Templates/buildspec.yml accordingly.

## Cleanup

Run the following command if you want to delete all the resources created before:

```shell
terraform destroy -var aws_profile="your-profile" -var AWS_REGION="your-region" -var environment_name="your-env" -var github_token="your-personal-token" -var repository_name="your-github-repository" - var repository_owner="the-github-repository-owner"
```
