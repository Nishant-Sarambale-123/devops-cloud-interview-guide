---

### **Question 130 — Which tools have you used to build observability stack?**

---

#### **Short Explanation**

This question checks your hands-on experience with tools used for **monitoring, logging, and tracing** — the three pillars of observability — and how you integrated them.

---

#### **Answer**

A common observability stack includes:

* **Metrics:** Prometheus + Grafana
* **Logs:** EFK/ELK Stack (Elasticsearch, Fluentd/Fluent Bit, Kibana)
* **Traces:** Jaeger or OpenTelemetry
* **Alerting:** Alertmanager
* **Visualization:** Grafana dashboards

---

#### **Detailed Explanation**

An **observability stack** helps engineers **monitor system health**, **debug performance issues**, and **trace user requests** end-to-end.

Here’s a typical real-world setup:

| **Component**       | **Tool**                            | **Purpose**                                                         |
| ------------------- | ----------------------------------- | ------------------------------------------------------------------- |
| **Metrics**         | Prometheus                          | Scrapes metrics from Kubernetes components, applications, and nodes |
| **Dashboards**      | Grafana                             | Visualizes data from Prometheus and Loki                            |
| **Logs**            | Fluent Bit → Elasticsearch → Kibana | Collects, stores, and visualizes application and cluster logs       |
| **Tracing**         | Jaeger / Tempo (Grafana)            | Traces requests across microservices                                |
| **Alerting**        | Alertmanager                        | Sends alerts via Slack, email, PagerDuty                            |
| **Instrumentation** | OpenTelemetry SDK                   | Exports custom metrics, logs, and traces from application code      |

**Example flow:**

1. Applications expose `/metrics` endpoint → Prometheus scrapes it.
2. Fluent Bit collects logs → pushes to Elasticsearch.
3. Jaeger/OpenTelemetry collects traces → sends to Jaeger backend.
4. Grafana connects to Prometheus, Elasticsearch, Jaeger → builds unified dashboards.
5. Alertmanager fires alerts when metrics cross thresholds.

**Example stack diagram (simplified):**

```
App → OpenTelemetry SDK → 
   ├── Prometheus → Grafana
   ├── Fluent Bit → Elasticsearch → Kibana
   └── Jaeger → Grafana
```

---

#### **Summary Table**

| **Observability Pillar** | **Tool Example**      | **Purpose**                         |
| ------------------------ | --------------------- | ----------------------------------- |
| Metrics                  | Prometheus, Grafana   | Quantitative performance monitoring |
| Logs                     | EFK/ELK               | Error and event tracking            |
| Traces                   | Jaeger, OpenTelemetry | Request path tracing                |
| Alerts                   | Alertmanager          | Notifications on thresholds         |
| Visualization            | Grafana               | Unified dashboards                  |

---

#### **Key Takeaway**

> A complete observability stack integrates **metrics, logs, and traces** using tools like **Prometheus, Grafana, EFK, and Jaeger**, giving 360° visibility into application health and performance.

---

Would you like me to continue with **Question 131 — “Users report slowness in app. Logs don’t show errors, and CPU is good. Fix it.”**?
