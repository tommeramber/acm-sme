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


cat > managedclustersetbinding.yaml << EOF
apiVersion: cluster.open-cluster-management.io/v1beta1
kind: ManagedClusterSetBinding
metadata:
  name: all-openshift-clusters
  namespace: openshift-gitops
spec:
  clusterSet: all-openshift-clusters
EOF

oc apply -f managedclustersetbinding.yaml

cat > placement.yaml << EOF
apiVersion: cluster.open-cluster-management.io/v1alpha1
kind: Placement
metadata:
  name: all-openshift-clusters
  namespace: openshift-gitops
spec:
  predicates:
  - requiredClusterSelector:
      labelSelector:
        matchExpressions:
        - key: managed-by-argo
          operator: In
          values:
          - "yes" 
EOF

oc apply -f placement.yaml

for i in $(oc get managedcluster -o name); do \
    oc label $i managed-by-argo=yes --overwrite ; done


cat > gitospcluster.yaml << EOF
apiVersion: apps.open-cluster-management.io/v1beta1
kind: GitOpsCluster
metadata:
  name: argo-acm-importer
  namespace: openshift-gitops
spec:
  argoServer:
    cluster: local-cluster
    argoNamespace: openshift-gitops
  placementRef:
    kind: Placement
    apiVersion: cluster.open-cluster-management.io/v1alpha1
    name: all-openshift-clusters
    namespace: openshift-gitops
EOF

oc apply -f gitospcluster.yaml
```
