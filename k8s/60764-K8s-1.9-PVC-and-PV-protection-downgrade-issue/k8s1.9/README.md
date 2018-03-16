# YAML Files Used during Manual Testing of PVC Protection Downgrade Issue in Kubernetes 1.9

[The PVC Protection downgrade issue](https://github.com/kubernetes/kubernetes/issues/60764).

## Verify PV/PVC finalizer removal

In this scenarion PV and PVC already contains a finalizer. Automatic finalizer removal after PV/PVC deletion is verified.

Create PV with a finalizer:
```
./kubectl.sh create -f /home/pp/go/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/60764-K8s-1.9-PVC-and-PV-protection-downgrade-issue/k8s1.9/pv.yaml
```

Create PVC with a finalizer:
```
./kubectl.sh create -f /home/pp/go/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/60764-K8s-1.9-PVC-and-PV-protection-downgrade-issue/k8s1.9/pvc.yaml
```

Delete PVC:
```
./kubectl.sh delete pvc task-pv-claim
./kubectl.sh get pvc
```

Delete PV:
```
./kubectl.sh delete pv task-pv-volume
./kubectl.sh get pv
```

## Verify newly created PV/PVC does not contain any finalizers

Create PV without a finalizer:
```
./kubectl.sh create -f /home/pp/go/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/60764-K8s-1.9-PVC-and-PV-protection-downgrade-issue/k8s1.9/no-finalizer-pv.yaml
./kubectl.sh get pv task-pv-volume -o yaml
```

Create PVC without a finalizer:
```
./kubectl.sh create -f /home/pp/go/src/github.com/pospispa/openshift-and-k8s-yaml-files/k8s/60764-K8s-1.9-PVC-and-PV-protection-downgrade-issue/k8s1.9/no-finalizer-pvc.yaml
./kubectl.sh get pvc task-pv-claim -o yaml
```
