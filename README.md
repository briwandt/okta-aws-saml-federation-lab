Okta → AWS IAM Role Federation Lab (Workforce Model)
Overview

This lab demonstrates how to configure federated AWS console access using Okta Workforce Identity Cloud and AWS IAM Roles.

Unlike traditional AssumeRoleWithSAML federation, the Okta Workforce AWS Account Federation (IAM Role) app uses an IAM user gateway model, where:

Okta authenticates the user

An IAM provisioning user assumes an AWS role via sts:AssumeRole

AWS grants console access based on the role’s trust policy

This project includes full configuration, trust policy alignment, provisioning setup, and CloudTrail validation of the resulting STS events.

Architecture
User → Okta (IdP) → IAM Provisioning User → IAM Role → AWS Console

Components

Okta Workforce AWS Account Federation app

IAM Provisioning User (Okta-Provisioning)

IAM Role (Okta-Admin-Role)

STS AssumeRole

AWS CloudTrail logging

AWS Configuration
1. IAM Provisioning User

Name: Okta-Provisioning

Programmatic access only

Policy: IAMFullAccess (lab scope)

This user acts as a gateway for role assumption.

2. IAM Role

Name: Okta-Admin-Role

Attached policy: AdministratorAccess

3. Critical Trust Policy

The role must trust the IAM provisioning user (NOT a SAML provider):

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


⚠️ Important:

Using:

sts:AssumeRoleWithSAML
Principal: Federated


caused AWS IAM Role SSO failures in this integration model.

Okta Configuration

Added AWS Account Federation (IAM Role) app

Enabled API provisioning

Entered:

Access Key

Secret Key

Connected Account ID

Enabled provisioning features

Assigned IAM role to user via role dropdown

Troubleshooting & Root Cause Analysis
Error Encountered
Amazon AWS IAM Role SSO Error
Your account has not been configured for use with the Amazon IAM Role SSO.

Root Cause

The IAM role trust policy was incorrectly configured for SAML federation:

Principal: Federated
Action: sts:AssumeRoleWithSAML


However, the Workforce IAM Role app uses:

Principal: IAM User
Action: sts:AssumeRole


The mismatch caused AWS to reject the role assumption.

Resolution

Updated trust policy to allow the IAM provisioning user to assume the role.

CloudTrail Evidence

Successful login generates:

eventName: AssumeRole
userIdentity.type: IAMUser
requestParameters.roleArn
sessionContext.sessionIssuer.arn


This differs from classic SAML federation, which logs:

AssumeRoleWithSAML


Understanding this distinction is critical when building detections for federated access.

Security & Detection Considerations

Monitor AssumeRole activity from IAM gateway users

Alert on changes to IAM role trust policies

Detect excessive or anomalous role assumption

Restrict provisioning user permissions in production

Enforce MFA at the IdP

Repository Structure
/screenshots   → Configuration UI captures
/configs       → IAM trust policies, provisioning configs
/diagrams      → Architecture diagrams
/logs          → CloudTrail samples

Lessons Learned

Federation models must align with IAM trust policies

AWS error messages can obscure root cause

Workforce IAM Role integration differs from classic SAML federation

STS event type reveals the true authentication path

Trust boundary debugging is critical in cloud identity engineering

Full Case Study

Detailed walkthrough available here:
Notion Case Study

