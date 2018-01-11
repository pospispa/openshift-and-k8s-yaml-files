# YAML Files Used during Manual Testing of PVC Finalizer Controller in OpenShift 3.7

Bugzilla: https://bugzilla.redhat.com/show_bug.cgi?id=1494257


## Using GCE

Created OpenShift 3.7 GCE installation.

Replaced installed `openshift` with local `openshift` build:
```
scp -i <work-ssh-key> openshift root@<both-master-and-node-nodes>:
scp -i <work-ssh-key> oc root@<both-master-and-node-nodes>:
```


### On Node

```
ssh -i <work-ssh-key> root@<node-node>
rm /usr/bin/oc
cp oc /usr/bin/
rm /usr/bin/openshift
cp openshift /usr/bin/

/usr/bin/openshift version
docker tag registry.reg-aws.openshift.com:443/openshift3/ose-pod:v3.7.22 registry.reg-aws.openshift.com:443/openshift3/ose-pod:v3.7.20

vim /etc/origin/node/node-config.yaml
```
Add into `kubeletArguments:`
```
feature-gates:
- PVCProtection=true
```

Restart: `systemctl restart atomic-openshift-node.service`


### On Master

```
ssh -i <work-ssh-key> root@<master-node>
rm /usr/bin/oc
cp oc /usr/bin/
rm /usr/bin/openshift
cp openshift /usr/bin/

/usr/bin/openshift version
docker tag registry.reg-aws.openshift.com:443/openshift3/ose-pod:v3.7.22 registry.reg-aws.openshift.com:443/openshift3/ose-pod:v3.7.20

oc adm manage-node <node-name> --schedulable=true

vim /etc/origin/node/node-config.yaml
```
Add into `kubeletArguments:`
```
feature-gates:
- PVCProtection=true
```

`vim /etc/origin/master/master-config.yaml`

Add into
```
admissionConfig:
  pluginConfig:
```
```
PVCProtection:
  configuration:
    apiVersion: v1
    disable: false
    kind: DefaultAdmissionConfig
```

Add into
```
kubernetesMasterConfig:
  apiServerArguments:
```
```
feature-gates:
- PVCProtection=true
```

Add into
```
kubernetesMasterConfig:
  controllerArguments:
```
```
feature-gates:
- PVCProtection=true
```

Restart:
```
systemctl restart atomic-openshift-master-api atomic-openshift-master-controllers atomic-openshift-node.service
```


### Manual Functional Testing
Finalizer is added to PVC on creation. PVCs that are not in active use by a pod are deleted immediatelly:
```
oc create -f sc-gce-no-zones.yaml
oc create -f pvc-gce.yaml
oc describe pvc
oc delete pvc slzc
oc describe pvc
```

PVC that is in active use by a pod and is deleted is not deleted immediatelly, but transitions into `Terminating` state:
```
oc create -f pvc-gce.yaml
oc create -f pod-gce.yaml
oc describe pod app1

oc delete pvc slzc
oc describe pvc slzc
```

Scheduling of a pod that uses a PVC that is being deleted fails:
```
oc create -f pod-gce-scheduling-must-fail.yaml
oc describe pod app2
```

PVC that is being deleted is deleted immediatelly after all pods that actively use it are deleted:
```
oc delete pod app1
oc describe pod app1
oc describe pvc slzc
```

Final tidying:
```
oc delete pod app2
oc delete sc slow
```
