# Okta → AWS SAML Federation Lab

## Overview
This lab demonstrates how to configure identity federation between Okta (IdP) and AWS (SP) using SAML 2.0. It includes SAML app configuration, IAM role mapping, CloudTrail validation, and troubleshooting common federation issues.

## Architecture
- Okta SAML Application
- AWS IAM Roles
- AWS SAML Provider
- Attribute mappings (Role, RoleSessionName, SessionDuration)
- CloudTrail logging for AssumeRoleWithSAML

## Steps (High-Level)
1. Configure Okta SAML application
2. Configure AWS as the service provider
3. Map Okta groups to IAM roles
4. Test SSO login
5. Validate CloudTrail events

## CloudTrail Evidence
See `/logs/cloudtrail-saml.json` for sample events.

## Misconfigurations Tested
- Incorrect role mapping
- Missing attribute statements
- Session duration mismatch
- Wrong SAML provider ARN

## Repository Structure
- `/screenshots` — UI screenshots
- `/configs` — SAML metadata, role mappings
- `/diagrams` — architecture diagrams
- `/logs` — CloudTrail samples

## Notion Case Study
Full write-up: *[paste your Notion link here]*

