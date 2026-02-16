# Okta → AWS IAM Role Federation Lab (Workforce Model)

## Overview

This lab demonstrates how to configure federated AWS console access using Okta Workforce Identity Cloud and AWS IAM Roles.

Unlike traditional `AssumeRoleWithSAML` federation, the Okta Workforce AWS Account Federation (IAM Role) app uses an IAM user gateway model. In this model:

- Okta authenticates the user
- An IAM provisioning user assumes an AWS role using `sts:AssumeRole`
- AWS grants console access based on the IAM role trust policy

This project includes configuration, troubleshooting, trust policy correction, and CloudTrail validation.

---

## Architecture

User → Okta (IdP) → IAM Provisioning User → IAM Role → AWS Console

### Components

- Okta Workforce AWS Account Federation app
- IAM Provisioning User (`Okta-Provisioning`)
- IAM Role (`Okta-Admin-Role`)
- AWS STS (`AssumeRole`)
- AWS CloudTrail logging

---

## AWS Configuration

### IAM Provisioning User

- Name: `Okta-Provisioning`
- Programmatic access only
- Policy: `IAMFullAccess` (lab use only)

This user acts as a gateway to assume AWS roles.

### IAM Role

- Name: `Okta-Admin-Role`
- Attached Policy: `AdministratorAccess`

### Required Trust Policy

The IAM role must trust the IAM provisioning user (NOT a SAML provider):

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::ACCOUNT_ID:user/Okta-Provisioning"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}

