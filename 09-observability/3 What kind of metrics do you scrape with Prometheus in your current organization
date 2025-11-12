Perfect 👍 — let’s go one by one.
We already covered **124** and **125**, so here’s the next one:

---

## 🧩 **126. What kind of metrics do you scrape with Prometheus in your current organization?**

### **Short explanation**

This question checks if you understand **what types of data Prometheus collects** — both from infrastructure and applications — and how they support monitoring and alerting.

---

### **Answer**

We scrape three main categories of metrics:

1. **Infrastructure metrics** — CPU, memory, disk, network from node exporters.
2. **Kubernetes metrics** — pod status, container restarts, resource limits.
3. **Application metrics** — request rate, latency, errors, custom business KPIs.

---

### **Detailed explanation**

Prometheus works on a **pull model**, scraping metrics from targets exposing `/metrics` endpoints.
Each metric falls into categories that give a full observability picture:

#### **1. Infrastructure metrics**

* Collected via **Node Exporter** or **CloudWatch Exporter**.
* Examples:

  * `node_cpu_seconds_total`
  * `node_memory_Active_bytes`
  * `node_network_transmit_bytes_total`

#### **2. Kubernetes metrics**

* Collected via **kube-state-metrics** and **cAdvisor**.
* Examples:

  * `kube_pod_container_status_restarts_total`
  * `container_cpu_usage_seconds_total`
  * `kube_deployment_status_replicas_available`

#### **3. Application metrics**

* Exposed via Prometheus client libraries.
* Examples:

  * `http_requests_total`
  * `http_request_duration_seconds`
  * `error_rate_total`
  * `custom_business_orders_processed_total`

#### **4. Business metrics (custom)**

* Represent app-level KPIs like:

  * “Payments processed per minute”
  * “Active users per region”

🧠 Example Prometheus target:

```yaml
scrape_configs:
  - job_name: "node"
    static_configs:
      - targets: ["node-exporter:9100"]

  - job_name: "app"
    metrics_path: /metrics
    static_configs:
      - targets: ["app-service:8000"]
```

---

### **Summary table**

| Category       | Source        | Example Metrics                | Exporter/Tool      |
| -------------- | ------------- | ------------------------------ | ------------------ |
| Infrastructure | Node/VM       | CPU, Memory, Disk              | node_exporter      |
| Kubernetes     | Pods/Clusters | Pod restarts, resource usage   | kube-state-metrics |
| Application    | Code level    | Request latency, error rate    | Prometheus client  |
| Business       | App logic     | Orders processed, transactions | Custom metrics     |

---

### **Key takeaway**

👉 Prometheus scrapes metrics at **four layers — infrastructure, cluster, application, and business —** to build complete visibility.

---

Would you like me to continue with **Question 127: “Have you worked on Observability? If yes, explain what did you do?”** next?
