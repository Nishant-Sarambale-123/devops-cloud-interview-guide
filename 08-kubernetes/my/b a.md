Great — I’ll answer several high-impact Kubernetes scenario questions in the **6-step format** you asked for. I’m starting with the first **six** scenarios (they’re common, diagnostic, and teach reusable troubleshooting patterns). If you want more after these, I’ll continue with the rest the same way.

---

# 1) **Pods Restarting Frequently**

### 1. **Question**

Your application pods keep restarting frequently (CrashLoopBackOff or continuous restarts). The developer says “app is fine.” How will you troubleshoot?

### 2. **Short explanation**

This tests your pod lifecycle, logs, probes, resource limits, and container start-up behaviour troubleshooting skills.

### 3. **Answer (brief)**

Check pod status and events, inspect container logs, examine readiness/liveness probes, verify resource limits (OOMKill), check image/command correctness, and reproduce locally if needed.

### 4. **Detailed explanation**

1. **Get basic status & events**

```bash
kubectl get pods -n <ns>
kubectl describe pod <pod-name> -n <ns>
# Look at Events and "State" section (CrashLoopBackOff, OOMKilled, BackOff)
```

2. **Inspect container logs (including previous instance)**

```bash
kubectl logs <pod> -c <container> -n <ns>
kubectl logs --previous <pod> -c <container> -n <ns>
```

Logs often show stack traces, exception messages, or "permission denied". If logs are empty, the process may exit before stdout is flushed (fix by adding logging or using `sleep` in entrypoint to debug).

3. **Check probes**

* Misconfigured **liveness** probe can kill healthy apps.
* **Readiness** probe failing → pod removed from service endpoints (not restart but affects traffic).
  Example probe snippet:

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 10
  periodSeconds: 10
