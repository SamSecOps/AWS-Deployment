# AWS Identity and Access Management and Cloud Security Project

![Project Status](https://img.shields.io/badge/status-completed-success)
![AWS](https://img.shields.io/badge/AWS-cloud%20security-orange)
![IAM](https://img.shields.io/badge/IAM-least%20privilege-blue)
![Amazon Bedrock](https://img.shields.io/badge/Amazon%20Bedrock-RAG-purple)

## Project Overview

This repository documents a hands-on Amazon Web Services cloud security project developed for a simulated organization called **SamSecOps**, meaning Samuel Security Operations.

The project demonstrates how identity security, resource isolation, encryption, audit logging, event-driven alerting, and governed artificial intelligence can be combined in one Amazon Web Services environment.

The implementation was completed in the **US East (Ohio) Region, `us-east-2`** and focused on the following areas:

- Root account protection with Multi-Factor Authentication
- Administrative access through a dedicated Identity and Access Management user
- Department-based Identity and Access Management groups and users
- Least-privilege access to Amazon Simple Storage Service buckets
- Controlled access to Amazon Elastic Compute Cloud instances
- Management-event logging with AWS CloudTrail
- Automated Amazon Simple Storage Service bucket alerts using Amazon EventBridge and Amazon Simple Notification Service
- Encryption of an Amazon DynamoDB table with a customer-managed AWS Key Management Service key
- A governed Amazon Bedrock knowledge base using Retrieval-Augmented Generation

> **Important:** This was a controlled cloud security lab. The users, departments, resources, and data were created for learning and demonstration purposes only.

---

## Project Objectives

The main objectives were to:

1. Secure the Amazon Web Services root account and avoid using it for routine administration.
2. Create a structured identity model based on departmental responsibilities.
3. Apply least-privilege permissions to storage, compute, database, and encryption resources.
4. Separate Information Technology administration from Cybersecurity oversight.
5. Record account activity for auditing and investigation.
6. Generate email alerts when an Amazon Simple Storage Service bucket is created or deleted.
7. Protect sensitive database records with customer-managed encryption.
8. Build an internal artificial intelligence chatbot grounded in approved documents.

---

## Architecture Summary

```text
AWS Root Account
      |
      | Multi-Factor Authentication enabled
      v
Administrative IAM User
      |
      +----------------------+----------------------+----------------------+
      |                      |                      |                      |
 Marketing Group       Sales Group             IT Group        Cybersecurity Group
      |                      |                      |                      |
 Assigned S3 and EC2   Assigned S3 and EC2   Assigned S3 and EC2   Elevated lab access
                                                                     across resources

CloudTrail Management Events
      |
      v
Amazon EventBridge Rule
      |
      v
Amazon SNS Topic
      |
      v
Email Security Alert

AWS KMS Customer-Managed Key
      |
      v
Encrypted Amazon DynamoDB Table

Approved Documents in Amazon S3
      |
      v
Amazon Bedrock Knowledge Base
      |
      v
Retrieval-Augmented Generation Chatbot
```

---

## Amazon Web Services Used

| Service | Full name | Purpose in the project |
|---|---|---|
| AWS IAM | AWS Identity and Access Management | Created users, groups, administrative access, and permission policies |
| Amazon S3 | Amazon Simple Storage Service | Hosted departmental objects, CloudTrail logs, and knowledge base documents |
| Amazon EC2 | Amazon Elastic Compute Cloud | Provided departmental server instances for access-control testing |
| AWS CloudTrail | AWS CloudTrail | Recorded management events and account application programming interface activity |
| Amazon SNS | Amazon Simple Notification Service | Delivered security alerts to a confirmed email subscriber |
| Amazon EventBridge | Amazon EventBridge | Detected selected CloudTrail events and routed them to Amazon Simple Notification Service |
| AWS KMS | AWS Key Management Service | Created and managed the encryption key used by Amazon DynamoDB |
| Amazon DynamoDB | Amazon DynamoDB | Hosted the encrypted lab database table |
| Amazon Bedrock | Amazon Bedrock | Hosted the knowledge base and Retrieval-Augmented Generation chatbot |

---

## Organizational Access Model

Four departmental groups were created, with two Identity and Access Management users assigned to each group.

| Group | Users | Amazon S3 access | Amazon EC2 access | Amazon DynamoDB access | AWS KMS access |
|---|---:|---|---|---|---|
| Marketing | 2 | Assigned bucket only | Assigned instance only | Read only | Decrypt for approved lab workflow |
| Sales | 2 | Assigned bucket only | Assigned instance only | Read only | Decrypt for approved lab workflow |
| Information Technology | 2 | Assigned bucket only | Assigned instance only | Read only | Decrypt for approved lab workflow |
| Cybersecurity | 2 | All departmental buckets | All departmental instances | Full lab access | Encrypt and decrypt |

The Cybersecurity group received elevated permissions to simulate security monitoring, investigation, and administrative support across the environment. In a production environment, these permissions should be narrowed to specific operational tasks and protected with permission boundaries, approval workflows, and temporary privileged access.

---

## Implementation Phases

### Phase 1: Root Account Security

- Enabled Multi-Factor Authentication on the Amazon Web Services root account.
- Restricted root usage to account-level tasks that cannot be performed by an Identity and Access Management administrator.
- Used a dedicated administrative Identity and Access Management user for the remaining project activities.

### Phase 2: Administrative Identity

- Created a dedicated administrative Identity and Access Management user.
- Granted administrative permissions for the controlled lab environment.
- Avoided routine use of the root account.

### Phase 3: Identity and Group Structure

- Created Marketing, Sales, Information Technology, and Cybersecurity groups.
- Created eight users, with two users assigned to each group.
- Applied permissions through group membership instead of managing every user separately.

### Phase 4: Amazon S3 Access Control

- Created departmental Amazon Simple Storage Service buckets.
- Kept Block Public Access enabled.
- Applied group-specific policies to limit each department to its assigned bucket.
- Granted the Cybersecurity group broader access for the lab's oversight function.

### Phase 5: Amazon EC2 Access Control

- Created departmental Amazon Elastic Compute Cloud instances.
- Allowed users to start, stop, and reboot only their assigned instances.
- Used resource-specific Amazon Resource Names for state-changing actions.
- Used account-wide `Describe` permissions where Amazon Elastic Compute Cloud requires them.

### Phase 6: Logging and Automated Alerting

AWS CloudTrail was configured to capture management events with both read and write activity enabled.

An Amazon EventBridge rule monitored CloudTrail for these Amazon Simple Storage Service application programming interface calls:

```json
{
  "source": ["aws.s3"],
  "detail-type": ["AWS API Call via CloudTrail"],
  "detail": {
    "eventSource": ["s3.amazonaws.com"],
    "eventName": ["CreateBucket", "DeleteBucket"]
  }
}
```

When either event occurred, Amazon EventBridge sent the event to an Amazon Simple Notification Service topic, which delivered an email alert to the confirmed subscriber.

```text
CreateBucket or DeleteBucket
          |
          v
      AWS CloudTrail
          |
          v
   Amazon EventBridge
          |
          v
       Amazon SNS
          |
          v
       Email Alert
```

### Phase 7: AWS KMS and Amazon DynamoDB Encryption

- Created a symmetric customer-managed AWS Key Management Service key.
- Enabled automatic key rotation.
- Assigned key administrators and key users.
- Created the `Samsecops-PII-DB` Amazon DynamoDB table.
- Linked the customer-managed key to the table for encryption at rest.
- Applied separate database permissions for the Cybersecurity and non-Cybersecurity groups.

### Phase 8: Amazon Bedrock Knowledge Base

An Amazon Bedrock knowledge base was created to demonstrate a governed internal artificial intelligence use case based on Retrieval-Augmented Generation.

The following two articles were uploaded to an Amazon Simple Storage Service bucket as source documents:

1. **Lessons from United Nations Policing Operations in South Sudan: Implications for Internal Security Management in Nigeria**
2. **Nigeria Cannot Defeat Terrorism Without the Trust of Its Communities**

The articles were uploaded **strictly for artificial intelligence lab and demonstration purposes**. They were not presented as official SamSecOps organizational policies or production knowledge base content.

The knowledge base was tested by asking questions related to the uploaded documents and reviewing the generated answers and citations.

---

## Security Controls Demonstrated

- Multi-Factor Authentication for the root account
- Separation of root and administrative access
- Department-based Identity and Access Management groups
- Group-based permission inheritance
- Resource-level Amazon Resource Name scoping
- Amazon Simple Storage Service Block Public Access
- Least-privilege storage and compute permissions
- Separation of Information Technology and Cybersecurity responsibilities
- AWS CloudTrail management-event logging
- Event-driven security alerting
- Customer-managed encryption keys
- Amazon DynamoDB encryption at rest
- Controlled access to an internal artificial intelligence knowledge base
- Grounded artificial intelligence responses using Retrieval-Augmented Generation

---

## Testing and Validation

The following tests were performed during the lab:

| Test | Expected result |
|---|---|
| Sign in as a departmental user | User inherits the permissions assigned to the user's group |
| Access another department's Amazon S3 bucket | Access is denied |
| Access the assigned Amazon S3 bucket | Approved actions succeed |
| Start or stop another department's Amazon EC2 instance | Access is denied |
| Start or stop the assigned Amazon EC2 instance | Approved action succeeds |
| Create or delete an Amazon S3 bucket | CloudTrail records the event and an email alert is generated |
| Review the Amazon DynamoDB encryption configuration | The customer-managed AWS Key Management Service key is shown as linked |
| Query the Amazon Bedrock knowledge base | The response is grounded in the uploaded source documents |
| Ask an unrelated knowledge base question | The system should avoid inventing unsupported answers |

---

## Key Findings

1. Root account hardening is the first critical control in a new Amazon Web Services environment.
2. Group-based permissions simplify administration and reduce inconsistent user-level assignments.
3. Resource-specific policies provide clearer boundaries than broad wildcard permissions.
4. AWS CloudTrail, Amazon EventBridge, and Amazon Simple Notification Service can form a serverless security-alerting pipeline.
5. Customer-managed AWS Key Management Service keys add a separate cryptographic control layer to sensitive data.
6. Retrieval-Augmented Generation can ground artificial intelligence responses in approved source documents.
7. Broad Cybersecurity permissions may be useful for a lab demonstration, but they should not be copied directly into production.

---

## Screenshot Gallery

Create a `screenshots` folder in the repository and save each image using the file names shown below.

### 1. Root Account Multi-Factor Authentication

![Root Account MFA Assigned](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-24%20131210.png)

<!-- Replace screenshots/01-root-account-mfa.png with the screenshot showing MFA assigned on the root Security Credentials page. -->

### 2. Administrative IAM User

![Administrative IAM User](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-24%20110554.png)

<!-- Show the dedicated administrative IAM user and its attached lab permissions. Redact account identifiers and sign-in details. -->

### 3. IAM Groups

![IAM Groups](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20173638.png)

<!-- Show the Marketing, Sales, Information Technology, and Cybersecurity groups. -->

### 4. IAM Users and Group Membership

![IAM Users](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20175524.png)

<!-- Show all eight departmental users and their group memberships. -->

### 5. Amazon S3 Buckets

![Amazon S3 Buckets](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-24%20142838.png)

<!-- Show the departmental Amazon S3 buckets. Redact account-specific information where appropriate. -->

### 6. Marketing Amazon S3 Policy

![Marketing S3 Policy](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20190955.png)

<!-- Show the Marketing group policy scoped to its assigned bucket. -->

### 7. Information Technology and Sales Amazon S3 Policies

![IT and Sales S3 Policies](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-24%20165802.png)

<!-- Show the Information Technology and Sales group policies scoped to their assigned buckets. -->

### 8. Cybersecurity Full Amazon S3 Lab Policy

![Cybersecurity S3 Policy](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20193537.png)

<!-- Show the Cybersecurity group's broader Amazon S3 lab policy. -->

### 9. Amazon EC2 Instances

![Amazon EC2 Instances](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20201506.png)

<!-- Show the departmental Amazon EC2 instances and their running states. Redact public Internet Protocol addresses. -->

### 10. Amazon EC2 Group Policies

![EC2 Group Policies](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-24%20210050.png)

<!-- Show the resource-scoped Amazon EC2 policies for the departmental groups. -->

### 11. AWS CloudTrail Trail

![CloudTrail Trail](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20212132.png)

<!-- Show the active CloudTrail trail and its management-event configuration. -->

### 12. Amazon SNS Topic and Subscription

![SNS Topic and Subscription](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20215245.png)

<!-- Show the Amazon SNS topic and confirmed subscription. Redact the subscriber's email address. -->

### 13. Amazon EventBridge Rule

![EventBridge Rule](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20221942.png)

<!-- Show the rule status, event pattern, and Amazon SNS target. -->

### 14. Email Security Alert

![Email Security Alert](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20222714.png)

<!-- Show the received Amazon S3 create or delete alert. Redact email addresses, account numbers, and unnecessary metadata. -->

### 15. AWS KMS Customer-Managed Key

![KMS Customer Managed Key](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20224759.png)

<!-- Show the enabled customer-managed key, alias, key type, and rotation status. Redact the complete key identifier if publishing publicly. -->

### 16. Amazon DynamoDB Encryption

![DynamoDB KMS Encryption](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20232202.png)

<!-- Show the Samsecops-PII-DB encryption configuration confirming that the customer-managed AWS KMS key is linked. -->

### 17. Amazon DynamoDB and AWS KMS Access Policy

![DynamoDB and KMS Policy](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20225016.png)

<!-- Show the sanitized Identity and Access Management policy used for Amazon DynamoDB and AWS KMS access. -->

### 18. Knowledge Base Source Documents

![Knowledge Base Documents](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-26%20005728.png)

<!-- Show the two approved lab articles in the Amazon S3 knowledge base data-source bucket. -->

### 19. Amazon Bedrock Knowledge Base

![Amazon Bedrock Knowledge Base](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20124320.png)

<!-- Show the knowledge base configuration summary and successful data-source synchronization. -->

### 20. Amazon Bedrock Test Console

![Amazon Bedrock Test Console](https://github.com/SamSecOps/AWS-Deployment/blob/main/screenshots/Screenshot%202026-07-25%20130845.png)

<!-- Show a successful question-and-answer test with source citations. Do not expose private document content beyond what is necessary. -->

---

## Suggested Repository Structure

```text
aws-iam-cloud-security-project/
|
|-- README.md
|-- documentation/
|   |-- AWS-IAM-Cloud-Security-Project.pdf
|
|-- policies/
|   |-- s3/
|   |   |-- marketing-s3-policy.json
|   |   |-- sales-s3-policy.json
|   |   |-- it-s3-policy.json
|   |   |-- cybersecurity-s3-policy.json
|   |
|   |-- ec2/
|   |   |-- marketing-ec2-policy.json
|   |   |-- sales-ec2-policy.json
|   |   |-- it-ec2-policy.json
|   |   |-- cybersecurity-ec2-policy.json
|   |
|   |-- dynamodb-kms/
|       |-- cybersecurity-dynamodb-kms-policy.json
|       |-- departmental-readonly-policy.json
|
|-- eventbridge/
|   |-- s3-create-delete-event-pattern.json
|
|-- screenshots/
|   |-- 01-root-account-mfa.png
|   |-- 02-admin-iam-user.png
|   |-- 03-iam-groups.png
|   |-- 04-iam-users.png
|   |-- 05-s3-buckets.png
|   |-- 06-marketing-s3-policy.png
|   |-- 07-it-sales-s3-policies.png
|   |-- 08-cybersecurity-s3-policy.png
|   |-- 09-ec2-instances.png
|   |-- 10-ec2-group-policies.png
|   |-- 11-cloudtrail-trail.png
|   |-- 12-sns-topic-subscription.png
|   |-- 13-eventbridge-rule.png
|   |-- 14-email-alert.png
|   |-- 15-kms-key.png
|   |-- 16-dynamodb-kms-encryption.png
|   |-- 17-dynamodb-kms-policy.png
|   |-- 18-knowledge-base-documents.png
|   |-- 19-bedrock-knowledge-base.png
|   |-- 20-bedrock-test-console.png
|
|-- LICENSE
```

---

## Security and Privacy Notice

Before publishing screenshots or policies, remove or mask:

- Amazon Web Services account identifiers
- Access key identifiers and secret access keys
- Passwords and sign-in links
- Email addresses and Amazon Simple Notification Service endpoints
- Full AWS Key Management Service key identifiers
- Public Internet Protocol addresses
- Session tokens
- Personally identifiable information
- Any real internal or confidential document content

No credentials, secrets, or live production data should be stored in this repository.

---

## Production Hardening Recommendations

This project was designed as a lab. A production implementation should also include:

- AWS IAM Identity Center for workforce access
- Short-lived roles instead of long-lived access keys
- Permission boundaries and service control policies
- Multi-Factor Authentication for privileged Identity and Access Management users
- AWS Config for continuous configuration assessment
- Amazon GuardDuty for threat detection
- Centralized AWS CloudTrail logging with log-file validation
- Amazon CloudWatch alarms and dashboards
- AWS Key Management Service key deletion safeguards
- Amazon Simple Storage Service versioning and lifecycle rules
- Amazon Bedrock model invocation logging where approved
- Infrastructure as Code using AWS CloudFormation or Terraform
- Narrower Cybersecurity permissions based on specific job functions

---

## Skills Demonstrated

- Amazon Web Services cloud administration
- Identity and access management
- Least-privilege policy design
- JavaScript Object Notation policy development
- Cloud resource isolation
- Event-driven security monitoring
- Audit logging and alert validation
- Encryption and key management
- Database access control
- Secure artificial intelligence deployment
- Retrieval-Augmented Generation
- Technical documentation
- Security testing and evidence collection

---

## Author

**Samuel Agboola**  
Cybersecurity and Cloud Security Practitioner

---

## Disclaimer

This repository is an educational portfolio project created in a controlled Amazon Web Services lab environment. It does not represent a production deployment or an official architecture for any real organization. Resource names, user identities, policies, and documents were used for demonstration purposes.
# AWS-Deployment
Configuration and hardening Cloud console in AWS
