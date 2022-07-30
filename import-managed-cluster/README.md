### HUB Cluster Bastion Machine

```bash
cat > subscription-openshift-gitops.yaml << EOF
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: openshift-gitops-operator
  namespace: openshift-operators
spec:
  channel: stable 
  installPlanApproval: Automatic
  name: openshift-gitops-operator 
  source: redhat-operators 
  sourceNamespace: openshift-marketplace
EOF

oc apply -f subscription-openshift-gitops.yaml

------------------------------------
oc new-project mycluster

oc label namespace mycluster cluster.open-cluster-management.io/managedCluster=mycluster

cat > managedcluster-and-klusterletaddonconfig.yaml << EOF
apiVersion: cluster.open-cluster-management.io/v1
kind: ManagedCluster
metadata:
  name: mycluster
  labels:
    name: mycluster
    cloud: auto-detect
    vendor: auto-detect
    env: dev
    cluster.open-cluster-management.io/clusterset: default
  annotations: {}
spec:
  hubAcceptsClient: true
---
apiVersion: agent.open-cluster-management.io/v1
kind: KlusterletAddonConfig
metadata:
  name: mycluster
  namespace: mycluster
spec:
  clusterName: mycluster
  clusterNamespace: mycluster
  clusterLabels:
    name: mycluster
    cloud: auto-detect
    vendor: auto-detect
    env: dev
    cluster.open-cluster-management.io/clusterset: default
  applicationManager:
    enabled: true
    argocdCluster: false
  policyController:
    enabled: true
  searchCollector:
    enabled: true
  certPolicyController:
    enabled: true
  iamPolicyController:
    enabled: true
EOF

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
