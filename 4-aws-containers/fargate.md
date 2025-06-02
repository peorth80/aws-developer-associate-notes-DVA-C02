## **AWS Fargate Cheat Sheet**


### 1. **What is AWS Fargate?**

* **Serverless compute engine for containers.**
* **No EC2 management required.**
* Works with **ECS** and **EKS** (Kubernetes).
* **Pay for vCPU and memory** used by running tasks.

---

### 2. **Key Features**

* **No infrastructure to provision/manage.**
* **Launch type** in ECS/EKS (`FARGATE`).
* **Automatic scaling** (up and down).
* **Per-task isolation** (each task gets its own kernel and ENI).
* **Integrates with IAM, VPC, CloudWatch, ALB/NLB, ECR.**

---

### 3. **How Fargate Works (ECS context)**

1. **Define Task Definition** (`requiresCompatibilities=["FARGATE"]`, `networkMode="awsvpc"`).
2. **Specify vCPU and memory** per task.
3. **Run Task or Create Service** with `--launch-type FARGATE`.
4. **Fargate provisions compute automatically**.

---

### 4. **Exam-Important Limits**

* **Task size:**

  * vCPU: 0.25–16 vCPU
  * Memory: 0.5–120 GiB
* **Max 120 tasks/subnet (soft limit, can increase).**
* **Must use `awsvpc` network mode (each task gets its own ENI).**
* **Supports only Linux (Windows support is limited, check latest docs).**

---

### 5. **Networking**

* **VPC only:** You must specify subnets and security groups.
* **Each task gets a private IP (ENI).**
* **Security groups apply to tasks.**

---

### 6. **IAM Roles**

* **Task Role:** Permissions for containers to access AWS resources.
* **Task Execution Role:** Used by Fargate to pull images from ECR, write logs, etc.

---

### 7. **Storage**

* **Ephemeral storage:** 20 GiB by default (can increase up to 200 GiB).
* **No persistent storage** (use EFS for persistent needs).

---

### 8. **Monitoring**

* **CloudWatch Logs:** Out-of-the-box integration.
* **CloudWatch Metrics:** Task/service metrics.

---

### 9. **Key CLI Commands**

#### **Run a Fargate Task**

```bash
aws ecs run-task \
  --cluster my-cluster \
  --launch-type FARGATE \
  --task-definition my-task \
  --network-configuration "awsvpcConfiguration={subnets=[subnet-abc],securityGroups=[sg-xyz],assignPublicIp=ENABLED}"
```

#### **Register Task Definition**

```json
{
  "family": "my-task",
  "networkMode": "awsvpc",
  "requiresCompatibilities": ["FARGATE"],
  "cpu": "256",
  "memory": "512",
  "containerDefinitions": [ ... ]
}
```

---

### 10. **Common Exam Tips**

* **You never manage EC2 instances with Fargate.**
* **You must use `awsvpc` network mode.**
* **IAM permissions are needed for tasks to pull images (task execution role).**
* **Fargate pricing is per vCPU-second and GB-second (billed per second).**
* **Supports only Application and Network Load Balancers.**
* **Autoscaling works with ECS/Fargate, based on CloudWatch metrics.**
* **Each Fargate task is isolated from others (security boundary).**
* **EFS can be mounted for persistent storage (if needed).**

---

### 11. **Fargate vs. EC2 Launch Type**

| Feature             | Fargate              | EC2                     |
| ------------------- | -------------------- | ----------------------- |
| Instance Management | AWS-managed (none)   | User-managed            |
| Network Mode        | awsvpc only          | all modes supported     |
| Pricing             | Per-task usage       | Per-instance            |
| OS Customization    | Not possible         | Full control            |
| Daemon Scheduling   | No                   | Yes                     |
| Max Task Size       | 16 vCPU, 120 GiB RAM | Up to EC2 instance size |

---

### 12. **Security**

* **Each task runs in its own kernel (strong isolation).**
* **Security group per task (awsvpc mode).**

---

### 13. **Quick Q\&A / Tips**

* **Q: Can you SSH into a Fargate task?**
  **A:** No (no underlying instance to access).
* **Q: Can you mount EFS?**
  **A:** Yes, EFS integration supported for persistent storage.
* **Q: What happens if a task fails?**
  **A:** If running as part of a Service, ECS restarts it. Standalone tasks are not restarted.