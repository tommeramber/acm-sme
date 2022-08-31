# Intro
This part of the PoC is based on the work I did in the following project: [GitOps-For-Multi-Openshift-Cluster-Management
](https://github.com/tommeramber/GitOps-For-Multi-Openshift-Cluster-Management)

To put it simply, we are creating a single [applicationsets](https://argo-cd.readthedocs.io/en/stable/user-guide/application-set/) object, that once created, will go over all the directories in this project, and will create ArgoCD-Applications for each subdir; 

Each subdir holds an Helm Chart that once deployed by ArgoCD will create an ACM policy; 

Each such ACM policy will make ACM to enforce a certin object to be created/configured on the remote cluster.

In such way we can use ArgoCD to hold all the ACM Policies that will configure the remote clusters for us in GitOps manner.

Each change we'll want to make on a remote cluster will be possiable only by changing the relevant ACM policy file in this Git repo (git commit + push); This operation will update the ArgoCD Application => that will update the ACM policy object on ACM => that will enforce the object's new configuration on the remote cluster, or on all the remote clusters if this policy is relevant for more than on cluster.


Each Helm Chart (that hold ACM policies) is designed in such a way that we can decide, by giving a cluster a unique label associated with the Helm Chart, which clusters should be affected by it. 
