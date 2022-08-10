### HUB Cluster Bastion Machine

```bash
cd install-openshift-gitops-and-link-acm

oc apply -f subscription-openshift-gitops.yaml

oc apply -f managedclusterset.yaml

for i in $(oc get managedcluster -o name); do \ 
    oc label $i cluster.open-cluster-management.io/clusterset=all-openshift-clusters --overwrite ; done

oc apply -f managedclustersetbinding.yaml

oc apply -f placement.yaml

for i in $(oc get managedcluster -o name); do \
    oc label $i managed-by-argo=yes --overwrite ; done

oc apply -f gitospcluster.yaml

oc adm policy add-cluster-role-to-user cluster-admin system:serviceaccount:openshift-gitops:openshift-gitops-argocd-application-controller
echo https://`oc get route -n openshift-gitops | grep openshift-gitops-server | awk '{print $2}'`

```

> The password is the password for ACM
