## Health Checks, Logging and Monitoring
In this section we will intentionally crash our pods, as well as make a pod non-responsive to the liveness probes and see how Kubernetes behaves.  We will first intentionally crash our pod and see that Kubernetes will self-heal by immediately spinning it back up. Then we will trigger the health check by stopping the response on the `/health` endpoint in our app. After three consecutive failures Kubernetes should kill the pod and then recreate it.

#### 1. Prepare the desktop
It would be best to prepare by splitting your screen between the OpenShift Web UI and the OSToy application so that you can see the results of our actions immediately.

![Splitscreen](//Images/Lab3-ostoy-splitscreen.png)

But if the screen is too small or that just won't work, then open the OSToy application in another tab so you can quickly switch to the OpenShift Web Console once you click the buttons in our app. To get to this deployment in the OpenShift Web Console go to make sure you've selected "ostoy" for the project and in the left menu go to:

*Workloads > Deployments > "ostoy-frontend"*

![Deploy Num](/Images/Lab3-ostoy-deployview.png)

#### 2. Intentionally crash the pod
Go to the OSToy app, click on *Home* in the left menu, and enter a message in the "Crash Pod" tile (ex: "This is goodbye!") and press the "Crash Pod" button.

![Crash pod](/Images/Lab3-ostoy-crashpod.png)

This will cause the pod to crash and Kubernetes should restart the pod. After you press the button you will see:

![Crash Message](/Images/Lab3-ostoy-crashmsg.png)

#### 3. View the revival
Quickly switch to the Deployments screen (from step 1). You will see that the pod turns yellowish, meaning it is down but should quickly come back up and show blue.  It does happen quickly so you might miss it.

![Pod Crash](/Images/Lab3-ostoy-podcrash.gif)

#### 4. Confirm
You can also check in the pod events. Click on *"Pods" sub tab > ostoy-frontend-xxxxxxx-xxxx*. This will change to the pods screen.

![Pod Events](/Images/Lab3-ostoy-events.png)

Then click the *Events* sub tab and further verify that the container has crashed and been restarted.

![Pod Events](/Images/Lab3-ostoy-podevents.png)

#### 5. Make the app malfunction
Keep the page from the pod events still open from step 4. Then in the OSToy app click on the "Toggle Health" button, in the "Toggle Health Status" tile.  You will see the "Current Health" switch to "I'm not feeling all that well".

![Pod Events](/Images/Lab3-ostoy-togglehealth.png)

#### 6. Confirm
This will cause the app to stop responding with a "200 HTTP code". After 3 such consecutive failures ("A"), Kubernetes will kill the pod ("B") and restart it ("C"). Quickly switch back to the pod events tab and you will see that the liveness probe failed and the pod as being restarted.

![Pod Events2](/Images/Lab3-ostoy-podevents2.png)

#### 7. Logging and Metrics
Click on the Home menu item and then click in the message box for “Log Message (stdout)” and write any message you want to output to the stdout stream. You can try “All is well!”. Then click “Send Message”.
![Message stdout](/Images/Lab3-ostoy-stdout.png)

Click in the message box for “Log Message (stderr)” and write any message you want to output to the stderr stream. You can try “Oh no! Error!”. Then click “Send Message”.
![Message stderr](/Images/Lab3-ostoy-stderr.png)

### View application logs using `oc`

1. Go to the CLI and enter the following command to retrieve the name of your frontend pod which we will use to view the pod logs:

		$ oc get pods -o name
		pod/ostoy-frontend-679cb85695-5cn7x
		pod/ostoy-microservice-86b4c6f559-p594d

So the pod name in this case is **ostoy-frontend-679cb85695-5cn7x**.  

1. Run `oc logs ostoy-frontend-679cb85695-5cn7x` and you should see your messages:

		$ oc logs ostoy-frontend-679cb85695-5cn7x
		[...]
		ostoy-frontend-679cb85695-5cn7x: server starting on port 8080
		Redirecting to /home
		stdout: All is well!
		stderr: Oh no! Error!

You should see both the *stdout* and *stderr* messages.
