# Jenkins CI/CD Pipeline – Deploy Java WAR Application to Apache Tomcat

## Project Overview

This project demonstrates a complete CI/CD pipeline that automatically builds and deploys a Java web application to a remote Apache Tomcat server using Jenkins.

The application source code is stored in GitHub, built with Maven, packaged as a WAR file, and deployed to Tomcat through a Jenkins pipeline.

---

## Architecture

GitHub → Jenkins → Maven Build → WAR Package → Apache Tomcat

---

## Technologies Used

* AWS EC2
* Jenkins
* Apache Tomcat 9
* Maven
* Java
* Git & GitHub
* Linux (Amazon Linux)

---

## Project Setup

### Jenkins Server

* Installed and configured Jenkins
* Connected Jenkins to GitHub repository
* Configured Maven build environment
* Created CI/CD pipeline

### Tomcat Server

* Installed Apache Tomcat 9
* Changed Tomcat port from 8080 to 8081 to avoid conflict with Jenkins
* Configured Tomcat Manager for remote deployments
* Created deployment user with manager-script role

### Tomcat User Configuration

Added the following configuration to `tomcat-users.xml`:

```xml
<role rolename="manager-script"/>
<user username="deployer" password="deployer" roles="manager-script"/>
```

---

## Jenkins Pipeline

The pipeline performs the following tasks:

1. Pull source code from GitHub
2. Build the application using Maven
3. Generate a WAR file
4. Deploy the WAR file to Apache Tomcat

### Pipeline Code

```groovy
pipeline {

    agent any

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy WAR to Tomcat') {
            steps {
                sh '''
                curl -u deployer:deployer \
                -T target/sampleapp.war \
                "http://3.216.80.183:8081/manager/text/deploy?path=/sampleapp&update=true"
                '''
            }
        }

    }

}
```

---

## Deployment Verification

### WAR File Generated Successfully

```bash
find /var/lib/jenkins/workspace -name "*.war"
```

Output:

```text
sampleapp.war
```

### WAR File Deployed to Tomcat

```bash
ls -l /opt/tomcat/webapps/
```

Output:

```text
sampleapp/
sampleapp.war
```

### Services Running

```bash
sudo ss -tulpn | grep java
```

Output:

```text
8080 -> Jenkins
8081 -> Apache Tomcat
```

---

## Live Application

Application successfully deployed and accessible through Apache Tomcat:

**http://3.216.80.183:8081/sampleapp**

---

## Challenges Encountered

* Jenkins and Tomcat port conflict on port 8080
* Tomcat Manager authentication issues
* Deployment plugin credential configuration problems
* Tomcat service downtime during testing

---

## Solutions Implemented

* Changed Tomcat service port to 8081
* Configured manager-script role for deployment user
* Implemented deployment using Tomcat Manager API
* Verified deployment through Jenkins pipeline and Tomcat Manager

---

## Outcome

Successfully implemented a CI/CD pipeline that:

* Pulls code from GitHub
* Builds a Java application using Maven
* Packages the application as a WAR file
* Deploys automatically to Apache Tomcat
* Makes the application available through a public URL

---

## Author

**Mike Osagie**

Cloud & DevOps Engineer

Skills: AWS | Jenkins | Maven | Apache Tomcat | GitHub | Linux | CI/CD
