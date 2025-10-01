# Expense Tracker - System Architecture

## Overview
A full-stack serverless expense tracking application built on AWS with React frontend.

## System Architecture
┌─────────────────┐ ┌──────────────────┐ ┌─────────────────┐
│ React SPA │───▶│ API Gateway │───▶│ Lambda │
│ (S3 Hosted) │ │ (REST API) │ │ (Python) │
└─────────────────┘ └──────────────────┘ └─────────┬───────┘
│ │ │
│ │ ▼
│ │ ┌─────────────────┐
└───────────────────────┼──────────────│ DynamoDB │
│ │ (Expenses) │
▼ └─────────────────┘
┌─────────────────┐
│ Cognito │
│ (Auth) │
└─────────────────┘

## Components

### Frontend
- **Technology**: React.js
- **Hosting**: AWS S3 (Static Website Hosting)
- **Authentication**: AWS Cognito via Amplify
- **Build**: GitHub Actions CI/CD

### Backend
- **Runtime**: AWS Lambda (Python 3.13)
- **API**: API Gateway REST API
- **Database**: DynamoDB with user isolation
- **Authentication**: JWT tokens via Cognito

### Infrastructure
- **IaC**: Terraform
- **Region**: ca-central-1 (Canada Central)
- **Security**: Least privilege IAM roles

## Data Flow
1. User authenticates via Cognito
2. React app stores JWT token
3. API calls include Authorization header
4. Lambda validates JWT and processes request
5. DynamoDB enforces user data isolation via userId partition key

## Security Architecture
- All S3 buckets private with encryption
- DynamoDB server-side encryption
- API Gateway with Cognito authorizer
- IAM roles with minimal permissions