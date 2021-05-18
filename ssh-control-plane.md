
# Connect to the Supervisor Cluster as a vCenter Single Sign-On User

https://docs.vmware.com/en/VMware-vSphere/7.0/vmware-vsphere-with-tanzu/GUID-F5114388-1838-4B3B-8A8D-4AE17F33526A.html#GUID-F5114388-1838-4B3B-8A8D-4AE17F33526A

```
~/ns1-admin.sh


To change context, use `kubectl config use-context <workload name>`
Switched to context "192.168.2.1".
```
Get the namespace
```
k get ns
NAME                                        STATUS   AGE
default                                     Active   3d
ns1                                         Active   2d23h
```

```
export NAMESPACE=ns1
kubectl config use-context $NAMESPACE
```
Get the ssh secret
```
k get secret
NAME                               TYPE                                  DATA   AGE
...
ns1-cluster1-ssh                   kubernetes.io/ssh-auth                1      2d23h
...
```

```
k apply -f jumpbox-pod.yaml

watch kubectl get pods
```
Get the vm image names
```
kubectl get virtualmachines

export VMNAME=ns1-cluster1-control-plane-dtsvk
export VMIP=$(kubectl -n $NAMESPACE get virtualmachine/$VMNAME -o jsonpath='{.status.vmIp}')
```

```
echo $VMIP
kubectl exec -it jumpbox  /usr/bin/ssh vmware-system-user@$VMIP
```
