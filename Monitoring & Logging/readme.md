
Here’s a structured approach to setting up **Prometheus & Grafana** for monitoring and logging:

---

## **Step 1: Install Prometheus**
1. **Download Prometheus:**
   - Go to [Prometheus Downloads](https://prometheus.io/download/)
   - Download the appropriate version for your OS
   - Extract the files and navigate to the directory

2. **Configure Prometheus:**
   - Edit `prometheus.yml` and define scrape jobs:
     ```yaml
     global:
       scrape_interval: 15s  # Adjust based on your needs

     scrape_configs:
       - job_name: 'application'
         static_configs:
           - targets: ['localhost:9090']  # Replace with your app's endpoint
     ```

3. **Run Prometheus:**
   ```bash
   ./prometheus --config.file=prometheus.yml
   ```

4. **Verify Setup:**
   - Open `http://localhost:9090` to access the Prometheus UI.

---

## **Step 2: Install Grafana**
1. **Download & Install Grafana:**
   - Get Grafana from [Grafana Downloads](https://grafana.com/grafana/download)
   - Install it using:
     ```bash
     sudo apt-get install -y grafana  # Ubuntu
     brew install grafana             # macOS
     ```

2. **Start Grafana:**
   ```bash
   sudo systemctl start grafana-server
   sudo systemctl enable grafana-server
   ```

3. **Access Grafana:**
   - Open `http://localhost:3000`
   - Default login: `admin / admin`

---

## **Step 3: Connect Prometheus to Grafana**
1. **Add Data Source:**
   - Navigate to **Grafana → Configuration → Data Sources**
   - Select **Prometheus**
   - Set **URL**: `http://localhost:9090`
   - Click **Save & Test**

2. **Create a Dashboard:**
   - Go to **Dashboards → New Dashboard → Add a Panel**
   - Use a PromQL query like:
     ```promql
     rate(http_requests_total[5m])
     ```
   - Save the dashboard.

---

## **Step 4: Set Up Alerts**
1. **Create Alerting Rules in Prometheus:**
   - Edit `alert.rules.yml`:
     ```yaml
     groups:
       - name: application_alerts
         rules:
           - alert: HighErrorRate
             expr: rate(http_requests_total{status="500"}[5m]) > 5
             for: 1m
             labels:
               severity: critical
             annotations:
               summary: "High error rate detected"
     ```

2. **Enable Alertmanager (Optional):**
   - Install Alertmanager to send notifications via Slack, Email, etc.

3. **Configure Alerts in Grafana:**
   - Go to **Alerting → Create Alert Rule**
   - Set conditions (e.g., error rate > threshold)
   - Configure **Notification Channels** (Email, Slack, etc.)

---

![Screenshot 2024-02-17 120919](https://github.com/user-attachments/assets/3c209009-41d1-4d2d-9355-0c2cb15e3402)
![Screenshot 2024-02-17 120857](https://github.com/user-attachments/assets/63e4464b-8be6-4ed9-a232-6e7465bf72d7)
