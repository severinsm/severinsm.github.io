---
lang: en
layout: post
title: "Run ansible from a stable environment anywhere"
subtitle: "get into docker ansible"
category: devops
tags: ansible
image:
  path: /assets/img/2022-01-17/ansible.png
---

> why ansible is great

<!--more-->

* this unordered seed list will be replaced by the toc
{:toc}

## Configure Ansible in a Docker container

This article shows you how to install Ansible running in a Docker container. Using a container for Ansible development solves the problem of "It works on my machine." by providing a consistent experience across all your environments, locally or in production.

In this article, you learn to:

> - Create an Azure service principal
> - Create a Dockerfile
> - Build a Docker image
> - Install Ansible in a Docker container
> - Use a Service Principal to authenticate Ansible to Azure > > - from a Docker container
> - Run Ansible commands from a Docker container

## Prerequisites
Azure **subscription:** If you don't have an Azure subscription, create a free account before you begin.
**Docker Desktop:** Installation options are available for Windows, Mac, and Linux.
## Create an Azure Service Principal
Run the following commands to create an Azure Service Principal:

Azure CLI
Azure PowerShell


Azure CLI
``` shell
az ad sp create-for-rbac --name <service-principal-name> \
        --role Contributor \
        --scopes /subscriptions/<subscription_id>
```
Replace <service-principal-name> with your service principal name.

```curl
$ curl -O wget http://example.com/pk.zip
```

```wget
$ wget http://example.com/pk.zip
```

## Single block

```
$ ls -lisa
```


 Important

Make note of the password value in the output, you won't be able to retrieve it afterwards.

Create a Dockerfile that will install Ansible
Create a directory in which to test and run the sample code and make it the current directory.

Create a new file named Dockerfile.

Insert the following Docker commands into the new file.

Dockerfile

Copy
FROM centos:7

ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8

RUN yum check-update; \
    yum install -y gcc libffi-devel python3 epel-release; \
    yum install -y python3-pip; \
    yum install -y wget; \
    yum clean all

RUN pip3 install --upgrade pip; \
    pip3 install --upgrade virtualenv; \
    pip3 install pywinrm[kerberos]; \
    pip3 install pywinrm; \
    pip3 install jmspath; \
    pip3 install requests; \
    python3 -m pip install ansible; \
    ansible-galaxy collection install azure.azcollection; \
    pip3 install -r ~/.ansible/collections/ansible_collections/azure/azcollection/requirements-azure.txt
Build the Ansible Docker image
Run docker build to build the Docker image used to run Ansible.

cmd

Copy
docker build . -t ansible
Start the Ansible container
Run the docker run to start the Ansible container.

cmd

Copy
docker run -it ansible
Key points:

By default, Docker containers start detached from the terminal, running in the background.
The -it option stands for interactive terminal allowing you to run commands inside the Docker container.
To confirm Ansible was installed in the container, run the Ansible command to print its version.

cmd

Copy
ansible --version
Connect to Azure from the Ansible container
Assign the following environment variables to connect to Azure:

Bash

Copy
export AZURE_TENANT="<azure_tenant_id>"
export AZURE_SUBSCRIPTION_ID="<azure_subscription_id>"
export AZURE_CLIENT_ID="<service_principal_app_id>"
export AZURE_SECRET="<service_principal_password>"
Create an Azure resource group
Run the following Ansible command to create a resource group:

Bash

Copy
ansible localhost -m azure_rm_resourcegroup -a 'name=myResourceGroup location=eastus'
Key points:

Upon completion, the command displays whether it was successful in creating the resource group.
Clean up resources
Run the following Ansible command to delete the resource group.

Bash

Copy
ansible localhost -m azure_rm_resourcegroup -a 'name=myResourceGroup location=eastus state=absent'
Key points:

Upon completion, the command displays whether it was successful in creating the resource group.