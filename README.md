# Jenkins CI/CD Pipeline Templates 🚀

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![Jenkins](https://img.shields.io/badge/Jenkins-2.387%2B-D24939?style=flat&logo=jenkins)
![Docker](https://img.shields.io/badge/Docker-Integrated-2496ED?style=flat&logo=docker)

Production-grade **Jenkins pipeline templates** for building, testing, and deploying containerized applications with automated code quality checks, security scanning, and multi-environment deployment strategies.

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Pipeline Stages](#pipeline-stages)
- [Installation](#installation)
- [Configuration](#configuration)
- [Examples](#examples)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

## 🎯 Overview

These Jenkins pipelines provide:
- **Automated Build & Test** - Continuous integration workflows
- **Code Quality Analysis** - SonarQube integration
- **Security Scanning** - Container & dependency scanning
- **Multi-Stage Deployment** - Dev → Staging → Production
- **GitOps Integration** - Git-driven deployments
- **Notification System** - Slack/Email alerts

## ✨ Features

✅ **Declarative Pipelines** - Easy-to-read, version-controlled  
✅ **Parallel Testing** - Run tests concurrently  
✅ **SonarQube Integration** - Static code analysis  
✅ **Docker Build** - Automated containerization  
✅ **Image Scanning** - Trivy security scans  
✅ **Multi-Environment** - Dev, Staging, Production deployment  
✅ **Artifact Management** - Nexus/Artifactory integration  
✅ **Notification System** - Slack/Teams/Email alerts  
✅ **Pipeline as Code** - Jenkinsfile in repository  
✅ **Retry Logic** - Automatic retry on failure  

## 🏗️ Architecture

```
┌──────────────────────────────────────────────────────────┐
│                    Jenkins Master                        │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  ┌─ Pipeline Orchestration ─────────────────────────┐  │
│  │                                                   │  │
│  │ ┌─────────────┐   ┌──────────────┐  ┌──────────┐ │  │
│  │ │  Checkout   │──▶│  Build Java  │──▶│  Test    │ │  │
│  │ └─────────────┘   └──────────────┘  └──────────┘ │  │
│  │      │                    │               │       │  │
│  │      ▼                    ▼               ▼       │  │
│  │  ┌──────────────────────────────────────────┐   │  │
│  │  │       SonarQube Code Analysis            │   │  │
│  │  └──────────────────────────────────────────┘   │  │
│  │      │                                           │  │
│  │      ▼                                           │  │
│  │  ┌──────────────────────────────────────────┐   │  │
│  │  │  Build & Push Docker Image               │   │  │
│  │  │  - Trivy Scan                            │   │  │
│  │  │  - Push to ECR                           │   │  │
│  │  └──────────────────────────────────────────┘   │  │
│  │      │                                           │  │
│  │      ▼                                           │  │
│  │  ┌──────────────────────────────────────────┐   │  │
│  │  │  Deploy to Dev/Staging/Production        │   │  │
│  │  │  - ArgoCD GitOps Sync                    │   │  │
│  │  │  - Kubernetes Deployment                │   │  │
│  │  └──────────────────────────────────────────┘   │  │
│  │      │                                           │  │
│  │      ▼                                           │  │
│  │  ┌──────────────────────────────────────────┐   │  │
│  │  │  Health Checks & Notifications           │   │  │
│  │  └──────────────────────────────────────────┘   │  │
│  │                                                   │  │
│  └─────────────────────────────────────────────────┘  │
│                                                         │
└──────────────────────────────────────────────────────┘
```

## 📦 Prerequisites

- **Jenkins** 2.387+
- **Plugins:**
  - Blue Ocean
  - Docker Pipeline
  - Kubernetes
  - SonarQube Scanner
  - Artifactory
  - Slack Notification
  - Email Extension
  - Pipeline: API
  - Pipeline: Basic Steps
- **External Tools:**
  - Docker
  - kubectl
  - Trivy
  - Git

## 🔄 Pipeline Stages

1. **Checkout** - Clone repository
2. **Build** - Compile & package application
3. **Unit Tests** - Run test suite
4. **Code Quality** - SonarQube analysis
5. **Build Docker** - Create container image
6. **Security Scan** - Trivy vulnerability scan
7. **Push Registry** - Push to ECR/Docker Hub
8. **Deploy Dev** - Deploy to development environment
9. **Deploy Staging** - Deploy to staging (manual approval)
10. **Deploy Production** - Deploy to production (manual approval)
11. **Health Checks** - Verify deployment health
12. **Notifications** - Send alerts

## 📂 Directory Structure

```
jenkins-cicd-pipeline-templates/
├── README.md
├── Jenkinsfile
├── Jenkinsfile.dev
├── Jenkinsfile.prod
├── Jenkinsfile.shared
├── src/
│   ├── main/
│   ├── test/
│   └── pom.xml
├── docker/
│   ├── Dockerfile
│   └── .dockerignore
├── k8s/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── configmap.yaml
│   └── secrets.yaml
├── scripts/
│   ├── build.sh
│   ├── test.sh
│   ├── sonar-scan.sh
│   ├── trivy-scan.sh
│   ├── deploy.sh
│   └── health-check.sh
├── config/
│   ├── sonar-project.properties
│   ├── jenkins-config.xml
│   └── pipeline-params.json
└── .gitignore
```

## 🚀 Installation

### 1. Jenkins Server Setup

```bash
# Docker Compose for Jenkins
version: '3.8'
services:
  jenkins:
    image: jenkins/jenkins:lts
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_home:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    environment:
      - JAVA_OPTS=-Xmx2048m
    networks:
      - jenkins

  sonarqube:
    image: sonarqube:lts
    ports:
      - "9000:9000"
    environment:
      - SONAR_JDBC_URL=jdbc:postgresql://db:5432/sonar
      - SONAR_JDBC_USERNAME=sonar
      - SONAR_JDBC_PASSWORD=sonar
    networks:
      - jenkins

volumes:
  jenkins_home:

networks:
  jenkins:
```

### 2. Install Required Plugins

```groovy
// Jenkins Script Console
import jenkins.model.Jenkins

def pluginList = [
    'blueocean:1.25.0',
    'docker-plugin:1.2.2',
    'docker-workflow:521.v1c31b_1864d13',
    'kubernetes:1.30.0',
    'sonar:2.12.1',
    'slack:2.50',
    'email-ext:2.98'
]

def pluginManager = Jenkins.instance.pluginManager
pluginList.each { plugin ->
    def pluginId = plugin.split(':')[0]
    if (!pluginManager.getPlugin(pluginId)) {
        println "Installing ${pluginId}..."
        // Installation logic
    }
}
```

### 3. Configure Jenkins Credentials

```bash
# Store AWS credentials
jenkins-cli -s http://localhost:8080 create-credentials-by-xml \
  system::system::jenkins \
  '(global)' \
  < aws-credentials.xml

# Store Docker Registry credentials
jenkins-cli -s http://localhost:8080 create-credentials-by-xml \
  system::system::jenkins \
  '(global)' \
  < docker-registry-credentials.xml
```

## 🔧 Configuration

### 1. Jenkinsfile (Main Pipeline)

```groovy
@Library('shared-library') _

pipeline {
    agent any
    
    options {
        timestamps()
        timeout(time: 1, unit: 'HOURS')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    
    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'staging', 'prod'],
            description: 'Deployment environment'
        )
        string(
            name: 'IMAGE_TAG',
            defaultValue: '${BUILD_NUMBER}',
            description: 'Docker image tag'
        )
    }
    
    environment {
        AWS_REGION = 'us-east-1'
        AWS_ACCOUNT_ID = credentials('aws-account-id')
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        IMAGE_NAME = "myapp"
        SONARQUBE_HOST = "http://sonarqube:9000"
        DOCKER_BUILDKIT = '1'
    }
    
    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '${GIT_BRANCH}']],
                        userRemoteConfigs: [[url: '${GIT_REPO}']]
                    ])
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    sh '''
                        echo "Building application..."
                        ./mvn clean package -DskipTests
                    '''
                }
            }
        }
        
        stage('Unit Tests') {
            parallel {
                stage('JUnit Tests') {
                    steps {
                        sh 'mvn test'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        sh 'mvn verify -DskipUnitTests'
                    }
                }
            }
        }
        
        stage('Code Quality') {
            steps {
                script {
                    sh '''
                        mvn sonar:sonar \
                            -Dsonar.projectKey=myapp \
                            -Dsonar.sources=src \
                            -Dsonar.host.url=${SONARQUBE_HOST} \
                            -Dsonar.login=${SONARQUBE_TOKEN}
                    '''
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                        docker build \
                            -t ${IMAGE_NAME}:${BUILD_NUMBER} \
                            -t ${IMAGE_NAME}:latest \
                            .
                    '''
                }
            }
        }
        
        stage('Security Scan') {
            steps {
                script {
                    sh '''
                        trivy image --severity HIGH,CRITICAL \
                            ${IMAGE_NAME}:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Push to Registry') {
            steps {
                script {
                    sh '''
                        aws ecr get-login-password --region ${AWS_REGION} | \
                            docker login --username AWS --password-stdin ${ECR_REGISTRY}
                        
                        docker tag ${IMAGE_NAME}:${BUILD_NUMBER} \
                            ${ECR_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}
                        
                        docker push ${ECR_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Deploy Dev') {
            when {
                branch 'develop'
            }
            steps {
                script {
                    sh '''
                        kubectl set image deployment/myapp-deployment \
                            myapp=${ECR_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER} \
                            -n development
                    '''
                }
            }
        }
        
        stage('Deploy Staging') {
            when {
                branch 'main'
            }
            steps {
                script {
                    input message: 'Deploy to Staging?', ok: 'Deploy'
                    
                    sh '''
                        kubectl set image deployment/myapp-deployment \
                            myapp=${ECR_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER} \
                            -n staging
                    '''
                }
            }
        }
        
        stage('Deploy Production') {
            when {
                branch 'main'
            }
            steps {
                script {
                    input message: 'Deploy to Production?', ok: 'Deploy'
                    
                    sh '''
                        kubectl set image deployment/myapp-deployment \
                            myapp=${ECR_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER} \
                            -n production
                    '''
                }
            }
        }
        
        stage('Health Checks') {
            steps {
                script {
                    sh '''
                        ./scripts/health-check.sh ${ENVIRONMENT}
                    '''
                }
            }
        }
    }
    
    post {
        success {
            script {
                slackSend(
                    channel: '#deployments',
                    color: 'good',
                    message: """
                        ✅ Build ${BUILD_NUMBER} successful!
                        Environment: ${ENVIRONMENT}
                        Image: ${IMAGE_NAME}:${BUILD_NUMBER}
                        """
                )
            }
        }
        
        failure {
            script {
                slackSend(
                    channel: '#deployments',
                    color: 'danger',
                    message: """
                        ❌ Build ${BUILD_NUMBER} failed!
                        Check Jenkins for details: ${BUILD_URL}
                        """
                )
            }
        }
        
        always {
            junit 'target/test-results/**/*.xml'
            archiveArtifacts artifacts: 'target/*.jar'
        }
    }
}
```

### 2. Shared Library Setup

```groovy
// vars/common.groovy
def buildDocker(String imageName, String imageTag) {
    sh """
        docker build -t ${imageName}:${imageTag} .
    """
}

def pushDockerImage(String registry, String imageName, String imageTag) {
    sh """
        docker tag ${imageName}:${imageTag} ${registry}/${imageName}:${imageTag}
        docker push ${registry}/${imageName}:${imageTag}
    """
}

def scanImage(String imageName, String imageTag) {
    sh """
        trivy image --severity HIGH,CRITICAL ${imageName}:${imageTag}
    """
}

def deployToKubernetes(String namespace, String deployment, String imageName, String imageTag) {
    sh """
        kubectl set image deployment/${deployment} \
            ${deployment}=${imageName}:${imageTag} \
            -n ${namespace}
    """
}

def sendSlackNotification(String channel, String color, String message) {
    slackSend(
        channel: channel,
        color: color,
        message: message
    )
}
```

### 3. SonarQube Configuration

```properties
# sonar-project.properties
sonar.projectKey=myapp
sonar.projectName=My Application
sonar.projectVersion=1.0
sonar.sources=src/main
sonar.tests=src/test
sonar.java.binaries=target/classes
sonar.java.test.binaries=target/test-classes
sonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
sonar.exclusions=**/test/**
```

### 4. Trivy Security Scanning

```yaml
# trivy-config.yaml
severity:
  - HIGH
  - CRITICAL

vulnerability:
  type:
    - os
    - library

skip-files:
  - "*.md"
  - "*.txt"

skip-dirs:
  - "docs"
  - "tests"

exit-code: 1
```

## 📊 Best Practices

✅ **Version Control** - Keep Jenkinsfile in repository  
✅ **Parallel Execution** - Run independent stages simultaneously  
✅ **Timeout Protection** - Prevent hanging builds  
✅ **Artifact Management** - Archive build outputs  
✅ **Security Scanning** - Integrate Trivy & SonarQube  
✅ **Notification System** - Alert on failures  
✅ **Manual Approvals** - Control production deployments  
✅ **Build Cleanup** - Discard old builds  
✅ **Shared Libraries** - Reuse common logic  
✅ **Parameter Validation** - Validate input parameters  

## 🔍 Monitoring & Logs

```bash
# View pipeline logs
# Jenkins UI → Build → Console Output

# Check Docker logs
docker logs -f jenkins_jenkins_1

# Monitor SonarQube
http://localhost:9000

# View Kubernetes deployments
kubectl get deployments -n production
kubectl logs -f deployment/myapp-deployment -n production

# View build artifacts
# Jenkins UI → Build → Artifacts
```

## 📈 Pipeline Performance Optimization

### Tips for Faster Builds

```groovy
// 1. Use Docker in Docker for better layer caching
agent {
    docker {
        image 'maven:3.8.1-openjdk-17-slim'
        args '-v /root/.m2:/root/.m2'
    }
}

// 2. Parallel testing stages
parallel {
    stage('Unit Tests') { ... }
    stage('Integration Tests') { ... }
    stage('Security Tests') { ... }
}

// 3. Cache dependencies
sh '''
    mvn clean package \
        -Dmaven.repo.local=${WORKSPACE}/.m2repository
'''
```

## 🔄 Advanced Features

### 1. Blue Ocean Interface

```bash
# Access Blue Ocean
http://localhost:8080/blue

# Features:
# - Visual pipeline editor
# - Real-time test reports
# - Pipeline activity view
# - Credential management
```

### 2. Declarative Syntax

```groovy
// Options
options {
    timestamps()
    timeout(time: 1, unit: 'HOURS')
    buildDiscarder(logRotator(numToKeepStr: '10'))
    disableConcurrentBuilds()
}

// Triggers
triggers {
    githubPush()
    pollSCM('H/5 * * * *')
    cron('H H * * *')
}

// Post conditions
post {
    always { ... }
    success { ... }
    failure { ... }
    unstable { ... }
    changed { ... }
}
```

### 3. Error Handling

```groovy
try {
    sh 'npm test'
} catch (Exception e) {
    echo "Tests failed: ${e.message}"
    currentBuild.result = 'UNSTABLE'
}

finally {
    junit 'test-results/**/*.xml'
}
```

## 🧪 Testing & Validation

### Local Pipeline Testing

```bash
# Install Jenkins CLI
wget http://localhost:8080/jnlpJars/jenkins-cli.jar

# Validate pipeline syntax
java -jar jenkins-cli.jar -s http://localhost:8080 \
    declarative-linter < Jenkinsfile

# Dry run (plan only)
# Use Jenkins "Build with Parameters" and cancel after validation
```

## 📚 Resources

- [Jenkins Documentation](https://www.jenkins.io/doc/)
- [Pipeline Documentation](https://www.jenkins.io/doc/book/pipeline/)
- [Shared Libraries](https://www.jenkins.io/doc/book/pipeline/shared-libraries/)
- [Blue Ocean](https://www.jenkins.io/doc/book/blueocean/)
- [Docker Pipeline Plugin](https://plugins.jenkins.io/docker-workflow/)
- [SonarQube Scanner](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-jenkins/)

## 🤝 Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit changes (`git commit -am 'Add improvement'`)
4. Push to branch (`git push origin feature/improvement`)
5. Create Pull Request

## 📄 License

MIT License

## 👤 Author

**Dinesh Kumar Gudla**  
DevOps Engineer | Jenkins & CI/CD Specialist  
📧 [dinesh.kumar2442000@gmail.com](mailto:dinesh.kumar2442000@gmail.com)  
🔗 [LinkedIn](https://www.linkedin.com/in/dineshgudla)  
🐙 [GitHub](https://github.com/DjDinesh1234)

## 📞 Support

For issues, questions, or suggestions:
- Open an [Issue](https://github.com/DjDinesh1234/jenkins-cicd-pipeline-templates/issues)
- Check [Discussions](https://github.com/DjDinesh1234/jenkins-cicd-pipeline-templates/discussions)
- Check [Jenkins Documentation](https://www.jenkins.io/doc/)

---

**Last Updated:** 2026-06-08  
**Status:** ✅ Production Ready
