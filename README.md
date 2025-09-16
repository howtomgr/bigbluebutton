# bigbluebutton Installation Guide

bigbluebutton is a free and open-source web conferencing. BigBlueButton provides open source web conferencing for online learning

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
  - CPU: 8+ cores
  - RAM: 16GB minimum
  - Storage: 50GB for recordings
  - Network: HTTP/WebRTC
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 443 (default bigbluebutton port)
  - Various service ports
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

# Install bigbluebutton
sudo dnf install -y bigbluebutton

# Enable and start service
sudo systemctl enable --now bigbluebutton

# Configure firewall
sudo firewall-cmd --permanent --add-port=443/tcp
sudo firewall-cmd --reload

# Verify installation
bigbluebutton --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install bigbluebutton
sudo apt install -y bigbluebutton

# Enable and start service
sudo systemctl enable --now bigbluebutton

# Configure firewall
sudo ufw allow 443

# Verify installation
bigbluebutton --version
```

### Arch Linux

```bash
# Install bigbluebutton
sudo pacman -S bigbluebutton

# Enable and start service
sudo systemctl enable --now bigbluebutton

# Verify installation
bigbluebutton --version
```

### Alpine Linux

```bash
# Install bigbluebutton
apk add --no-cache bigbluebutton

# Enable and start service
rc-update add bigbluebutton default
rc-service bigbluebutton start

# Verify installation
bigbluebutton --version
```

### openSUSE/SLES

```bash
# Install bigbluebutton
sudo zypper install -y bigbluebutton

# Enable and start service
sudo systemctl enable --now bigbluebutton

# Configure firewall
sudo firewall-cmd --permanent --add-port=443/tcp
sudo firewall-cmd --reload

# Verify installation
bigbluebutton --version
```

### macOS

```bash
# Using Homebrew
brew install bigbluebutton

# Start service
brew services start bigbluebutton

# Verify installation
bigbluebutton --version
```

### FreeBSD

```bash
# Using pkg
pkg install bigbluebutton

# Enable in rc.conf
echo 'bigbluebutton_enable="YES"' >> /etc/rc.conf

# Start service
service bigbluebutton start

# Verify installation
bigbluebutton --version
```

### Windows

```bash
# Using Chocolatey
choco install bigbluebutton

# Or using Scoop
scoop install bigbluebutton

# Verify installation
bigbluebutton --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/bigbluebutton

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
bigbluebutton --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable bigbluebutton

# Start service
sudo systemctl start bigbluebutton

# Stop service
sudo systemctl stop bigbluebutton

# Restart service
sudo systemctl restart bigbluebutton

# Check status
sudo systemctl status bigbluebutton

# View logs
sudo journalctl -u bigbluebutton -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add bigbluebutton default

# Start service
rc-service bigbluebutton start

# Stop service
rc-service bigbluebutton stop

# Restart service
rc-service bigbluebutton restart

# Check status
rc-service bigbluebutton status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'bigbluebutton_enable="YES"' >> /etc/rc.conf

# Start service
service bigbluebutton start

# Stop service
service bigbluebutton stop

# Restart service
service bigbluebutton restart

# Check status
service bigbluebutton status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start bigbluebutton
brew services stop bigbluebutton
brew services restart bigbluebutton

# Check status
brew services list | grep bigbluebutton
```

### Windows Service Manager

```powershell
# Start service
net start bigbluebutton

# Stop service
net stop bigbluebutton

# Using PowerShell
Start-Service bigbluebutton
Stop-Service bigbluebutton
Restart-Service bigbluebutton

# Check status
Get-Service bigbluebutton
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream bigbluebutton_backend {
    server 127.0.0.1:443;
}

server {
    listen 80;
    server_name bigbluebutton.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name bigbluebutton.example.com;

    ssl_certificate /etc/ssl/certs/bigbluebutton.example.com.crt;
    ssl_certificate_key /etc/ssl/private/bigbluebutton.example.com.key;

    location / {
        proxy_pass http://bigbluebutton_backend;
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
    ServerName bigbluebutton.example.com
    Redirect permanent / https://bigbluebutton.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName bigbluebutton.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/bigbluebutton.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/bigbluebutton.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:443/
    ProxyPassReverse / http://127.0.0.1:443/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend bigbluebutton_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/bigbluebutton.pem
    redirect scheme https if !{ ssl_fc }
    default_backend bigbluebutton_backend

backend bigbluebutton_backend
    balance roundrobin
    server bigbluebutton1 127.0.0.1:443 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R bigbluebutton:bigbluebutton /etc/bigbluebutton
sudo chmod 750 /etc/bigbluebutton

# Configure firewall
sudo firewall-cmd --permanent --add-port=443/tcp
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
sudo systemctl status bigbluebutton

# View logs
sudo journalctl -u bigbluebutton -f

# Monitor resource usage
top -p $(pgrep bigbluebutton)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/bigbluebutton"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/bigbluebutton-backup-$DATE.tar.gz" /etc/bigbluebutton /var/lib/bigbluebutton

echo "Backup completed: $BACKUP_DIR/bigbluebutton-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop bigbluebutton

# Restore from backup
tar -xzf /backup/bigbluebutton/bigbluebutton-backup-*.tar.gz -C /

# Start service
sudo systemctl start bigbluebutton
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u bigbluebutton -n 100
sudo tail -f /var/log/bigbluebutton/bigbluebutton.log

# Check configuration
bigbluebutton --version

# Check permissions
ls -la /etc/bigbluebutton
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 443

# Test connectivity
telnet localhost 443

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep bigbluebutton)

# Check disk I/O
iotop -p $(pgrep bigbluebutton)

# Check connections
ss -an | grep 443
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  bigbluebutton:
    image: bigbluebutton:latest
    ports:
      - "443:443"
    volumes:
      - ./config:/etc/bigbluebutton
      - ./data:/var/lib/bigbluebutton
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update bigbluebutton

# Debian/Ubuntu
sudo apt update && sudo apt upgrade bigbluebutton

# Arch Linux
sudo pacman -Syu bigbluebutton

# Alpine Linux
apk update && apk upgrade bigbluebutton

# openSUSE
sudo zypper update bigbluebutton

# FreeBSD
pkg update && pkg upgrade bigbluebutton

# Always backup before updates
tar -czf /backup/bigbluebutton-pre-update-$(date +%Y%m%d).tar.gz /etc/bigbluebutton

# Restart after updates
sudo systemctl restart bigbluebutton
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/bigbluebutton

# Clean old logs
find /var/log/bigbluebutton -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/bigbluebutton
```

## Additional Resources

- Official Documentation: https://docs.bigbluebutton.org/
- GitHub Repository: https://github.com/bigbluebutton/bigbluebutton
- Community Forum: https://forum.bigbluebutton.org/
- Best Practices Guide: https://docs.bigbluebutton.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
