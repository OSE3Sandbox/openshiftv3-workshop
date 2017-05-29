## Managing storage

In previous versions, OpenShift was using PV/PVC method to provision storage. A PV (Persistent Volume) resource is a representation of a storage resource (NFS, Gluster, EBS, Fiber...) in the OpenShift Cluster.  A PVC (Persistent Volume Claim) is a request for storage with specific attribute.

Introduced in OpenShift 3.4, Storage Classes are a way of setting up persistent storage that offers more flexibility and control than the simple PV / PVC options.

Storage classes are resource objects that describe and classify storage that can be requested.

One of the primary features of Storage Classes is that they can be used to provide storage that is dynamically provisioned. This is explained in detail  in [our docs](https://docs.openshift.com/container-platform/latest/install_config/persistent_storage/dynamically_provisioning_pvs.html#install-config-persistent-storage-dynamically-provisioning-pvs) , and dynamic provisioning is supported for:

* OpenStack Cinder
* AWS EBS
* GCE PersistentDisk
* GlusterFS
* Ceph RBD

In this lab, we will be using GlusterFS.

#### Lab Scenario

We’ve defined two Gluster classes to simulate two different types of storage:
* “gluster-fast” will be our placeholder for a high I/O speed storage option (SSD-based, for instance)
* "gluster-slow” will be our placeholder for a slower I/O speed storage option (like rotational-media old school hard drive storage)

We are going to deploy two applications into a new namespace. We will provision the fast storage to one of the apps and slow storage to the other.

##### Step 1 - Create Storage Classes

Storage Classes have already been provisioned for you.
To look at them, do:

* oc get storageclasses

The output should look like:

```
NAME                     TYPE
cns-standard (default)   kubernetes.io/glusterfs
gluster-fast             kubernetes.io/glusterfs
gluster-slow             kubernetes.io/glusterfs
```

In this environment, cns-standard is set as the default storage class. The default storage class is use when no specific type of storage is requested.

Look at each storage class:

* oc describe storageclass cns-standard

```
storageclass.beta.kubernetes.io/is-default-class=true
```

Is the annotation used to define a default class.


##### Step 2 - Create project and application

Create a new project to deploy applications using these storage classes:

```
oc new-project userXX-storage-classes --display-name='userXX Storage Classes'
```

Deploy the demo application in your newly created project:

```
oc new-app docker.io/nhripps/sleeper:v1.0 --name=fast-app
```

##### Step 3 (Optional) - Create PVC using the CLI.

You can either create the PVC from the command line or directly in the OpenShift User Interface.

<strong> If you choose to create using the UI, please skip this step. </strong>

To create from the command line, look at the fast-gluster-pvc.yaml file:

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-gluster-fast
  annotations:
    volume.beta.kubernetes.io/storage-class: gluster-fast
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 100Mi
```

The annotation:     volume.beta.kubernetes.io/storage-class: gluster-fast
 is used to select the desired storage type.

Select your storage project in the OpenShift UI

In the left-hand navigation panel, click the Storage tab. Here you should see the PersistentVolumeClaim that you have created. It should be bound to a GlusterFS Volume.


##### Step 4 - Attach storage to your application

In this step, we will attach persistent storage to our application.

* Using the OpenShift UI, navigate to the storage project. Use the left-hand navigation panel to go to: Applications => Deployments. You should see the ‘fast-app’ Deployment. Click into ‘fast-app’ to see a summary of the Deployment configuration.

![image](images/sc-image1.png)


* In the Template section of this Deployment summary page, find the Volumes heading and the ‘Add storage’ link below it. Click this link to move to the ‘Add Storage’ view.

* In the ‘Add Storage’ view:
    * You should see that you can choose which PersistentVolumeClaim to use for this deployment. If you have done Step 3, use your ‘pvc-gluster-fast’ storage claim. If not, create one with the following attributes:

![image](images/sc-image-2.png)

* In the next screen, Under the ‘Volume’ heading, set the value of ‘Mount Path’ to /mountpoint. This is an existing directory path inside of the container that will become a mount point in the container for our Gluster volume.

* Press the ‘Add’ button to apply this change

* You should be looking at the Deployment summary page again. Under the ‘Volumes’ heading, you should see an entry for the volume that you just added.

##### Step 5 - Validate persistence of data

* Using the left-hand navigation panel, now go to Applications => Pods. You should see at least one pod with a name like ‘fast-app-2-.....`. Click on this pod to switch into the Pod summary view.

* Click on the terminal tab. You will be connected to the running pod and presented with a shell.

* In the shell, type:
   * echo fast >/mountpoint/fast.txt

* In the CLI, delete your pod:

```
oc get pods

oc delete pod fast-app-xXXX

```

* OpenShift will restart your application automatically
* if you look in the newly created pod, your fast.txt file should be there.


The Instructor can also show you the file in GlusterFS
