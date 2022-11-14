Welcome to OpenShift!!

This lab provides a quick tour of the console to help you get familiar with the user interface along with some key terminology we will use in subsequent lab content. If you are already familiar with the basics of OpenShift simply ensure you can login and create the project.

#### Estimate time
Completing this tutorial should take about 15 minutes.

#### Key Terms
We will be using the following terms throughout the workshop labs so here are some basic definitions you should be familiar with. You'll learn more terms along the way, but these are the basics to get you started.

* Container - Your software wrapped in a complete filesystem containing everything it needs to run
* Image - We are talking about docker images; read-only and used to create containers
* Pod - One or more docker containers that run together
* Service - Provides a common DNS name to access a pod (or replicated set of pods)
* Project - A project is a group of services that are related logically
* Deployment - an update to your application triggered by a image change or config change
* Build - The process of turning your source code into a runnable image
* BuildConfig - configuration data that determines how to manage your build
* Route - a labeled and DNS mapped network path to a service from outside OpenShift
* Master - The foreman of the OpenShift architecture, the master schedules operations, watches for problems, and orchestrates everything
* Node - Where the compute happens, your software is run on nodes

### Accessing OpenShift

To retrieve the console URL run:

```bash
az aro show --name "${AZ_ARO}" --resource-group \
  "${AZ_RG}" -o tsv --query consoleProfile
```

Login to the console with the provided credentials through a browser.

### OpenShift CLI Login
You can utilize a command line tool to perfrom tasks.

a. To retrieve the API server's address.

```bash
az aro show -g "${AZ_RG}" -n "${AZ_ARO}" --query apiserverProfile.url -o tsv
```

b. Login to the OpenShift cluster's API server using the following command.

```bash
oc login "${OCP_API}" -u "${OCP_USER}" -p "${OCP_PASS}"
```
### Deploying first application via Web console
a. Once you have logged into your clusters you should see the following 

! [OpenShift Webconsole](../Images/Lab1-Webconsole.png)

There are 2 views administrator and developer view on left hand side. 

The Administrator perspective enables you to view the cluster inventory, capacity, general and specific utilization information, and the stream of important events, all of which help you to simplify planning and troubleshooting tasks. Both project administrators and cluster administrators can view the Administrator perspective.

The Developer perspective offers several built-in ways to deploy applications, services, and databases

b. Select the Administration and the click Projects on the left and then Click Create project 
! [OpenShift Create Project](../Images/Lab1-CreateProject.png)

c. Give the name of the project.
Example:*user1-lab1*
Project names are unique namespace.
! [OpenShift Create Project Name](../Images/Lab1-CreateProject-name.png)

d. Switch to developer view
! [OpenShift Create Project Name](../Images/Lab1-DeveloperView.png)

e. Click the view all quickstart link 
! [OpenShift QuickStart](../Images/Lab1-Quickstart.png)

f. You should see several tiles and click sample application and side window will openup. Click Start and follow the instructions
! [OpenShift Sample Application](../Images/Lab1-SampleApplication.png)
! [OpenShift Sample Application](../Images/Lab1-SampleApplication-start.png)

g. Complete all this steps to finish this lab. At the end of it you should see a webbrowser with **"Hello from Node.js Starter Application!"**

f. Start with 
