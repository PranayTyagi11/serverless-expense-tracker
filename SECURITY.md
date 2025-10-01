# Security Model

## Overview
This document outlines the security measures, protocols, and best practices implemented in the Expense Tracker application to ensure data protection, secure authentication, and infrastructure security.

## Authentication & Authorization

### AWS Cognito User Pool
- **Email Verification**: Required for all new user registrations  
- **Password Policy**:  
  - Minimum 8 characters  
  - Requires numbers, uppercase, and special characters  
- **JWT Tokens**:  
  - Access tokens valid for 1 hour  
  - Refresh token mechanism for extended sessions  
  - Secure token storage in React application  
- **Multi-Factor Authentication**: Available but optional for users  

### API Security
- **JWT Validation**: All API endpoints validate Cognito JWT tokens  
- **User Isolation**: Database queries are scoped to individual users via `userId` partition key  
- **No Direct Database Access**: Frontend cannot access database directly  

## Data Protection

### Encryption at Rest
- **S3 Buckets**: Server-Side Encryption with Amazon S3-Managed Keys (SSE-S3)  
- **DynamoDB**: AWS managed encryption (AES-256) for all tables  
- **Lambda Environment Variables**: Encrypted using AWS KMS  
- **Terraform State**: Stored locally with no sensitive data exposure  

### Encryption in Transit
- **HTTPS Enforcement**: All API Gateway endpoints require TLS 1.2+  
- **S3 Website**: HTTPS only for static website hosting  
- **Internal Communication**: All AWS service-to-service communication encrypted  

## Access Control & IAM Security

### Principle of Least Privilege
```yaml
Lambda Execution Role:
  Permissions:
    - dynamodb:GetItem (Expenses table only)
    - dynamodb:PutItem (Expenses table only)
    - dynamodb:UpdateItem (Expenses table only)
    - dynamodb:DeleteItem (Expenses table only)
    - dynamodb:Query (Expenses table only)
    - dynamodb:Scan (Expenses table only)
    - logs:CreateLogGroup
    - logs:CreateLogStream
    - logs:PutLogEvents
  Restrictions:
    - No S3 access
    - No other DynamoDB table access
    - No network configuration permissions
```

### S3 Security Controls
- **Block Public Access**: Enabled at account level  
- **Bucket Policies**: No public read/write permissions  
- **CORS Configuration**: Restricted to specific origins  
- **Versioning**: Disabled to reduce cost and complexity  

### Database Security
- **Row-Level Security**: Implemented via `userId` partition key  
- **No Public Access**: DynamoDB has no public endpoints  
- **Backup & Recovery**: Point-in-time recovery available but not configured  

## Infrastructure Security

### Terraform Security
- **State Management**: Local state files (consider remote state for teams)  
- **No Hardcoded Secrets**: All sensitive values via variables or environment  
- **Resource Tagging**: All resources tagged for cost tracking and management  
- **Plan/Apply Workflow**: All changes reviewed before application  

### Network Security
- **No Public Subnets**: Entire infrastructure operates in default VPC  
- **Security Groups**: Default security groups with restricted inbound rules  
- **API Gateway**: Single public entry point with rate limiting  

## CI/CD Pipeline Security

### GitHub Actions Security
- **Secret Management**: AWS credentials stored in GitHub Secrets  
- **Repository Access**: Private repositories for all code  
- **Workflow Permissions**: Minimal permissions required for deployments  
- **Code Scanning**: Basic ESLint security rules enabled  

### Deployment Security
```yaml
Frontend Deployment:
  - Build occurs in isolated GitHub runner
  - No sensitive data in build artifacts
  - S3 sync with --delete flag to remove orphaned files

Backend Deployment:
  - Dependencies installed from official PyPI
  - Lambda function updates are atomic
  - Rollback capability via Lambda versions
```

## Data Privacy & Compliance

### Data Classification
- **Personal Data**: User emails (stored in Cognito only)  
- **Financial Data**: Expense amounts and descriptions  
- **Metadata**: Dates, categories, user IDs  

### Data Retention
- **User Data**: Retained until user account deletion  
- **Logs**: CloudWatch logs retained for 30 days  
- **Backups**: Manual backup process recommended for production  

### GDPR & Compliance Considerations
- **Right to Erasure**: Users can delete accounts via Cognito  
- **Data Portability**: Export functionality available via API  
- **Privacy by Design**: No unnecessary data collection  

## Monitoring & Incident Response

### Logging & Monitoring
- **CloudWatch Logs**: All Lambda function executions logged  
- **API Gateway Access Logs**: Enabled for all API endpoints  
- **Error Tracking**: Structured error responses with appropriate logging levels  

### Incident Response Plan
**Detection**: Monitor CloudWatch alarms and error rates  

**Containment**:  
- Revoke compromised IAM credentials immediately  
- Block suspicious IP addresses if needed  

**Investigation**:  
- Review CloudWatch logs for unusual patterns  
- Check for unauthorized database access  

**Recovery**:  
- Redeploy infrastructure via Terraform if compromised  
- Rotate all credentials and secrets  

**Post-Mortem**: Document incident and improve controls  

## Security Testing
- **Static Analysis**: ESLint security rules in CI pipeline  
- **Dependency Scanning**: Regular `npm audit` and `pip` security checks  
- **Penetration Testing**: Manual testing of authentication flows  

## Security Best Practices Implemented

✅ **Implemented**  
- Least privilege IAM roles  
- Encryption at rest and in transit  
- JWT-based authentication  
- User data isolation  
- Private S3 buckets  
- Secure CI/CD with secrets management  
- Infrastructure as Code with Terraform  
- Regular dependency updates  

🔄 **Recommended for Production**  
- Web Application Firewall (WAF)  
- AWS Config for compliance monitoring  
- Regular security audits  
- Backup and disaster recovery testing  
- Security headers for S3 website  
- Certificate Manager for custom domains  

## Contact & Reporting

### Security Contacts
- **Primary**: Project maintainer  
- **Backup**: AWS support for infrastructure issues  

### Vulnerability Reporting
Please report security vulnerabilities via:  
- **GitHub Issues** (for non-sensitive reports)  
- **Direct email** to maintainer (for sensitive security issues)  

### Response SLA
- **Initial Response**: 24 hours for security reports  
- **Patch Deployment**: 7 days for critical vulnerabilities  
- **Disclosure Timeline**: Coordinated disclosure based on severity  

## Version History
- **v1.0**: Initial security model (September 2024)  
- **Updates**: Regular reviews with infrastructure changes  

---

This security model is reviewed and updated with each significant change to the application architecture or infrastructure.
