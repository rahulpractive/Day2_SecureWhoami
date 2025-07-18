Awesome! Let’s jump into **Day 2** of your Docker Compose 30-day security-first project, focusing on practical learning,
system hardening, and Trivy integration using a separate tools setup.

---

### ✅ **Day 2: Secure Whoami with Trivy Scan (Hosted Separately)**

#### 🎯 **Goal:**
Deploy a secure [traefik/whoami](https://hub.docker.com/r/traefik/whoami) service with a security-first Docker 
Compose config, and learn to run **Trivy scans manually** from a separate "tools" Docker Compose stack.

---

## 📁 Folder Structure

```
DockerComposeDaily/
├── Day2_SecureWhoami/
│   ├── docker-compose.yml
│   └── README.md
└── Tools/
    └── Trivy/
        ├── docker-compose.yml
        └── config/
```

---

## 🐳 **Day2: docker-compose.yml**
```yaml
version: "3.8"
services:
  whoami:
    image: traefik/whoami:v1.8.6
    # Tiny Go webserver that prints OS information and HTTP request to output.
    container_name: secure-whoami
    read_only: true
    tmpfs:
      - /tmp
    security_opt:
      - no-new-privileges:true
    ports:
      - "8080:80"
    healthcheck:
      test: ["CMD", "wget", "--spider", "-q", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
    restart: "no"
```

---

## 🧪 **Tools/Trivy: docker-compose.yml**
```yaml
version: "3.8"
services:
  trivy:
    image: aquasec/trivy:latest
    container_name: trivy-cli
    entrypoint: [ "tail", "-f", "/dev/null" ]
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ../:/scandir
```

---

## 🛠️ Step-by-Step: What To Do

### 🧱 **1. Set up Day 2 project**
- Create the folder `Day2_SecureWhoami`
- Add the Docker Compose file
- Run:
```bash
cd DockerComposeDaily/Day2_SecureWhoami
docker compose up -d
```

### 🔎 **2. Set up Trivy (hosted in Tools)**
- Navigate to `Tools/Trivy`
- Start the Trivy container:
```bash
cd DockerComposeDaily/Tools/Trivy
docker compose up -d
```

### 🧪 **3. Run Trivy scan manually from the Trivy container**
```bash
docker exec -it trivy-cli sh
```

Inside the container, run this to scan `whoami` image:
```bash
trivy image traefik/whoami:v1.8.6
```

Or scan the entire folder:
```bash
trivy fs /scandir/Day2_SecureWhoami
```

---

## 🎓 What You’re Learning

| Area | What You Learn |
|------|----------------|
| **Security** | Read-only FS, no-new-privileges, Trivy scanning |
| **Lean & 6 Sigma** | Using minimal images, isolating tools, reusability |
| **Operations** | Healthchecks, container naming, folder structuring |
| **Tooling** | Trivy integration as a service, volume mounts for scanning |

---

Would you like me to generate a **README.md** template for this day too, including scan command reference and outcomes?

Let’s keep the momentum going! 🔐🔥