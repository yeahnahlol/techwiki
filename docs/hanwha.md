---
title: Hanwha / Wisenet
description: Comprehensive reference for Hanwha Wisenet CCTV systems including setup, configuration, and troubleshooting
tags:
  - cctv
  - hanwha
  - wisenet
  - security
  - networking
---

# Hanwha Wisenet CCTV Technician Cheatsheet

!!! tip "Quick Navigation"
    - [Quick Reference](#quick-reference)
    - [Default Credentials](#default-credentials)
    - [Network Configuration](#network-configuration)
    - [Camera Setup](#camera-setup)
    - [NVR Configuration](#nvr-configuration)
    - [Common Commands](#common-commands)
    - [Troubleshooting](#troubleshooting)
    - [Useful Tools](#useful-tools)
    - [Mobile Access](#mobile-access)

## Quick Reference

=== "Network Ports"
    | Port | Protocol | Service          |
    |------|----------|------------------|
    | 80   | TCP      | HTTP             |
    | 443  | TCP      | HTTPS            |
    | 554  | TCP/UDP  | RTSP             |
    | 80   | TCP      | ONVIF            |
    | 4520 | TCP      | Wisenet DDNS     |
    | 32768-33747 | UDP | Wisenet P2P     |
    | 4510 | TCP      | Wisenet WAVE     |

=== "Common IP Ranges"
    - `192.168.1.x` - Default network
    - `192.168.2.x` - PoE camera network (on some NVRs)
    - `10.0.0.x` - Common alternative network

## Default Credentials

!!! danger "Security Notice"
    Always change default credentials immediately after first login. These are well-known defaults.

=== "Common Defaults"
    | Username | Password | Access Level |
    |----------|----------|--------------|
    | admin    | 4321     | Administrator|
    | user     | 1234     | Viewer       |
    | operator | 1234     | Operator     |
    | backup   | 1234     | Backup       |

=== "Older Models"
    | Username | Password |
    |----------|----------|
    | admin    | 1111111  |
    | admin    | 123456   |
    | admin    | 4321     |

## Network Configuration

### IP Address Assignment

=== "DHCP (Default)"
    ```bash
    # Most devices come with DHCP enabled by default
    # To verify:
    ipconfig /all      # Windows
    ifconfig           # Linux/macOS
    ```

=== "Static IP"
    ```bash
    1. Access web interface (http://[device-ip])
    2. Navigate to Network > TCP/IP
    3. Configure settings:
       - IP Address: 192.168.1.x
       - Subnet Mask: 255.255.255.0
       - Gateway: 192.168.1.1
       - DNS: 8.8.8.8 / 8.8.4.4
    ```

### Port Forwarding

!!! warning "Security Consideration"
    Consider using VPN instead of port forwarding for remote access.

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
    ```

## Camera Setup

### Initial Setup

!!! step "1. Physical Installation"
    - Mount the camera in desired location
    - Connect to power (PoE or separate power supply)
    - Verify network connectivity

!!! step "2. Device Detection"
    ```bash
    # Use Wisenet Device Manager to find the camera
    # Download from: https://www.hanwhavision.com/support/tools/
    # Run as administrator for full functionality
    ```

!!! step "3. Initial Configuration"
    1. Access web interface at `http://[camera-ip]`
    2. Complete first-time setup wizard
    3. Set strong admin password
    4. Configure network settings
    5. Set correct time zone and NTP server

### Camera Settings

=== "Video Settings"
    ```yaml
    Resolution: 1080p (1920x1080) or higher
    Frame Rate: 15-30 FPS
    Bitrate: 4096-8192 kbps
    Compression: H.265+ (recommended)
    ```

=== "Image Settings"
    ```yaml
    Brightness: 50
    Contrast: 50
    Saturation: 50
    Sharpness: 50
    WDR: On (for high-contrast scenes)
    ```

=== "Network Settings"
    ```yaml
    IP Assignment: Static (recommended) or DHCP
    MTU: 1500 (default)
    QoS: Enable for video traffic
    ```

## NVR Configuration

### Adding Cameras

!!! tip "Before You Begin"
    Ensure all cameras are properly connected to the network and have been configured with static IPs.

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
    3. Enter camera details manually:
       - IP Address
       - Port
       - Protocol (HTTP/HTTPS)
       - Username/Password
    ```

### Storage & Recording

=== "Storage Setup"
    ```yaml
    # Storage Configuration
    RAID: RAID 5 (recommended for redundancy)
    HDD Groups: 
      - Group 1: Cameras 1-8
      - Group 2: Cameras 9-16
    ```

=== "Recording Settings"
    ```yaml
    # Recording Schedule
    - Continuous: 24/7 for critical areas
    - Motion: For storage efficiency
    - Event: For specific triggers
    
    # Retention
    - 30 days (minimum recommended)
    - Auto-overwrite oldest when full
    ```

### Live View Configuration

```yaml
# View Layouts
- 1x1, 2x2, 3x3, 4x4, 5x5, 6x6, 8x8
- Custom layouts available

# Display Options
- Digital Zoom: Up to 16x
- PTZ Controls: On-screen joystick
- Audio: Two-way audio support
```

## Common Commands & Tools

### Web Interface Access

=== "Standard Access"
    ```bash
    # Main Web Interface
    http://[IP-ADDRESS]
    
    # ONVIF Service
    http://[IP-ADDRESS]/onvif/device_service
    
    # RTSP Streams
    Main:    rtsp://[USER]:[PASS]@[IP]:554/profile2/media.smp
    Sub:     rtsp://[USER]:[PASS]@[IP]:554/profile3/media.smp
    Mobile:  rtsp://[USER]:[PASS]@[IP]:554/profile1/media.smp
    ```

### Wisenet Device Manager

!!! info "Key Features"
    - Device discovery on local network
    - Batch firmware updates
    - Network configuration
    - Password recovery
    - System backup/restore

=== "Installation"
    ```bash
    # Download from:
    https://www.hanwhavision.com/support/tools/
    
    # System Requirements:
    - Windows 7/10/11 (64-bit)
    - .NET Framework 4.6.1 or later
    - 4GB RAM minimum
    ```

=== "Common Tasks"
    ```bash
    # Find all devices on network
    WisenetDeviceManager.exe /scan
    
    # Update firmware
    WisenetDeviceManager.exe /update /ip:192.168.1.100 /file:firmware.bin
    
    # Reset password
    WisenetDeviceManager.exe /resetpwd /ip:192.168.1.100
    ```

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
    ```

=== "Login Issues"
    !!! warning "Security Notice"
        - Try default credentials first
        - If locked out, factory reset may be required
        - Document new credentials securely
    
    ```bash
    # Common Defaults
    admin / 4321
    admin / 123456
    admin / 1111111
    ```

=== "Video Quality Problems"
    ```yaml
    # Common Fixes:
    - Increase bitrate (min 4096 kbps for 1080p)
    - Lower frame rate (15 FPS is often sufficient)
    - Enable H.265+ compression
    - Check network bandwidth
    - Verify switch supports Gigabit Ethernet
    ```

### Factory Reset Procedures

=== "Cameras"
    ```
    1. Locate reset button (pinhole)
    2. Press and hold for 15+ seconds
    3. Wait for IR LEDs to blink
    4. Release and wait for reboot
    ```

=== "NVRs"
    ```
    1. Power off the NVR
    2. Press and hold reset button
    3. Power on while holding button
    4. Continue holding for 15 seconds
    5. Release and wait for boot
    ```

### Log Collection

!!! tip "For Technical Support"
    Always collect these logs when opening a support case:
    
    ```bash
    # System Logs
    http://[IP]/system_log.cgi
    
    # Event Logs
    http://[IP]/event_log.cgi
    
    # Network Configuration
    http://[IP]/network.cgi
    ```

## Useful Tools & Resources

### Desktop Applications

=== "Wisenet Device Manager"
    ```yaml
    Purpose: Device discovery and management
    Platform: Windows
    Key Features:
      - Batch configuration
      - Firmware updates
      - Network diagnostics
    ```

=== "Wisenet WAVE"
    ```yaml
    Purpose: Video management software
    Platform: Windows, Linux
    Key Features:
      - Multi-camera monitoring
      - Recording management
      - Advanced analytics
    ```

=== "Wisenet SSM"
    ```yaml
    Purpose: System management
    Platform: Windows
    Key Features:
      - Centralized management
      - User access control
      - System monitoring
    ```

### Mobile Applications

=== "Wisenet Mobile"
    ```yaml
    Platforms: iOS, Android
    Key Features:
      - Live view
      - Playback
      - Push notifications
      - PTZ control
    ```

=== "Wisenet Wave Light"
    ```yaml
    Platforms: iOS, Android
    Key Features:
      - Lightweight client
      - Quick access to live view
      - Basic playback
    ```

### Browser Compatibility

| Browser       | Supported | Notes                     |
|---------------|-----------|---------------------------|
| Chrome        | ✓         | Recommended               |
| Firefox       | ✓         |                           |
| Edge          | ✓         |                           |
| Safari        | ✓         | Some features limited     |
| Mobile Chrome | ✓         |                           |
| Mobile Safari | ✓         | Some features limited     |

## Firmware Management

### Update Procedure

!!! danger "Important"
    Always backup configuration before updating firmware. Never power off during update.

=== "Web Interface"
    ```
    1. Download firmware from Hanwha Vision Support
    2. Log in to web interface
    3. Go to Maintenance > Firmware
    4. Upload firmware file
    5. Click "Update" and wait for completion
    ```

=== "Wisenet Device Manager"
    ```
    1. Select device(s) in WDM
    2. Right-click > Firmware Update
    3. Select firmware file
    4. Click "Start Update"
    5. Monitor progress and wait for reboot
    ```

### Version Compatibility

| Device Type | Minimum Version | Recommended Version |
|-------------|----------------|---------------------|
| Wisenet X   | 1.2.3          | 2.0.1               |
| Wisenet P   | 1.5.0          | 2.1.0               |
| Wisenet Q   | 2.0.0          | 2.3.0               |

## Mobile & Remote Access

### Wisenet Mobile App

!!! tip "Best Practices"
    - Use Wisenet DDNS for dynamic IPs
    - Enable 2FA for security
    - Configure push notifications

=== "Setup Guide"
    ```markdown
    1. Install Wisenet Mobile from App Store/Play Store
    2. Tap "+" to add device
    3. Choose connection method:
       - Scan QR code
       - Manual entry (IP/Domain)
       - Wisenet DDNS
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
    
    # Test Connection:
    - Verify port is open: telnet [IP] [PORT]
    - Check router logs
    - Test with mobile data (bypass local network)
    ```

## Additional Resources

### Official Documentation

=== "Technical Resources"
    - [Product Manuals](https://www.hanwhavision.com/support/technical-documents/)
    - [Firmware Downloads](https://www.hanwhavision.com/support/download/)
    - [API Documentation](https://www.hanwhavision.com/support/technical-documents/)

=== "Learning & Support"
    - [Training Portal](https://www.hanwhavision.com/training/)
    - [Video Tutorials](https://www.youtube.com/hanwhavision)
    - [Knowledge Base](https://support.hanwhavision.com/)

### Community & Support

=== "Online Communities"
    - [Wisenet Community Forum](https://community.hanwhavision.com/)
    - [Reddit r/cctv](https://www.reddit.com/r/cctv/)
    - [IPVM Forum](https://ipvm.com/)

=== "Contact Support"
    - [Submit a Ticket](https://support.hanwhavision.com/hc/en-us/requests/new)
    - Phone: +1-201-325-6924
    - Email: support@hanwhavision.com

## Best Practices

### Security Checklist

!!! check "Essential Security Measures"
    - [ ] Change default passwords
    - [ ] Enable HTTPS
    - [ ] Update firmware regularly
    - [ ] Use strong user passwords
    - [ ] Enable account lockout
    - [ ] Disable unused services
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
      <p>Reach out to our support team for assistance with your Hanwha Wisenet system.</p>
      <a href="https://support.hanwhavision.com/" class="md-button md-button--primary">
        Contact Support
      </a>
    </div>
  </div>
</div>

*Document last updated: {{ git_revision_date_localized }}*
*For official documentation, visit [Hanwha Vision Support](https://www.hanwhavision.com/support/)*