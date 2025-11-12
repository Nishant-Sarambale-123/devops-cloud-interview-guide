---

### **139. Explain NACL vs SG and which one do you use in your organization?**

**Short Explanation:**
This question tests your understanding of **AWS Security Layers** — mainly the difference between **Network ACLs (NACLs)** and **Security Groups (SGs)** and when to use each.

---

**Answer:**

* **Security Groups (SGs)** act as **stateful firewalls** at the **instance level** — they control inbound/outbound traffic for EC2 instances or resources.
* **Network ACLs (NACLs)** act as **stateless firewalls** at the **subnet level** — they control traffic entering and leaving a subnet.
  👉 In most real-world setups, **Security Groups** are used more frequently for instance-level access control, while **NACLs** provide an extra layer of protection.

---

**Detailed Explanation:**

#### 🧱 **1. Security Groups (SGs):**

* Associated **with EC2 instances, load balancers, RDS**, etc.
* **Stateful** → If inbound traffic is allowed, the response is automatically allowed.
* Rules are **allow-only** (no explicit deny).
* Easier to manage because they can reference other SGs (e.g., “allow traffic from web-SG to app-SG”).

**Example:**

```text
SG-Web:
  Inbound: Allow HTTP (80) from 0.0.0.0/0
  Outbound: Allow all
```

#### 🧱 **2. Network ACLs (NACLs):**

* Attached **to subnets** (apply to all instances in that subnet).
* **Stateless** → Return traffic must be explicitly allowed.
* Support both **allow and deny** rules.
* Evaluated in **order** (lowest rule number first).

**Example:**

```text
Inbound Rule 100: Allow HTTP (80) from 0.0.0.0/0
Outbound Rule 100: Allow all
Inbound Rule *: Deny all (default)
```

---

#### **3. Usage in Real Organizations (Practical View):**

* **Primary security control:** Security Groups (simpler and stateful).
* **NACLs usage:** Rarely changed — used mainly to block entire IP ranges or add another layer of subnet-level security.

**Example in Organization:**

* SGs control access between tiers (Web → App → DB).
* NACLs deny known malicious IP ranges or unused ports.

---

**Summary Table:**

| Feature              | Security Group (SG)                  | Network ACL (NACL)         |
| -------------------- | ------------------------------------ | -------------------------- |
| Level                | Instance Level                       | Subnet Level               |
| Stateful / Stateless | Stateful                             | Stateless                  |
| Rules Type           | Allow only                           | Allow + Deny               |
| Default Behavior     | Deny all inbound, allow all outbound | Allow all inbound/outbound |
| Evaluated            | All rules                            | In order (by rule number)  |
| Association          | EC2, ENI, RDS                        | Subnets                    |
| Real Use             | Fine-grained control                 | Broad subnet-level control |

---

**Key Takeaway:**
Use **Security Groups** for most instance-level access control and **NACLs** for subnet-level traffic filtering — together they create **defense-in-depth** security in AWS.

---

Continue with **Question 140 (EC2 Instance terminated unexpectedly, How will you troubleshoot?)** next?
