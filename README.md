### Day 2 Recap: Hardened Whoami + Manual Trivy Scan

---

On Day 2, we focused on deploying a simple web service—`traefik/whoami`—with a strong emphasis on security. Rather than just getting the container up and running, the goal was to apply real-world hardening techniques that you'd actually use in production environments.

---

### What We Did

We used Docker Compose to run the `traefik/whoami:v1.11.0` container, which is a lightweight service that echoes HTTP request data—great for testing reverse proxies like Traefik.

Then, we added several security-focused configurations:

* **read\_only: true** – This makes the container’s filesystem read-only at runtime, protecting it from tampering.
* **tmpfs: /tmp** – We mounted a temporary in-memory filesystem to the `/tmp` directory, preventing the container from writing to disk.
* **security\_opt: no-new-privileges\:true** – This blocks the container from gaining extra privileges, even if a binary within it tries to do so.
* **Health check** – We added a basic `wget` check to confirm the container is responding properly on `localhost`.
* **restart: "no"** – We explicitly disabled automatic restarts. This gives us more control, especially when we're doing manual scans.

---

### Why This Matters

| Topic                      | Benefit                                                                                          |
| -------------------------- | ------------------------------------------------------------------------------------------------ |
| Filesystem Lockdown        | Reduces the risk of file tampering or malware being dropped in the container.                    |
| No New Privileges          | Helps enforce least privilege across the container runtime.                                      |
| Healthcheck Configuration  | Adds visibility into the container’s health, which is useful for orchestration later on.         |
| Separation of Tools & Apps | Prepares us for using Trivy as a separate tool, which aligns with clean architecture principles. |

---

### Who This Helps

* **You, as a security-minded engineer** – You’re not just spinning up containers anymore; you’re learning how to run them responsibly, with the right guardrails in place.
* **Teams and stakeholders** – These small details show that you prioritize security and stability from day one, which builds trust and credibility.

This exercise lays the groundwork for scanning the image using Trivy in a separate container, which we’ll cover in the next session.
