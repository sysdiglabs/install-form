## Java Script Tool to create Agent Helm values.yaml
### This can be used by SaaS and On-Prem

### Product
* Agent and Node Analyzer for Sysdig Platform

### Version
* 0.2

### Notes
* Still in Alpha Creation
* Will be presented to Engineering for integration in the Sysdig UI
* Will be a tool available in the Customer Portal

### Project can be forked from:

[Git repo](https://github.com/dtaracks/sysdig-agent-installation)

### Version Requirements
* kubectl +/-1 version between Client and Server
	Example `kubectl version --short`
	Output:
	```
	Client Version: v1.26.4
	Kustomize Version: v4.5.7
	Server Version: v1.26.5
	```
* helm >=3.10.1
* python3 >= 3.11
* awscli >= 2.11.20 (If running on the AWS Cloud)
* eksctl >=0.141.0 (If running EKS in the AWS Cloud)
* gcloud >=430.0.0 (If running in GCP)

## Verify Installation
* Ensure all sysdig pods are 1/1 Running state.
* Double check the largest image size to make sure a new larger image is not encountered.
* If a new and larger image is encountered, we may need to tune the runtime scanner to accommodate the larger size.
* You can also watch for log messages with "too" that say image scans are being skipped due to size.
* Grep all of the agent pod logs for "Error," and ensure there are no recurring errors of concern.
* Grep all of the agent pod logs for "POLICIES_V2". You should see something like "Received command 22 (POLICIES_V2)" This signifies that the pod is up and running successfully.
* Grep all of the agent node analyzer pod logs for "\"level\":\"error\"" and ensure there are no recurring errors of concern.
* Sometimes you will see recurring errors if a scan is attempted for an application pod that has not come up or is failing.
* Grep all of the agent node analyzer pods logs for "\"message\":\"startup sleep\"". This signifies that the pod is up and running successfully.

## Helm Quickstart

### Initialize Sysdig Helm Chart Repository
```
helm repo add sysdig https://charts.sysdig.com --force-update
```

### Install Sysdig Chart
* [Helm Install Docs](https://helm.sh/docs/helm/helm_install/)
```
helm install sysdig-agent \
--namespace <namespace> \
--set global.sysdig.accessKey=<access-key> \
...
-f example-values-file.yaml \
sysdig/sysdig-deploy
```

### Upgrade Sysdig Deployment or Override Values
* Refer to these docs for additional params: [Helm Upgrade Docs](https://helm.sh/docs/helm/helm_upgrade/)
* helm upgrade --force to override values
```
helm upgrade sysdig-agent \
 --namespace <namespace> \
 --set global.sysdig.accessKey=<access key> \
 --set agent.image.tag=<version> \
 --set nodeAnalyzer.nodeAnalyzer.runtimeScanner.image.tag=<version> \
 ...
 -f example-values-file.yaml \
sysdig/sysdig-deploy
```

```
helm upgrade --force sysdig-agent \
 --namespace <namespace> \
 --set global.sysdig.accessKey=<access key> \
 ...
 -f example-values-file.yaml \
sysdig/sysdig-deploy
```

### List Releases
* [Helm List Docs](https://helm.sh/docs/helm/helm_list/)
```
helm list --namespace <namespace>
```

### View Status
* [Helm Status Docs](https://helm.sh/docs/helm/helm_status/)
```
helm status sysdig-agent --namespace <namespace>
```

### Delete Sysdig Deployment (Uninstall)
* [Helm Uninstall Docs](https://helm.sh/docs/helm/helm_uninstall/)
```
helm uninstall sysdig-agent --namespace <namespace>
```

### Delete Sysdig Deployment (Deprecated)
```
helm delete sysdig-agent --namespace <namespace>
```
