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

cat > managedclusterset.yaml << EOF
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ManagedClusterSet
metadata:
  name: all-openshift-clusters
spec: {}
EOF

oc apply -f managedclusterset.yaml

for i in $(oc get managedcluster -o name); do \ 
    oc label $i cluster.open-cluster-management.io/clusterset=all-openshift-clusters --overwrite ; done

```
