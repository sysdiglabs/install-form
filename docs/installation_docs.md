## Table of Contents
* [Sysdig Installation Pre-Reqs](#sysdig-installation-pre-reqs)
* [Sysdig Installation](#sysdig-installation)
* [Verify Sysdig Installation](#verify-sysdig-installation)


## Sysdig Installation Pre-Reqs

* If unfamiliar with helm, please reference the helm quickstart page [here](sysdig_helm_quickstart.md).
* Refer to Cluster Shield documentation [here](https://docs.sysdig.com/en/docs/sysdig-secure/install-agent-components/kubernetes/cluster-shield/#install-cluster-shield) and cluster-shield helm chart [here](https://charts.sysdig.com/charts/cluster-shield/) if you are migrating to Cluster Shield.
* Confirm correct kubernetes cluster is targeted
  * ```
    kubectl config get-contexts
    ```
* Access to kubernetes cluster. Ex: "kubectl get pods -A" returns list of pods running on cluster
* kubectl version must be +-1 of kubernetes cluster. Ex: K8s cluster v1.25, kubectl(client) version is recommended to be no lower than v1.24
  * ```
    kubectl version --short
    
    Client Version: v1.26.0
    Kustomize Version: v4.5.7
    Server Version: v1.25.11-eks-a5565ad
    ```
* Appropriate helm version installed. Compatibility table found [here](https://helm.sh/docs/topics/version_skew/).
  * ```
     helm version
    ```
* Ensure adequate resources on nodes are available:
  * Minimum sysdig resources:
    * Limits
      * CPU: 1000m
      * Memory: 4Gi
      * Ephemeral-storage: 6Gi
    * Requests
      * Ephemeral-storage: 3Gi
  * ```
    kubectl describe nodes

    Allocated resources:
     Resource                    Requests      Limits
     --------                    --------      ------
     cpu                         1525m (38%)   2150m (54%)
     memory                      1756Mi (11%)  5440Mi (36%)
     ephemeral-storage           3322Mi (19%)  6394Mi (36%)
     hugepages-1Gi               0 (0%)        0 (0%)
     hugepages-2Mi               0 (0%)        0 (0%)
     attachable-volumes-aws-ebs  0             0
    ```
* Port 6443 open for outbound traffic The Sysdig Agent communicates with the collector on port 6443. If you’re using a firewall, make sure to open port 6443 for outbound traffic so that the agent can communicate with the collector. This also applies to proxies. Ensure that port 6443 is open on your proxy.
  * Validate connection from kubernetes worker node using commands below:
    ```
    ssh myuser@k8s_worker_node
    
    export http{s,}_proxy=http://myproxy.com:8080
    curl -sL ingest.us3.sysdig.com:6443 -v
    ```
* If using a proxy, make sure to include clusterIP in the no_proxy list in the onboarding form.
  * Command to find clusterIP: 
    ```
    kubectl get service kubernetes -o jsonpath='{.spec.clusterIP}'; echo
    ```
* If using internal registry, make sure to pull images(agent-slim and cluster-shield) from sysdig's [repository](https://quay.io/organization/sysdig) and have those images pushed to your internal registry with same path, name, and tag. (ex: sysdig/agent-slim:13.5.0, sydig/cluster-shield:1.5.0)
* [Agent Requirement Docs](https://docs.sysdig.com/en/docs/installation/sysdig-secure/install-agent-components/installation-requirements/sysdig-agent/)

## Sysdig Installation

* A self-service form for generating Sysdig installation files, commands and instructions has been created to standardize the process for installing Sysdig at Verizon.
* The form should be self-explanatory. Use the tooltips in the form for explanation for each field.
* The form is designed so that after all fields are populated correctly, the generate install commands button will provide the installation files and instructions.
  * A static-configs.yaml file will be downloaded. You should save this file to the directory you will execute the install from.
  * IMPORTANT: Please make sure pre-reqs are completed and you're targeting the correct cluster before executing install commands.
  * Copy the generated commands and execute them from the directory where the static-configs.yaml is located to install Sysdig.
* At this stage, you can also download the form input values and save them for future use.
  * This file can be uploaded with the "Choose File" option in the form.

## Verify Sysdig Installation
Verify that you see the agent and cluster-shield pods

```
    kubectl get pods -A
    kubectl get pods -n kube-system
```
* Grep all of the agent pod logs for "POLICIES_V2". You should see something like "Received command 22 (POLICIES_V2)" This signifies that the pod is up and running successfully.
  * ```
    kubectl -n kube-system logs <agent-pod-name> | grep -i POLICIES_V2
    ``` 
You should see sysdig-agent-xxxxx and sysdig-agent-clustershield-xxxxxxxxx-xxxxx Pods in the respective cluster
If any of the pods are not 1/1 Running state, you can review the logs for that pod by

```
    kubectl logs sysdig-agent-xxxxx -n kube-system | grep -i error
```


