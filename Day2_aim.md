## 🧠 **What Is Trivy?**

**Trivy** (by Aqua Security) is a **security scanner** for:
- Docker images 🐳
- Filesystems 📁
- Git repositories 📦
- Kubernetes clusters ☸️

It scans for:
- OS vulnerabilities (CVEs)
- Misconfigurations
- SBOM (Software Bill of Materials)
- Secrets in code

Think of it as your **"security gatekeeper"** before deploying or using any image.

---

## ✅ **What You Are Doing**

You're:
1. **Running Trivy as a service in a separate Docker Compose file** (smart! you're modularizing tools).
2. Using Trivy from that container to **scan your Day2 `whoami` project** image or container.
3. Avoiding clutter — tools live in their own `tools/` folder, clean and scalable.

---

## 🔁 Why Separate Compose for Trivy?

Here’s the logic (Lean mindset + security):

| Benefit                        | Explanation                                                                 |
|-------------------------------|-----------------------------------------------------------------------------|
| 🔧 **Tool Decoupling**        | You can reuse the tool across many projects without repeating setup.       |
| 🔐 **Security Isolation**     | Tools like Trivy don’t run in the same network or scope as your app.       |
| 📦 **Reusability**            | Future projects can call the same Trivy container — DRY principle.         |
| 🧪 **Scan Other Images**      | Trivy can scan *any* image or container from Docker, not just Compose apps.|

---

## 🛠️ Manual Scanning With Trivy (Outside Compose)

Once Trivy is installed or running in Docker, you can scan:

### 1. **Prebuilt Public Image**
```bash
docker run --rm aquasec/trivy image traefik/whoami:v1.10.1
```

### 2. **Local Built Image (from your Compose project)**
```bash
docker images  # Get the name or ID
docker run --rm aquasec/trivy image day2-securewhoami_whoami
```

### 3. **Scan Running Container**
```bash
docker ps  # Get container ID or name
docker inspect day2-securewhoami_whoami | grep Image
# Get the image ID and scan:
docker run --rm aquasec/trivy image <image-id>
```

---

## 🤖 Using Trivy from Docker Compose (Your Setup)

Let’s say your tools folder has this `docker-compose.yml`:

```yaml
version: "3.8"

services:
  trivy:
    image: aquasec/trivy:latest
    container_name: trivy-scanner
    entrypoint: ["/app/entrypoint.sh"]
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./trivy:/app
```

And `entrypoint.sh` lets you run:
```bash
#!/bin/sh
TARGET_IMAGE=$1

if [ -z "$TARGET_IMAGE" ]; then
  echo "❌ No image provided. Usage: docker compose run trivy <image>"
  exit 1
fi

trivy image "$TARGET_IMAGE"
```

### 👇 To scan the Day2 image:

```bash
cd tools
docker compose run trivy day2-securewhoami_whoami
```

This runs Trivy inside the container and scans the target image!

---

## 🔍 Pro-Level Tips

| Task                      | Command                                                                            |
|---------------------------|-------------------------------------------------------------------------------------|
| Scan & Save JSON report   | `trivy image -f json -o output.json <image>`                                       |
| Scan Dockerfile           | `trivy config /path/to/Dockerfile`                                                 |
| Scan local folder         | `trivy fs .`                                                                        |
| Show only CRITICAL issues| `trivy image --severity CRITICAL <image>`                                          |
| Exclude vulnerabilities   | `trivy image --ignore-unfixed <image>`                                            |

---

## 📌 Summary

- Trivy scans your Docker images (even your own builds).
- You're hosting Trivy separately via Docker Compose (`tools/`).
- You call it manually to scan your project images like `whoami`.
- This improves reusability, modularity, and makes scanning consistent.
