### 📝 SECTION 1: Objective Questions (Lightning Round)

**1. In a Jenkins pipeline, why did we use `reuseNode true` when using a Docker agent?**
*   **Answer:** To tell the temporary Docker container to use the exact same physical workspace directory on the Jenkins host. This prevents us from having to compress and `stash/unstash` heavy `.jar` files, which saves RAM and prevents crashes!

**2. Which folder inside an Ansible Role is used to store `.j2` files?**
*   **Answer:** The `templates/` directory.

**3. In Kubernetes, if your Pod status is stuck in `ImagePullBackOff` or `ErrImagePull` when pulling from Harbor, what did you likely forget?**
*   **Answer:** You either forgot to create the Kubernetes Secret (`kubectl create secret docker-registry`), or you forgot to add `imagePullSecrets` in your Pod/Deployment YAML.

**4. What does the `-y` flag do in the command `sudo apt-get install -y nginx`?**
*   **Answer:** It automatically answers "Yes" to all prompts. It is mandatory in Bash scripts, Dockerfiles, and Jenkins pipelines; otherwise, the automation will freeze forever waiting for human input!

**5. In AWS, if you need a persistent hard drive to store MySQL data for one specific EC2 instance, which storage type do you use?**
*   **Answer:** EBS (Elastic Block Store). 

**6. Which Linux command allows you to see all actively listening ports and running services?**
*   **Answer:** `ss -tulpn` (or `netstat -tulpn`).

**7. In a Dockerfile, what does the `WORKDIR` instruction do?**
*   **Answer:** It acts like the `cd` (change directory) command. It sets the working directory for any `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, or `ADD` instructions that follow it. If the folder doesn't exist, it creates it.

---

### 🗣️ SECTION 2: Subjective Questions (Interview Style)

**Q1: Explain the difference between `CMD` and `ENTRYPOINT` in a Dockerfile. How do they work together?**
*   **Answer Guide:** 
    *   **`ENTRYPOINT`** is the main engine. It is the hardcoded command that *must* run (e.g., `["java", "-jar"]`). It is hard to override.
    *   **`CMD`** is the default argument passed to the Entrypoint (e.g., `["app.jar"]`). 
    *   *Together:* If a user types `docker run my-image`, it runs `java -jar app.jar`. If the user types `docker run my-image test.jar`, the `CMD` is easily overridden, and it runs `java -jar test.jar` instead!

**Q2: Why do we decouple CI and CD into two separate pipelines (Upstream and Downstream) instead of writing one massive Jenkinsfile?**
*   **Answer Guide:** Separation of concerns and reusability. The CI pipeline's only job is to compile, test, and upload an immutable artifact (like a `.jar`) to a registry like Nexus. The CD pipeline's only job is to deploy. If a server crashes in production, you can trigger the CD pipeline manually to redeploy the exact same `.jar` file without having to recompile the Java code from scratch.

**Q3: Explain the difference between a ConfigMap and a Secret in Kubernetes. How are Secrets stored?**
*   **Answer Guide:** A **ConfigMap** is used for non-sensitive data, like database URLs and environment names. A **Secret** is used for highly sensitive data, like passwords and API tokens. Secrets are encoded in Base64 and stored securely inside the Kubernetes `etcd` database.

**Q4: If Prometheus shows your Docker Engine is "UP", but your imported Grafana dashboard says "No Data", what is the most likely cause and how do you fix it?**
*   **Answer Guide:** It is almost certainly a **Label Mismatch**. The dashboard template is probably looking for a specific job name (like `job="docker"`), but my `prometheus.yml` file might have named it `job="docker-engine"`. I would fix it by editing the Grafana variables or editing the PromQL query in the dashboard panel to match my actual labels.

---

### 💻 SECTION 3: Practical Tasks (Hands-On Scenarios)

**Task 1: The Docker Compose "Race Condition"**
*   **Scenario:** Your backend API keeps throwing a "Connection Refused" error because it starts faster than your MySQL database. Write the exact YAML syntax in `docker-compose.yml` to force the backend to wait until MySQL is 100% ready.
*   **Code:**
    ```yaml
    backend-api:
      depends_on:
        db:
          condition: service_healthy
    ```

**Task 2: Linux System Administration**
*   **Scenario:** You just created a new background service file called `nexus.service` in `/etc/systemd/system/`. Write the three Linux commands needed to apply the file, start the service, and make it start automatically on boot.
*   **Commands:**
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl start nexus
    sudo systemctl enable nexus
    ```

**Task 3: Git Branching & Merging**
*   **Scenario:** You are on the `main` branch. Create a new branch named `hotfix`, create a file named `fix.txt`, commit it, and merge it back into `main` while forcing a "Merge Commit" (disabling fast-forward).
*   **Commands:**
    ```bash
    git checkout -b hotfix
    touch fix.txt
    git add fix.txt
    git commit -m "added hotfix"
    git checkout main
    git merge hotfix --no-ff -m "merging hotfix branch"
    ```

**Task 4: Kubernetes Imperative Commands**
*   **Scenario:** You need to instantly scale your `event-api` Deployment down to 1 replica because of a database lock issue. Write the command.
*   **Command:**
    ```bash
    kubectl scale deployment event-api --replicas=1
    ```

---