```

Temporarily remove/relax liveness probe to confirm if probes cause restarts.

4. **Resource limits / OOMKilled**

```bash
kubectl describe pod <pod> -n <ns> | grep -A5 "State"
# Or check node/kubelet logs for OOM eviction
```

If `OOMKilled`, increase `resources.limits.memory` or optimize memory usage. Also check `kubectl top pod` (metrics-server) if available.

5. **Image/Command/Entrypoint problems**

* Mistyped command or missing binary → container exits.
* Use `kubectl run -it --rm --image=<image> debug -- /bin/sh` to open a shell in same image and try starting the app manually.

6. **File permissions / missing secrets/config**

* Mounts with wrong permissions (e.g., ConfigMap/Secret mounted with root-only access).
* Check `volumeMounts`, `securityContext`, SELinux/AppArmor.

7. **CrashLoopBackOff backoff debugging**

* Get timestamps of restarts to identify patterns.
* Use `kubectl get events --sort-by='.lastTimestamp'`

8. **If ephemeral startup race conditions**

* Add initContainers to prepare environment or increase `initialDelaySeconds` for probes.

9. **When everything else fails**

* Reproduce container locally (`docker run`) with same env and cmd.
* Add temporary debugging container with same mounts to inspect file system.

### 5. **Summary table (common causes vs quick checks)**

| Symptom                 |                 Likely cause | Quick check                          |
| ----------------------- | ---------------------------: | ------------------------------------ |
| CrashLoopBackOff        |         App crash on startup | `kubectl logs --previous`            |
| OOMKilled               |         Memory limit too low | `kubectl describe pod`               |
| Empty logs              | Process exits before logging | `kubectl run` into image             |
| Repeated probe failures |       Bad liveness/readiness | Check probe path/ports, relax probes |

### 6. **Key takeaway**

Start with `describe` + `logs` + `events`, then iterate through probes, resources, and image/command — don’t assume “app is fine”.

---

# 2) **Node Becomes NotReady**

### 1. **Question**

A node suddenly goes into `NotReady` state in production. What steps will you take to bring the cluster back to a healthy state?

### 2. **Short explanation**

Tests node-level debugging: kubelet, network, kube-proxy, node resources, and cluster resiliency (cordon/drain/eviction).

### 3. **Answer (brief)**

Check node conditions/events, kubelet status, disk/cpu/memory, kube-proxy and CNI, review kubelet logs, cordon and drain problematic node, and either fix and uncordon or replace the node.

### 4. **Detailed explanation**

1. **Check node status**

```bash
kubectl get nodes
kubectl describe node <node-name>
# Look at Conditions: Ready, MemoryPressure, DiskPressure, PIDPressure
```

2. **Check events**

```bash
kubectl get events --field-selector involvedObject.kind=Node,involvedObject.name=<node> --sort-by='.lastTimestamp'
```

3. **SSH to node (if allowed)**

* Check `systemctl status kubelet`, `docker/containerd`:

```bash
sudo systemctl status kubelet
sudo journalctl -u kubelet -l --no-pager
sudo journalctl -u kubelet --since "1 hour ago"
```

* Check disk: `df -h` (DiskPressure common cause), inodes: `df -i`.
* Check memory/cpu: `top`, `free -m`.

4. **Check kube-proxy & CNI**

* If kubelet is running but networking is broken, pods may lose connectivity.
* On node, check CNI plugin logs (calico, flannel, etc.) and containers.

5. **If node is unhealthy, cordon & drain**

```bash
kubectl cordon <node>
kubectl drain <node> --ignore-daemonsets --delete-emptydir-data --timeout=10m
```

This moves workloads to healthy nodes. If drain fails due to PDBs, handle accordingly.

6. **Fix or replace**

* If transient (network flapping), restarting kubelet or network service may help:

```bash
sudo systemctl restart kubelet
sudo systemctl restart docker     # or containerd
```

* If disk is full, free space (logrotate, remove docker images). If irrecoverable, replace the node (terminate & let autoscaler recreate or recreate VM).

7. **Post-recovery**
   Uncordon: `kubectl uncordon <node>`. Monitor pods and node conditions.

### 5. **Summary table**

| Check           | Command                 |
| --------------- | ----------------------- |
| Node conditions | `kubectl describe node` |
| Kubelet logs    | `journalctl -u kubelet` |
| Disk inodes     | `df -i`                 |
| Cordon & drain  | `kubectl cordon/drain`  |

### 6. **Key takeaway**

Quickly cordon/drain to protect workloads; diagnose kubelet/CNI/disk on the node and either fix or replace the node.

---

# 3) **High Latency Between Microservices**

### 1. **Question**

Two microservices in the same cluster show high latency when communicating. How will you debug network issues?

### 2. **Short explanation**

This checks networking, service discovery, DNS, CNI, network policies, and application-level problems like retries/timeouts.

### 3. **Answer (brief)**

Measure latency with curl/ping/traceroute inside pods, check DNS resolution, inspect NetworkPolicy, examine service type and endpoints, review kube-proxy/CNI metrics, and verify application timeouts/retries.

### 4. **Detailed explanation**

1. **Measure raw network from pod to pod**

```bash
kubectl exec -it <pod-a> -- /bin/sh
# From inside:
ping <pod-b-ip>
curl -v http://<service-name>.<ns>.svc.cluster.local:port/health
traceroute <pod-b-ip>
```

2. **Check DNS**

```bash
kubectl exec -it <pod-a> -- nslookup <service-name>.<ns>.svc.cluster.local
kubectl exec -it <pod-a> -- dig +short <service>
```

DNS latency could come from CoreDNS issues. Check CoreDNS pods: `kubectl get pods -n kube-system -l k8s-app=kube-dns` and logs `kubectl logs -n kube-system <coredns-pod>`.

3. **Check service endpoints & iptables/ipvs**

```bash
kubectl get endpoints <svc> -n <ns>
kubectl get endpointslices -n <ns>
# On a node, check iptables/ipvs entries (if ipvs mode)
sudo iptables -t nat -L -n -v
sudo ipvsadm -L -n
```

If traffic goes through node-proxy, misconfig or huge iptables rulesets can slow packets. Large clusters may require IPVS for performance.

4. **NetworkPolicy**

* A restrictive NetworkPolicy may cause unexpected routing via proxy or cause extra hops. Check applied policies in both namespaces.

5. **CNI / MTU / Network fabric**

* MTU mismatch leads to fragmentation and high latency. Check CNI plugin docs and node MTU settings.
* Check CNI pod logs (calico, weave): `kubectl logs -n kube-system <calico-pod>`

6. **Application-level causes**

* Long GC pauses, database calls, or synchronous I/O add latency. Profile app (traces), check logs for slow requests.
* Verify client timeouts and retry loops creating thundering herd.

7. **Use tools**

* `tcpdump` on nodes, use eBPF/tracing tools (if available): `kubectl exec` into node or privileged pod to capture packets.
* Distributed tracing (Jaeger, Zipkin, OpenTelemetry) helps pinpoint service-side slowness.

### 5. **Summary table**

| Cause               | Check                       |
| ------------------- | --------------------------- |
| DNS slow            | CoreDNS pods & logs         |
| kube-proxy overhead | iptables/ipvs rules         |
| CNI/mTU issues      | CNI logs, MTU check         |
| App-level           | App traces, GC logs         |
| NetworkPolicy       | `kubectl get networkpolicy` |

### 6. **Key takeaway**

Differentiate network-layer latency from application-layer latency: measure from inside pods, then move outward (DNS → service → CNI → node network → app).

---

# 4) **ConfigMap Update Not Reflecting in Pod**

### 1. **Question**

You updated a ConfigMap but pods are still reading old config. What will you do to apply ConfigMap changes with zero downtime?

### 2. **Short explanation**

Tests understanding of how ConfigMaps are mounted and when updates propagate — mounted files are updated but env vars are not; rolling restart strategies are needed for env-based config.

### 3. **Answer (brief)**

If mounted as files, changes appear automatically (depends on kubelet sync). If envFrom/env vars were used, pods must be restarted (rolling restart). Use rolling update (kubectl rollout restart) or trigger rolling by changing pod template hash (annotations) to achieve zero-downtime.

### 4. **Detailed explanation**

1. **Understand mount method**

* **Volume mount**: ConfigMap mounted as files under `/etc/config` — kubelet periodically (or on update) updates files; app must re-read file. No pod restart required.
* **Environment variables**: ConfigMap used as env vars in pod spec → values are set at container creation and don't update. Pod restart required.

2. **Check current usage**

```bash
kubectl get deploy -n <ns> -o yaml | yq '.spec.template.spec.containers[].env'
# Or check volume mounts
```

3. **Apply change for file mounts**

* Confirm mounted file changed:

```bash
kubectl exec -it <pod> -- cat /path/to/config
```

* If change not there, check kubelet config or volume plugin.

4. **Rolling restart for env-based**

```bash
kubectl rollout restart deployment/<deployment-name> -n <ns>
# Or patch annotation:
kubectl patch deployment <d> -p '{"spec":{"template":{"metadata":{"annotations":{"configmap-reload":"<timestamp>"}}}}}'
```

A `rollout restart` triggers a controlled pod replacement honoring readiness probes (zero downtime if capacity allows).

5. **Alternative: use a sidecar to reload**

* Use a small sidecar process that watches configmap volume and sends SIGHUP or triggers the app reload (e.g., `reloader`, `consul-template`, `configmap-reload`).

6. **Best practice**

* For dynamic config, prefer file-based mounts + application support to re-read config or use a service discovery/config system (e.g., HashiCorp Consul, Spring Cloud Config).

### 5. **Summary table**

| Mount method | Propagation                   | Action to update                          |
| ------------ | ----------------------------- | ----------------------------------------- |
| File mount   | kubelet updates automatically | Ensure app reloads or use sidecar         |
| Env vars     | Static at container start     | Rolling restart (kubectl rollout restart) |

### 6. **Key takeaway**

Know how the config is injected: file mounts can change live (if app re-reads), while env vars require pod restart — use rolling restarts or reload sidecars for zero downtime.

---

# 5) **Horizontal Pod Autoscaling (HPA) Not Working**

### 1. **Question**

HPA is configured, but replicas are not scaling even when CPU is high. How do you troubleshoot HPA?

### 2. **Short explanation**

Tests HPA knowledge: metrics pipeline (metrics-server), HPA configuration (target metrics), resource requests, and API compatibility (v2/v2beta, custom metrics).

### 3. **Answer (brief)**

Ensure metrics-server is installed and healthy, check HPA status and events, verify pods have `resources.requests` (HPA uses requests), confirm HPA target metric and resource type are correct, and validate cluster-autoscaler/node capacity if nodes cannot be created.

### 4. **Detailed explanation**

1. **Check HPA object**

```bash
kubectl get hpa -n <ns>
kubectl describe hpa <hpa-name> -n <ns>
```

Look at `Events` and `Current/Target` metrics shown in the describe output.

2. **Verify metrics-server**

```bash
kubectl get deployment metrics-server -n kube-system
kubectl logs -n kube-system deployment/metrics-server
kubectl top pods -n <ns>   # ensures metrics API returns data
```

If `kubectl top` fails, HPA won’t get metrics.

3. **Ensure pods have resource requests**
   HPA compares current CPU usage to CPU **requests**. Without `resources.requests`, HPA cannot calculate percentage.

```yaml
resources:
  requests:
    cpu: "200m"
  limits:
    cpu: "500m"
