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
- What is a Jenkins agent?
- Sample Dockerfile question from the interview.
- Troubleshooting
- Database connection from a pod is not working only for you. How will you troubleshoot?
- Database logs are not being written.
- Security
- What are CVEs?
- What CVEs have you seen in production and how did you resolve them?
- Name 5 tools to identify/fix CVEs.
- Docker
- Write a sample multi-stage Dockerfile.
- Mostly he have asked me CI/CD in depth and write the stages in detailed.
