# gitbucket Installation Guide

gitbucket is a free and open-source Git platform powered by Scala. GitBucket provides a GitHub-like interface powered by Scala

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
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 5GB for repos
  - Network: HTTP/SSH access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8080 (default gitbucket port)
  - SSH on port 29418
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

# Install gitbucket
sudo dnf install -y gitbucket

# Enable and start service
sudo systemctl enable --now gitbucket

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
gitbucket --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install gitbucket
sudo apt install -y gitbucket

# Enable and start service
sudo systemctl enable --now gitbucket

# Configure firewall
sudo ufw allow 8080

# Verify installation
gitbucket --version
```

### Arch Linux

```bash
# Install gitbucket
sudo pacman -S gitbucket

# Enable and start service
sudo systemctl enable --now gitbucket

# Verify installation
gitbucket --version
```

### Alpine Linux

```bash
# Install gitbucket
apk add --no-cache gitbucket

# Enable and start service
rc-update add gitbucket default
rc-service gitbucket start

# Verify installation
gitbucket --version
```

### openSUSE/SLES

```bash
# Install gitbucket
sudo zypper install -y gitbucket

# Enable and start service
sudo systemctl enable --now gitbucket

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
gitbucket --version
```

### macOS

```bash
# Using Homebrew
brew install gitbucket

# Start service
brew services start gitbucket

# Verify installation
gitbucket --version
```

### FreeBSD

```bash
# Using pkg
pkg install gitbucket

# Enable in rc.conf
echo 'gitbucket_enable="YES"' >> /etc/rc.conf

# Start service
service gitbucket start

# Verify installation
gitbucket --version
```

### Windows

```bash
# Using Chocolatey
choco install gitbucket

# Or using Scoop
scoop install gitbucket

# Verify installation
gitbucket --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/gitbucket

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
gitbucket --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable gitbucket

# Start service
sudo systemctl start gitbucket

# Stop service
sudo systemctl stop gitbucket

# Restart service
sudo systemctl restart gitbucket

# Check status
sudo systemctl status gitbucket

# View logs
sudo journalctl -u gitbucket -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add gitbucket default

# Start service
rc-service gitbucket start

# Stop service
rc-service gitbucket stop

# Restart service
rc-service gitbucket restart

# Check status
rc-service gitbucket status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'gitbucket_enable="YES"' >> /etc/rc.conf

# Start service
service gitbucket start

# Stop service
service gitbucket stop

# Restart service
service gitbucket restart

# Check status
service gitbucket status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start gitbucket
brew services stop gitbucket
brew services restart gitbucket

# Check status
brew services list | grep gitbucket
```

### Windows Service Manager

```powershell
# Start service
net start gitbucket

# Stop service
net stop gitbucket

# Using PowerShell
Start-Service gitbucket
Stop-Service gitbucket
Restart-Service gitbucket

# Check status
Get-Service gitbucket
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream gitbucket_backend {
    server 127.0.0.1:8080;
}

server {
    listen 80;
    server_name gitbucket.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name gitbucket.example.com;

    ssl_certificate /etc/ssl/certs/gitbucket.example.com.crt;
    ssl_certificate_key /etc/ssl/private/gitbucket.example.com.key;

    location / {
        proxy_pass http://gitbucket_backend;
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
    ServerName gitbucket.example.com
    Redirect permanent / https://gitbucket.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName gitbucket.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/gitbucket.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/gitbucket.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend gitbucket_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/gitbucket.pem
    redirect scheme https if !{ ssl_fc }
    default_backend gitbucket_backend

backend gitbucket_backend
    balance roundrobin
    server gitbucket1 127.0.0.1:8080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R gitbucket:gitbucket /etc/gitbucket
sudo chmod 750 /etc/gitbucket

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
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
sudo systemctl status gitbucket

# View logs
sudo journalctl -u gitbucket -f

# Monitor resource usage
top -p $(pgrep gitbucket)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/gitbucket"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/gitbucket-backup-$DATE.tar.gz" /etc/gitbucket /var/lib/gitbucket

echo "Backup completed: $BACKUP_DIR/gitbucket-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop gitbucket

# Restore from backup
tar -xzf /backup/gitbucket/gitbucket-backup-*.tar.gz -C /

# Start service
sudo systemctl start gitbucket
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u gitbucket -n 100
sudo tail -f /var/log/gitbucket/gitbucket.log

# Check configuration
gitbucket --version

# Check permissions
ls -la /etc/gitbucket
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8080

# Test connectivity
telnet localhost 8080

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep gitbucket)

# Check disk I/O
iotop -p $(pgrep gitbucket)

# Check connections
ss -an | grep 8080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  gitbucket:
    image: gitbucket:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/etc/gitbucket
      - ./data:/var/lib/gitbucket
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update gitbucket

# Debian/Ubuntu
sudo apt update && sudo apt upgrade gitbucket

# Arch Linux
sudo pacman -Syu gitbucket

# Alpine Linux
apk update && apk upgrade gitbucket

# openSUSE
sudo zypper update gitbucket

# FreeBSD
pkg update && pkg upgrade gitbucket

# Always backup before updates
tar -czf /backup/gitbucket-pre-update-$(date +%Y%m%d).tar.gz /etc/gitbucket

# Restart after updates
sudo systemctl restart gitbucket
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/gitbucket

# Clean old logs
find /var/log/gitbucket -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/gitbucket
```

## Additional Resources

- Official Documentation: https://docs.gitbucket.org/
- GitHub Repository: https://github.com/gitbucket/gitbucket
- Community Forum: https://forum.gitbucket.org/
- Best Practices Guide: https://docs.gitbucket.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
