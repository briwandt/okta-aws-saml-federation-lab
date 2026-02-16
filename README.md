# Okta → AWS IAM Role Federation Lab (Workforce Model)

## Project Summary

Configured and troubleshot federated AWS console access using Okta Workforce Identity Cloud and AWS IAM Roles. Identified and resolved a trust policy mismatch between SAML federation (`AssumeRoleWithSAML`) and the Workforce IAM gateway model (`AssumeRole`), demonstrating practical IAM trust boundary debugging and STS event validation.

---

## Overview

This project demonstrates how to configure federated AWS console access using Okta Workforce Identity Cloud and AWS IAM Roles.

Unlike traditional `AssumeRoleWithSAML` federation, the Okta Workforce AWS Account Federation (IAM Role) integration uses an IAM user gateway model. In this model:

- Okta authenticates the user  
- An IAM provisioning user assumes an AWS role via `sts:AssumeRole`  
- AWS grants console access based on the IAM role trust policy  

This lab includes configuration, troubleshooting, trust alignment, and CloudTrail validation.

---

## Architecture



### Key Components

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
- Policy: `IAMFullAccess` (lab scope only)  

This user acts as a controlled gateway to assume AWS roles.

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
Using Principal: Federated with sts:AssumeRoleWithSAML will cause AWS IAM Role SSO failure in this integration model.

Okta Configuration (High-Level)

Added AWS Account Federation (IAM Role) app

Enabled API provisioning

Configured Access Key, Secret Key, and Connected Account ID

Enabled provisioning features

Assigned IAM role to the user

```

---


