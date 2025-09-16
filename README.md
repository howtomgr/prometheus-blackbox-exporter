# blackbox-exporter Installation Guide

blackbox-exporter is a free and open-source Prometheus exporter for probing endpoints. Blackbox Exporter allows blackbox probing of endpoints over HTTP, HTTPS, DNS, TCP and ICMP, essential for monitoring external services with Prometheus

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 128MB minimum
  - Storage: 100MB for installation
  - Network: Network access to probe targets
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 9115 (default blackbox-exporter port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install blackbox-exporter
sudo dnf install -y prometheus-blackbox-exporter

# Enable and start service
sudo systemctl enable --now blackbox_exporter

# Configure firewall
sudo firewall-cmd --permanent --add-port=9115/tcp
sudo firewall-cmd --reload

# Verify installation
blackbox_exporter --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install blackbox-exporter
sudo apt install -y prometheus-blackbox-exporter

# Enable and start service
sudo systemctl enable --now blackbox_exporter

# Configure firewall
sudo ufw allow 9115

# Verify installation
blackbox_exporter --version
```

### Arch Linux

```bash
# Install blackbox-exporter
sudo pacman -S prometheus-blackbox-exporter

# Enable and start service
sudo systemctl enable --now blackbox_exporter

# Verify installation
blackbox_exporter --version
```

### Alpine Linux

```bash
# Install blackbox-exporter
apk add --no-cache prometheus-blackbox-exporter

# Enable and start service
rc-update add blackbox_exporter default
rc-service blackbox_exporter start

# Verify installation
blackbox_exporter --version
```

### openSUSE/SLES

```bash
# Install blackbox-exporter
sudo zypper install -y prometheus-blackbox-exporter

# Enable and start service
sudo systemctl enable --now blackbox_exporter

# Configure firewall
sudo firewall-cmd --permanent --add-port=9115/tcp
sudo firewall-cmd --reload

# Verify installation
blackbox_exporter --version
```

### macOS

```bash
# Using Homebrew
brew install prometheus-blackbox-exporter

# Start service
brew services start prometheus-blackbox-exporter

# Verify installation
blackbox_exporter --version
```

### FreeBSD

```bash
# Using pkg
pkg install prometheus-blackbox-exporter

# Enable in rc.conf
echo 'blackbox_exporter_enable="YES"' >> /etc/rc.conf

# Start service
service blackbox_exporter start

# Verify installation
blackbox_exporter --version
```

### Windows

```bash
# Using Chocolatey
choco install prometheus-blackbox-exporter

# Or using Scoop
scoop install prometheus-blackbox-exporter

# Verify installation
blackbox_exporter --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/prometheus-blackbox-exporter

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
blackbox_exporter --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable blackbox_exporter

# Start service
sudo systemctl start blackbox_exporter

# Stop service
sudo systemctl stop blackbox_exporter

# Restart service
sudo systemctl restart blackbox_exporter

# Check status
sudo systemctl status blackbox_exporter

# View logs
sudo journalctl -u blackbox_exporter -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add blackbox_exporter default

# Start service
rc-service blackbox_exporter start

# Stop service
rc-service blackbox_exporter stop

# Restart service
rc-service blackbox_exporter restart

# Check status
rc-service blackbox_exporter status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'blackbox_exporter_enable="YES"' >> /etc/rc.conf

# Start service
service blackbox_exporter start

# Stop service
service blackbox_exporter stop

# Restart service
service blackbox_exporter restart

# Check status
service blackbox_exporter status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start prometheus-blackbox-exporter
brew services stop prometheus-blackbox-exporter
brew services restart prometheus-blackbox-exporter

# Check status
brew services list | grep prometheus-blackbox-exporter
```

### Windows Service Manager

```powershell
# Start service
net start blackbox_exporter

# Stop service
net stop blackbox_exporter

# Using PowerShell
Start-Service blackbox_exporter
Stop-Service blackbox_exporter
Restart-Service blackbox_exporter

# Check status
Get-Service blackbox_exporter
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream prometheus-blackbox-exporter_backend {
    server 127.0.0.1:9115;
}

server {
    listen 80;
    server_name prometheus-blackbox-exporter.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name prometheus-blackbox-exporter.example.com;

    ssl_certificate /etc/ssl/certs/prometheus-blackbox-exporter.example.com.crt;
    ssl_certificate_key /etc/ssl/private/prometheus-blackbox-exporter.example.com.key;

    location / {
        proxy_pass http://prometheus-blackbox-exporter_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName prometheus-blackbox-exporter.example.com
    Redirect permanent / https://prometheus-blackbox-exporter.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName prometheus-blackbox-exporter.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/prometheus-blackbox-exporter.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/prometheus-blackbox-exporter.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:9115/
    ProxyPassReverse / http://127.0.0.1:9115/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend prometheus-blackbox-exporter_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/prometheus-blackbox-exporter.pem
    redirect scheme https if !{ ssl_fc }
    default_backend prometheus-blackbox-exporter_backend

backend prometheus-blackbox-exporter_backend
    balance roundrobin
    server prometheus-blackbox-exporter1 127.0.0.1:9115 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R prometheus-blackbox-exporter:prometheus-blackbox-exporter /etc/prometheus-blackbox-exporter
sudo chmod 750 /etc/prometheus-blackbox-exporter

# Configure firewall
sudo firewall-cmd --permanent --add-port=9115/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status blackbox_exporter

# View logs
sudo journalctl -u blackbox_exporter -f

# Monitor resource usage
top -p $(pgrep prometheus-blackbox-exporter)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/prometheus-blackbox-exporter"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/prometheus-blackbox-exporter-backup-$DATE.tar.gz" /etc/prometheus-blackbox-exporter /var/lib/prometheus-blackbox-exporter

echo "Backup completed: $BACKUP_DIR/prometheus-blackbox-exporter-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop blackbox_exporter

# Restore from backup
tar -xzf /backup/prometheus-blackbox-exporter/prometheus-blackbox-exporter-backup-*.tar.gz -C /

# Start service
sudo systemctl start blackbox_exporter
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u blackbox_exporter -n 100
sudo tail -f /var/log/prometheus-blackbox-exporter/prometheus-blackbox-exporter.log

# Check configuration
blackbox_exporter --version

# Check permissions
ls -la /etc/prometheus-blackbox-exporter
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 9115

# Test connectivity
telnet localhost 9115

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep prometheus-blackbox-exporter)

# Check disk I/O
iotop -p $(pgrep prometheus-blackbox-exporter)

# Check connections
ss -an | grep 9115
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  prometheus-blackbox-exporter:
    image: prometheus-blackbox-exporter:latest
    ports:
      - "9115:9115"
    volumes:
      - ./config:/etc/prometheus-blackbox-exporter
      - ./data:/var/lib/prometheus-blackbox-exporter
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update prometheus-blackbox-exporter

# Debian/Ubuntu
sudo apt update && sudo apt upgrade prometheus-blackbox-exporter

# Arch Linux
sudo pacman -Syu prometheus-blackbox-exporter

# Alpine Linux
apk update && apk upgrade prometheus-blackbox-exporter

# openSUSE
sudo zypper update prometheus-blackbox-exporter

# FreeBSD
pkg update && pkg upgrade prometheus-blackbox-exporter

# Always backup before updates
tar -czf /backup/prometheus-blackbox-exporter-pre-update-$(date +%Y%m%d).tar.gz /etc/prometheus-blackbox-exporter

# Restart after updates
sudo systemctl restart blackbox_exporter
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/prometheus-blackbox-exporter

# Clean old logs
find /var/log/prometheus-blackbox-exporter -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/prometheus-blackbox-exporter
```

## Additional Resources

- Official Documentation: https://docs.prometheus-blackbox-exporter.org/
- GitHub Repository: https://github.com/prometheus-blackbox-exporter/prometheus-blackbox-exporter
- Community Forum: https://forum.prometheus-blackbox-exporter.org/
- Best Practices Guide: https://docs.prometheus-blackbox-exporter.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
