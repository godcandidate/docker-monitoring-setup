# **Monitoring Setup with Prometheus, Grafana, and Alertmanager**

This repository contains a Docker Compose-based setup for monitoring containers using **Prometheus**, **Grafana**, **Alertmanager**, and **Node Exporter**. It is designed to be simple, modular, and easy to extend for small to medium-sized projects.

---

## **Features**
- **Prometheus**: Scrapes metrics from exporters and monitored containers.
- **Node Exporter**: Collects system-level metrics (CPU, memory, disk, etc.).
- **Alertmanager**: Handles alerts and sends notifications (e.g., via email or Slack).
- **Grafana**: Visualizes metrics with pre-configured dashboards.
- **Docker Compose**: Simplifies deployment and management of all components.

---

## **Prerequisites**
Before getting started, ensure you have the following installed:
- **Docker**: [Install Docker](https://docs.docker.com/get-docker/)
- **Docker Compose**: Included with Docker Desktop or install separately.

---

## **Quick Start**

### 1. Clone the Repository
```bash
git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>
```

### 2. Start the Monitoring Stack
Run the following command to start all services:
```bash
docker-compose up -d
```

### 3. Access the Tools
Once the stack is running, you can access the tools via your browser:
- **Prometheus**: [http://localhost:9090](http://localhost:9090)
- **Alertmanager**: [http://localhost:9093](http://localhost:9093)
- **Grafana**: [http://localhost:3000](http://localhost:3000)  
  Default credentials: `admin/admin`

---

## **Configuration**

### Adding Custom Containers for Monitoring
To monitor additional containers:
1. Ensure the container exposes metrics in a format Prometheus can scrape (e.g., `/metrics` endpoint).
2. Add the container's metrics endpoint to `prometheus/prometheus.yml` under `scrape_configs`. For example:
   ```yaml
   - job_name: 'my-container'
     static_configs:
       - targets: ['my-container:8080']
   ```
3. Restart the stack:
   ```bash
   docker-compose down
   docker-compose up -d
   ```

### Alerting Rules
Customize alerting rules in `prometheus/alert.rules.yml`. For example:
```yaml
groups:
  - name: example-alerts
    rules:
      - alert: HighMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemFree_bytes) / node_memory_MemTotal_bytes * 100 > 80
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High memory usage detected"
          description: "Memory usage on {{ $labels.instance }} is above 80%."
```

### Notification Configuration
Update `prometheus/alertmanager.yml` to configure how alerts are sent (e.g., email, Slack). Example for Slack:
```yaml
route:
  receiver: 'slack-notifications'

receivers:
  - name: 'slack-notifications'
    slack_configs:
      - api_url: 'https://hooks.slack.com/services/your/slack/webhook/url'
        channel: '#alerts'
```

---

## **Grafana Dashboards**
Pre-configured dashboards are automatically loaded into Grafana. You can also import additional dashboards:
1. Go to Grafana's web interface ([http://localhost:3000](http://localhost:3000)).
2. Navigate to **Dashboards > Manage > Import**.
3. Use the following dashboard IDs:
   - Node Exporter: `1860`
   - Caddy Exporter: Search for relevant dashboards on [Grafana Dashboards](https://grafana.com/grafana/dashboards/).

---

## **Stopping the Stack**
To stop all services, run:
```bash
docker-compose down
```

---

## **Contributing**
Feel free to contribute by opening issues or submitting pull requests. Suggestions for improvements are always welcome!

---

## **License**
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
