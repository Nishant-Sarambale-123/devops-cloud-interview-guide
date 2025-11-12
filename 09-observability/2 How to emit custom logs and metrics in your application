Excellent 👍
Let’s continue with **Question 125**.

---

### **125. How to emit custom logs and metrics in your application?**

#### **Short explanation**

This question checks your understanding of how applications expose **custom observability data** — logs for debugging and metrics for monitoring performance.

---

#### **Answer**

You can emit **custom logs** using application logging libraries (like `log4j`, `winston`, or `logging` module) and **custom metrics** using libraries that expose Prometheus endpoints (like `prometheus_client` in Python or `micrometer` in Java).
Logs go to centralized systems like ELK or Loki, while metrics are scraped by Prometheus.

---

#### **Detailed explanation**

**💬 Logs**

* Logs record *event-based* data — e.g., errors, warnings, transactions.
* Usually written to stdout/stderr in containerized apps.
* In Kubernetes, logs are automatically collected by logging agents (like Fluentd, Fluent Bit, or Loki).

**🧾 Example — Python custom log**

```python
import logging

logging.basicConfig(level=logging.INFO)
logging.info("User login successful")
logging.error("Payment gateway timeout")
```

**🚀 Custom Metrics**

* Metrics are *numerical measurements* (e.g., request count, latency, errors).
* You can expose custom metrics via `/metrics` HTTP endpoint for Prometheus.

**🧮 Example — Python with Prometheus client**

```python
from prometheus_client import Counter, Gauge, start_http_server
import time

REQUEST_COUNT = Counter('app_requests_total', 'Total number of requests')
CPU_LOAD = Gauge('app_cpu_usage', 'CPU usage percentage')

start_http_server(8000)

while True:
    REQUEST_COUNT.inc()
    CPU_LOAD.set(70)
    time.sleep(5)
```

Prometheus scrapes these metrics from `http://app:8000/metrics`.

**📦 Real-world flow**

1. Application emits logs → Fluent Bit → Loki or ELK.
2. Application exposes metrics endpoint → Prometheus scrapes → Grafana visualizes.
3. Alerts trigger based on thresholds (e.g., latency > 500ms).

---

#### **Summary table**

| Type    | Purpose                  | How to Emit            | Tool Example           | Storage/Visualization |
| ------- | ------------------------ | ---------------------- | ---------------------- | --------------------- |
| Logs    | Record events & errors   | Logging libraries      | Fluent Bit, ELK, Loki  | Kibana, Grafana Loki  |
| Metrics | Numeric performance data | Prometheus client libs | Prometheus, Micrometer | Grafana dashboards    |

---

#### **Key takeaway**

👉 **Emit logs for detailed events and metrics for quantifiable performance — both are essential pillars of observability.**

---

Shall I continue with **Question 126: “What kind of metrics do you scrape with Prometheus in your current organization?”** next?
