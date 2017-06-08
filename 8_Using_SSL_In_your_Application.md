## Using SSL in your application

In this exercise you will set up your application to encrypt traffic with the OpenShift Wildcard certificate.

**Step 1: Switch to an existing project**

For this exercise, we will use an application that we created before. We will be using the **myjbossapp-UserName** that you created in the previous labs. Make sure you are switched to that project by using the **oc project** command. **Remember** to substitute UserName.

```
$ oc project myjbossapp-UserName
```

**Step 2: View the routing config**

To view the routing config you will need to use the `oc get route` command

```
$ oc get route/ks -o yaml

apiVersion: v1
kind: Route
metadata:
  annotations:
    openshift.io/host.generated: "true"
  creationTimestamp: 2017-05-25T11:52:59Z
  labels:
    app: myapp
  name: myapp
  namespace: binarydeploy-UserName
  resourceVersion: "178815"
  selfLink: /oapi/v1/namespaces/binarydeploy-UserName/routes/myapp
  uid: b2ecd8df-4140-11e7-a11f-0e702637dc7c
spec:
  host: myapp-binarydeploy-UserName.apps.ose3sandbox.com
  port:
    targetPort: 8080-tcp
  to:
    kind: Service
    name: myapp
    weight: 100
  wildcardPolicy: None
status:
  ingress:
  - conditions:
    - lastTransitionTime: 2017-05-25T11:52:59Z
      status: "True"
      type: Admitted
    host: myapp-binarydeploy-UserName.apps.ose3sandbox.com
    routerName: router
    wildcardPolicy: None
```

Note here that the `host:` is set to the FQDN that your application is running on.

Currently the routing component of OpenShift 3 supports ports `80` and `443`. When you first create your route, the mapping of `80` to your pod is done automatically. There are a few things that need to be done in order to get the `443` mapping to work.

**Step 3: TLS Edge Termination**

OpenShift has a *wildcard* SSL certificate that it can use for any application. We can use this SSL certificate to serve SSL from our application without having to generate a cert of our own (which is sometimes called SSL-offloading).

Edit your routing configuration:

```
oc edit route/ks
```

You are going to add `tls: termination: edge` right below the `host:` section. It should look something like this.

```
...
spec:
  host: myapp-binarydeploy-UserName.apps.ose3sandbox.com
  tls:
    termination: edge
  port:
    targetPort: 8080-tcp
  to:
    kind: Service
    name: myapp
    weight: 100
  wildcardPolicy: None
...
```

**Step 4: Verify**

Verify by visiting your page by using the `https://` URI


Congratulations!! In this exercise you have learned about service SSL from your application
