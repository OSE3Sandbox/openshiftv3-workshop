## SCM Web Hooks

OpenShift offers several mechanisms to trigger the automatic deployment of
applications. In this Lab, we will demonstrate the deployment of a very simple
application via a GitHub Web Hook trigger.

## Part I

In Part I of the Lab we will:

- Import an application in your gitlab server
- Create a new project.
- Deploy an application using a source to image strategy.
- Create a route to expose the newly deployed application.

**Step 1: Import an app in gitlab**

We will import the bluegreen app previously used in your gitlab server.

On your gitlab server, create a new project named lab-webhook.

In "Import project from" select "git repo by url" and type this url :
https://github.com/RedHatWorkshops/bluegreen

In visibility, select Public

![image](images/gitlab-webhook.png)

Click on create project.

**Step 2: Create new project**

**Remember** to substitute the UserName

```
$ oc new-project scm-web-hooks-UserName --display-name="Test WebHooks"
```

**Step 3: Create new application**

- We will use the blugreen app that you have imported in your gitlab server in the Step1

**NOTE** replace USERXX by your openshift user login and USERNAME by your gitlab username
```
$ oc new-app --image-stream=php --code=http://gitlab-ce-USERXX-gitlab.apps.ose3sandbox.com/USERNAME/webhook.git  --name=scm-web-hooks
```

**Step 4: Look at some of the created resources**

- Build configuration

```
$ oc get bc
```

- Deployment configuration

```
$ oc get dc
```

- Show created service

```
$ oc get service
```

- Show replication controller

```
$ oc get rc
```

- Show route

```
$ oc get route
```

Notice that there is no route created yet for this application.

- Show the builds in progress (Running)

```
$ oc get builds
NAME              TYPE      STATUS    POD
scm-web-hooks-1   Source    Running   scm-web-hooks-1-build
```

- Monitor the build

```
$ oc logs build/scm-web-hooks-1
```

The build should finish similar to:

```
Pushing image 172.30.89.28:5000/scm-web-hooks-admin/scm-web-hooks:latest ...
Pushed 0/5 layers, 2% complete
Pushed 1/5 layers, 22% complete
Pushed 2/5 layers, 44% complete
Pushed 3/5 layers, 70% complete
Pushed 3/5 layers, 100% complete
Pushed 4/5 layers, 100% complete
Pushed 5/5 layers, 100% complete
Push successful
```

**Step 5: Create a route**

```
$ oc get service

NAME            CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
scm-web-hooks   172.30.71.191   <none>        8080/TCP   4m
```

**Remember** to substitute the UserName.
```
$ oc expose service scm-web-hooks
NAME            HOST/PORT                                  PATH      SERVICE         LABELS              TLS TERMINATION
scm-web-hooks   scm-web-hooks.UserName.apps.osecloud.com             scm-web-hooks   app=scm-web-hooks   
```
**Step 6: Test the application**

- Use the newly created route, in the above case `scm-web-hooks.UserName.apps.osecloud.com` and paste it in your browser.

- You should see:

![image](images/green_deployment.png)

## Part II

At this point you have an application with one single replica running inside a
docker container in OpenShift. We used the source retrieved from the git repository
and layered it using a builder or image stream strategy; in this particular case,
the php image stream.

In this part of the Lab we will:

- Create a web hook for the recently deployed application.
- Make a small change to the application.
- Test to make sure the application was re-deployed.

**Step 1: Retrieve the OpenShift Web Hook URL**

- Navigate to the OpenShift Web console and login.
- Select your **Test WebHooks** project, and click **Builds** and then **Builds**.
- Click onto the build name from the list. You should have just one in this case.
- Click **Configuration** tab to get list of **Triggers** for the GitHub link.
- Copy the **Generic Webhook URL**. You will need this URL for next step.

![image](images/github_show_url.png)

**Step 2: Configure GitHub repository Web Hook**

- Login to your Gitlab account.
- Navigate to the forked repository you used to create the application.
- Click on Settings
- Click on Integrations
- Add the recently copied Web Hook URL from OpenShift.
- Uncheck the **Enable SSL Verification**
- Confirm by adding the **Add webhook** button in green at the bottom of the page.

![image](images/gitlab-webhook2.png)

**Step 3: Redeploy the application**

- Edit in your Gitlab account the `image.php` file.
- Change the title of the page.
- Commit the file and go to the openshift web console.

**Step 4: Monitor new deployment process**

- After saving/committing the `image.php` file with the small change, you'll notice
in the OpenShift Web Console that a new build process has been automatically
triggered. **You didn't have to start a build yourself.**
- Monitor the build process using:

```
$ oc get builds

$ oc logs build/the-new-build-process-name
```

## Summary

We have shown in this Lab how simple it is to configure automatic deployments
of applications using OpenShift and Gitlab Web Hook triggers (this is also working with Github).
