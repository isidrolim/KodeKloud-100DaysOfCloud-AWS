# 100 Days of Cloud AWS – Day 001: Create Key Pair

## Scenario

The Nautilus DevOps team is preparing for a gradual migration of infrastructure to AWS. As part of the initial setup, an EC2 key pair is required for securely accessing future EC2 instances.

## Requirement

Create an AWS EC2 key pair with the following configuration:

- **Key Pair Name:** `devops-kp`
- **Key Type:** RSA
- **Region:** `us-east-1`

## Implementation

Created the key pair using the AWS CLI from the `aws-client` host.

```bash
aws ec2 create-key-pair \
  --key-name devops-kp \
  --key-type rsa \
  --query 'KeyMaterial' \
  --output text > devops-kp.pem
```

The command:

1. Creates an RSA key pair named `devops-kp` in AWS.
2. Returns the private key material when the key pair is created.
3. Redirects the private key into `devops-kp.pem`.

Secure the private key permissions:

```bash
chmod 400 devops-kp.pem
```

## Validation

Verify that the key pair exists in AWS:

```bash
aws ec2 describe-key-pairs \
  --key-names devops-kp \
  --query 'KeyPairs[*].[KeyName,KeyType]' \
  --output table
```

Expected result:

```text
devops-kp    rsa
```

Optionally verify the private key file:

```bash
ls -l devops-kp.pem
```

## Result

✅ Key pair `devops-kp` successfully created.

✅ Key type is `RSA`.

✅ Private key saved locally as `devops-kp.pem`.

✅ Challenge successfully completed and validated.

## Key Takeaway

When `aws ec2 create-key-pair` is executed, AWS stores the **public key**, while the **private key is returned only once during creation**. The private key should therefore be saved securely and never committed to a Git repository.

A useful `.gitignore` rule for AWS lab repositories is:

```gitignore
*.pem
```
