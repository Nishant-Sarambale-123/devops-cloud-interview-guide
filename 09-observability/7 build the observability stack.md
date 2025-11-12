Perfect 👌
Here’s the next one — **Question 130**.

---

## 🧰 **130. Which tools have you used to build the observability stack?**

### **Short explanation**

This question checks your **hands-on experience** with the tools and technologies used for collecting, storing, and visualizing **metrics, logs, and traces** in a modern observability setup.

---

### **Answer**

I have built a complete observability stack using:

* **Prometheus + Grafana** for metrics and dashboards,
* **Loki + Fluent Bit** for log aggregation,
* **Jaeger + OpenTelemetry** for distributed tracing, and
* **Alertmanager** for alert routing.

---

### **Detailed explanation**

#### **1. Metrics Layer**

* **Prometheus** to scrape application, Kubernetes, and node metrics.
* **Grafana** for visualization and alerting dashboards.
* Added exporters:

  * `node_exporter` → VM metrics
  * `kube-state-metrics` → pod/cluster data
  * `blackbox_exporter` → external probes
* Configured alerts:

  ```yaml
  groups:
  - name: instance-down
    rules:
    - alert: InstanceDown
      expr: up == 0
      for: 1m
      labels:
        severity: critical
      annotations:
        description: "Instance {{ $labels.instance }} is down"
  ```

#### **2. Logging Layer**

* **Fluent Bit** as a DaemonSet to collect container logs from `/var/log/containers`.
* Logs sent to **Loki** (lightweight log aggregation).
* Queried in **Grafana** using LogQL.
* Example LogQL query:

  ```
  {app="order-service"} |= "error" | json | line_format "{{.msg}}"
  ```

#### **3. Tracing Layer**

* **Jaeger** integrated via **OpenTelemetry SDK**.
* Enabled distributed tracing headers (`trace_id`) in services.
* Visualized call latency across microservices (frontend → backend → DB).

#### **4. Alerting & Notifications**

* **Alertmanager** for Prometheus alerts.
* Configured routing:

  * Critical → Slack channel
  * Warning → Email
* Reduced false alarms with alert deduplication and grouping.

#### **5. Visualization**

* Unified dashboards in **Grafana** combining metrics, logs, and traces in one view.
* Example: Clicking on a latency graph auto-opens related logs/traces in Grafana.

---

### **Summary table**

| Observability Pillar | Tool Used             | Purpose                    |
| -------------------- | --------------------- | -------------------------- |
| Metrics              | Prometheus, Grafana   | System and app metrics     |
| Logs                 | Fluent Bit, Loki      | Log aggregation & analysis |
| Traces               | Jaeger, OpenTelemetry | Request-level tracing      |
| Alerting             | Alertmanager          | Intelligent notifications  |
| Visualization        | Grafana               | Unified observability view |

---

### **Key takeaway**

👉 A strong observability stack = **Prometheus (metrics) + Loki (logs) + Jaeger (traces) + Grafana (visualization)** — the modern “LGTM” stack.

---

Would you like me to continue with **Question 131: “Users report slowness in app. Logs don’t show errors, and CPU is good. Fix it.”** next?
