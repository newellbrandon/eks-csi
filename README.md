# eks-csi

1. Create storage class
2. Change default storage classes
3. Install external-snapshotter CRDs and controller
4. Create `volumesnapshotclass` and annotate it


```
#!/bin/bash

# Capture the output of `pwd` into the WORKINGDIR variable
WORKINGDIR=$(pwd)

git clone https://github.com/kubernetes-sigs/aws-ebs-csi-driver.git
### Create the storage class
cd aws-ebs-csi-driver/examples/kubernetes/dynamic-provisioning/
kubectl apply -f manifests/
### Change default storage classes
kubectl annotate sc gp2 storageclass.kubernetes.io/is-default-class=false --overwrite
kubectl annotate sc ebs-sc storageclass.kubernetes.io/is-default-class=true
### Install snapshot CRD and controller
cd $WORKINGDIR
git clone https://github.com/kubernetes-csi/external-snapshotter.git
cd external-snapshotter
kubectl kustomize client/config/crd | kubectl create -f -
kubectl -n kube-system kustomize deploy/kubernetes/snapshot-controller | kubectl create -f -
cd $WORKINGDIR
### Create volumesnapshotclass and annotate it
cd aws-ebs-csi-driver/examples/kubernetes/snapshot
kubectl apply -f manifests/classes/snapshotclass.yaml
kubectl annotate volumesnapshotclass csi-aws-vsc k10.kasten.io/is-snapshot-class=true
```
