
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
NAME                                          AGE
ns1-cluster1-control-plane-dtsvk              2d23h
ns1-cluster1-workers-9jg77-6b6f9cc77b-2t6wc   2d22h
ns1-cluster1-workers-9jg77-6b6f9cc77b-pcqh2   2d22h
ns1-cluster1-workers-9jg77-6b6f9cc77b-r5xdk   2d22h

export VMNAME=ns1-cluster1-control-plane-dtsvk
export VMIP=$(kubectl -n $NAMESPACE get virtualmachine/$VMNAME -o jsonpath='{.status.vmIp}')
```

```
echo $VMIP
kubectl exec -it jumpbox  /usr/bin/ssh vmware-system-user@$VMIP
```

Typical session
```
bmullan-a01:ns1 bmullan$ kubectl exec -it jumpbox  /usr/bin/ssh vmware-system-user@$VMIP
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
The authenticity of host '10.244.0.36 (10.244.0.36)' can't be established.
ECDSA key fingerprint is SHA256:+s387yvhxDQMppQofH/aXrjZypIe/baZnYl2oh8oIMg.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.244.0.36' (ECDSA) to the list of known hosts.
Welcome to Photon 3.0 (\m) - Kernel \r (\l)
 15:23:17 up 2 days, 22:49,  0 users,  load average: 0.23, 0.44, 0.50

32 Security notice(s)
Run 'tdnf updateinfo info' to see the details.
vmware-system-user@ns1-cluster1-workers-9jg77-6b6f9cc77b-2t6wc [ ~ ]$ sudo su
root [ /home/vmware-system-user ]# export TERM=vt100
root [ /home/vmware-system-user ]# ls

root [ /home/vmware-system-user ]# ls /var/log
audit                  cron        rpmcheck        vmware-network.log
auth.log               devicelist  sa              vmware-vgauthsvc.log.0
btmp                   journal     sgidlist        vmware-vmsvc-root.log
cloud-init.log         kubernetes  stigreport.log  vmware-vmtoolsd-root.log
cloud-init-output.log  lastlog     suidlist        wtmp
conntrackd-stats.log   pods        tallylog
containers             private     vmware-imc
