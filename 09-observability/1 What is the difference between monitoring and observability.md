Great 👍
Let’s start with **Question 124**.

---

### **124. What is the difference between monitoring and observability?**

#### **Short explanation**

This question tests whether you understand how traditional monitoring differs from the broader concept of observability — a key principle in modern DevOps and cloud-native systems.

---

#### **Answer**

Monitoring tells you *what* is wrong by collecting predefined metrics and logs.
Observability helps you understand *why* it’s wrong by providing deep visibility into the system’s internal state.

---

#### **Detailed explanation**

**💡 Monitoring**

* Involves collecting metrics, logs, and alerts based on predefined conditions.
* Focuses on known issues — “known unknowns.”
* Example: CPU > 90%, or pod restarts detected.
* Tools: Prometheus, Grafana, CloudWatch, Nagios.

**🧠 Observability**

* Goes beyond monitoring. It’s about exploring *why* something happened, not just detecting that it happened.
* Focuses on unknown issues — “unknown unknowns.”
* It combines **metrics, logs, and traces** to give a complete picture.
* Tools: Prometheus (metrics), Loki/ELK (logs), Jaeger/Tempo (traces).
* Enables engineers to debug complex, distributed systems like Kubernetes microservices.

**🔧 Example scenario**

* Monitoring shows: “API latency increased.”
* Observability helps find: “Latency increased due to a timeout in the payment microservice triggered by a slow Redis cache.”

---

#### **Summary table**

| Feature      | Monitoring               | Observability                        |
| ------------ | ------------------------ | ------------------------------------ |
| Focus        | Detect known problems    | Explore unknown issues               |
| Approach     | Reactive                 | Proactive                            |
| Data sources | Metrics, basic logs      | Metrics, logs, traces                |
| Goal         | Tell you *what* is wrong | Help you understand *why* it’s wrong |
| Tools        | Prometheus, Nagios       | Grafana, Loki, Jaeger, Tempo         |

---

#### **Key takeaway**

👉 **Monitoring alerts you to problems; observability helps you understand and fix them.**

---

Would you like me to continue with **Question 125: “How to emit custom logs and metrics in your application?”** next?
