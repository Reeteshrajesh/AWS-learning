# AWS IAM (Identity and Access Management) - Deep Dive

## 📌 Purpose

IAM is a foundational AWS service that enables **secure control of access** to AWS services and resources. It lets you **manage users, groups, roles, and permissions**.

---

## 🔐 Key Concepts

### 1. **Principals** (Entities that interact with AWS)

* **IAM Users**: Individual identities with long-term credentials (username/password or access keys).
* **IAM Groups**: Collections of users for applying the same policies.
* **IAM Roles**: Identities with temporary credentials, assumed by trusted entities (users, services, or applications).
* **Federated Users**: External identities (via SSO, AD, etc.) granted temporary access.

---

### 2. **Policies** (Permissions Definition)

* JSON documents defining what actions are allowed or denied.
* Attached to users, groups, or roles.
* **Types:**

  * **Identity-Based Policies**: Attached to IAM identities (users, groups, roles).
  * **Resource-Based Policies**: Attached directly to resources (like S3 bucket policies).
  * **Permission Boundaries**: Max permissions a role/user can get.
  * **Session Policies**: Passed when assuming a role.
  * **Service Control Policies (SCP)**: For AWS Organizations – limit permissions across accounts.

#### Example:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::example-bucket"
    }
  ]
}
```

---

### 3. **IAM Permissions Evaluation Logic**

* **Explicit Deny > Allow > Implicit Deny**
* By default, all actions are denied.
* Policies must explicitly allow.
* If there’s any `Deny` in any policy, it overrides `Allow`.

---

### 4. **IAM Roles Deep Dive**

* Temporary credentials via STS.
* Trusted entities must be specified (who can assume the role).
* Roles are used in:

  * EC2 Instances (via instance profile)
  * Lambda functions
  * Cross-account access
  * Federated access

---

### 5. **Best Practices**

* Enforce MFA for root and IAM users.
* Grant least privilege.
* Use roles for applications and services.
* Use IAM Access Analyzer.
* Rotate credentials regularly.
* Use permission boundaries to limit scope.

---

## 🔍 Use Cases

* Create IAM Role for EC2 to access S3
* Create a user with read-only access to DynamoDB
* Use IAM Identity Center for workforce identity federation

---

## 🔧 Tools and Monitoring

* **IAM Access Analyzer**: Detects overly permissive policies.
* **AWS Config**: Tracks IAM config changes.
* **CloudTrail**: Logs all IAM activity.
* **IAM Credential Reports**: Review credential usage

---

## 📘 Exam Tips (AWS SAA-C03)

* IAM is a **100% must-know**.
* Expect questions on:

  * Policy evaluation logic
  * Least privilege
  * Cross-account roles
  * Identity vs resource-based policies
  * Role assumption and trust policies

---

## ✅ Summary

| Component       | Description                                |
| --------------- | ------------------------------------------ |
| User            | Long-term credentials                      |
| Group           | Apply same policy to multiple users        |
| Role            | Temporary credentials, assumed by entities |
| Policy          | Permissions in JSON format                 |
| Access Analyzer | Find unintended access                     |
| SCP             | Control access at org/account level        |



# AWS IAM Deep Dive (Advanced Topics)

This section extends the existing IAM knowledge by covering **advanced and exam-critical IAM concepts** not yet documented.


## 1. IAM Roles - Advanced Use Cases

### EC2 IAM Roles

* Attach IAM roles to EC2 instances to provide **temporary credentials** for accessing AWS services like S3, DynamoDB, etc.
* Avoid storing AWS credentials inside the instance.

### Lambda IAM Roles

* Lambda **execution roles** grant permissions for logging to CloudWatch, accessing S3, etc.
* IAM policies must grant **least privilege**.

### Cross-Account Access

* Use IAM roles with **trust policies** allowing principals from other AWS accounts.
* Assumed using `sts:AssumeRole` API.

### OIDC & Web Identity Federation

* Used by services like **EKS pods** to assume IAM roles via OIDC.

---

## 2. IAM Policy Deep Dive

### Conditions

* `StringEquals`, `StringLike`, `Bool`, `IpAddress`, `DateGreaterThan`, etc.
* Can enforce MFA, source IP ranges, encryption requirements, etc.

### Wildcards and Prefixes

* `s3:Get*` = all GET actions in S3.
* Use sparingly to avoid over-permissive policies.

### Inline vs Managed Policies

* **Inline**: tightly coupled with one identity (harder to manage at scale).
* **Managed**: reusable across users, groups, and roles.

---

## 3. IAM Permission Boundaries

* Used to set **maximum allowed permissions** for a user or role.
* Useful when delegating IAM creation to developers.
* Boundaries + inline/managed policies must both allow an action.

---

## 4. AWS STS (Security Token Service)

### Temporary Credentials

* Used for IAM role assumption, federated access, or session-based login.

### Key APIs:

* `AssumeRole`: Role chaining, CLI access, cross-account delegation
* `AssumeRoleWithSAML`: SSO with corporate directories
* `AssumeRoleWithWebIdentity`: Cognito, OIDC (EKS, etc.)

---

## 5. IAM Policy Evaluation Logic

### Evaluation Order

1. **Explicit Deny** (always overrides)
2. **Allow** (from identity or resource policies)
3. **Implicit Deny** (default)

### Combined Policies

* Identity policy (attached to user/group/role)
* Resource-based policy (e.g., S3 bucket policy)
* Session policy (STS)
* SCPs (AWS Organizations)

---

## 6. IAM Access Analyzer

* Identifies resources shared **publicly** or **across accounts**.
* Supports S3, KMS, IAM, Lambda, SQS, Secrets Manager.
* Helps tighten over-permissive configurations.

---

## 7. Service-Linked Roles

* Created and managed by AWS services automatically (e.g., Elastic Beanstalk, Auto Scaling).
* Cannot be manually created.
* Example: `AWSServiceRoleForAutoScaling`

---

## 8. IAM Policy Simulator

* Tool to test and validate permission policies.
* Helps troubleshoot **why access is denied or allowed**.
* Web UI and CLI: `aws iam simulate-principal-policy`

---

## ✅ Summary

For AWS SAA exam, understand IAM **beyond basics** — especially in these areas:

* Policy structure & evaluation logic
* STS and trust-based role assumption
* Conditions and permission boundaries
* IAM roles across services (EC2, Lambda, EKS)
* Security and best practices (least privilege, MFA, etc.)

