# xwiki Installation Guide

xwiki is a free and open-source enterprise wiki. XWiki provides powerful wiki platform with application development capabilities

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
  - Storage: 5GB for data
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8080 (default xwiki port)
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

# Install xwiki
sudo dnf install -y xwiki

# Enable and start service
sudo systemctl enable --now xwiki

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
xwiki --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install xwiki
sudo apt install -y xwiki

# Enable and start service
sudo systemctl enable --now xwiki

# Configure firewall
sudo ufw allow 8080

# Verify installation
xwiki --version
```

### Arch Linux

```bash
# Install xwiki
sudo pacman -S xwiki

# Enable and start service
sudo systemctl enable --now xwiki

# Verify installation
xwiki --version
```

### Alpine Linux

```bash
# Install xwiki
apk add --no-cache xwiki

# Enable and start service
rc-update add xwiki default
rc-service xwiki start

# Verify installation
xwiki --version
```

### openSUSE/SLES

```bash
# Install xwiki
sudo zypper install -y xwiki

# Enable and start service
sudo systemctl enable --now xwiki

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
xwiki --version
```

### macOS

```bash
# Using Homebrew
brew install xwiki

# Start service
brew services start xwiki

# Verify installation
xwiki --version
```

### FreeBSD

```bash
# Using pkg
pkg install xwiki

# Enable in rc.conf
echo 'xwiki_enable="YES"' >> /etc/rc.conf

# Start service
service xwiki start

# Verify installation
xwiki --version
```

### Windows

```bash
# Using Chocolatey
choco install xwiki

# Or using Scoop
scoop install xwiki

# Verify installation
xwiki --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/xwiki

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
xwiki --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable xwiki

# Start service
sudo systemctl start xwiki

# Stop service
sudo systemctl stop xwiki

# Restart service
sudo systemctl restart xwiki

# Check status
sudo systemctl status xwiki

# View logs
sudo journalctl -u xwiki -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add xwiki default

# Start service
rc-service xwiki start

# Stop service
rc-service xwiki stop

# Restart service
rc-service xwiki restart

# Check status
rc-service xwiki status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'xwiki_enable="YES"' >> /etc/rc.conf

# Start service
service xwiki start

# Stop service
service xwiki stop

# Restart service
service xwiki restart

# Check status
service xwiki status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start xwiki
brew services stop xwiki
brew services restart xwiki

# Check status
brew services list | grep xwiki
```

### Windows Service Manager

```powershell
# Start service
net start xwiki

# Stop service
net stop xwiki

# Using PowerShell
Start-Service xwiki
Stop-Service xwiki
Restart-Service xwiki

# Check status
Get-Service xwiki
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream xwiki_backend {
    server 127.0.0.1:8080;
}

server {
    listen 80;
    server_name xwiki.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name xwiki.example.com;

    ssl_certificate /etc/ssl/certs/xwiki.example.com.crt;
    ssl_certificate_key /etc/ssl/private/xwiki.example.com.key;

    location / {
        proxy_pass http://xwiki_backend;
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
    ServerName xwiki.example.com
    Redirect permanent / https://xwiki.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName xwiki.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/xwiki.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/xwiki.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend xwiki_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/xwiki.pem
    redirect scheme https if !{ ssl_fc }
    default_backend xwiki_backend

backend xwiki_backend
    balance roundrobin
    server xwiki1 127.0.0.1:8080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R xwiki:xwiki /etc/xwiki
sudo chmod 750 /etc/xwiki

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
sudo systemctl status xwiki

# View logs
sudo journalctl -u xwiki -f

# Monitor resource usage
top -p $(pgrep xwiki)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/xwiki"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/xwiki-backup-$DATE.tar.gz" /etc/xwiki /var/lib/xwiki

echo "Backup completed: $BACKUP_DIR/xwiki-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop xwiki

# Restore from backup
tar -xzf /backup/xwiki/xwiki-backup-*.tar.gz -C /

# Start service
sudo systemctl start xwiki
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u xwiki -n 100
sudo tail -f /var/log/xwiki/xwiki.log

# Check configuration
xwiki --version

# Check permissions
ls -la /etc/xwiki
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
top -p $(pgrep xwiki)

# Check disk I/O
iotop -p $(pgrep xwiki)

# Check connections
ss -an | grep 8080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  xwiki:
    image: xwiki:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/etc/xwiki
      - ./data:/var/lib/xwiki
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update xwiki

# Debian/Ubuntu
sudo apt update && sudo apt upgrade xwiki

# Arch Linux
sudo pacman -Syu xwiki

# Alpine Linux
apk update && apk upgrade xwiki

# openSUSE
sudo zypper update xwiki

# FreeBSD
pkg update && pkg upgrade xwiki

# Always backup before updates
tar -czf /backup/xwiki-pre-update-$(date +%Y%m%d).tar.gz /etc/xwiki

# Restart after updates
sudo systemctl restart xwiki
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/xwiki

# Clean old logs
find /var/log/xwiki -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/xwiki
```

## Additional Resources

- Official Documentation: https://docs.xwiki.org/
- GitHub Repository: https://github.com/xwiki/xwiki
- Community Forum: https://forum.xwiki.org/
- Best Practices Guide: https://docs.xwiki.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
