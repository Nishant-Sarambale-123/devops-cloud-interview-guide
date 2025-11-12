---

### **Question 131 — Users report slowness in app. Logs don’t show errors, and CPU is good. Fix it.**

---

#### **Short Explanation**

This question tests your **troubleshooting and observability skills** — how you investigate **performance degradation** when usual indicators (logs, CPU) don’t show issues.

---

#### **Answer**

Check **memory, network, and database latency**. Use **metrics and tracing** to isolate the bottleneck. Possible causes: high memory usage, slow DB queries, network latency, or thread exhaustion.

---

#### **Detailed Explanation**

When CPU and logs are fine but users still face **slowness**, the problem is likely hidden in one of the following:

| **Category**          | **Possible Root Cause**                 | **Tools to Check**                                                             |
| --------------------- | --------------------------------------- | ------------------------------------------------------------------------------ |
| **Memory**            | Memory leaks, high GC pauses            | `kubectl top pod`, Prometheus `container_memory_usage_bytes`                   |
| **Network**           | Latency between pods/services           | cAdvisor, `kubectl exec` + `curl`, Istio metrics                               |
| **Database**          | Slow queries, high I/O wait             | DB metrics, APM tools                                                          |
| **Application**       | Thread pool exhaustion, blocking calls  | Traces (Jaeger), OpenTelemetry                                                 |
| **Disk I/O**          | Slow storage or high read/write latency | Node exporter, CloudWatch                                                      |
| **Kubernetes Issues** | Pod throttling, high restart count      | `kubectl describe pod`, Prometheus `container_cpu_cfs_throttled_seconds_total` |

**Step-by-step troubleshooting approach:**

1. **Check pod-level metrics**

   ```bash
   kubectl top pods
   kubectl describe pod <pod-name>
   ```

   Look for throttling, restarts, or OOMKilled events.

2. **Check network latency**

   ```bash
   kubectl exec -it <pod> -- curl -w "%{time_total}\n" -o /dev/null -s http://service:port
   ```

   Compare latency with expected values.

3. **Check database performance**

   * View DB slow query logs.
   * Use `pg_stat_activity` (Postgres) or `performance_schema` (MySQL).

4. **Use tracing**

   * Use **Jaeger** or **Tempo** to trace a request end-to-end.
   * Identify which service or endpoint takes longer.

5. **Check resource limits**

   * If the pod is throttled due to low limits, increase CPU/memory in the YAML spec:

     ```yaml
     resources:
       requests:
         cpu: 200m
         memory: 256Mi
       limits:
         cpu: 1
         memory: 512Mi
     ```

6. **Analyze GC or thread bottlenecks**

   * In Java apps, check GC logs or thread dumps.
   * For Node.js, check event loop delay.

---

#### **Summary Table**

| **Layer** | **Symptom**              | **Tool**                  | **Action**                    |
| --------- | ------------------------ | ------------------------- | ----------------------------- |
| Memory    | OOMKilled, slow response | Prometheus, `kubectl top` | Optimize memory, fix leaks    |
| Network   | High latency             | `curl`, Istio metrics     | Check DNS, load balancer      |
| Database  | Query delays             | APM, DB logs              | Optimize queries, add indexes |
| App       | Threads blocked          | Traces, APM               | Fix async issues              |
| Pod       | Throttled                | `kubectl describe pod`    | Adjust limits                 |

---

#### **Key Takeaway**

> When logs and CPU look normal, **use metrics + traces** to pinpoint slowness — often it’s **memory leaks, network latency, or DB delays**, not errors.

---

Would you like me to continue with **Question 132 — “How do you trace a request across multiple microservices in a Kubernetes cluster?”**
