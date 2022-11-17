This tutorial walks you through getting your node.js application instance up on an OpenShift managed cluster on ARO, using OpenShift command-line interface (CLI) tools

After the successful deployment of an Azure Red Hat OpenShift cluster Developers, Engineers, DevOps and SRE can start to use it easily through the portal or through the oc CLI.

### Estimate time
Completing this tutorial should take about 30 minutes.

### Resources
The source code for this app is available here: https://github.com/openshift-cs/ostoy

OSToy front-end container image: https://quay.io/repository/ostoylab/ostoy-frontend?tab=tags

OSToy microservice container image: https://quay.io/repository/ostoylab/ostoy-microservice?tab=tags

Deployment Definition YAMLs:
ostoy-fe-deployment.yaml
ostoy-microservice-deployment.yaml

### About OSToy
OSToy is a simple Node.js application that we will deploy to Azure Red Hat OpenShift. It is used to help us explore the functionality of Kubernetes. This application has a user interface which you can:

write messages to the log (stdout / stderr)
intentionally crash the application to view self-healing
toggle a liveness probe and monitor OpenShift behavior

### Deploy via a Kubernetes Deployment
One way to deploy the application would be to have the images for the front-end and back-end microservice containers already created (via CI/CD) and stored in an image repository.  You can then create Kubernetes deployments (YAML) and use those to deploy the application.  We will do that now.

#### 1. Retrieve the login command
If you are not logged in via the CLI, [access your cluster via the web console](https://github.com/nedoshi/aroworkshop/blob/main/Lab1%20-%20OpenShift%20Basics/OpenShiftBasics-readme.md), then click on the dropdown arrow next to your name in the top-right and select *Copy Login Command*.

![CLI Login](/Images/Lab2-cli-login.png)

A new tab will open and select the authentication method you are using (in our case it's *github*)

Click *Display Token*

Copy the command under where it says "Log in with this token". Then go to your terminal and paste that command and press enter.  You will see a similar confirmation message if you successfully logged in.

    $ oc login --token=RYhFlXXXXXXXXXXXX --server=https://api.osd4-demo.abc1.p1.openshiftapps.com:6443
    Logged into "https://api.osd4-demo.abc1.p1.openshiftapps.com:6443" as "0kashi" using the token provided.

    You don't have any projects. You can try to create a new project, by running

    oc new-project <projectname>

#### 2. Create new project
Create a new project called "ostoy" in your cluster by entering the following command: 

Please use different project names ***example: user1-ostoy***

    oc new-project <USERID>-ostoy

You should receive the following response

    $ oc new-project ostoy
    Now using project "ostoy" on server "https://api.osd4-demo.abc1.p1.openshiftapps.com:6443".

    You can add applications to this project with the 'new-app' command. For example, try:

        oc new-app centos/ruby-25-centos7~https://github.com/sclorg/ruby-ex.git

    to build a new example application in Ruby.

Equivalently you can also create this new project using the web console UI as shown in Lab1 by clicking on "Projects" under "Home" on the left menu, and then click "Create Project" button on the right. 

![UI Create Project](/Images/Lab1-CreateProject.png)

<!---
#### 3. Download the YAML configuration
Download the Kubernetes deployment object yamls from the following locations to your local machine, in a directory of your choosing (just remember where you placed them for the next step).

[ostoy-fe-deployment.yaml](https://raw.githubusercontent.com/openshift-cs/rosaworkshop/master/ostoy/yaml/ostoy-fe-deployment.yaml)

[ostoy-microservice-deployment.yaml](https://raw.githubusercontent.com/openshift-cs/rosaworkshop/master/ostoy/yaml/ostoy-microservice-deployment.yaml)

Feel free to open them up and take a look at what we will be deploying. For simplicity of this lab we have placed all the Kubernetes objects for the front-end in an "all-in-one" yaml file.  Though in reality there are benefits (ease of maintenance and less risk) to separating these out into individual yaml files.
-->

#### 3. Deploy the backend microservice
The microservice serves internal web requests and returns a JSON object containing the current hostname and a randomly generated color string.

In your terminal deploy the microservice using the following command:

    oc apply -f https://raw.githubusercontent.com/nedoshi/aroworkshop/main/yaml/ostoy-microservice-deployment.yaml

You should see the following response:

    $ oc apply -f https://raw.githubusercontent.com/nedoshi/aroworkshop/main/yaml/ostoy-microservice-deployment.yaml
    deployment.apps/ostoy-microservice created
    service/ostoy-microservice-svc created

#### 4. Deploy the front-end service
The frontend deployment contains the node.js frontend for our application along with a few other Kubernetes objects to illustrate examples.

 If you open the *ostoy-fe-deployment.yaml* you will see we are defining:

- Persistent Volume Claim
- Deployment Object
- Service
- Route
- Configmaps
- Secrets

In your terminal, deploy the frontend along with creating all objects mentioned above by entering:

    oc apply -f https://raw.githubusercontent.com/nedoshi/aroworkshop/main/yaml/ostoy-fe-deployment.yaml

You should see all objects created successfully

    $ oc apply -f https://raw.githubusercontent.com/nedoshi/aroworkshop/main/yaml/ostoy-fe-deployment.yaml
    persistentvolumeclaim/ostoy-pvc created
    deployment.apps/ostoy-frontend created
    service/ostoy-frontend-svc created
    route.route.openshift.io/ostoy-route created
    configmap/ostoy-configmap-env created
    secret/ostoy-secret-env created
    configmap/ostoy-configmap-files created
    secret/ostoy-secret created

#### 5. Get the route
Get the route so that we can access the application via 
    
    oc get route

You should see the following response:

    NAME          HOST/PORT                                                 PATH   SERVICES             PORT    TERMINATION   WILDCARD
    ostoy-route   ostoy-route-ostoy.apps.my-rosa-cluster.g14t.p1.openshiftapps.com          ostoy-frontend-svc   <all>                 None

#### 6. View the app
Copy `ostoy-route-ostoy.apps.my-rosa-cluster.g14t.p1.openshiftapps.com` above and paste it into your browser and press enter. You should see the homepage of our application. If the page does not come up make sure that it is using `http` and **not** `https`.

![Home Page](/Images/Lab2-ostoy-homepage.png)



