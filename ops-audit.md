## Audit Logging ##

Audit provides a security-relevant chronological set of records documenting the sequence of activities that have affected system by individual users, administrators, or other components of the system.

To enable auditing, a section must be added to the master-config.yaml configuration file.

On the OpenShift server:

```
sudo -i
cd /etc/origin/master/
cp master-config.yaml master-config.yaml.backup
```
Edit master-config.yaml and add the following section:

```
auditConfig:
  auditFilePath: "/var/log/audit-ocp.log"
  enabled: true
  maximumFileRetentionDays: 2
  maximumFileSizeMegabytes: 10
  maximumRetainedFiles: 3
```

Restart the OpenShift master:

```
sudo systemctl restart atomic-openshift-master-controllers
sudo systemctl restart atomic-openshift-master-api
```

To filter the audit log for a specific user, you can do:

```
tail -100f /var/log/audit-ocp.log  | grep user=\"admin\"
```

Navigate in the OpenShift console to view your actions!
