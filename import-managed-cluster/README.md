### HUB Cluster Bastion Machine

```cd``` to this directory 

```bash

oc apply -f subscription-openshift-gitops.yaml

------------------------------------
oc new-project mycluster

oc apply -f managedcluster-and-klusterletaddonconfig.yaml -n mycluster

# sed -i 's,mycluster,<NAME>,g' managedcluster-and-klusterletaddonconfig.yaml

mkdir import-mycluster

oc get secret mycluster-import \
  -n mycluster -o jsonpath={.data.crds\\.yaml} | base64 \
  --decode > import-mycluster/klusterlet-crd.yaml
  
oc get secret mycluster-import \
  -n mycluster -o jsonpath={.data.import\\.yaml} | base64 \
  --decode > import-mycluster/import.yaml
  
rsync -av import-mycluster <USER>@<Bastion-managed-cluster-url>:/tmp

rm -rf import-mycluster

```

### Managed Cluster Bastion Machine
```bash
cd /tmp/import-mycluster
oc apply -f klusterlet-crd.yaml
oc apply -f import.yaml
```

### HUB Cluster Bastion Machine
```bash
oc get managedcluster
```

Desired Output:
```bash
[lab-user@bastion ~]$ oc get managedcluster
NAME            HUB ACCEPTED   MANAGED CLUSTER URLS       JOINED   AVAILABLE   AGE
local-cluster   true           https://api.xxx.com:6443   True     True        22h
mycluster       true           https://api.yyy.com:6443   True     True        7m5s
```
