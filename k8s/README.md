# Kubernetes & Docker Deployment Guide for rate.sx

This guide explains how to build, load, and deploy the `rate.sx` application with Kubernetes and Docker using the files in the `k8s/` directory.

---

## 1. Build the Docker Image

From the project root (`ratesx`):

```bash
docker build -t ratesx:latest -f k8s/Dockerfile .
```

---

## 2. Load the Image into k3s

Pipe the built image directly into k3s (requires root privileges):

```bash
docker save ratesx:latest | sudo k3s ctr images import -
```

---

## 3. Configure Secrets

Edit `k8s/api-keys-secret.yaml` and fill in your real API keys:

```yaml
stringData:
  CMC_API_KEY: "your_real_cmc_api_key"
  FIXER_API_KEY: "your_real_fixer_api_key"
```

Apply the secret:

```bash
kubectl apply -f k8s/api-keys-secret.yaml
```

---

## 4. Deploy Kubernetes Resources

Apply the manifests in the `k8s/` directory:

```bash
kubectl apply -f k8s/mongodb-deployment.yaml
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
kubectl apply -f k8s/collect-job-coins.yaml
kubectl apply -f k8s/collect-job-currencies.yaml
kubectl apply -f k8s/collect-job-marketcap.yaml
```

---

## 5. Trigger a Job Manually

To trigger a job immediately (e.g., for coins):

```bash
kubectl create job --from=cronjob/collect-data-cronjob-coins coins-manual-$(date +%s)
```

---

## 6. Notes

- All API keys are managed via a single Kubernetes Secret (`api-keys`).
- The CronJobs for data collection are set to run hourly by default. Adjust schedules in the YAMLs if needed.
- If you make changes to the code or Dockerfile, repeat steps 1 and 2 to update the image in k3s.
- Use `kubectl logs job/<job-name>` to view job output.

---

## Directory Structure
- `k8s/` — All Kubernetes manifests and Dockerfile
- `bin/`, `lib/`, `etc/` — Application source code and configuration
