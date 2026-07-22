### 📝 SECTION 1: Objective Questions (Test Your Reflexes)
*These test your quick knowledge of commands, ports, and default behaviors.*

**1. In a Dockerfile, what is the main difference between `COPY` and `ADD`?**
*   **Answer:** Both copy files from the host to the container. However, `ADD` can also download files from a URL and automatically extract `.tar.gz` files. Best practice is to use `COPY` 95% of the time.

**2. Which Kubernetes component is the "Brain" (the only component you talk to via `kubectl`)?**
*   **Answer:** `kube-apiserver`

**3. In Ansible, what does the concept of "Idempotency" mean?**
*   **Answer:** It means you can run a playbook 1,000 times, and it will only make changes if the system is not in the desired state. If the state is already correct, it does nothing (returns `ok`).

**4. What is the default Service type in Kubernetes if you do not specify one?**
*   **Answer:** `ClusterIP` (Internal only).

**5. What is the difference between `git fetch` and `git pull`?**
*   **Answer:** `git fetch` downloads the latest changes from the remote repo but *does not* merge them into your working files. `git pull` downloads the changes AND automatically merges them.

**6. In Prometheus, does it use a PUSH or PULL model to collect metrics?**
*   **Answer:** It uses a **PULL (Scrape)** model. It visits the `/metrics` endpoint of the targets to pull the data.

**7. In Linux, which command forcefully kills a process instantly without letting it save its data?**
*   **Answer:** `kill -9 <PID>` (Signal 9 / SIGKILL).

---

### 🗣️ SECTION 2: Subjective Questions (Interview / Essay Style)
*These test your architectural thinking. Explain the "Why."*

**Q1: Explain the difference between a Virtual Machine and a Docker Container.**
*   **Answer Guide:** A Virtual Machine virtualizes the **Hardware**. It requires a full, heavy Operating System (Guest OS) for every app, taking gigabytes of space and minutes to boot. A Container virtualizes the **Operating System**. It shares the Host OS Kernel, meaning it doesn't need its own OS. Containers are lightweight (megabytes) and boot in milliseconds.

**Q2: What is the difference between `Git Merge` and `Git Rebase`? Why do we never rebase a public branch?**
*   **Answer Guide:** `Git Merge` takes two branches and ties them together with a new "Merge Commit," preserving the exact, truthful history (looks like a diamond). `Git Rebase` unplugs your branch and pastes your commits on the very tip of the main branch, creating a perfectly straight, clean history. You **never rebase a public branch** because it rewrites history; if your teammates have the old history, it will destroy their local repositories.

**Q3: Why do we use Multi-Stage Dockerfiles for Java applications?**
*   **Answer Guide:** For Security, Size, and Speed. In Stage 1 (Builder), we use a heavy JDK image with Maven to compile the `.jar` file. In Stage 2 (Production), we use a tiny JRE Alpine image and only `COPY` the finished `.jar`. This keeps our raw source code safe from hackers, removes Maven from production, and shrinks the image from 1GB down to 200MB.

**Q4: In Kubernetes, why do we create a `Deployment` instead of just creating `Pods` directly?**
*   **Answer Guide:** Pods are mortal; if they die, they don't come back. A Deployment manages a `ReplicaSet` to ensure self-healing and auto-scaling. Most importantly, Deployments allow for **Zero-Downtime Rolling Updates** and instant **Rollbacks** if a new version has a bug.

**Q5: What are the Three Pillars of Observability?**
*   **Answer Guide:** 
    1. **Metrics:** Numbers over time (e.g., CPU is at 80%) used for Prometheus alerts.
    2. **Logs:** Text records of events (e.g., Error messages) used for debugging.
    3. **Traces:** Tracking a single user's request across multiple microservices to find bottlenecks.

---

### 💻 SECTION 3: Practical Tasks (Hands-On Lab Scenarios)
*They might give you a terminal and ask you to perform these tasks. Be ready!*

**Task 1: Linux Text Manipulation**
*   **Scenario:** You have a log file named `server.log`. You need to find all lines containing the word "ERROR" and save them into a new file called `errors.txt`.
*   **Command:** 
    ```bash
    grep "ERROR" server.log > errors.txt
    ```

**Task 2: Kubernetes Deployment & Service**
*   **Scenario:** Using the command line (imperatively), create an NGINX deployment with 3 replicas, and expose it to the public on a NodePort.
*   **Commands:**
    ```bash
    kubectl create deployment my-nginx --image=nginx
    kubectl scale deployment my-nginx --replicas=3
    kubectl expose deployment my-nginx --port=80 --type=NodePort
    ```

**Task 3: Docker & Linux Port Troubleshooting**
*   **Scenario:** You try to run `docker compose up -d` for a MySQL database, but it throws an error: `failed to bind port 0.0.0.0:3306: address already in use`. How do you fix it?
*   **Action:** It means a local MySQL service is already running on the VM. 
    1. Check what is running: `sudo ss -tulpn | grep 3306`
    2. Stop the local service: `sudo systemctl stop mysql`
    3. Run `docker compose up -d` again.

**Task 4: Ansible Playbook Basics**
*   **Scenario:** Write a quick Ansible playbook (`install_git.yml`) to install Git on all servers in the `web` group.
*   **Code:**
    ```yaml
    ---
    - name: Install Git
      hosts: web
      become: yes
      tasks:
        - name: Install git package
          apt:
            name: git
            state: present
            update_cache: yes
    ```

---