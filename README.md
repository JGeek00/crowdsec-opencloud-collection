# CrowdSec OpenCloud Collection

![CrowdSec](https://img.shields.io/badge/CrowdSec-Collection-blue)
![License](https://img.shields.io/badge/license-MIT-green)

A CrowdSec collection to detect and prevent bruteforce attacks against OpenCloud authentication.

## 📋 Description

This collection provides comprehensive protection against authentication bruteforce attacks targeting OpenCloud installations. It detects: 

- **Classic bruteforce attacks**: Multiple failed authentication attempts from a single IP
- **User enumeration**: Attempts to discover valid users/endpoints
- **Distributed bruteforce**: Coordinated attacks from multiple IPs

## 🚀 Installation

### From CrowdSec Hub (when published)

```bash
sudo cscli collections install jgeek00/opencloud
```

### Manual Installation

```bash
# Clone the repository
git clone https://github.com/jgeek00/crowdsec-opencloud-collection.git
cd crowdsec-opencloud-collection

# Copy files to CrowdSec directories
sudo cp -r parsers/* /etc/crowdsec/parsers/
sudo cp -r scenarios/* /etc/crowdsec/scenarios/
sudo cp -r collections/* /etc/crowdsec/collections/

# Restart CrowdSec
sudo systemctl restart crowdsec
```

## ⚙️ Configuration

### Configure log acquisition

Add to your `/etc/crowdsec/acquis.yaml`:

```yaml
---
filenames:
  - /var/log/opencloud/*.log
  - /path/to/your/opencloud.log
labels:
  type: opencloud
```

### Restart CrowdSec

```bash
sudo systemctl restart crowdsec
```

## 📊 Scenarios Included

### jgeek00/opencloud-bf
Detects classic bruteforce attacks: 
- **Trigger**: 5 failed auth attempts from the same IP in 100 seconds
- **Action**: Ban IP for 1 minute
- **MITRE ATT&CK**: T1110 (Brute Force)

### jgeek00/opencloud-bf_user-enum
Detects user enumeration attempts:
- **Trigger**: 5 different endpoints accessed from the same IP in 50 seconds
- **Action**: Ban IP for 1 minute
- **MITRE ATT&CK**: T1589 (Gather Victim Identity Information)

### jgeek00/opencloud-bf_distributed
Detects distributed bruteforce attacks: 
- **Trigger**: 10 different IPs attacking the same endpoint in 300 seconds
- **Action**: Ban all IPs for 5 minutes
- **MITRE ATT&CK**: T1110 (Brute Force)

## 🧪 Testing

### Verify installation

```bash
# Check parsers
sudo cscli parsers list | grep jgeek00

# Check scenarios
sudo cscli scenarios list | grep jgeek00

# View metrics
sudo cscli metrics
```

## 📝 Log Format

The parser expects OpenCloud logs in JSON format: 

```json
{
  "level": "error",
  "service": "proxy",
  "error": "failed to verify access token: token signature is invalid: crypto/rsa: verification error",
  "authenticator": "oidc",
  "path": "/api/v0/settings/roles-list",
  "user_agent": "Mozilla/5.0...",
  "client.address": "95.127.180.157",
  "time": "2026-01-18T16:25:36Z",
  "message": "failed to authenticate the request"
}
```

## 🔧 Customization

### Adjust sensitivity

Edit `/etc/crowdsec/scenarios/jgeek00/opencloud-bf.yaml`:

```yaml
# More strict (ban after 3 attempts)
capacity: 3
leakspeed: "10s"

# More permissive (ban after 10 attempts)
capacity: 10
leakspeed: "30s"
```

### Change ban duration

```yaml
# Ban for 1 hour
blackhole: 1h

# Ban for 24 hours
blackhole: 24h
```

## 📄 License

MIT License - see LICENSE file for details

## 🔗 Links

- [CrowdSec Hub](https://hub.crowdsec.net)
- [CrowdSec Documentation](https://docs.crowdsec.net)
- [MITRE ATT&CK Framework](https://attack.mitre.org)

---

**Author**: JGeek00  
**Version**: 1.0.0  
**Last Updated**: 2026-01-18