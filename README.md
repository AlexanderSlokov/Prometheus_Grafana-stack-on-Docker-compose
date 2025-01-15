
# Usage Instructions

## 1. Overview
This Docker Compose stack includes the following monitoring services:
- **Prometheus:** Metrics collection and storage.
- **Alertmanager:** Handles alerts triggered by Prometheus.
- **Grafana:** Visualizes metrics with custom dashboards.
- **Node Exporter:** Collects hardware and OS-level metrics from the host.
- **cAdvisor:** Monitors running containers and their resource usage.

## 2. How to Start
1. Clone this repository to your local machine.
2. **Review and update the following configuration files:**
   - `prometheus.yml`: Configures Prometheus scraping jobs.
   - `alertmanager.yml`: Configures alert channels (email, Slack, etc.).
   - `grafana.ini`: Customizes Grafana settings (optional).
3. Run the stack:
   ```bash
   docker compose up -d
   ```
4. Access the services:
   - Grafana: [http://localhost:3000](http://localhost:3000)
   - Prometheus: [http://localhost:9090](http://localhost:9090)
   - Alertmanager: [http://localhost:9093](http://localhost:9093)
   - cAdvisor: [http://localhost:8080](http://localhost:8080)
   - Node Exporter metrics: [http://localhost:9100/metrics](http://localhost:9100/metrics)

## 3. Precautions for Production
**Security is critical when exposing monitoring services on the Internet. Follow these best practices:**
1. Use a reverse proxy (e.g., Nginx) with SSL (HTTPS) to secure access to Grafana and Prometheus.
2. **Do NOT expose Node Exporter and cAdvisor to the Internet** as they contain sensitive system information.
3. Change the default Grafana admin password (`admin/admin`).
   - Update the `GF_SECURITY_ADMIN_USER` and `GF_SECURITY_ADMIN_PASSWORD` environment variables in `docker-compose.yml`.

4. Set up **firewall rules** using UFW or similar tools to restrict access.
5. Use tools like **Fail2ban** to prevent brute-force attacks.

## 4. Customizing Configurations
### Prometheus (`prometheus.yml`):
This file configures what Prometheus will scrape. Example:
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
  - job_name: 'node-exporter'
    static_configs:
      - targets: ['node-exporter:9100']
  - job_name: 'cadvisor'
    static_configs:
      - targets: ['cadvisor:8080']
  - job_name: 'alertmanager'
    static_configs:
      - targets: ['alertmanager:9093']
```

### Alertmanager (`alertmanager.yml`):
Configure how alerts are routed. Example for email alerts:
```yaml
global:
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'your_email@gmail.com'
  smtp_auth_username: 'your_email@gmail.com'
  smtp_auth_password: 'your_password'

route:
  receiver: 'email-alert'

receivers:
  - name: 'email-alert'
    email_configs:
      - to: 'your_alert_email@gmail.com'
```

### Grafana (`grafana.ini`):
You can customize Grafana settings, such as enabling HTTPS:
```ini
[server]
protocol = https
cert_file = /etc/grafana/ssl/grafana.crt
cert_key = /etc/grafana/ssl/grafana.key
```

## 5. Logs and Troubleshooting
To check the logs of a service:
```bash
docker logs <container_name>
```
Example:
```bash
docker logs prometheus
```

To restart the stack:
```bash
docker compose down && docker compose up -d
```

## 6. Volumes and Data Persistence
**Important:** Data for Prometheus and Grafana is stored in the following directories:
- `./prometheus_data`: Prometheus metrics.
- `./grafana_data`: Grafana dashboards and settings.

Ensure these directories are backed up regularly.

## 7. Next Steps
1. Explore the metrics in Prometheus and Grafana.
2. Create custom dashboards in Grafana to monitor your infrastructure.
3. Test alerts using Alertmanager to ensure proper notifications.

**Happy Monitoring!** 😊

