# DoD Contractor Site Network

**Deployment:** Government Contractor Infrastructure (March 2025)  
**Security Level:** DoD contractor compliance  
**Status:** Active, 0 audit findings  

## Overview

This network supports a DoD contractor office requiring strict security compliance, network segmentation, and guest isolation. The infrastructure follows government security best practices while maintaining usability for staff.

## Security Requirements Met

- ✅ Network segmentation (secure vs guest)
- ✅ WPA3-Enterprise authentication
- ✅ No cross-VLAN routing between secure and guest
- ✅ MAC filtering on administrative access
- ✅ Encrypted management traffic only
- ✅ Regular security audits and updates
- ✅ Detailed logging and monitoring

## Network Architecture

### Physical Topology

```
Internet (Fiber 250Mbps)
    ↓
Ubiquiti EdgeRouter 4
    ↓
UniFi Switch 24 PoE (managed)
    ↓
├── Secure Network (VLAN 10)
│   ├── Staff workstations
│   ├── Secure file server
│   └── IP phones
│
├── Management Network (VLAN 99)
│   ├── Network equipment management
│   └── Admin workstation only
│
└── Guest Network (VLAN 200)
    └── Visitor WiFi (completely isolated)
```

### Equipment

**Edge/Routing:**
- 1x Ubiquiti EdgeRouter 4 (main gateway/firewall)

**Switching:**
- 1x UniFi Switch 24 PoE (core distribution)
- 2x UniFi Switch 8 PoE (desk aggregation)

**Wireless:**
- 4x UniFi AP AC Pro
- 1x UniFi Cloud Key Gen2+ (controller)

**Security:**
- RADIUS server (FreeRADIUS on Linux VM)
- Network monitoring (PRTG on Windows VM)

## VLAN Design

| VLAN | Network | Purpose | Firewall Zone | Internet Access |
|------|---------|---------|---------------|-----------------|
| 10 | 10.10.10.0/24 | Secure Network | TRUSTED | Yes (filtered) |
| 99 | 10.10.99.0/24 | Management | MANAGEMENT | No |
| 200 | 10.10.200.0/24 | Guest WiFi | UNTRUSTED | Yes (unrestricted) |

## Security Policies

### Firewall Rules Summary

**Secure Network (VLAN 10):**
- ✅ Outbound: Allowed to Internet (content filtered)
- ✅ Inbound: Blocked from Internet
- ❌ Cannot access Guest network
- ✅ Can access Management (IT staff only)
- ✅ All traffic logged

**Guest Network (VLAN 200):**
- ✅ Outbound: Internet only
- ❌ Cannot access Secure network
- ❌ Cannot access Management network
- ❌ Cannot see other guest devices
- ⏱️ 4-hour session timeout

**Management Network (VLAN 99):**
- ✅ Full access to all VLANs (for administration)
- ❌ No Internet access (air-gapped for security)
- ✅ Access restricted by MAC address whitelist

### WiFi Security

**Secure SSID:** `[COMPANY]-Secure`
- WPA3-Enterprise
- 802.1X authentication via RADIUS
- Individual user credentials
- Device certificates required
- Session timeout: 12 hours

**Guest SSID:** `[COMPANY]-Guest`
- WPA2-PSK (rotating daily password)
- Captive portal with acceptable use policy
- Bandwidth limit: 10Mbps per device
- Session timeout: 4 hours

**Hidden Management SSID:** `[COMPANY]-Admin`
- WPA3-Enterprise
- MAC address whitelist (5 devices max)
- Admin credentials + certificate
- Only accessible in IT office area

## Content Filtering

### Allowed Categories
- Business and productivity
- Government and legal
- Technology and computing
- News and media
- Professional development

### Blocked Categories
- Social media (except LinkedIn)
- Streaming media
- Gaming
- File sharing
- Explicit content
- Anonymous proxies/VPNs

### Special Exceptions
- YouTube restricted mode allowed for training
- Cloud storage limited to OneDrive/SharePoint
- Video conferencing (Teams, Zoom) allowed

## Monitoring & Compliance

### What Gets Logged

**Network Events:**
- All firewall rule hits/blocks
- DHCP assignments
- Authentication attempts (success/failure)
- VPN connections
- Port scans or suspicious activity

**Retention:**
- Real-time alerts: Immediate notification
- Security logs: 90 days
- Traffic stats: 30 days
- Configuration changes: Indefinite

### Compliance Checks

**Monthly:**
- Review firewall logs for anomalies
- Verify no unauthorized devices
- Check encryption status
- Test guest network isolation

**Quarterly:**
- Firmware updates on all equipment
- Password rotation
- Access list review
- Penetration testing

**Annually:**
- Full security audit
- Equipment replacement assessment
- Policy review and updates

## Incident Response

### Procedure

1. **Detection:** Automated alerts or manual discovery
2. **Isolation:** Quarantine affected VLAN/device
3. **Analysis:** Review logs, determine scope
4. **Remediation:** Remove threat, patch vulnerability
5. **Recovery:** Restore normal operations
6. **Documentation:** Complete incident report

### Common Scenarios

**Unauthorized device on secure network:**
- Automatic DHCP deny
- MAC not in whitelist = blocked
- Alert sent to admin

**Suspected malware:**
- Isolate device at switch level
- Block outbound connections
- Scan with network-based AV
- Reimage if necessary

**Guest trying to access secure network:**
- Firewall automatically blocks
- Log attempt with source MAC
- If repeated: Ban MAC for 24 hours

## Deployment Notes

### What Worked Well

1. **RADIUS authentication** - Central user management is crucial
2. **Completely isolated guest** - Prevented several drive-by attacks
3. **MAC filtering on management** - Stopped unauthorized admin access
4. **Regular audits** - Caught config drift before it became an issue

### Challenges Faced

1. **User onboarding** - WPA3-Enterprise has learning curve
   - Solution: Created detailed setup guides per device type
   
2. **Certificate management** - Initial RADIUS setup was complex
   - Solution: Automated cert renewal, documentation improved

3. **Guest WiFi complaints** - Users wanted faster speeds
   - Solution: Explained security policy, didn't budge on limits

## Compliance Documentation

**Standards met:**
- NIST 800-171 (partial - relevant controls)
- General security best practices
- Guest network isolation requirements

**Note:** This is not a full NIST 800-171 implementation. For true DoD compliance, additional controls are required (encryption at rest, MFA, continuous monitoring, etc.)

---

*Deployed: March 2025 | Last audit: January 2026 | Status: 0 findings*
