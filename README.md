# Jenkins CI/CD Lab — Experimental Pipeline (Portfolio Implementation)

This repository is my hands-on implementation of the **01_01 Jenkins** lab from the LinkedIn Learning course **Continuous Integration: Tools**.

Instead of only “following steps”, I treated this as a mini real-world DevOps exercise:
- Provisioned Jenkins on AWS via **CloudFormation**
- Built a **Pipeline-as-Code** workflow using a `Jenkinsfile`
- Wired **secure credential management** in Jenkins
- Ran an end-to-end CI/CD pipeline that deploys to **two AWS Lambda environments** (staging + production)
- Verified deployments via URL smoke tests

## What I built (my contribution)
- **Self-hosted Jenkins** deployed on AWS EC2 (CloudFormation)
- **Declarative Jenkins Pipeline** (`Jenkinsfile`) implementing a full CI/CD flow
- **Credential-driven deployments** using Jenkins Credentials Store (no secrets in Git)
- **Multi-environment deployments** (staging + production) to AWS Lambda
- **Automated smoke tests** against deployed endpoints to confirm correct version/build
- **Troubleshooting + hardening** during first run (pipeline execution issues and fixes documented)

## Pipeline stages (CI/CD flow)
1. **Requirements** — create Python venv and install dependencies  
2. **Check** — verify required tools and run quality gates  
3. **Test** — run unit tests  
4. **Build** — package application into `lambda.zip`  
5. **Deploy Staging** — deploy to Lambda staging  
6. **Test Staging** — smoke test staging URL  
7. **Deploy Production** — deploy to Lambda production  
8. **Test Production** — smoke test production URL  

## Tech used
- **Jenkins** (Pipeline-as-Code)
- **AWS CloudFormation** (Jenkins server provisioning)
- **AWS Lambda** + Function URLs (deployment targets)
- **Python** + `venv`
- **Makefile** (standardized CI commands)
- **awscli** (deployment automation)

---

# Lab Setup (reproducible steps)

## Prerequisites
1. GitHub account (to host this repo)
2. AWS account (to run CloudFormation + Lambda sample app)
3. Sample application deployed from the course exercise files  
   See: `ch0_introduction/00_06_about_the_exercise_files/sample-application.yml`

You will need the **Outputs** from the sample application stack:
- `AwsAccessKeyId`
- `AwsSecretAccessKey`
- `AwsDefaultRegion`
- `StagingFunctionName`, `StagingURL`
- `ProductionFunctionName`, `ProductionURL`

---

## 1) Deploy Jenkins on AWS (CloudFormation)
1. AWS Console → CloudFormation → **Create stack**
2. Upload: `jenkins-cloudformation-template.yml`
3. Wait for **CREATE_COMPLETE**
4. In **Outputs**, note:
   - `URL` (Jenkins UI)
   - `AdminPassword` hint command: `sudo docker logs jenkins`

---

## 2) Configure Jenkins (unlock + plugins)

### 2.1 Retrieve initial admin password
1. CloudFormation → Stack → **Resources**
2. Open EC2 instance → **Connect** → **Session Manager**
3. Run:
   ```bash
   sudo docker logs jenkins