```

4. **Check HPA API version & metric type**

* v1 HPA: only CPU (and memory on some k8s versions).
* v2/v2beta2: supports custom metrics and external metrics.
  If using custom metrics, confirm custom-metrics API is installed and returning values.

5. **Check min/max & target**
   If target is already satisfied (e.g., low target or min replicas equals current replicas), no change.

6. **If HPA scales pods but nodes cannot be provisioned**

* Ensure cluster-autoscaler is installed/working or there’s free capacity. Check events for scheduling failures due to insufficient resources.

7. **Debug commands**

```bash
kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1" | jq
kubectl describe hpa <hpa> -n <ns>
```

### 5. **Summary table**

| Problem        | Check                                           |
| -------------- | ----------------------------------------------- |
| No metrics     | metrics-server health, `kubectl top`            |
| No scaling     | `kubectl describe hpa` events, requests set?    |
| Custom metrics | custom-metrics API deployed                     |
| Node capacity  | cluster-autoscaler & node resource availability |

### 6. **Key takeaway**

HPA relies on a healthy metrics pipeline and properly set `resources.requests`; verify metrics-server, HPA config, and node capacity before assuming HPA is broken.

---

# 6) **Service Type LoadBalancer Always Stuck in Pending**

### 1. **Question**

You created a Service with `type: LoadBalancer`, but the external IP stays **Pending**. How will you identify the root cause?

### 2. **Short explanation**

Checks knowledge of cloud provider integration (external load-balancer controllers), MetalLB for bare-metal, and Service-to-provider mapping.

### 3. **Answer (brief)**

Check cloud provider support and controller (kube-controller-manager cloud-provider or cloud LB controller), ensure cloud credentials/permissions exist, confirm annotations for the provider, and on bare-metal install MetalLB. Inspect service events for errors.

### 4. **Detailed explanation**

1. **Check service & events**

```bash
kubectl describe svc <svc> -n <ns>
# Look for events indicating why external IP wasn't provisioned
```

2. **Cloud-provider clusters**

* On managed k8s (EKS/GKE/AKS), `type: LoadBalancer` should provision cloud LB automatically.
* If no LB appears, check:

  * Cloud controller manager logs.
  * IAM/ServiceAccount permissions (e.g., AWS IAM roles for service accounts, GCP service account permissions).
  * Required annotations (e.g., for AWS NLB/ALB/annotations for scheme).

3. **On-prem / bare-metal**

* Kubernetes doesn’t create an external LB by default. Install MetalLB and configure address pool:
  Example MetalLB config:

```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: my-pool
  namespace: metallb-system
