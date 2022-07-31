### HUB Cluster Bastion Machine

```bash
oc new-project acm-policies 

oc apply -f applicationset-acm-policies.yaml 
```

#### To install kyverno on all managed clusters, run the following command:
```bash
for i in $(oc get managedcluster -o name); do \ 
    oc label $i install-kyverno=yes --overwrite ; done

```

