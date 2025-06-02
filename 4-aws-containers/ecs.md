## **AWS ECS**

### 1. **Core Concepts**

* **Cluster:** Logical grouping of ECS resources (EC2 or Fargate).
* **Task Definition:** Blueprint for running Docker containers (image, CPU/mem, ports, etc.).
* **Task:** Instantiation of a Task Definition (can be single or multiple containers).
* **Service:** Maintains desired number of task copies running, enables load balancing, and auto recovery.
* **Launch Types:**
  * **EC2:** You manage EC2 instances.
  * **Fargate:** Serverless, AWS manages compute resources.

---

### 2. **ECS Task Definition Key Fields**

```json
{
  "family": "my-task-family",
  "containerDefinitions": [
    {
      "name": "my-container",
      "image": "nginx:latest",
      "cpu": 256,
      "memory": 512,
      "essential": true,
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80
        }
      ],
      "environment": [
        {"name": "ENV_VAR", "value": "value"}
      ]
    }
  ],
  "requiresCompatibilities": ["FARGATE"],
  "networkMode": "awsvpc",
  "cpu": "256",
  "memory": "512",
  "executionRoleArn": "...",
  "taskRoleArn": "..."
}
```

* **Tip:** Use `awsvpc` mode with Fargate for ENI per task (gets its own private IP).

---

### 3. **Common CLI Commands**

* **List Clusters:**

  ```
  aws ecs list-clusters
  ```
* **Register Task Definition:**

  ```
  aws ecs register-task-definition --cli-input-json file://taskdef.json
  ```
* **Run Task (Fargate):**

  ```
  aws ecs run-task \
    --cluster my-cluster \
    --launch-type FARGATE \
    --task-definition my-task-family \
    --network-configuration "awsvpcConfiguration={subnets=[subnet-abc],securityGroups=[sg-xyz],assignPublicIp=ENABLED}"
  ```
* **Create Service:**

  ```
  aws ecs create-service \
    --cluster my-cluster \
    --service-name my-service \
    --task-definition my-task-family \
    --desired-count 2 \
    --launch-type FARGATE \
    --network-configuration ...
  ```
* **Update Service:**

  ```
  aws ecs update-service \
    --cluster my-cluster \
    --service my-service \
    --task-definition new-task-family
  ```
* **Stop Task:**

  ```
  aws ecs stop-task --cluster my-cluster --task task-id
  ```

---

### 4. **ECS Service Types**

* **Replica:** Maintains the number of desired tasks.
* **Daemon:** One task per EC2 instance (EC2 mode only).

---

### 5. **Networking Modes**

* **bridge:** Docker default (EC2 only).
* **host:** Host’s network namespace (EC2 only).
* **awsvpc:** Task gets its own ENI (Fargate or EC2).
* **none:** No networking.

---

### 6. **IAM Roles**

* **Task Role:** Used by containers for AWS API access (S3, DynamoDB, etc.).
* **Execution Role:** Pulls container images, writes logs to CloudWatch, etc.

---

### 7. **Load Balancing**

* **Types:** Application Load Balancer (ALB), Network Load Balancer (NLB).
* **Target Group:** Tasks register as targets.
* **Health Checks:** Service replaces unhealthy tasks.

---

### 8. **Deployment Types**

* **Rolling update (default):** Slowly replaces old tasks.
* **Blue/Green:** (via CodeDeploy) Swaps traffic after new tasks are healthy.

---

### 9. **Monitoring & Logging**

* **CloudWatch Logs:** Stream container logs.
* **CloudWatch Metrics:** ECS service/task metrics (CPU, mem).
* **Container Insights:** Detailed metrics & troubleshooting.

---

### 10. **Key Exam Tips**

* **ECS Fargate requires `awsvpc` networking.**
* **Fargate is serverless; you don’t manage EC2 instances.**
* **EC2 launch type requires managing and scaling EC2 instances.**
* **Services auto-recover failed tasks. Standalone tasks do not.**
* **ECS integrates with ECR (Elastic Container Registry) or DockerHub.**
* **Security Groups/Subnets:** ECS tasks (in awsvpc) use ENIs, so attach security groups/subnets accordingly.
* **Autoscaling:** ECS can scale services based on CloudWatch alarms.

---

## **Quick Reference Table**

| Concept           | EC2 Launch | Fargate Launch |
| ----------------- | ---------- | -------------- |
| Managed Compute   | You        | AWS            |
| Networking Modes  | all        | awsvpc only    |
| Task ENI          | Optional   | Always         |
| OS/Kernel Control | Yes        | No             |
| Daemon Service    | Yes        | No             |

