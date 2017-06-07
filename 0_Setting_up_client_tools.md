## Setting up client tools

In this Lab we will look at how to install the OpenShift CLI tools.
Make sure you are using a CLI version that matches the OpenShift server version. The current version for this workshop is OpenShift 3.5 (Origin 1.5).  Older version may work, but some issue may occur.

**Installing the Command-line Tools**

After completing this section, you should be able to:

- Locate the binaries for the OpenShift Enterprise command-line interface
(OSE CLI)
- Install the OSE CLI tools.
- CLI basic configuration

**Locating the binaries**

The OSE CLI exposes commands for managing applications, as well as lower-level
tools to interact with each component of a system. The binaries for Mac, Windows,
and Linux are available for download from the Red Hat Customer Portal via the
following link:

Red Hat Portal (Red Hat Login required) https://access.redhat.com/downloads/content/290

or

OpenShift Origin:  https://www.openshift.org/download.html

**Installing the CLI tools**

The CLI is provided as compressed files that can be decompressed to any
directory. In order to make it simple for any user to access the OSE CLI, it is
recommended that it is made available in a directory mapped to the environment
variable called `PATH` from the OS. More information can be found about installation process here: https://docs.openshift.com/container-platform/3.5/cli_reference/get_started_cli.html

1. **OSX and Linux:**

	1.1. Copy the binary to the `/usr/local/bin` directory, or one of the paths listed in the `PATH` environment variable.

2. **RHEL:**

	2.1. For Red Hat Enterprise Linux (RHEL) 7, you can install the CLI as an RPM using Red Hat Subscription Management (RHSM) if you have an active OpenShift Enterprise subscription on your Red Hat account:

	```shell
  	$ sudo subscription-manager attach --pool=<OPENSHIFT_pool_ID>
  	$ sudo subscription-manager repos --enable="rhel-7-server-ose-3.5-rpms"
  	$ sudo yum install atomic-openshift-clients
	```

3. **Windows:**

   3.1. Use oc.exe to open an OpenShift shell. If you getting error from running oc, go to git-scm.com to download git bash for Windows (during installation you need to specify in the selection to integrate with the command prompt)

   3.2. Download and install Notepad++ and install the JSON plugin or use http://jsonlint.com/ to edit and validate JSON.
        http://ammonsonline.com/formatted-json-in-notepad/

   3.3. Configure your default editor to be Notepad++


**CLI basic configuration**

The easiest way to initially setup the OpenShift CLI is to use the `oc login`
command. It'll interactively ask you a server URL, username and password. The
information is automatically saved in a CLI configuration file that is then used
for subsequent commands.

To login to a remote server use oc login <hostname>:<port> :

```shell
$ oc login https://openshift-master.ose3sandbox.com
```
