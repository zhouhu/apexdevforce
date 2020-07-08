---
layout: post
title: Salesforce SFDX CI/CD - Bitbucket Pipelines Example
tags: [SFDX, Bitbucket Pipelines, Docker Container, CI/CD]
categories: [Software Engineering]
---

# Salesforce SFDX CI/CD - Bitbucket Pipelines Example

## 1 Virtual Machine (Optional) 

### 1.1 Install Virtual Machine on your local computer 
(optional, providing a safe sandbox, don't mess up with your working computer system)
### 1.2 Enable virtualisation on Local Computer BIOS config
### 1.3 Install VirtualBox
### 1.4 Create a new VirtualMachine
Debian 64bit, latest stable version
### 1.5 update VM OS to lates
```bash
  # sudo apt-get update && apt-get upgrade
```  
### 1.6 Install Guest Additions CD image in VM 

## 2. Docker

### 2.1 Create a Docker Hub User Account 
e.g.  username  [Docker Hub Signup](https://hub.docker.com/signup)
### 2.2 Create a Repository in Docker Hub 
e.g. sfdx
### 2.3 Install "docker" command in VM 
[Docker Doc for Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
### 2.4 Create a Docker Image from Dockerfile
```bash
  #mkdir DockerHub
  #cd DockerHub
  #vi Dockerfile (example)
  #docker build --no-cache -t username/sfdx .
```  
#### 2.4.1 Dockerfile Example
```
FROM debian:stable-slim as build

# Configure base image
RUN apt-get update && apt-get install -y \
  wget \
  xz-utils \
  && rm -rf /var/lib/apt/lists/*

# Install Salesforce CLI binary
WORKDIR /
RUN mkdir /sfdx \
    && wget -qO- https://developer.salesforce.com/media/salesforce-cli/sfdx-linux-amd64.tar.xz | tar xJ -C sfdx --strip-components 1 \
    && /sfdx/install \
    && rm -rf /sfdx

### LAST STAGE
FROM debian:stable-slim as run
###

# Setup CLI exports
ENV SFDX_AUTOUPDATE_DISABLE=false \
  # export SFDX_USE_GENERIC_UNIX_KEYCHAIN=true \
  SFDX_DOMAIN_RETRY=300 \
  SFDX_DISABLE_APP_HUB=true \
  SFDX_LOG_LEVEL=DEBUG \
  TERM=xterm-256color

COPY --from=build /usr/local/lib/sfdx /usr/local/lib/sfdx
RUN ln -sf /usr/local/lib/sfdx/bin/sfdx /usr/local/bin/sfdx
RUN sfdx update

RUN mkdir /keys
COPY server.key /keys

# Run the entrypoint script on startup
ENTRYPOINT ["/bin/bash"]
```

### 2.5 Push Docker Image to Docker Hub
```bash
  #docker push username/sfdx:latest
```

## 3. Bitbucket

### 3.1. Create an empty project / use an exisitng project
### 3.2. Add .gitignore to the Local Directory e.g.
```
	/.sfdx
	/.vscode
	/force-app/main/default/lwc/
```  
### 3.3. Enable repository Bitbucket Pipeline
### 3.4. Add repository variables to the Pipeline [https://support.atlassian.com/bitbucket-cloud/docs/variables-in-pipelines/#User-defined-variables](https://support.atlassian.com/bitbucket-cloud/docs/variables-in-pipelines/#User-defined-variables)
### 3.5. Modify the auto-created "bitbucket-pipelines.yml"
```yaml
image:
  name: username/sfdx:latest
  username: $DOCKER_HUB_USERNAME
  password: $DOCKER_HUB_PASSWORD
  email: $DOCKER_HUB_EMAIL

pipelines:
  default:
    - step:
        script:
          - echo "Commited changes to a branch that does not match the listed branches in bitbucket-pipelines.yml."
          - echo "You can skip running pipline by adding [skip ci] or [ci skip] (with []) to the git commit message."
          - sfdx --version
          - sfdx plugins --core
  branches:
    DEVTEST:
     - step:
         script:
           - echo "Commited changes to branch - DEVTEST"
           - echo "Connecting to Salesforce Org - DEVTEST"
           - sfdx force:auth:jwt:grant -i $SFDC_DEVTEST_CONSUMER_KEY -f security/jwt.key -u $SFDC_DEVTEST_USER -d -s -a DEVTEST -r $SFDC_DEVTEST_URL
           - echo "Authentication command executed to Salesforce Org - DEVTEST"
           - echo "Deploying changes to Salesforce Org - DEVTEST"
           - sfdx force:source:deploy -x manifest/package.xml
           - echo "Deployment command executed to Salesforce Org - DEVTEST"
    UAT:
     - step:
         script:
           - echo "Commited changes to branch - UAT"
           - echo "Connecting to Salesforce Org - UAT"
           - sfdx force:auth:jwt:grant -i $SFDC_UAT_CONSUMER_KEY -f security/jwt.key -u $SFDC_UAT_USER -d -s -a UAT -r $SFDC_UAT_URL
           - echo "Authentication command executed to Salesforce Org - UAT"
           - echo "Deploying changes to Salesforce Org - UAT"
           - sfdx force:source:deploy -x manifest/package.xml
           - echo "Deployment command executed to Salesforce Org - UAT"
```

## 4. Salesforce

### 4.1 Create a certificate
[https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_key_and_cert.htm](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_key_and_cert.htm)
### 4.2 Create a connected app
[https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_connected_app.htm](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_auth_connected_app.htm)
Classic UI: Setup->Create->Apps->New Connected Apps

## 5. Local Computer

### 5.1 Download and install Git 
[https://git-scm.com/downloads](https://git-scm.com/downloads)
### 5.2 Downlaod and install Sourcetree 
[https://www.sourcetreeapp.com](https://www.sourcetreeapp.com) (Bitbucket Git GUI, optional if you like command line or other Git GUI) 
### 5.3 Download and install sfdx 
[https://developer.salesforce.com/tools/sfdxcli](https://developer.salesforce.com/tools/sfdxcli)
### 5.4 Download and install Visual Studio Code 
[https://code.visualstudio.com/](https://code.visualstudio.com/) 
### 5.5 Install Visual Studio Code Salesforce Extensions, search extension (publisher:"Salesforce") 
(5.4 and 5.5 will provide a GUI for sfdx, it is optional if you like command line)

## 6. Setup complets, workflow starts here

### 6.1 ...to be continued...
