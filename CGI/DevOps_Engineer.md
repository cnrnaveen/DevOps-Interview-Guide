# CGI

**YOE---> 4.1 yrs**


- CI/CD & Jenkins
- How did you reduce a pipeline from 1 hour to 20 minutes?
- ---------------------------------------------------------------------------------------
**Simple Interview Answer**
We reduced the Jenkins pipeline from 1 hour to 20 minutes by identifying bottlenecks and optimizing the workflow. The biggest improvement came from running independent stages such as testing, security scanning, and Docker builds in parallel instead of sequentially. We also enabled dependency and Docker layer caching, avoided running unnecessary tests, and used more powerful Jenkins agents. As a result, the pipeline execution time dropped from about 60 minutes to around 20 minutes while maintaining the same quality checks.
A simple interview-friendly answer is to explain where the 1 hour was being spent and what optimizations reduced it to 20 minutes.

**Before Optimization** Suppose your Jenkins pipeline looked like this:
Build       → 20 min
Unit Tests  → 15 min
Docker Build→ 10 min
Security Scan→ 10 min
Deploy      → 5 min
----------------------
Total       → 60 min

Everything runs one after another (sequentially). Total = 60 minutes
Build
  ↓
Tests
  ↓
Docker
  ↓
Scan
  ↓
Deploy
**Optimization 1**: Run Jobs in Parallel
**Instead of:** Build (20 min)
    ↓
Tests (15 min)
    ↓
Security Scan (10 min)
**Run them together:** Now,total 40 min saved 20min 
Build = 20 min
Tests + Scan = 15 min (parallel)
Deploy = 5 min
**Optimization 2:** Docker Layer Caching
Before:Every build downloaded dependencies again.
Maven Dependencies
Node Modules
Example: npm install taking 10min, But now use caching it takes only 2min saved 8 min
First build  → Downloads packages
Next builds  → Reuses cache
**Optimization 3:** Run Only Changed Tests
Before: 100 tests cases every commit 15min 
After optimization: If only frontend code changed:Run frontend tests only  5min saved 10 min 
**Optimization 4:** Use Faster Build Agents
Before: 1 Jenkins agent
2 CPU
4 GB RAM
After: 8 cpu 16GB RAM
Build compilation became much faster.Saved a few more minutes.

------------------------------------------------------------------------------------------
- Write a checkout stage with Git credentials.
- ----------------------------------------------------------------------------------------
In interviews, they usually want to check if you know how Jenkins accesses a Git repository securely using credentials.
**Simple Scenario**
**Suppose:**Git Repository:
https://github.com/company/project.git
Jenkins Credential ID:
github-creds
The pipeline first needs to download the source code from Git.
**Jenkins Checkout Stage**
stage('Checkout') {
    steps {
        git branch: 'main',
            credentialsId: 'github-creds',
            url: 'https://github.com/company/project.git'
    }
}

**credentialsId: 'github-creds'** This tells Jenkins: Use the stored username/password or token instead of hardcoding credentials.
The credentials are stored securely in Jenkins Credentials Manager

-------------------------------------------------------------------------------------------
- If everyone wants to use variables, what approach would you use?
- ------------------------------------------------------------------------------------------
**Interview Answer**
If multiple teams need to use the same variables in Jenkins, I avoid hardcoding values in individual pipelines. I use environment variables for common values, parameterized builds for user-selected values, and Shared Libraries or centralized configuration files for enterprise-scale reuse. This improves maintainability because a change needs to be made only once instead of updating every pipeline.

--------------------------------------------------------------------------------------------
- How do you call variables in a Jenkins pipeline?
--------------------------------------------------------------------------------------------
**Interview Answer**
In Jenkins, variables can be defined using the environment block, build parameters, or Groovy variables. We call them using ${VARIABLE_NAME}. This helps avoid hardcoding values and makes pipelines reusable.
**Example:**
pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
    }

    stages {
        stage('Deploy') {
            steps {
                echo "Deploying to ${AWS_REGION}"
            }
        }
    }
}

