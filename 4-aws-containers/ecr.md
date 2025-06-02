## **AWS ECR (Elastic Container Registry) Cheat Sheet**

### 1. **What is ECR?**

* **Managed Docker container registry.**
* **Private and public registries supported.**
* Secure storage, versioning, and retrieval of container images.

---

### 2. **Key Features**

* **Integration with ECS, EKS, Lambda.**
* **Supports image scanning for vulnerabilities.**
* **Image lifecycle policies** to automatically expire old images.
* **Encryption at rest** (AES-256, can use KMS).
* **Fine-grained access control** via IAM.

---

### 3. **Workflow Overview**

1. **Build Docker image.**
2. **Tag the image.**
3. **Authenticate Docker to ECR.**
4. **Push image to ECR.**
5. **(ECS, EKS, Lambda) Pull image from ECR.**

---

### 4. **Important CLI Commands**

#### **Create a repository**

```bash
aws ecr create-repository --repository-name my-repo
```

#### **Authenticate Docker to ECR**

```bash
aws ecr get-login-password --region <region> \
  | docker login --username AWS --password-stdin <account-id>.dkr.ecr.<region>.amazonaws.com
```

#### **Tag your image**

```bash
docker tag my-image:latest <account-id>.dkr.ecr.<region>.amazonaws.com/my-repo:latest
```

#### **Push your image**

```bash
docker push <account-id>.dkr.ecr.<region>.amazonaws.com/my-repo:latest
```

#### **Pull your image**

```bash
docker pull <account-id>.dkr.ecr.<region>.amazonaws.com/my-repo:latest
```

---

### 5. **Access Control & Permissions**

* **IAM policies** control who can push/pull images.
* For cross-account access, use **resource policies** on the repository.

---

### 6. **Lifecycle Policies**

* Used to **delete old/untagged images automatically** (save space, cost).
* Example: Keep only last 10 images, delete the rest.

---

### 7. **Image Scanning**

* Can be enabled on push (basic) or use enhanced scanning (w/ Amazon Inspector).
* Detects known vulnerabilities.

---

### 8. **Encryption**

* **At rest:** Always encrypted (AES-256, optionally KMS CMK).
* **In transit:** TLS.

---

### 9. **ECR Public**

* **Public repositories** can be shared globally.
* **Anonymous or authenticated access.**

---

### 10. **Common Exam Gotchas**

* **ECR does not build images**—it only stores and serves them.
* **Authentication required** for all private repo operations (even pull!).
* **ECS Task Execution Role** needs permissions to pull from ECR.
* **You pay for storage and data transfer out** (inbound is free).
* **Image URIs** are region and account-specific.
* **ECR Lifecycle policies** help manage repo size/cost automatically.
* **Can integrate with CodeBuild/CodePipeline** for CI/CD.
* **Lambda can pull images from ECR** 

---

### **Sample IAM Policy for Push/Pull**

```json
{
  "Effect": "Allow",
  "Action": [
    "ecr:GetAuthorizationToken",
    "ecr:BatchCheckLayerAvailability",
    "ecr:GetDownloadUrlForLayer",
    "ecr:BatchGetImage",
    "ecr:PutImage"
  ],
  "Resource": "*"
}
```

