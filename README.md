# Biro Pipeline - CI/CD with AWS CodePipeline

## Overview
A fully automated CI/CD pipeline that deploys a web application 
to an EC2 instance using AWS CodePipeline, CodeBuild, 
CloudFormation and CodeDeploy.

## Architecture
Source (CodeCommit) → Build (CodeBuild) → Infrastructure 
(CloudFormation) → Manual Approval → Deploy (CodeDeploy)

## Prerequisites
- AWS Account
- IAM permissions for CodePipeline, CodeBuild, CodeDeploy, 
  CloudFormation, EC2 and IAM
- VPC with at least one subnet

## Pipeline Stages

### 1. Source
- Provider: AWS CodeCommit
- Monitors the main branch for changes

### 2. Build
- Provider: AWS CodeBuild
- Runs tests against index.html
- Packages artifact for deployment

### 3. CloudFormation
- Creates EC2 instance, IAM role and Security Group
- Instance is tagged as CodeDeployServer

### 4. Manual Approval
- Pipeline pauses for human review
- Check EC2 instance in AWS Console under 
  EC2 → Instances → CodeDeployServer for the public IP

### 5. Deploy
- Provider: AWS CodeDeploy
- Deploys application to EC2 instance

## Required IAM Roles
| Role | Purpose |
|------|---------|
| AWSCodePipelineServiceRole | Orchestrates all pipeline stages |
| CodePipeline-CloudFormation-Role | Creates AWS resources via CloudFormation |
| CodeDeployServiceRole | Manages CodeDeploy deployments |
| BiroStack-EC2Role | Allows EC2 instance to access AWS services |

## Key Files
| File | Purpose |
|------|---------|
| `buildspec.yml` | CodeBuild build instructions |
| `template.yaml` | CloudFormation infrastructure template |
| `appspec.yml` | CodeDeploy deployment instructions |
| `index.html` | Web application |

## Infrastructure (CloudFormation)
- EC2 instance: t2.micro
- Region: us-east-1
- VPC: vpc-03aec1134175388ea
- Subnet: subnet-04e5817180c4561b4
- Ports open: 80 (HTTP), 22 (SSH)

## Troubleshooting
Common issues encountered and fixes:
- **IAM permissions errors** — Add missing permissions to the 
  relevant role as indicated in the error message
- **No BuildArtif in S3** — Ensure buildspec.yml has an 
  artifacts section
- **Subnet not found** — Specify SubnetId explicitly in 
  CloudFormation template
