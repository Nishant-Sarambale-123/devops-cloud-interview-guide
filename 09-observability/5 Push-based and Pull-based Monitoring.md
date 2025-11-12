Awesome 👍
Here’s the next one — **Question 129**.

---

## ⚙️ **129. What is the difference between Push-based and Pull-based Monitoring?**

### **Short explanation**

This question tests your understanding of **how monitoring systems collect data** — whether the data source *pushes* metrics or the monitoring system *pulls* them.

---

### **Answer**

* **Push-based monitoring:** Applications *send (push)* metrics to the monitoring server.
* **Pull-based monitoring:** The monitoring server *scrapes (pulls)* metrics from targets at intervals.
  Prometheus primarily uses a **pull model**, while tools like **StatsD** or **Graphite** use a **push model**.

---

### **Detailed explanation**

#### **1. Pull-based monitoring**

* The **monitoring server** (e.g., Prometheus) fetches metrics from endpoints like `/metrics`.
* Each application exposes metrics via HTTP.
* Easier for **service discovery** (especially in Kubernetes).
* Example configuration:

  ```yaml
  scrape_configs:
    - job_name: "app"
      static_configs:
        - targets: ["app-service:8000"]
  ```
* **Advantages:**

  * No client-side configuration for destinations.
  * Easier to manage in dynamic environments (K8s, microservices).
* **Disadvantages:**

  * May not work for short-lived jobs unless Pushgateway is used.

---

#### **2. Push-based monitoring**

* Applications or agents **push data** directly to a central system (e.g., Graphite, CloudWatch, Datadog).
* Example: Prometheus **Pushgateway** or StatsD clients.
* **Advantages:**

  * Good for short-lived or batch jobs.
  * Works well when pull access isn’t possible (e.g., firewalls).
* **Disadvantages:**

  * Harder to manage source discovery.
  * Risk of data overload or incorrect timestamps.

---

#### **Real-world example**

* **Prometheus (Pull):** Scrapes `/metrics` from Kubernetes pods automatically via service discovery.
* **CloudWatch (Push):** Apps push custom metrics via API using `PutMetricData`.

---

### **Summary table**

| Feature       | Pull-based Monitoring        | Push-based Monitoring  |
| ------------- | ---------------------------- | ---------------------- |
| Data flow     | Server pulls from targets    | Clients push to server |
| Example tools | Prometheus                   | Graphite, CloudWatch   |
| Use case      | Long-running services        | Short-lived jobs       |
| Configuration | Managed by server            | Managed by client      |
| Pros          | Auto-discovery, easy scaling | Works behind firewalls |
| Cons          | Misses short jobs            | Higher client overhead |

---

### **Key takeaway**

👉 Use **pull-based** (Prometheus) for dynamic systems like Kubernetes, and **push-based** for short-lived or restricted environments.

---

Would you like me to continue with **Question 130: “Which tools have you used to build observability stack?”** next?
