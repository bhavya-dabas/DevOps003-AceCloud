# MERN DevOps Hackathon – Starter Repository
A stripped-down codebase ready for full containerisation, routing, TLS and CI/CD_



 1  Objective

ABC Company is migrating its social-app workload from a single Linux host to a fully containerised, automated deployment.
This repository contains **only the application source code** (React front-end, Express API, Mongo data layer). All Dockerfiles, Compose manifests, Nginx configurations and CI/CD workflows have been removed.
Your task is to design and implement a **production-ready, secure, reproducible deployment** on a single Ubuntu VM provided for the hackathon.



2  Target Architecture

| Tier        | Technology                | Responsibilities                                  |
|-------------|---------------------------|---------------------------------------------------|
| Front-end   | React 18 SPA              | Serves static assets via Nginx                    |
| API Service | Node 16 / Express 4       | REST endpoints, JWT auth, business logic          |
| Database    | MongoDB 6                 | Persistent document store                         |
| Reverse Proxy| Nginx 1.23               | TLS termination, static delivery, `/api/**` proxy |
| Pipeline    | Jenkins or GitHub Actions | Build, test, image push, automated rollout        |

---

3  Scope of Work

1. **Containerise** each tier with best-practice Dockerfiles (multi-stage where needed).
2. **Orchestrate** services with a single `docker-compose.yml` (or Kubernetes manifests).
3. **Secure** the public domain with Let’s Encrypt TLS, redirect HTTP→HTTPS.
4. **Automate** build–test–deploy using a CI/CD pipeline that achieves near-zero downtime.
5. **Document** bootstrap, rollback, certificate renewal & environment variables.

---

4  Required Compose Elements (Production)

| Service name | Image (expected)         | Exposed ports | Key environment variables                |
|--------------|--------------------------|---------------|------------------------------------------|
| mongo        | `mongo:6` (official)     | _none_        | `MONGO_INITDB_*` for root user & DB name |
| backend      | `mern-backend:<tag>`     | _none_        | `PORT`, `MONGO_URI`, `JWT_SECRET`        |
| frontend     | `mern-frontend:<tag>`    | _none_        | _(none at runtime; build env uses `REACT_APP_API_BASE`)_ |
| nginx        | `nginx:alpine`           | **80, 443**   | TLS certificates mounted (`/etc/letsencrypt`) |

* Only `nginx` publishes host ports 80/443.
* Internal communication happens on a custom bridge network; use `expose:` for other containers.
* Persist database files (`mongo-data` named volume) and certificates (`certs` named volume).

---

5  Environment Variables / Secrets

| Variable          | Consumed by | Description                           |
|-------------------|-------------|---------------------------------------|
| `MONGO_URI`       | backend     | Connection string to Mongo container  |
| `JWT_SECRET`      | backend     | Secret used to sign access tokens     |
| `REACT_APP_API_BASE` | frontend build | Base path for API calls (`/api`) |

Store secrets in pipeline vaults or Docker Secrets; do **not** commit them.

---

6  TLS Workflow (Mandatory)

1. Point the organiser-supplied domain to the VM’s public IP.
2. Run Certbot (or Caddy) inside the Nginx container to obtain certificates.
3. Mount `/etc/letsencrypt` on the named `certs` volume so renewals survive container restarts.
4. Add an HTTP→HTTPS redirect rule in Nginx.

---

7  CI/CD Expectations

* Lint and unit tests must run for both front-end and back-end.
* Images are tagged with the commit SHA and pushed to a registry.
* Deployment step pulls new images on the VM and performs `docker compose up -d`.
* Provide a rollback procedure (previous tag).
* Store your pipeline file (`Jenkinsfile` or `.github/workflows/ci.yml`) at repo root.

---

8  Health Checks

| Service   | Endpoint / command                        |
|-----------|-------------------------------------------|
| backend   | `GET /api/health` returns `{ "status":"OK" }` |
| nginx     | `GET /` returns the React index page      |

Add Docker `healthcheck:` directives so Compose (or Docker Swarm/K8s) can restart failing containers.

---

9  Submission Checklist

- [ ] Dockerfiles for front-end and back-end, non-root, multi-stage.
- [ ] `docker-compose.yml` (or K8s manifests) defining all services & volumes.
- [ ] Nginx configuration with HTTPS and proxy rules.
- [ ] CI/CD pipeline definition (Jenkinsfile or GitHub Actions).
- [ ] README / Wiki covering setup, env-vars, TLS renewal, rollback.
- [ ] Public HTTPS URL demonstrating SPA and `/api/health`.

---

10  Evaluation Rubric

| Weight | Criterion                                       |
|-------:|-------------------------------------------------|
| 30 %   | Live HTTPS deployment reachable by judges       |
| 25 %   | CI/CD pipeline robustness and traceability      |
| 15 %   | Docker image & Compose quality (size, security) |
| 15 %   | Secrets management and TLS best-practices       |
| 10 %   | Documentation clarity and reproducibility       |
| 5 %    | Innovation (monitoring, blue-green, IaC, etc.)  |

---

Start Now

Clone this repository, create your Docker/Compose/Nginx assets in a new `deploy/` folder (or similar), commit, and iterate until the application runs securely on the provided VM under your domain. Good luck.
