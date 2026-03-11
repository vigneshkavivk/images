Nice 👍 Your app is already accessible:

```
http://192.168.49.2:30007
```

Output:

```
Hello from Jenkins Kubernetes CI/CD!
```

So **Kubernetes + NodePort service working perfectly**.
Now we can **deploy Prometheus and Grafana using Docker** to monitor it.

I’ll give you the **simple setup (5–10 minutes)**.

---

# 1️⃣ Create Prometheus folder

```bash
mkdir monitoring
cd monitoring
```

---

# 2️⃣ Create Prometheus config

Create file:

```bash
nano prometheus.yml
```

Paste this:

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "python-k8s-app"
    static_configs:
      - targets: ["192.168.49.2:30007"]
```

Save.

---

# 3️⃣ Run Prometheus container

```bash
docker run -d \
-p 9090:9090 \
--name prometheus \
-v $(pwd)/prometheus.yml:/etc/prometheus/prometheus.yml \
prom/prometheus
```

Check container:

```bash
docker ps
```

---

# 4️⃣ Open Prometheus UI

Open browser:

```
http://localhost:9090
```

Go to:

```
Status → Targets
```

You should see:

```
python-k8s-app   UP
```

---

# 5️⃣ Run Grafana container

```bash
docker run -d \
-p 3000:3000 \
--name grafana \
grafana/grafana
```

Check:

```bash
docker ps
```

---

# 6️⃣ Open Grafana

Open:

```
http://localhost:3000
```

Login:

```
username: admin
password: admin
```

It will ask to change password.

---

# 7️⃣ Add Prometheus datasource

In Grafana:

```
Settings → Data Sources → Add Data Source
```

Choose **Prometheus**

URL:

```
http://host.docker.internal:9090
```

(or try `http://localhost:9090`)

Click:

```
Save & Test
```

---

# 8️⃣ Create Dashboard

Go to:

```
Dashboards → New → Add Visualization
```

Example query:

```
up
```

---

# 🎯 Final Architecture

Your setup will be:

```
Python App (Kubernetes Pods)
        │
        │
NodePort Service :30007
        │
        │
Prometheus (Docker :9090)
        │
        │
Grafana (Docker :3000)
```

---

# ⚠️ One small improvement (important)

Right now your Python app returns:

```
Hello from Jenkins Kubernetes CI/CD!
```

Prometheus works best if app exposes **/metrics** endpoint.

Example:

```
http://192.168.49.2:30007/metrics
```

Then Prometheus can collect:

```
http_requests_total
cpu_usage
memory_usage
```

---

✅ If you want, I can also show a **very clean DevOps demo setup**:

* Python app with `/metrics`
* Prometheus auto scraping
* Grafana dashboard
* Kubernetes + Docker monitoring architecture

This looks **very impressive in interviews or presentations**.
