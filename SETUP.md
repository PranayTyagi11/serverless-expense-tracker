# Setup Guide

## Prerequisites

-   **AWS Account** with appropriate permissions
-   **GitHub Account** for CI/CD and repository hosting
-   **Node.js** (v18 or higher) for frontend development
-   **Python** (v3.9 or higher) for backend development
-   **Terraform** (v1.6 or higher) for infrastructure management
-   **AWS CLI** configured with appropriate credentials

## Repository Structure

expense-tracker/ ├── frontend/ \# React application │ ├── src/ │ ├──
public/ │ └── package.json ├── backend/ \# Lambda functions │ ├──
lambda_function.py │ └── requirements.txt ├── infrastructure/ \#
Terraform configurations │ ├── main.tf │ ├── lambda.tf │ └── database.tf
└── docs/ \# Documentation ├── ARCHITECTURE.md ├── SECURITY.md └──
DEPLOYMENT.md

## Local Development Setup

### Frontend Development

\`\`\`bash \# Clone the repository git clone `<repository-url>`{=html}
cd expense-tracker/frontend

# Install dependencies

npm install

# Start development server

npm start \`\`\` The application will be available at
http://localhost:3000

### Backend Development

\`\`\`bash cd expense-tracker/backend

# Test Lambda function locally (optional)

# You can test Lambda logic with mock events

python3 -c "import lambda_function; print('Backend imports working')"
\`\`\`

### Environment Configuration

#### Frontend Environment Variables

Create `frontend/.env` file:

\`\`\`env REACT_APP_COGNITO_USER_POOL_ID=ca-central-1_xxxxxxxxx
REACT_APP_COGNITO_CLIENT_ID=xxxxxxxxxxxxxxxxxxxxxxxxxx
REACT_APP_API_GATEWAY_URL=https://xxxxxxxxxx.execute-api.ca-central-1.amazonaws.com
REACT_APP_REGION=ca-central-1 \`\`\`

#### Backend Environment Variables

Configure in AWS Lambda Console:

\`\`\`env EXPENSES_TABLE=Expenses \`\`\`

## Infrastructure Deployment

### Initial Infrastructure Setup

\`\`\`bash cd infrastructure

# Initialize Terraform

terraform init

# Review planned changes

terraform plan

# Deploy infrastructure

terraform apply \`\`\` This will create: - S3 bucket for frontend
hosting - DynamoDB table for expenses - Lambda function for API logic -
IAM roles and policies - API Gateway endpoints

### Required Terraform Variables

Create `terraform.tfvars` file:

\`\`\`hcl aws_region = "ca-central-1" project_name = "expense-tracker"
environment = "development" \`\`\`

## Authentication Setup

### Cognito User Pool Configuration

-   Navigate to AWS Cognito Console
-   Create a new User Pool or use the one created by Terraform
-   Note the User Pool ID and Client ID
-   Update frontend `.env` file with these values

### User Registration

-   Users can self-register through the React application
-   Email verification is required
-   Password policies are enforced by Cognito

## CI/CD Pipeline Setup

### GitHub Secrets Configuration

In each repository, configure the following secrets: -
`AWS_ACCESS_KEY_ID` - `AWS_SECRET_ACCESS_KEY`

### Frontend Deployment Pipeline

-   **Trigger:** Push to main branch with changes to frontend files
-   **Action:** Build React app and deploy to S3
-   **Configuration:** `.github/workflows/deploy-frontend.yml`

### Backend Deployment Pipeline

-   **Trigger:** Push to main branch with changes to backend files
-   **Action:** Package Lambda function and update AWS Lambda
-   **Configuration:** `.github/workflows/deploy-backend.yml`

## Testing the Application

### Frontend Testing

\`\`\`bash cd frontend npm test \`\`\`

### API Testing

Use tools like Postman or curl to test endpoints:

\`\`\`bash \# Get all expenses (requires JWT token) curl -H
"Authorization: Bearer `<JWT_TOKEN>`{=html}"
https://your-api-gateway-url/expenses

# Create new expense

curl -X POST -H "Authorization: Bearer `<JWT_TOKEN>`{=html}" -H
"Content-Type: application/json" -d
'{"title":"Test","amount":25.50,"date":"2024-01-15"}'
https://your-api-gateway-url/expenses \`\`\`

## Production Deployment Checklist

### Pre-Deployment

-   All environment variables configured
-   Terraform infrastructure deployed
-   Cognito User Pool configured
-   GitHub Secrets set up
-   Database migrations completed (if any)

### Deployment

-   Frontend deployed to S3 via CI/CD
-   Backend Lambda functions updated
-   API Gateway endpoints verified
-   CORS configuration validated

### Post-Deployment

-   Application accessible via localhost
-   User registration and login working
-   CRUD operations functional
-   Error handling verified

## Troubleshooting

### Common Issues

#### Frontend Build Failures

\`\`\`bash \# Clear node_modules and reinstall rm -rf node_modules
package-lock.json npm install \`\`\`

#### API Gateway CORS Errors

-   Verify CORS configuration in API Gateway
-   Check frontend API URL configuration
-   Ensure proper headers in requests

#### Lambda Permission Errors

-   Verify IAM role permissions
-   Check DynamoDB table name in environment variables
-   Review CloudWatch logs for detailed errors

#### Authentication Issues

-   Confirm Cognito User Pool configuration
-   Verify JWT token validity
-   Check token expiration times

### Debugging Tools

-   **CloudWatch Logs**
    -   Lambda function execution logs
    -   API Gateway access logs
    -   Error tracking and monitoring
-   **Browser Developer Tools**
    -   Network tab for API requests
    -   Console for JavaScript errors
    -   Application tab for storage inspection

## Cost Management

### AWS Free Tier

-   S3: 5GB storage
-   Lambda: 1M requests per month
-   DynamoDB: 25GB storage
-   API Gateway: 1M API calls per month

### Cost Optimization

-   Use S3 Intelligent-Tiering for storage
-   Configure Lambda appropriate memory and timeout
-   Enable DynamoDB auto-scaling
-   Monitor usage with AWS Cost Explorer

#### Issue Reporting

-   Check existing documentation
-   Review CloudWatch logs for errors
-   Create issue in GitHub repository with:
    -   Error messages
    -   Steps to reproduce
    -   Environment details

#### Community Support

-   GitHub Discussions
-   AWS Developer Forums
-   Terraform Community