spec:
  addresses:
  - 203.0.113.240-203.0.113.250
```

Then the LoadBalancer service will get an IP from that pool.

4. **Check controller logs**

* For cloud: check cloud-controller-manager or cloud provider operator logs in `kube-system`.
* For MetalLB: `kubectl logs -n metallb-system <controller-pod>`

5. **Other causes**

* Service annotation requiring internal LB, specific subnet selection or availability zones misconfigured.
* Lack of free IP addresses in provider quota.

6. **Example: AWS**

* Ensure cluster is in subnets tagged for ELB (for classic) or target group. EKS with `service.beta.kubernetes.io/aws-load-balancer-type` set to `nlb` if needed.

7. **Temporary workaround**

* Use `NodePort` + external LB or Ingress controller (with cloud LB) if service must be exposed immediately.

### 5. **Summary table**

| Environment                 |     Expected controller | Quick fix                            |
| --------------------------- | ----------------------: | ------------------------------------ |
| Managed cloud (EKS/GKE/AKS) |       Cloud provider LB | Check cloud-controller-manager & IAM |
| Bare-metal                  |                 MetalLB | Install/configure MetalLB IP pool    |
| Custom                      | External LB via Ingress | Use Ingress + controller annotations |

### 6. **Key takeaway**

`type: LoadBalancer` needs an external LB controller (cloud provider or MetalLB). Check `kubectl describe svc` events and the relevant controller permissions/logs.

---

If you want, I’ll continue with the next set of scenarios (7–12) in the same 6-step format. Which do you want next — I can continue in order or jump to any particular scenario you care about.
