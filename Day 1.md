# AWS 100 Days of Cloud – Day 1: Create Key Pair

## Scenario
The Nautilus DevOps team is beginning an incremental migration of infrastructure to AWS. As part of the initial setup, an EC2 key pair is required for securely accessing future EC2 instances.

## Requirement
Create an AWS EC2 key pair with the following configuration:

- **Key Pair Name:** `devops-kp`
- **Key Pair Type:** RSA
- **Region:** `us-east-1`
- **Method:** AWS CLI

## Implementation

Created the RSA key pair using the AWS CLI and saved the returned private key locally:

```bash
aws ec2 create-key-pair \
  --key-name devops-kp \
  --key-type rsa \
  --query 'KeyMaterial' \
  --output text > devops-kp.pem \
  --region us-east-1
