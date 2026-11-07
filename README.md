
# Exam Portal — AWS Cloud & DevOps Deployment

![AWS](https://img.shields.io/badge/AWS-ap--south--1-orange)
![Terraform](https://img.shields.io/badge/IaC-Terraform-7B42BC)
![Docker](https://img.shields.io/badge/Containers-Docker-2496ED)
![ECS](https://img.shields.io/badge/Compute-ECS_Fargate-232F3E)
![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub_Actions-2088FF)

## Project Overview

A full-stack examination management platform deployed on Amazon Web Services using modern cloud and DevOps practices.

The project demonstrates infrastructure automation, containerized application deployment, private networking, continuous integration and deployment, cloud monitoring, and performance testing.

The React frontend is hosted using Amazon S3 and CloudFront. The Node.js and Express backend runs on Amazon ECS Fargate behind an internal Application Load Balancer. Amazon RDS PostgreSQL provides persistent data storage.

Infrastructure is managed using Terraform, deployment automation uses GitHub Actions, monitoring uses Amazon CloudWatch, and load testing uses Grafana k6.

### Live Application

**Website:** https://d2ulirdq336tyh.cloudfront.net

**API Health:** https://d2ulirdq336tyh.cloudfront.net/api/health

**AWS Region:** ap-south-1 (Mumbai)

**Environment:** Staging

**Original Application Repository:** https://github.com/yugandhar0050/test-_demo

### My Responsibilities

My contribution focuses on:

- AWS infrastructure provisioning using Terraform
- Docker containerization and Amazon ECR
- ECS Fargate deployment
- VPC networking and security configuration
- Internal Application Load Balancer
- Amazon RDS PostgreSQL provisioning
- S3 and CloudFront deployment
- AWS Secrets Manager integration
- GitHub Actions CI/CD configuration
- Amazon CloudWatch monitoring and SNS alarms
- k6 load testing
- Deployment troubleshooting and documentation

Application development is credited to the original repository owner.

---

## AWS Architecture

```text
                      Students / Users
                             |
                             v
                       HTTPS (443)
                             |
                             v
                      Amazon CloudFront
                       /            \
                      /              \
                     v                v
              Amazon S3        CloudFront VPC Origin
             React Frontend           |
                                      v
                                Internal ALB
                                /          \
                               v            v
                         ECS Task 1     ECS Task 2
                         Node.js API    Node.js API
                               \           /
                                \         /
                                 v       v
                              Amazon RDS
                              PostgreSQL

Supporting AWS Services
-----------------------
Amazon ECR        - Docker image storage
AWS Secrets Manager - Database and JWT secrets
Amazon CloudWatch - Infrastructure monitoring
Amazon SNS        - Email alerts
Amazon VPC        - Private networking
AWS IAM           - Access control
Terraform         - Infrastructure provisioning
GitHub Actions    - Deployment automation
```

### Architecture Explanation

The application uses CloudFront as its public HTTPS entry point.

The frontend is stored in a private S3 bucket and delivered through CloudFront using Origin Access Control.

API requests matching `/api/*` are routed to an internal Application Load Balancer through a CloudFront VPC origin.

The ALB distributes traffic across two ECS Fargate tasks running the Node.js backend.

The backend uses Amazon RDS PostgreSQL for persistent application data.

Database credentials and JWT secrets are stored in AWS Secrets Manager rather than being committed to the source repository.

---

## Technology Stack

| Category | Technologies |
|---|---|
| Frontend | React, Vite, JavaScript |
| Backend | Node.js, Express.js |
| Database | PostgreSQL |
| Containerization | Docker |
| Container Registry | Amazon ECR |
| Container Service | Amazon ECS Fargate |
| Load Balancing | Application Load Balancer |
| Networking | Amazon VPC, Subnets, Security Groups |
| Frontend Hosting | Amazon S3 |
| HTTPS/CDN | Amazon CloudFront |
| Infrastructure as Code | Terraform |
| Secret Management | AWS Secrets Manager |
| CI/CD | GitHub Actions |
| Monitoring | Amazon CloudWatch |
| Alerts | Amazon SNS |
| Load Testing | Grafana k6 |
| Version Control | Git and GitHub |

---

## Repository Structure

```text
exam-portal-aws-devops/
|
|-- .github/
|   |-- workflows/
|       |-- exam-portal-cicd.yml
|
|-- backend/
|   |-- Dockerfile
|   |-- migrations/
|   |-- src/
|   |-- tests/
|   |-- package.json
|
|-- frontend/
|   |-- Dockerfile
|   |-- public/
|   |-- src/
|   |-- package.json
|
|-- infrastructure/
|   |-- terraform/
|       |-- environments/
|           |-- staging/
|               |-- main.tf
|               |-- security.tf
|               |-- rds.tf
|               |-- alb.tf
|               |-- ecs-service.tf
|               |-- cloudfront.tf
|               |-- ...
|
|-- monitoring/
|   |-- create-alarms.sh
|   |-- create-dashboard.py
|   |-- dashboard.json
|
|-- performance/
|   |-- baseline.js
|   |-- load-500.js
|   |-- 500-users-results.json
|
|-- docs/
|   |-- screenshots/
|
|-- docker-compose.yml
|-- .gitignore
|-- README.md
```

---

## AWS Infrastructure

The application infrastructure is provisioned using Terraform.

### Networking

- Dedicated Amazon VPC
- Public and private subnets
- Security groups for application access
- AWS VPC endpoints
- Private backend networking
- Internal Application Load Balancer

### Amazon ECS Fargate

| Resource | Configuration |
|---|---|
| ECS Cluster | exam-portal-staging |
| Backend Service | exam-portal-staging-backend |
| Running Tasks | 2 |
| CPU per Task | 0.5 vCPU |
| Memory per Task | 1 GB |
| Backend Port | 5000 |
| Deployment Type | ECS Fargate |

The backend containers run in private subnets and are not directly exposed to the public internet.

### Amazon RDS

The backend uses Amazon RDS PostgreSQL.

Database access is controlled through VPC security groups and application credentials stored in AWS Secrets Manager.

Seven database migrations were executed during initial deployment.

---

## Infrastructure Deployment with Terraform

### Step 1 — Navigate to Terraform

```bash
cd infrastructure/terraform/environments/staging
```

### Step 2 — Initialize Terraform

```bash
terraform init
```

Downloads required providers and initializes Terraform.

### Step 3 — Validate Terraform

```bash
terraform fmt -check
terraform validate
```

Checks formatting and configuration validity.

### Step 4 — Review Infrastructure Changes

```bash
terraform plan -out=staging.tfplan
terraform show staging.tfplan
```

Shows the proposed infrastructure modifications before applying them.

### Step 5 — Apply Infrastructure

```bash
terraform apply staging.tfplan
```

Deploys the reviewed Terraform plan.

**Important:** Review AWS costs and changes before applying. Terraform state and sensitive variables must be stored securely.

---

## Docker and Backend Deployment

The Express backend is packaged as a Docker container and stored in Amazon ECR.

### Step 1 — Build Docker Image

```bash
docker build -t exam-portal-backend ./backend
```

### Step 2 — Authenticate to ECR

```bash
aws ecr get-login-password --region ap-south-1 \
  | docker login --username AWS --password-stdin \
  848635215310.dkr.ecr.ap-south-1.amazonaws.com
```

### Step 3 — Tag Docker Image

```bash
docker tag exam-portal-backend:latest \
  848635215310.dkr.ecr.ap-south-1.amazonaws.com/exam-portal-staging-backend:manual
```

### Step 4 — Push Docker Image

```bash
docker push \
  848635215310.dkr.ecr.ap-south-1.amazonaws.com/exam-portal-staging-backend:manual
```

### Step 5 — Deploy to ECS

The ECS service runs Docker containers using Fargate.

A new task-definition revision referencing the image must be registered, followed by a service update.

GitHub Actions automates the image push and ECS task-definition deployment.

---

## React Frontend Deployment

The frontend is built using Vite and hosted in a private S3 bucket.

### Step 1 — Install Dependencies

```bash
cd frontend
npm ci
```

### Step 2 — Build React

```bash
VITE_API_URL=/api npm run build
```

The frontend uses a relative `/api` address so API requests are routed through the same CloudFront HTTPS domain.

### Step 3 — Upload Frontend to S3

```bash
aws s3 sync dist/ \
  s3://exam-portal-staging-frontend-225c8a4c4482d3e7301164dc90/ \
  --region ap-south-1
```

### Step 4 — Refresh CloudFront

```bash
aws cloudfront create-invalidation \
  --distribution-id E1ZQA9IX2HJZPW \
  --paths "/" "/index.html"
```

CloudFront delivers the updated frontend through the application's public HTTPS URL.

---

## CI/CD Pipeline — GitHub Actions

The project includes GitHub Actions automation.

Workflow file:

`.github/workflows/exam-portal-cicd.yml`

### Pipeline Architecture

```text
Developer Push to main
          |
          v
   GitHub Repository
          |
          v
     GitHub Actions
          |
          v
   Checkout Source Code
          |
          v
     Backend Tests
          |
          v
    React Build
          |
          v
   AWS Authentication
          |
          v
   Docker Image Build
          |
          v
     Push to ECR
          |
          v
   Update ECS Task Definition
          |
          v
   Deploy ECS Fargate
          |
          v
   Upload React Build to S3
          |
          v
   CloudFront Invalidation
          |
          v
   API Health Verification
```

### Workflow Triggers

The workflow supports:

- Automatic execution on pushes to `main`
- Manual execution using `workflow_dispatch`

The workflow deploys to the existing staging environment. Protect the main branch and review code before merging.

### GitHub Repository Secrets

The following secrets are required:

| Secret Name | Purpose |
|---|---|
| AWS_ACCESS_KEY_ID | IAM deployment user access key |
| AWS_SECRET_ACCESS_KEY | IAM deployment user secret key |

Do not store access keys in source code.

The IAM deployment user requires least-privilege permissions for Amazon ECR, ECS, the required ECS task roles, S3, and CloudFront.

### Pipeline Verification

Open:

https://github.com/PINJALA780/exam-portal-aws-devops/actions

Verify that the tests, build, deployment, and health-check steps complete successfully.

The pipeline configuration is included in this repository; an actual successful workflow run must be checked independently.

---

## Amazon CloudWatch Monitoring

CloudWatch provides infrastructure monitoring and operational visibility.

### Monitoring Architecture

```text
ECS Fargate --------\
                     \
Application ALB ------> Amazon CloudWatch
                     /        |
Amazon RDS ---------/         v
                         CloudWatch Alarms
                                |
                                v
                           Amazon SNS
                                |
                                v
                        Email Notifications
```

### Monitored Metrics

| AWS Service | Metric |
|---|---|
| ECS Fargate | CPU Utilization |
| ECS Fargate | Memory Utilization |
| ALB | Healthy Host Count |
| ALB | HTTP 5xx Errors |
| Amazon RDS | CPU Utilization |
| Amazon RDS | Free Storage Space |

CloudWatch dashboard:

`ExamPortal-Staging`

Monitoring scripts are available inside the `monitoring/` directory.

---

## Performance Testing with Grafana k6

A controlled load test was performed against the public Exam Portal API health endpoint.

The test gradually increased the number of virtual users to 500.

### Test Execution

```bash
k6 run \
  --summary-export=performance/500-users-results.json \
  performance/load-500.js
```

### Actual Performance Results

| Metric | Result |
|---|---|
| Maximum Virtual Users | 500 |
| Total HTTP Requests | 5,582 |
| Successful Checks | 11,164 / 11,164 |
| HTTP Failure Rate | 0.00% |
| Average Response Time | 49.19 ms |
| Median Response Time | 44.83 ms |
| p90 Response Time | 57.10 ms |
| p95 Response Time | 76.29 ms |
| Maximum Response Time | 547.17 ms |

### Performance Test Outcome

The application successfully served the tested API health-check workload with 500 configured virtual users.

The test completed with zero HTTP failures and all health checks passing.

**Important:** This validates health-check performance under the tested request pattern. It does not establish that 500 students can simultaneously complete full exams. Authenticated examination workflows, answer saving, and simultaneous submissions require additional testing.

---

## Troubleshooting and Production Issues

### Issue 1 — React Frontend Could Not Connect

**Error:**

```text
Unable to connect. Check the API and try again.
```

The frontend initially used a localhost fallback:

```javascript
http://localhost:5000/api
```

This caused browser requests to target the user's own computer rather than the AWS backend.

**Fix:**

```javascript
baseURL: import.meta.env.VITE_API_URL || "/api"
```

The React frontend was rebuilt, uploaded to S3, and refreshed through CloudFront.

### Issue 2 — Database Connectivity

PostgreSQL connectivity was verified through the application API health endpoint:

```bash
curl -fsS https://d2ulirdq336tyh.cloudfront.net/api/health
```

Expected response:

```json
{
  "status": "ok",
  "database": "connected"
}
```

### Issue 3 — GitHub Permission Error

An earlier push failed with HTTP 403 because the selected GitHub account did not have permission to push to the original application's repository.

A separate portfolio repository was created for the DevOps work.

### Issue 4 — CloudFront API Routing

CloudFront serves static frontend files from S3 and forwards `/api/*` requests through its VPC origin to the internal ALB.

Backend route connectivity was verified separately from successful user authentication.

---

## Project Screenshots

The following screenshots document the deployment, infrastructure, CI/CD, monitoring, and performance results.

Screenshots are uploaded manually into `docs/screenshots/`.

### 1. Live Exam Portal

[View Exam Portal Homepage](screenshots/01-homepage.png)

### 2. ECS Fargate Running Tasks

[View ECS Running Tasks](screenshots/02-ecs-running-tasks.png)

### 3. Application Load Balancer

[View ALB Healthy Targets](screenshots/03-alb-healthy-targets.png)

### 4. Amazon RDS PostgreSQL

[View RDS Database](screenshots/04-rds-postgresql.png)

### 5. GitHub Actions CI/CD

[View Successful GitHub Actions Workflow](screenshots/05-github-actions-success.png)

### 6. CloudWatch Monitoring

[View CloudWatch Dashboard](screenshots/06-cloudwatch-dashboard.png)

### 7. k6 Performance Test

[View k6 500-User Test Results](screenshots/07-k6-500-users.png)

> Screenshot links become active after the corresponding image files are uploaded. Only use genuine screenshots of verified results, and hide credentials or student personal information.

---

## Key DevOps Skills Demonstrated

- AWS infrastructure design
- Terraform Infrastructure as Code
- Private VPC networking
- Security groups and IAM
- Docker image builds
- Amazon ECR
- Amazon ECS Fargate deployment
- Application Load Balancer
- PostgreSQL on Amazon RDS
- AWS Secrets Manager
- S3 and CloudFront
- GitHub Actions CI/CD
- CloudWatch monitoring
- SNS notifications
- k6 performance testing
- Troubleshooting cloud deployments

---

## Future Improvements

- Test 500 realistic concurrent examination sessions.
- Validate ECS Service Auto Scaling.
- Complete dependency security remediation.
- Verify database backup and recovery procedures.
- Implement automated deployment rollback.
- Add application-level exam submission metrics.
- Strengthen production IAM and network controls.

---

## Project Credits

**Original Application Development:**  
https://github.com/yugandhar0050/test-_demo

**AWS Cloud Infrastructure, DevOps Deployment, Monitoring and Performance Testing:**  
https://github.com/PINJALA780

This repository documents my AWS cloud engineering and DevOps implementation of the Exam Portal application.
