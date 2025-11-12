Excellent 👍
Here’s the detailed structured answer for **Question 127**.

---

## 🧭 **127. Have you worked on Observability? If yes, explain what did you do?**

### **Short explanation**

This question evaluates your **hands-on experience** in building or managing an **observability stack** — how you handled logs, metrics, and traces for troubleshooting and performance insights.

---

### **Answer**

Yes. I worked on implementing a **complete observability stack** using **Prometheus, Grafana, Loki, and Jaeger**.
I set up monitoring dashboards, log aggregation, and distributed tracing to identify latency issues across microservices running in Kubernetes.

---

### **Detailed explanation**

#### **🧩 Components I implemented**

1. **Metrics (Prometheus + Grafana)**

   * Deployed Prometheus using Helm in EKS.
   * Configured scrape targets for:

     * Node Exporter (system metrics)
     * Kube-State-Metrics (K8s objects)
     * App metrics from `/metrics` endpoint.
   * Built Grafana dashboards to visualize:

     * Pod CPU/memory trends
     * HTTP request rates
     * Error percentages
     * Application latency histograms

   **Example dashboard metric:**

   ```promql
   rate(http_requests_total{job="app"}[5m])
   ```

2. **Logs (Fluent Bit + Loki)**

   * Configured Fluent Bit DaemonSet to collect container logs from `/var/log/containers`.
   * Streamed logs into **Grafana Loki**.
   * Created log queries to correlate with specific pod failures or 500 errors.

   **Example query:**

   ```
   {app="payment-service"} |= "timeout"
   ```

3. **Traces (Jaeger)**

   * Integrated **OpenTelemetry SDK** in application code.
   * Enabled tracing headers (`trace_id`) to follow a request across multiple services.
   * Used Jaeger to visualize end-to-end latency and pinpoint bottlenecks.

4. **Alerting**

   * Configured **Alertmanager** for CPU/memory thresholds and application errors.
   * Routed alerts to Slack & email with clear severity levels.

---

### **Example Architecture Diagram (conceptually)**

```
Application --> (metrics) --> Prometheus --> Grafana
             --> (logs) ----> Fluent Bit --> Loki --> Grafana
             --> (traces) --> OpenTelemetry --> Jaeger --> Grafana
```

---

### **Summary table**

| Observability Pillar | Tool Used             | Purpose                   | Example                      |
| -------------------- | --------------------- | ------------------------- | ---------------------------- |
| Metrics              | Prometheus, Grafana   | Quantitative performance  | CPU, latency, error rate     |
| Logs                 | Fluent Bit, Loki      | Event details & debugging | Error messages, stack traces |
| Traces               | Jaeger, OpenTelemetry | Distributed request flow  | Trace IDs, service spans     |
| Alerting             | Alertmanager          | Real-time notifications   | High CPU, pod restarts       |

---

### **Key takeaway**

👉 Observability isn’t just about tools — it’s about connecting **metrics, logs, and traces** to quickly find the *root cause* of complex issues.

---

Would you like me to continue with **Question 128: “What is the difference between logs, metrics, and traces?”** next?
