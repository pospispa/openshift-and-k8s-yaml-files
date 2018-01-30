# YAML Files Used during Manual Testing of PVC Protection Controller Kubernetes 1.10

Bugzilla: https://bugzilla.redhat.com/show_bug.cgi?id=1535339

## Using GCE after merge

```
gcloud compute --project "openshift-gce-devel" scp --zone "us-central1-b"  /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/k8s.1.10/*.yaml ppospisi@ppospisi-e2e-master:

kubectl create -f sc-gce-no-zones.yaml
kubectl create -f pvc-gce.yaml
kubectl edit pvc slzc
# add a finalizer so that the PVC can't be removed from the system even though it's deleted (in Terminating state)
kubectl delete pvc slzc
kubectl describe pvc slzc
kubectl get nodes
vim pod-gce.yaml
# replace the <node-name> with an exiting node so that the pod is not scheduled by is placed directly onto the node
kubectl create -f pod-gce.yaml
kubectl describe pod app1
# see the Warning, i.e. the error message
```
