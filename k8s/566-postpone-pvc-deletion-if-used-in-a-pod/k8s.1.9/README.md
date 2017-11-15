# YAML Files Used during Manual Testing of PVC Finalizer Controller Kubernetes 1.9

Trello card: https://trello.com/c/cRyaIf5E/566-8-prevent-pvc-deletion-if-pvc-in-use-by-a-pod

## Using host path external dynamic hostpath provisioner
Host path external dynamic hostpath provisioner: https://github.com/kubernetes-incubator/external-storage/tree/master/docs/demo/hostpath-provisioner

```
make
./hack/local-up-cluster.sh -o _output/bin/
export KUBECONFIG=/var/run/kubernetes/admin.kubeconfig
./cluster/kubectl.sh create -f /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/serviceaccount-hostpath-provisioner.yaml
./cluster/kubectl.sh create -f /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/clusterrole-hostpath-provisioner.yaml
./cluster/kubectl.sh create -f /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/clusterrolebinding-hostpath-provisioner.yaml

./cluster/kubectl.sh create -f /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/pod-hostpath-external-dynamic-provisioner.yaml

./cluster/kubectl.sh create -f /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/sc.yaml
./cluster/kubectl.sh create -f /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/pvc-hostpath-dynamic-provisioner.yaml

./cluster/kubectl.sh create -f /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/app1.yaml
./cluster/kubectl.sh delete pvc hostpath-pvc
./cluster/kubectl.sh create -f /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/app2.yaml
```

## Using GCE after merge

```
gcloud compute --project "openshift-gce-devel" scp --zone "us-central1-b"  /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/sc-gce-no-zones.yaml ppospisi@ppospisi-e2e-master:

gcloud compute --project "openshift-gce-devel" scp --zone "us-central1-b"  /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/pvc-gce.yaml ppospisi@ppospisi-e2e-master:


kubectl create -f sc-gce-no-zones.yaml
kubectl create -f pvc-gce.yaml
kubectl delete pvc slzc

gcloud compute --project "openshift-gce-devel" scp --zone "us-central1-b"  /home/ppospisi/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/566-postpone-pvc-deletion-if-used-in-a-pod/pod-gce.yaml ppospisi@ppospisi-e2e-master:

kubectl create -f pvc-gce.yaml
kubectl create -f pod-gce.yaml
kubectl describe pod app1

kubectl delete pvc slzc
kubectl describe pvc slzc
kubectl describe pod app1

kubectl delete pod app1
kubectl describe pod app1
kubectl describe pvc slzc
```
