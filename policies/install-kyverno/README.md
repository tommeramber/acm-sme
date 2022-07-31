### HUB Cluster Bastion Machine

```bash
for i in $(oc get managedcluster -o name); do \ 
    oc label $i install-kyverno=yes --overwrite ; done

oc apply -f 
```

