<h2 id="table-of-contents">Table of Contents</h2>
<ul>
<li><a href="#sysdig-installation-pre-reqs">Sysdig Installation Pre-Reqs</a></li>
<li><a href="#sysdig-installation">Sysdig Installation</a></li>
<li><a href="#verify-sysdig-installation">Verify Sysdig Installation</a></li>
</ul>
<h2 id="sysdig-installation-pre-reqs">Sysdig Installation Pre-Reqs</h2>
<ul>
<li>If unfamiliar with helm, please reference the helm quickstart page <a href="sysdig_helm_quickstart.md">here</a>.</li>
<li>Refer to Cluster Shield documentation <a href="https://docs.sysdig.com/en/docs/sysdig-secure/install-agent-components/kubernetes/cluster-shield/#install-cluster-shield">here</a> and cluster-shield helm chart <a href="https://charts.sysdig.com/charts/cluster-shield/">here</a> if you are migrating to Cluster Shield.</li>
<li>Confirm correct kubernetes cluster is targeted<ul>
<li><pre><code>kubectl <span class="hljs-built_in">config</span> <span class="hljs-built_in">get</span>-contexts
</code></pre></li>
</ul>
</li>
<li>Access to kubernetes cluster. Ex: &quot;kubectl get pods -A&quot; returns list of pods running on cluster</li>
<li><p>kubectl version must be +-1 of kubernetes cluster. Ex: K8s cluster v1.25, kubectl(client) version is recommended to be no lower than v1.24</p>
<ul>
<li><pre><code><span class="hljs-selector-tag">kubectl</span> <span class="hljs-selector-tag">version</span> <span class="hljs-selector-tag">--short</span>

<span class="hljs-selector-tag">Client</span> <span class="hljs-selector-tag">Version</span>: <span class="hljs-selector-tag">v1</span><span class="hljs-selector-class">.26</span><span class="hljs-selector-class">.0</span>
<span class="hljs-selector-tag">Kustomize</span> <span class="hljs-selector-tag">Version</span>: <span class="hljs-selector-tag">v4</span><span class="hljs-selector-class">.5</span><span class="hljs-selector-class">.7</span>
<span class="hljs-selector-tag">Server</span> <span class="hljs-selector-tag">Version</span>: <span class="hljs-selector-tag">v1</span><span class="hljs-selector-class">.25</span><span class="hljs-selector-class">.11-eks-a5565ad</span>
</code></pre></li>
</ul>
</li>
<li>Appropriate helm version installed. Compatibility table found <a href="https://helm.sh/docs/topics/version_skew/">here</a>.<ul>
<li><pre><code> helm <span class="hljs-built_in">version</span>
</code></pre></li>
</ul>
</li>
<li><p>Ensure adequate resources on nodes are available:</p>
<ul>
<li>Minimum sysdig resources:<ul>
<li>Limits<ul>
<li>CPU: 1000m</li>
<li>Memory: 4Gi</li>
<li>Ephemeral-storage: 6Gi</li>
</ul>
</li>
<li>Requests<ul>
<li>Ephemeral-storage: 3Gi</li>
</ul>
</li>
</ul>
</li>
<li><pre><code>kubectl describe nodes

Allocated resources:
 Resource                    Requests      Limits
 --------                    --------      ------
 cpu                         <span class="hljs-number">1525</span>m (<span class="hljs-number">38</span>%)   <span class="hljs-number">2150</span>m (<span class="hljs-number">54</span>%)
 <span class="hljs-keyword">memory</span>                      <span class="hljs-number">1756</span>Mi (<span class="hljs-number">11</span>%)  <span class="hljs-number">5440</span>Mi (<span class="hljs-number">36</span>%)
 ephemeral-storage           <span class="hljs-number">3322</span>Mi (<span class="hljs-number">19</span>%)  <span class="hljs-number">6394</span>Mi (<span class="hljs-number">36</span>%)
 hugepages<span class="hljs-number">-1</span>Gi               <span class="hljs-number">0</span> (<span class="hljs-number">0</span>%)        <span class="hljs-number">0</span> (<span class="hljs-number">0</span>%)
 hugepages<span class="hljs-number">-2</span>Mi               <span class="hljs-number">0</span> (<span class="hljs-number">0</span>%)        <span class="hljs-number">0</span> (<span class="hljs-number">0</span>%)
 attachable-volumes-aws-ebs  <span class="hljs-number">0</span>             <span class="hljs-number">0</span>
