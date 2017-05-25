**Lab: GitLab**

This lab sets up a GitLab Community Edition server with MongoDB and Redis backends.

A GitLab template have been added to the environment in the openshift namespace. You can look at the template here: https://raw.githubusercontent.com/OSE3Sandbox/gitlab-ocp3/master/gitlab.yaml

Create a new project using oc or the OpenShift console.

oc new-project gitlab-USERNAME

Using the OpenShift console, select "Add to Project" and select the gitlab template:

![image](images/gitlab-1.png)

Make sure to specify Gitlab instance hostname which should be the fully qualified name of the GitLab server

Also assign a github root user password.

For example:  gitlab-ce-gitlab-USERNAME.apps.ose3sandbox.com

![image](images/gitlab2.png)

Wait for all the containers to start. Once they are all started, access our gitlab server through a web browser.

You should see :

![image](images/gitlab-register.png)

Create a new account then login.  You can also login as root, using any password ... You should take note of this very secure practice :)   

Create a project and start to put some files in. Validate that it works using what you have learned during the Git presentation.   
