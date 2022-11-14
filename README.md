# Azure Red Hat OpenShift Workshop

[Azure Red Hat OpenShift](https://azure.microsoft.com/en-us/products/openshift/?wt.mc_id=aroworkshop#overview)(ARO) is a fully managed Red Hat OpenShift service in Azure that is jointly engineered and supported by Microsoft and Red Hat. In this lab, you’ll go through a set of tasks that will help you understand some of the concepts of deploying and securing container based applications on top of Azure Red Hat OpenShift.

You can use this guide as an OpenShift tutorial and as study material to help you get started to learn OpenShift.

### Description
This workshop will have you deploying and creating native docker images for a Node.js based website and learning to leverage the power of OpenShift to build, deploy, scale, and automate.

### Who should attend
Anyone who has had any exposure to Containers
Architects
Developers
Technical leads
Operations Engineers

### What you will learn
This tutorial walks you through getting your first application instance up and running on an OpenShift managed cluster on ARO, using OpenShift command-line interface (CLI) tools and the web console.

Some of the things you’ll be going through:

- Creating a [project](https://docs.openshift.com/container-platform/4.11/applications/projects/working-with-projects.html) on the Azure Red Hat OpenShift Web Console
- Deploying a database container for backend application
- Deploying a frontend app from Git Hub
- Exposing the web application frontend using Routes
- Introduction to operators

### Prerequistes
a. OpenShift cluster on ARO is already created

b. Install the OpenShift CLI on terminal
   You can [download and install](https://docs.openshift.com/container-platform/4.10/cli_reference/openshift_cli/getting-started-cli.html#installing-openshift-cli) the latest OpenShift CLI (oc)
    Or, 
   if you already have an OpenShift cluster you can access the command line tools page by clicking on the Question mark > Command Line Tools. Then download the relevant one for your operating system.
![OpenShift CLI](/Images/Prereq-ocli.png)

c. GitHub Account
   You’ll need a personal GitHub account. You can sign up for free [here](https://github.com/). 

d. Download Azure command-line (CLI) documentation   
   The Azure command-line interface (Azure CLI) is a set of commands used to create and manage Azure resources. You can downlaod it [here](https://learn.microsoft.com/en-us/cli/azure/)

### Why use oc over kubectl
Being Kubernetes, one can definitely use kubectl with their OpenShift cluster. oc is specific to OpenShift in that it includes the standard set of features from kubectl plus additional support for OpenShift functionality. See [Usage of oc and kubectl](https://docs.openshift.com/container-platform/4.11/cli_reference/openshift_cli/usage-oc-kubectl.html) commands for more details

### ARO credentials 
Credentials will be provided to you by the organizing staff on the day of the event.