---------------------------------------------------------------------------------------------
- What is a Jenkins agent?
- -------------------------------------------------------------------------------------------
A Jenkins Agent is a machine (computer, VM, container, or server) that performs the actual work assigned by the Jenkins Controller (previously called Master).

Think of it like this:

Jenkins Controller (Master) = Manager
Jenkins Agent = Employee

The manager assigns tasks, and the employee does the work.
Suppose you have 100 Jenkins jobs.

If the Jenkins Controller does everything itself:

It becomes slow.
CPU and memory usage become high.
Builds take longer.

Instead, Jenkins distributes the workload across multiple agents.

--------------------------------------------------------------------------------------------
- Sample Dockerfile question from the interview.
- ------------------------------------------------------------------------------------------
A Dockerfile is a text file that contains instructions to build a Docker image.
FROM openjdk:17
WORKDIR /app
COPY target/app.jar app.jar
EXPOSE 8080
CMD ["java","-jar","app.jar"]

FROM      → Base OS/Image
WORKDIR   → Change Directory
COPY      → Copy Files
RUN       → Execute During Build
EXPOSE    → Application Port
CMD       → Start Command

---------------------------------------------------------------------------------------------
- Troubleshooting
- Database connection from a pod is not working only for you. How will you troubleshoot?
- -------------------------------------------------------------------------------------------
Before jumping to conclusions, I would troubleshoot layer by layer:
Application
    ↓
Pod
    ↓
Service
    ↓
Network
    ↓
Database
If a pod cannot connect to the database, I would first verify the pod status and check application logs. Then I would validate the database host, port, and credentials configured in the pod. Next, I would test DNS resolution and confirm that the Kubernetes Service and its endpoints are correctly configured. After that, I would check network connectivity and Network Policies. If networking is fine, I would verify the database pod health, ensure the database is listening on the expected port, and test the credentials by connecting manually. This systematic approach helps isolate whether the problem is with the application, DNS, service, networking, or the database itself.
**Troubleshooting Flow (Easy to Remember)**
1. Pod Running?
      ↓
2. Check Logs
      ↓
3. Check Environment Variables
      ↓
4. DNS Working?
      ↓
5. Service Exists?
      ↓
6. Endpoints Available?
      ↓
7. Network Connectivity?
      ↓
8. Network Policies?
      ↓
9. Database Pod Healthy?
      ↓
10. Database Listening?
      ↓
11. Credentials Correct?
      ↓
12. Manual Connection Test
---------------------------------------------------------------------------------------------
- Database logs are not being written.How will you troubleshoot?"
- -------------------------------------------------------------------------------------------
Think of it this way:
Database Running
      ↓
Generate Logs
      ↓
Write Logs to File
      ↓
Store on Disk
If logs are not being written, the issue could be at any of these stages.
**Step 1: Check if Database is Running**

First verify the database is actually running.  systemctl status mysqld
If db is down: No database process
→ No logs
**Step 2: Check Database Configuration**

Database may be running but logging is disabled.
Mysql check cat /etc/my.cnf
look for log_error=/var/log/mysql/error.log
**Step 3: Verify Log File Path**
Check whether the configured log path exists.
Example:log_error=/var/log/mysql/error.log
Verify: ls -l /var/log/mysql/
Possible issue: Directory does not exist
**Step 4: Check File Permissions**
This is one of the most common reasons.
Example:ls -ld /var/log/mysql
o/p: drwx------ root root
Database runs as: mysql user
Database cannot write there.
**Fix:** chown mysql:mysql /var/log/mysql
**Step 5: Check Disk Space** No space means no logs.
Check: df -h
If database logs are not being written, I would first verify that the database service is running. Then I would check the database configuration to ensure logging is enabled and the log file path is correctly configured. Next, I would verify that the log directory exists and has proper permissions for the database user. After that, I would check disk space and inode availability. If the database runs in Kubernetes, I would check pod logs, volume mounts, and container logging configuration. Finally, I would verify log rotation settings and ensure any configuration changes have been applied by restarting or reloading the database service.

