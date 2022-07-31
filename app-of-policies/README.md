
## HUB Cluster Bastion Machine

```bash
oc new-project acm-policies 

oc apply -f applicationset-acm-policies.yaml 
```

#### To install kyverno on all managed clusters, run the following command:
```bash
for i in $(oc get managedcluster -o name); do \ 
    oc label $i install-kyverno=yes --overwrite ; done

```
#### To Install compliance Operator upstream on all managed clusters, run the following command:
```bash 
for i in $(oc get managedcluster -o name); do \
    oc label $i install-compliance-operator-upstream=yes --overwrite ; done
```



### Add one/some of the following labels to enforce policies on your cluster/s

| Policy/Best Practice                                                                          | Label Key            | Label Value |
| :------------                                                                                 |:--------------------:| :----:|  
| [Encrypt Etcd](policies/best-practices-enforced-by-acm/etcd-encryption)                       | etcd-encryption      | "yes" |
| [Always Pull Images](policies/best-practices-enforced-by-kyverno/always-pull-images)          | always-pull-images   | "yes" |

# Credits to the stolostron project that provided many policies [Link](https://github.com/stolostron/policy-collection)
