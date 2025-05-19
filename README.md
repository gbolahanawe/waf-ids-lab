# WAF and IDS/IPS Lab with pfSense, ModSecurity & Snort

This lab demonstrates how a layered security architecture can help detect and block suspicious web traffic through the use of a Web Application Firewall (WAF) and Intrusion Detection/Prevention System (IDS/IPS).

## 🔧 Lab Environment

```
[Client Browser] ──> [Reverse Proxy WAF] ──> [Windows Web Server]
                        ↑
            (Kali/Ubuntu with NGINX + ModSecurity)
```

- **Reverse Proxy WAF**: Kali/Ubuntu running NGINX + ModSecurity (with OWASP CRS)
- **Web Server**: Windows IIS Server at IP `172.16.50.254`
- **Network**: Internal network segment for controlled testing

## ⚙️ 1. Setting Up NGINX + ModSecurity on Kali

### 🧪 Enable ModSecurity

1. Install required packages:
   ```bash
   sudo apt install nginx libnginx-mod-security
   ```

2. Enable ModSecurity:
   ```bash
   sudo cp /etc/modsecurity/modsecurity.conf-recommended /etc/modsecurity/modsecurity.conf
   sudo nano /etc/modsecurity/modsecurity.conf
   ```
   Set:
   ```
   SecRuleEngine On
   ```

3. Configure NGINX to use ModSecurity:
   ```bash
   sudo nano /etc/nginx/modsec/main.conf
   ```

4. Include OWASP CRS rules:
   ```bash
   git clone https://github.com/coreruleset/coreruleset.git /etc/nginx/modsec/crs
   ln -s /etc/nginx/modsec/crs/crs-setup.conf.example /etc/nginx/modsec/crs/crs-setup.conf
   ```

5. Update `nginx.conf`:
   ```nginx
   modsecurity on;
   modsecurity_rules_file /etc/nginx/modsec/main.conf;
   ```

6. Restart NGINX:
   ```bash
   sudo systemctl restart nginx
   ```

## 🧰 2. Troubleshooting ModSecurity

- **Check if rules are loading**:
  ```bash
  sudo nginx -t
  sudo tail -f /var/log/modsec_audit.log
  ```

- **Verify ModSecurity Logs**:
  ```bash
  grep --color -i "ModSecurity" /var/log/nginx/error.log
  ```

- **NGINX Proxy Configuration**:
  ```nginx
  location / {
      proxy_pass http://172.16.50.254;
      include proxy_params;
  }
  ```

## 📎 Files in This Repo

- `pfSense-config.xml`: Exported pfSense settings (with Snort/Suricata enabled)
- `modsecurity-rules/`: Custom or modified OWASP rules
- `snort-alerts.log`: Example alerts from IDS/IPS layer
- `setup-diagram.png`: Visual architecture of the lab
- `reverse-proxy-config.txt`: NGINX ModSecurity proxy settings

## 📌 Objective

- Simulate common evasion techniques like payload obfuscation, request splitting, and header manipulation
- Detect suspicious traffic using WAF and IDS tools
- Understand limitations and blind spots of each layer

## 👨‍💻 Author

Gbolahan Awe – Junior Penetration Tester | Cybersecurity Researcher  
🔗 [LinkedIn](https://www.linkedin.com/in/gbolahan-awe) | 📧 gbolahan.awe@gmail.com
