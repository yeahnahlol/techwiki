---
title: Dahua
description: Comprehensive reference for Dahua CCTV systems including setup, configuration, and troubleshooting
tags:
  - cctv
  - dahua
  - security
  - networking
  - surveillance
---

# Dahua CCTV Technician Cheatsheet

!!! tip "Quick Navigation"
    - [Quick Reference](#quick-reference)
    - [Default Credentials](#default-credentials)
    - [Network Configuration](#network-configuration)
    - [Camera Setup](#camera-setup)
    - [NVR Configuration](#nvr-configuration)
    - [Common Commands](#common-commands)
    - [Troubleshooting](#troubleshooting)
    - [Mobile Access](#mobile-access)

## Quick Reference

=== "Network Ports"
    | Port     | Protocol | Service         |
    |----------|----------|-----------------|
    | 80       | TCP      | HTTP            |
    | 443      | TCP      | HTTPS           |
    | 554      | TCP/UDP  | RTSP            |
    | 80       | TCP      | ONVIF           |
    | 37777    | TCP      | Dahua P2P       |
    | 443      | TCP      | Dahua Cloud     |
    | 37777    | UDP      | Dahua Media     |
    | 37777    | TCP      | Mobile Access   |

=== "Common IP Ranges"
    - `192.168.1.x` - Default network
    - `10.0.0.x` - Common alternative network
    - `172.16.0.x` - Common alternative network

## Default Credentials

!!! danger "Security Notice"
    Always change default credentials immediately after first login. These are well-known defaults. Newer Dahua firmware enforces password changes on first login.

=== "Common Defaults"
    | Username | Password | Access Level |
    |----------|----------|--------------|
    | admin    | admin    | Administrator|
    | admin    | 123456   | Administrator|
    | admin    | 1111111  | Administrator|
    | operator | 1234     | Operator     |
    | 888888   | 888888   | User         |

=== "Older Models"
    ```yaml
    # Common Default Combinations
    - admin / admin
    - admin / 123456
    - admin / 1111111
    - admin / 888888
    - admin / 666666
    - admin / admin123
    - admin / 12345
    - admin / 1234
    ```

## Network Configuration

### IP Address Assignment

=== "DHCP (Default)"
    ```bash
    # Most devices come with DHCP enabled by default
    # To verify IP assignment:
    ipconfig /all      # Windows
    ifconfig           # Linux/macOS
    
    # Find Dahua devices on network:
    arp -a | findstr "dahua"
    ```

=== "Static IP"
    ```bash
    1. Access web interface (http://[device-ip])
    2. Navigate to Configuration > Network > TCP/IP
    3. Configure settings:
       - IP Address: 192.168.1.x
       - Subnet Mask: 255.255.255.0
       - Gateway: 192.168.1.1
       - DNS: 8.8.8.8 / 8.8.4.4
    4. Apply changes and reboot if required
    ```

### Port Forwarding

!!! warning "Security Consideration"
    Consider using VPN instead of port forwarding for remote access. If port forwarding is necessary, use non-standard external ports.

=== "Router Configuration"
    ```bash
    External Port: 80 (HTTP) or 443 (HTTPS)
    Internal Port: 80
    Protocol: TCP
    Internal IP: [Device IP]
    ```

=== "Firewall Rules"
    ```bash
    # Allow incoming traffic
    iptables -A INPUT -p tcp --dport 80 -j ACCEPT
    iptables -A INPUT -p tcp --dport 443 -j ACCEPT
    iptables -A INPUT -p tcp --dport 554 -j ACCEPT
    iptables -A INPUT -p tcp --dport 37777 -j ACCEPT
    ```

## Camera Setup

### Initial Setup

!!! step "1. Physical Installation"
    - Mount the camera in desired location
    - Connect to power (PoE or separate power supply)
    - Verify network connectivity with link lights

!!! step "2. Device Detection"
    ```bash
    # Use Dahua ConfigTool to find the camera
    # Download from: https://www.dahuasecurity.com/support/tools
    # Run as administrator for full functionality
    
    # Alternative: Use arp-scan (Linux/macOS)
    sudo arp-scan --localnet | grep -i dahua
    ```

!!! step "3. Initial Configuration"
    1. Access web interface at `http://[camera-ip]`
    2. Complete first-time setup wizard
    3. Set strong admin password (12+ characters recommended)
    4. Configure network settings (static IP recommended)
    5. Set correct time zone and NTP server

### Camera Settings

=== "Video Settings"
    ```yaml
    # Recommended Settings:
    Resolution: 1080p (1920x1080) or higher
    Frame Rate: 15-30 FPS
    Bitrate: 4096-8192 kbps
    Compression: H.265+ (recommended)
    I-Frame Interval: 30 (or 2x frame rate)
    ```

=== "Image Settings"
    ```yaml
    # Day/Night Settings:
    Mode: Auto
    ICR: Auto
    BLC: On for backlit scenes
    WDR: On (adjust level as needed)
    
    # Image Adjustment:
    Brightness: 50
    Contrast: 50
    Saturation: 50
    Sharpness: 50
    ```

=== "Network Settings"
    ```yaml
    # Recommended Configuration:
    IP Assignment: Static
    MTU: 1500 (default)
    QoS: Enable for video traffic
    HTTP/HTTPS Port: 80/443
    RTSP Port: 554
    ```
- Smart Codec: Enable for better storage efficiency

## NVR Configuration

### Adding Cameras

!!! tip "Before You Begin"
    - Ensure all cameras are on the same network segment
    - Verify camera credentials
    - Check firmware compatibility

=== "Auto Discovery"
    ```
    1. Go to Camera > Registration
    2. Click "Auto Search"
    3. Select cameras to add
    4. Enter camera credentials
    5. Click "Add"
    ```

=== "Manual Addition"
    ```
    1. Go to Camera > Registration
    2. Click "Add" > "IP Camera"
    3. Enter camera details:
       - IP Address
       - Port (default: 80/HTTP, 443/HTTPS)
       - Protocol: ONVIF or Private
       - Username/Password
    ```

### Storage & Recording

=== "Storage Setup"
    ```yaml
    # Storage Configuration:
    HDD Management: RAID 5 (recommended for redundancy)
    HDD Groups: 
      - Group 1: Cameras 1-8 (if using multiple HDDs)
      - Group 2: Cameras 9-16
    
    # Recording Schedule:
    - Continuous: 24/7 for critical areas
    - Motion: For storage efficiency
    - Event: For specific triggers
    ```

=== "Retention Settings"
    ```yaml
    # Recommended Settings:
    - 30 days retention (minimum)
    - Auto-overwrite oldest when full
    - Enable H.265+ for better compression
    - Adjust bitrate based on storage needs
    ```

### Live View Configuration

```yaml
# View Layouts:
- 1x1, 2x2, 3x3, 4x4, 5+5, 6+4, 8+8, etc.
- Custom layouts available

# Display Options:
- Digital Zoom: Up to 16x
- PTZ Controls: On-screen joystick
- Audio: Two-way audio support
- Tour: Create camera tours
```

## Common Commands

### Web Interface URLs
- Main interface: `http://[IP-ADDRESS]`
- ONVIF: `http://[IP-ADDRESS]/onvif/device_service`
- RTSP Stream: 
  - Main stream: `rtsp://[USER]:[PASSWORD]@[IP-ADDRESS]:554/cam/realmonitor?channel=1&subtype=0`
  - Sub stream: `rtsp://[USER]:[PASSWORD]@[IP-ADDRESS]:554/cam/realmonitor?channel=1&subtype=1`

### Dahua ConfigTool
- Download from: [Dahua Download Center](https://www.dahuasecurity.com/support/download/software/ConfigTool/)
- Used for:
  - Device discovery
  - Batch configuration
  - Firmware updates
  - Password recovery
  - Network configuration

## Troubleshooting Guide

### Common Issues & Solutions

=== "Camera Not Found"
    !!! failure "Check these first"
        1. Verify power and network cables
        2. Check switch/router port status
        3. Ensure IP is in correct subnet
    
    ```bash
    # Network Diagnostics
    ping [CAMERA_IP]
    arp -a | findstr [CAMERA_MAC]
    nmap -p 80,443,554 [CAMERA_IP]
    
    # Reset network settings to default
    # (Hold reset button for 30 seconds)
    ```

=== "Login Issues"
    !!! warning "Security Notice"
        - Try default credentials first
        - If locked out, factory reset may be required
        - Document new credentials securely
    
    ```bash
    # Common Defaults
    admin / admin
    admin / 123456
    admin / 1111111
    admin / 888888
    ```

=== "Video Quality Problems"
    ```yaml
    # Common Fixes:
    - Increase bitrate (min 4096 kbps for 1080p)
    - Lower frame rate (15 FPS is often sufficient)
    - Enable H.265+/Smart Codec
    - Check network bandwidth
    - Verify switch supports Gigabit Ethernet
    - Adjust WDR/BLC settings for challenging lighting
    ```

### Factory Reset Procedures

=== "Cameras"
    ```
    1. Locate reset button (pinhole)
    2. Press and hold for 30+ seconds
    3. Wait for IR LEDs to blink
    4. Release and wait for reboot
    ```

=== "NVRs"
    ```
    1. Power off the NVR
    2. Press and hold reset button
    3. Power on while holding button
    4. Continue holding for 30 seconds
    5. Release and wait for boot
    ```

### Log Collection

!!! tip "For Technical Support"
    Always collect these logs when opening a support case:
    
    ```bash
    # System Logs
    http://[IP]/cgi-bin/magicBox.cgi?action=getSystemInfo
    
    # Event Logs
    http://[IP]/cgi-bin/eventManager.cgi?action=attach&codes=[All]
    
    # Network Configuration
    http://[IP]/cgi-bin/configManager.cgi?action=getConfig&name=Network
    ```

## Useful Tools & Resources

### Desktop Applications

=== "Dahua ConfigTool"
    ```yaml
    Purpose: Device discovery and management
    Platform: Windows
    Key Features:
      - Batch configuration
      - Firmware updates
      - Network diagnostics
      - Password recovery
    ```

=== "SmartPSS"
    ```yaml
    Purpose: Video management software
    Platform: Windows
    Key Features:
      - Multi-camera monitoring
      - Recording management
      - Playback and export
      - User management
    ```

=== "Smart Viewer"
    ```yaml
    Purpose: Quick camera viewing
    Platform: Windows
    Key Features:
      - Lightweight application
      - Quick camera access
      - Basic PTZ controls
      - No installation required
    ```

### Mobile Applications

=== "DMSS"
    ```yaml
    Platforms: iOS, Android
    Key Features:
      - Live view
      - Playback
      - Push notifications
      - PTZ control
      - Two-way audio
    ```

=== "gDMSS Lite"
    ```yaml
    Platforms: iOS, Android
    Key Features:
      - Lightweight client
      - Quick access to live view
      - Basic playback
      - Push notifications
    ```

## Firmware Management

### Update Procedure

!!! danger "Important"
    Always backup configuration before updating firmware. Never power off during update.

=== "Web Interface"
    ```
    1. Download firmware from Dahua Support
    2. Log in to web interface
    3. Go to Maintenance > System > Upgrade
    4. Upload firmware file
    5. Click "Upgrade" and wait for completion
    ```

=== "ConfigTool"
    ```
    1. Select device(s) in ConfigTool
    2. Right-click > Upgrade
    3. Select firmware file
    4. Click "Start Upgrade"
    5. Monitor progress and wait for reboot
    ```

### Version Compatibility

| Device Type | Minimum Version | Recommended Version |
|-------------|----------------|---------------------|
| IPC-HDW    | V2.800.0000000.1.R | V4.001.0000000.1.R |
| NVR52XX    | V3.200.0000000.0 | V4.002.0000000.0 |
| NVR58XX    | V3.200.0000000.0 | V4.002.0000000.0 |

## Mobile & Remote Access

### DMSS App

!!! tip "Best Practices"
    - Use P2P for easy remote access
    - Enable 2FA for security
    - Configure push notifications
    - Set up motion detection alerts

=== "Setup Guide"
    ```markdown
    1. Install DMSS from App Store/Play Store
    2. Tap "+" to add device
    3. Choose connection method:
       - Scan QR code (easiest)
       - Manual entry (IP/Domain)
       - P2P (cloud-based)
    4. Enter credentials
    5. Save and connect
    ```

=== "Troubleshooting"
    ```yaml
    # Common Issues:
    - Port forwarding not configured
    - Firewall blocking connection
    - Incorrect credentials
    - Firmware version mismatch
    - P2P service not enabled
    
    # Test Connection:
    - Verify port is open: telnet [IP] [PORT]
    - Check router logs
    - Test with mobile data (bypass local network)
    ```

## Additional Resources

### Official Documentation

=== "Technical Resources"
    - [Product Manuals](https://www.dahuasecurity.com/support/technical-document)
    - [Firmware Downloads](https://www.dahuasecurity.com/support/firmware)
    - [API Documentation](https://www.dahuasecurity.com/support/technical-document)

=== "Learning & Support"
    - [Training Portal](https://www.dahuasecurity.com/support/training)
    - [Video Tutorials](https://www.youtube.com/dahuatechnology)
    - [Knowledge Base](https://support.dahuasecurity.com/)

### Community & Support

=== "Online Communities"
    - [Dahua Community](https://community.dahuasecurity.com/)
    - [Reddit r/cctv](https://www.reddit.com/r/cctv/)
    - [IPVM Forum](https://ipvm.com/)

=== "Contact Support"
    - [Submit a Ticket](https://support.dahuasecurity.com/hc/en-us/requests/new)
    - Phone: +1-877-511-4820
    - Email: support@dahuasecurity.com

## Best Practices

### Security Checklist

!!! check "Essential Security Measures"
    - [ ] Change default passwords
    - [ ] Enable HTTPS
    - [ ] Update firmware regularly
    - [ ] Use strong user passwords
    - [ ] Enable account lockout
    - [ ] Disable unused services (UPnP, P2P if not needed)
    - [ ] Enable logging
    - [ ] Regular backups

### Maintenance Schedule

| Task                  | Frequency   | Notes                     |
|-----------------------|-------------|---------------------------|
| Firmware Updates      | Quarterly   | Check for security fixes  |
| Password Rotation     | 90 days     | All user accounts         |
| Storage Health Check  | Monthly     | Check HDD/SD card status  |
| Backup Configuration  | After changes| Keep multiple versions    |
| Camera Cleaning       | Quarterly   | Lenses and housings       |

---

<div class="mdx-connect" markdown>
  <div class="mdx-connect__column">
    <div class="mdx-connect__content">
      <h3>Need Help?</h3>
      <p>Reach out to Dahua support for assistance with your CCTV system.</p>
      <a href="https://support.dahuasecurity.com/" class="md-button md-button--primary">
        Contact Support
      </a>
    </div>
  </div>
</div>

*Document last updated: {{ git_revision_date_localized }}*
*For official documentation, visit [Dahua Support](https://www.dahuasecurity.com/support/)*
