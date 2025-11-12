---

### **Question 132 — How do you trace a request across multiple microservices in a Kubernetes cluster?**

---

#### **Short Explanation**

This question tests your knowledge of **distributed tracing** — how to follow a request as it travels through multiple microservices to identify where latency or failures occur.

---

#### **Answer**

Use **distributed tracing tools** like **Jaeger, Tempo, or Zipkin** integrated with **OpenTelemetry**.
Each service propagates a **trace ID** through HTTP headers (e.g., `traceparent`), allowing you to visualize the full request path and detect latency bottlenecks.

---

#### **Detailed Explanation**

In a **microservices architecture**, a single user request may pass through 10+ services — API Gateway → Auth → Orders → Payments → DB.

To debug latency or failures, you need **end-to-end visibility** — this is where **distributed tracing** helps.

---

### 🧩 **How Distributed Tracing Works**

1. **Trace** — Represents one user request end-to-end.
2. **Span** — Represents one operation or service call within that trace.
3. **Trace ID** — Shared across all spans for that request.
4. **Parent-Child Relationship** — Shows which service called which.

---

### ⚙️ **Implementation Steps**

**1. Instrument your application (with OpenTelemetry SDK)**
For example, in Python:

```python
from opentelemetry import trace
from opentelemetry.instrumentation.requests import RequestsInstrumentor

RequestsInstrumentor().instrument()
tracer = trace.get_tracer(__name__)

with tracer.start_as_current_span("checkout-service"):
    # perform logic or call another service
    pass
```

**2. Propagate Trace Context**

* Include tracing headers in HTTP calls:

  ```
  traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01
  ```
* Most SDKs handle this automatically once instrumented.

**3. Deploy a Tracing Backend**

* Use one of these:

  * **Jaeger** (open source)
  * **Tempo (Grafana)**
  * **Zipkin**
  * **AWS X-Ray**, **Datadog APM**

Example Jaeger deployment in Kubernetes:

```bash
kubectl create namespace observability
kubectl apply -f https://github.com/jaegertracing/jaeger-operator/releases/download/v1.52.0/jaeger-operator.yaml
```

**4. Visualize the Trace**

* In Jaeger UI → search by service name.
* See timeline showing each microservice’s latency.
* Identify slow or failed spans.

---

### 🔍 **Real-World Example**

> In our production system, an API request took 2 seconds. Using Jaeger tracing, we found 1.8s spent in the `payment-service` due to a slow DB call. Without tracing, we’d only see total latency — not *where* it occurred.

---

### **Summary Table**

| **Concept**   | **Description**                 | **Tool Example**   | **Purpose**               |
| ------------- | ------------------------------- | ------------------ | ------------------------- |
| Trace         | End-to-end journey of a request | OpenTelemetry      | Identify latency path     |
| Span          | One service operation           | Jaeger, Tempo      | Measure sub-operations    |
| Trace ID      | Unique request identifier       | Auto-generated     | Correlate across services |
| Visualization | Graphical flow of spans         | Jaeger UI, Grafana | Root-cause latency        |

---

#### **Key Takeaway**

> Use **OpenTelemetry + Jaeger (or Tempo)** to trace requests across microservices — this gives you **visibility into latency and bottlenecks** in complex Kubernetes applications.

---

Shall I continue with **Question 133 — “A pod crashes randomly with OOMKilled. How do you identify and fix this?”**