</code></pre></li>
</ul>
</li>
<li><p>Port 6443 open for outbound traffic The Sysdig Agent communicates with the collector on port 6443. If you’re using a firewall, make sure to open port 6443 for outbound traffic so that the agent can communicate with the collector. This also applies to proxies. Ensure that port 6443 is open on your proxy.</p>
<ul>
<li><p>Validate connection from kubernetes worker node using commands below:</p>
<pre><code>ssh myuser@k8s_worker_node

export http{<span class="hljs-attribute">s,}_proxy=http</span>://myproxy<span class="hljs-variable">.com</span>:8080
curl -sL ingest<span class="hljs-variable">.us</span>3<span class="hljs-variable">.sysdig</span><span class="hljs-variable">.com</span>:6443 -v
</code></pre></li>
</ul>
</li>
<li>If using a proxy, make sure to include clusterIP in the no_proxy list in the onboarding form.<ul>
<li>Command to find clusterIP: <pre><code><span class="hljs-attribute">kubectl get service kubernetes -o jsonpath</span>=<span class="hljs-string">'{.spec.clusterIP}'</span>; <span class="hljs-attribute">e</span><span class="hljs-attribute">c</span><span class="hljs-attribute">h</span><span class="hljs-attribute">o</span>
</code></pre></li>
</ul>
</li>
<li>If using internal registry, make sure to pull images(agent-slim and cluster-shield) from sysdig&#39;s <a href="https://quay.io/organization/sysdig">repository</a> and have those images pushed to your internal registry with same path, name, and tag. (ex: sysdig/agent-slim:13.5.0, sydig/cluster-shield:1.5.0)</li>
<li><a href="https://docs.sysdig.com/en/docs/installation/sysdig-secure/install-agent-components/installation-requirements/sysdig-agent/">Agent Requirement Docs</a></li>
</ul>
<h2 id="sysdig-installation">Sysdig Installation</h2>
<ul>
<li>A self-service form for generating Sysdig installation files, commands and instructions has been created to standardize the process for installing Sysdig at Verizon.</li>
<li>The form should be self-explanatory. Use the tooltips in the form for explanation for each field.</li>
<li>The form is designed so that after all fields are populated correctly, the generate install commands button will provide the installation files and instructions.<ul>
<li>A static-configs.yaml file will be downloaded. You should save this file to the directory you will execute the install from.</li>
<li>IMPORTANT: Please make sure pre-reqs are completed and you&#39;re targeting the correct cluster before executing install commands.</li>
<li>Copy the generated commands and execute them from the directory where the static-configs.yaml is located to install Sysdig.</li>
</ul>
</li>
<li>At this stage, you can also download the form input values and save them for future use.<ul>
<li>This file can be uploaded with the &quot;Choose File&quot; option in the form.</li>
</ul>
</li>
</ul>
<h2 id="verify-sysdig-installation">Verify Sysdig Installation</h2>
<p>Verify that you see the agent and cluster-shield pods</p>
<pre><code>    kubectl <span class="hljs-built_in">get</span> pods -A
    kubectl <span class="hljs-built_in">get</span> pods -n kube-<span class="hljs-keyword">system</span>
</code></pre><ul>
<li>Grep all of the agent pod logs for &quot;POLICIES_V2&quot;. You should see something like &quot;Received command 22 (POLICIES_V2)&quot; This signifies that the pod is up and running successfully.<ul>
<li><pre><code>kubectl -n kube-<span class="hljs-built_in">system</span> logs <span class="hljs-symbol">&lt;agent-pod-name&gt;</span> | <span class="hljs-keyword">grep</span> -i POLICIES_V2
</code></pre>You should see sysdig-agent-xxxxx and sysdig-agent-clustershield-xxxxxxxxx-xxxxx Pods in the respective cluster
If any of the pods are not 1/1 Running state, you can review the logs for that pod by</li>
</ul>
</li>
</ul>
<pre><code>    kubectl logs sysdig-agent-xxxxx -n kube-<span class="hljs-keyword">system</span> | grep -i <span class="hljs-built_in">error</span>
</code></pre>
