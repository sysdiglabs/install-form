## This form is to install Agent and Cluster Shield in Kubernetes.

**First Version of the Cluster Shield Onboarding Form.**
If Testing internally -
1. Using Static Values -
Modify the static-configs.yaml file to update the region from us3 to us4
If you leave the Kubernetes Namespace to 'kube-system', you will see the agent and clustershield pods in the kube-system namespace. (Recommend changing this to sysdig-agent)
2. Using Complete values.yaml file - change the region to 'us4' from 'us3'