---------------------------------------------------------------------------------------------
- Security
- What are CVEs?
- ---------------------------------------------------------------------------------------------
A CVE (Common Vulnerabilities and Exposures) is a publicly identified security vulnerability assigned a unique ID, allowing organizations to track, assess, and remediate security issues consistently across software, operating systems, and containers.
Bug Found
    ↓
CVE Number Assigned
    ↓
Security Scan Detects It
    ↓
Upgrade/Patch
    ↓
Re-scan
    ↓
Deploy
Suppose a road has a pothole. Without a ticket number: "That pothole near the signal..."
Everyone may get confused.
Instead:
Issue ID: ROAD-1001
Now everyone knows exactly which issue is being discussed.
A CVE works the same way for security vulnerabilities.

----------------------------------------------------------------------------------------------
- What CVEs have you seen in production and how did you resolve them?
- -------------------------------------------------------------------------------------------
One of the common production issues I handled was vulnerabilities found in Docker base images. Using Trivy scans, we identified critical CVEs in the operating system packages. We upgraded the base image to a supported version, rebuilt and tested the application container, redeployed it, and verified remediation through another vulnerability scan.
**Vulnerable Docker Image**
This is extremely common in Kubernetes environments.
**Situation:** A Trivy scan reported:
Critical CVEs, High CVEs in our Docker image.
**Example: Dockerfile**
FROM ubuntu:18.04
The base image was old.

How We Investigated
Ran:
trivy image myapp:latest
Output showed multiple OpenSSL and OS package vulnerabilities.

Resolution
**Before**
FROM ubuntu:18.04

**After**
FROM ubuntu:22.04
Updated packages:
RUN apt-get update && apt-get upgrade -y
**Rebuild**
docker build -t myapp:v2 .
**Validate**
trivy image myapp:v2
Confirmed critical vulnerabilities were removed.

---------------------------------------------------------------------------------------------
- Name 5 tools to identify/fix CVEs.
-------------------------------------------------------------------------------------------------
We typically use tools such as Trivy, Qualys, Nessus, Snyk, and Tenable to identify CVEs.

--------------------------------------------------------------------------------------------
- Docker
- Write a sample multi-stage Dockerfile.
- ----------------------------------------------------------------------------------------
A multi-stage Dockerfile uses multiple FROM statements to separate the build environment from the runtime environment. In the first stage, we compile or package the application using build tools such as Maven or Node.js. In the second stage, we use a lightweight runtime image and copy only the final artifact using COPY --from=<stage>. This reduces image size, improves security, and speeds up deployments.
# Build Stage
FROM node:18 AS build

WORKDIR /app

COPY . .

RUN npm install
RUN npm run build

# Runtime Stage
FROM nginx:latest

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

---------------------------------------------------------------------------------------
- Mostly he have asked me CI/CD in depth and write the stages in detailed.
In my project, the CI/CD pipeline starts when a developer pushes code to GitHub. Jenkins triggers the pipeline and performs source checkout. The application is then built using Maven, followed by SonarQube code quality checks and unit testing. After successful validation, a deployable artifact is generated and scanned for vulnerabilities using security tools such as Trivy. Next, a Docker image is created, scanned for CVEs, and pushed to a container registry. The application is deployed to the Dev environment, where integration and functional tests are executed. After successful UAT and necessary approvals, the application is deployed to production using rolling, blue-green, or canary deployment strategies. Finally, we monitor the application using Prometheus, Grafana, and centralized logging tools to ensure system health and performance.
Developer Commit
        ↓
Source Control (Git)
        ↓
Build Stage
        ↓
Code Quality Scan
        ↓
Unit Testing
        ↓
Package Artifact
        ↓
Security Scan
        ↓
Docker Build
        ↓
Docker Image Scan
        ↓
Push Image to Registry
        ↓
Deploy to Dev
        ↓
Integration Testing
        ↓
UAT
        ↓
Approval
        ↓
Production Deployment
        ↓
Monitoring
