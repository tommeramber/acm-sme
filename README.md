### Prerequisits

```bash
wget https://get.helm.sh/helm-v3.9.1-linux-amd64.tar.gz
tar xzvf helm-v3.9.1-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/local/bin/helm
rm -rf linux-amd64 helm-v3.9.1-linux-amd64.tar.gz
helm repo add kyverno https://kyverno.github.io/kyverno/
helm pull kyverno/kyverno
tar xvzf kyverno-v2.5.1.tgz 
rm -rf kyverno-v2.5.1.tgz 
sed -i 's,tag:,tag: v1.7.2-rc1,g' kyverno/values.yaml 
```

#### Run
1. [import-managed-cluster](./import-managed-cluster)
2. [install-openshift-gitops-and-link-acm](./install-openshift-gitops-and-link-acm)
```bash
oc new-project acm-policies
```
3. [everything-in-argo](./everything-in-argo) 

