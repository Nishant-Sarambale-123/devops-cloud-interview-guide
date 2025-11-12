Excellent ⚡
Here’s the detailed structured answer for **Question 131** — a classic real-world **observability troubleshooting scenario**.

---

## 🚨 **131. Users report slowness in app. Logs don’t show errors, and CPU is good. Fix it.**

### **Short explanation**

This scenario tests your **root-cause analysis skills** using observability tools — how you diagnose performance degradation when traditional indicators (logs, CPU) look normal.

---

### **Answer**

If logs are clean and CPU usage is normal, the issue may be related to **latency, memory, I/O, or network bottlenecks**.
I’d use **metrics (Prometheus)** and **traces (Jaeger)** to pinpoint the bottleneck — e.g., slow DB queries, network delays, or saturation of downstream services.

---

### **Detailed explanation**

#### **Step-by-step troubleshooting:**

1. **Check latency metrics (Prometheus / Grafana)**

   * Query key metrics:

     ```promql
     rate(http_request_duration_seconds_sum[5m]) / rate(http_request_duration_seconds_count[5m])
     ```
   * Look for spikes in **request duration** or **p99 latency**.

   🧠 *If latency is increasing while CPU is fine, the slowness might be due to external dependencies (DB, cache, API calls).*

---

2. **Use Traces (Jaeger or OpenTelemetry)**

   * Trace a slow request end-to-end.
   * Identify which span (microservice or DB query) adds delay.
   * Example: Payment service → 120ms, DB query → 800ms → root cause found.

   **Trace example:**

   ```
   Frontend (20ms)
     ├── Auth Service (30ms)
     └── Payment Service (800ms) ❌
            └── MySQL query (780ms)
   ```

---

3. **Check Memory & Garbage Collection**

   * CPU may look fine, but **high memory usage or GC pauses** can slow the app.
   * Use metrics:

     ```promql
     process_resident_memory_bytes
     jvm_gc_pause_seconds_count
     ```
   * Java example: Too frequent GC → latency spikes.

---

4. **Check Network & I/O bottlenecks**

   * Inspect `node_network_receive_drop_total`, `node_disk_io_time_seconds_total`.
   * Sometimes, a slow NFS or cloud volume (EBS) can delay responses.

---

5. **Correlate with external dependencies**

   * Check Redis, RDS, API endpoints.
   * Example: Increased DB connection time from 10ms → 200ms.

---

6. **Remediation**

   * Fix identified bottleneck (e.g., optimize query, scale out service, increase DB connections).
   * Add **readiness/liveness probes** and **alerts on latency thresholds** to prevent recurrence.

---

### **Summary table**

| Symptom                               | Likely Cause            | Observability Tool | Action                         |
| ------------------------------------- | ----------------------- | ------------------ | ------------------------------ |
| High latency, normal CPU              | DB/API slowness         | Jaeger, Prometheus | Analyze traces, optimize query |
| Increased response time               | GC pauses, memory leak  | Prometheus         | Tune GC, add heap monitoring   |
| Normal metrics, intermittent slowness | Network lag             | node_exporter      | Check packet drops, DNS issues |
| No logs, but user slowness            | Hidden dependency issue | Traces             | Map end-to-end request path    |

---

### **Key takeaway**

👉 **When CPU and logs look fine, traces and latency metrics reveal the real story — always correlate across all three observability pillars.**

---

Would you like me to continue with **Question 132: “How do you trace a request across multiple microservices in a Kubernetes cluster?”** next?
