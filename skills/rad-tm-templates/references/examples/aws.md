# AWS Deployment Threat Template

- **Context:** deployment (AWS runtime/infrastructure)
- **Scope:** Threats arising from how a feature is deployed and configured on AWS. Apply to features running on AWS; assess against infrastructure-as-code (Terraform/CloudFormation) and console/config where available.
- **Maintainer:** Cloud security / DevOps · **Version:** v1 (starter) · **Reviewed:** starter — review before production use

| ID | Threat | Description | Default severity | Suggested controls |
|----|--------|-------------|------------------|--------------------|
| AWS-IAM1 | Over-privileged IAM | Roles/users/policies grant more than needed (`*` actions/resources), widening blast radius | High | Least-privilege policies; no wildcard admin; scoped resource ARNs; IAM Access Analyzer |
| AWS-IAM2 | Long-lived / static credentials | Access keys embedded in code or not rotated | High | IAM roles over static keys; short-lived STS creds; rotation; no keys in code/AMIs |
| AWS-DP1 | Public/exposed storage | S3 buckets, snapshots, or volumes unintentionally public or broadly shared | High | Block Public Access; bucket policies least-privilege; review ACLs; access logging |
| AWS-DP2 | Data unencrypted at rest | RDS/S3/EBS/EFS without encryption | Moderate | Enforce KMS encryption at rest (e.g. `storage_encrypted = true`); customer-managed keys for sensitive data |
| AWS-DP3 | Data unencrypted in transit | Internal/external traffic without TLS | Moderate | TLS on ALB/NLB listeners and service-to-service; enforce HTTPS; redirect HTTP |
| AWS-NET1 | Overly open security groups / NACLs | Ingress from `0.0.0.0/0` to sensitive ports, flat networks | High | Least-privilege security groups; private subnets for data tiers; no public DB; restrict mgmt ports |
| AWS-NET2 | Missing network segmentation | No separation between tiers/environments | Moderate | VPC segmentation; private subnets; VPC endpoints; separate prod/non-prod accounts |
| AWS-SEC1 | Secrets not managed | Secrets in env vars, AMIs, or task definitions instead of a secret store | High | AWS Secrets Manager / SSM Parameter Store (SecureString); inject at runtime; rotation |
| AWS-LOG1 | Insufficient audit logging | CloudTrail/Config/flow logs disabled or not retained | Moderate | Enable CloudTrail org-wide; VPC flow logs; centralized, tamper-resistant log retention |
| AWS-MON1 | No detection/alerting | Malicious activity goes unnoticed | Low | GuardDuty; Security Hub; CloudWatch alarms on anomalous activity |
| AWS-KEY1 | Weak KMS key policy | KMS keys usable by overly broad principals | Moderate | Scoped key policies; separate keys per data domain; rotation enabled |
| AWS-SVC1 | Insecure service exposure | Functions/containers/APIs reachable publicly without authN or WAF | High | API Gateway authorizers; WAF; private integrations; auth at the edge |
| AWS-SUP1 | Untrusted images/artifacts | Container images or Lambda layers from unverified sources | Moderate | Image scanning (ECR); signed/verified artifacts; pinned base images; private registries |
| AWS-CFG1 | Drift / unmanaged changes | Console changes diverge from IaC, bypassing review | Low | IaC as source of truth; drift detection; restrict console write access; change review |
