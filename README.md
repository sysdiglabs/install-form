## This form is to install Agent and Cluster Shield in Kubernetes.

**First Version of the Cluster Shield Onboarding Form.**

If Testing internally -
1. Using Static Values -
Modify the static-configs.yaml file to update the region from us3 to us4
If you leave the Kubernetes Namespace to 'kube-system', you will see the agent and clustershield pods in the kube-system namespace. (Recommend changing this to sysdig-agent)
2. Using Complete values.yaml file - change the region to 'us4' from 'us3'

**Post Installation**
Verify that you see the agent and cluster-shield pods

```
    kubectl get pods -A
    kubectl get pods -n kube-system
```


You should see sysdig-agent-xxxxx and sysdig-agent-clustershield-xxxxxxxxx-xxxxx Pods in the respective cluster
If any of the pods are not 1/1 Running state, you can review the logs for that pod by

```
    kubectl logs sysdig-agent-xxxxx -n kube-system | grep error
```
