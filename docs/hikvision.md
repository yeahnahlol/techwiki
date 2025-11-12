---
title: Hikvision
description: Comprehensive reference for Hikvision CCTV systems including setup, configuration, and troubleshooting
tags:
  - cctv
  - hikvision
  - security
  - networking
  - surveillance
---

# Hikvision CCTV Technician Cheatsheet

!!! tip "Quick Navigation"
    - [Default Credentials](#default-credentials)
    - [Network Setup](#network-configuration)
    - [Camera Setup](#camera-setup)
    - [NVR Configuration](#nvr-configuration)
    - [Troubleshooting](#troubleshooting)
    - [Mobile Access](#mobile-access)

## Quick Reference

=== "Network Ports"
    ```yaml
    # Standard Ports:
    HTTP/HTTPS: 80/443
    RTSP: 554 (TCP)
    ONVIF: 80 (TCP)
    RSTP/RTMP: 10554 (TCP)
    Hik-Connect P2P: 6500 (TCP)
    Server Port: 8000 (TCP)
    Mobile Port: 65001 (TCP)
    iVMS-4200: 8000 (TCP)
    ```

=== "Common IP Ranges"
    ```yaml
    # Default IP Ranges:
    192.168.1.64/24 (Common Default)
    192.168.0.64/24
    192.168.0.0/16 (Common for SMB)
    10.0.0.0/8 (Enterprise)
    172.16.0.0/12 (Enterprise)
    ```

!!! note "Before You Begin"
    - Download [SADP Tool](https://www.hikvision.com/en/support/tools/hitools/)
    - Have admin credentials ready
    - Document all IP assignments

## Default Credentials

!!! danger "Security Notice"
    - Change default credentials immediately after setup
    - Use strong, unique passwords
    - Enable two-factor authentication if available
    - Document credentials securely

=== "Common Defaults"
    ```yaml
    # Admin Accounts:
    admin / 12345 (Older models)
    admin / [Activation Code] (Newer models)
    admin / admin
    admin / 123456
    
    # Operator Account:
    operator / 12345 (If enabled)
    
    # Default IP:
    192.168.1.64
    ```

=== "Activation Process"
    ```markdown
    1. Connect device to network
    2. Open SADP Tool
    3. Right-click device > Activate
    4. Enter activation code (from device label)
    5. Set strong admin password
    6. Confirm activation
    ```

!!! info "Finding Activation Code"
    - Check device label or packaging
    - Format: 5-6 groups of 4 alphanumeric characters
    - Contact Hikvision support if missing

## Network Configuration

### IP Address Assignment

=== "DHCP (Default)"
    ```yaml
    # Most devices come with DHCP enabled
    # To verify/change:
    1. Open SADP Tool
    2. Right-click device > Network
    3. Check 'Obtain IP address automatically'
    4. Click 'Save' and reboot if needed
    ```

=== "Static IP"
    ```yaml
    # Recommended for production:
    1. Open SADP Tool
    2. Right-click device > Network
    3. Select 'Manual' IP assignment
    4. Configure:
       - IP Address
       - Subnet Mask
       - Default Gateway
       - Preferred DNS
    5. Click 'Save' and reboot
    ```

### Port Configuration

=== "Port Forwarding"
    !!! warning "Security Consideration"
        - Only forward necessary ports
        - Consider using VPN instead
        - Change default ports if possible
    
    ```yaml
    # Common Ports to Forward:
    - HTTP: 80/TCP
    - HTTPS: 443/TCP
    - RTSP: 554/TCP
    - Mobile: 65001/TCP
    - iVMS: 8000/TCP
    ```

=== "Port Configuration via Web UI"
    ```
    1. Log in to web interface
    2. Go to Configuration > Network > Advanced Settings > More Settings
    3. Configure ports as needed
    4. Click 'Save' and restart service
    ```

## Camera Setup

### Initial Setup

!!! step "1. Physical Installation"
    - Mount the camera in desired location
    - Connect to power (PoE or separate power supply)
    - Verify network connectivity with link lights

!!! step "2. Device Activation"
    ```bash
    # Using SADP Tool:
    1. Launch SADP Tool
    2. Locate uninitialized device (red highlight)
    3. Right-click > Activate
    4. Set strong admin password (12+ characters)
    5. Enter activation code (from device label)
    ```

!!! step "3. Initial Configuration"
    1. Access web interface at `http://[camera-ip]`
    2. Complete first-time setup wizard
    3. Configure network settings (static IP recommended)
    4. Set correct time zone and NTP server
    5. Enable HTTPS for secure access

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

## NVR Configuration

### Adding Cameras

!!! tip "Before You Begin"
    - Ensure all cameras are on the same network
    - Verify camera credentials
    - Check firmware compatibility

=== "Auto Discovery"
    ```
    1. Go to Camera > IP Camera
    2. Click 'Auto Search'
    3. Select cameras to add
    4. Enter camera credentials
    5. Click 'Add'
    ```

=== "Manual Addition"
    ```
    1. Go to Camera > IP Camera > Manual Add
    2. Enter camera details:
       - IP Address
       - Port (default: 80/HTTP, 443/HTTPS)
       - Protocol: HIKVISION, ONVIF, or RTSP
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

=== "Recording Settings"
    ```yaml
    # Recommended Settings:
    - Pre-record: 5 seconds
    - Post-record: 10 seconds
    - Record Audio: Enable if needed
    - Enable H.265+ for better compression
    - Adjust bitrate based on storage needs
    ```

### Live View Configuration

```yaml
# View Layouts:
- 1x1, 2x2, 3x3, 4x4, 5+1, 6+4, 8+0, etc.
- Custom layouts available

# Display Options:
- Digital Zoom: Up to 16x
- PTZ Controls: On-screen joystick
- Audio: Two-way audio support
- Tour: Create camera tours
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
    Main:    rtsp://[USER]:[PASS]@[IP]:554/Streaming/Channels/101
    Sub:     rtsp://[USER]:[PASS]@[IP]:554/Streaming/Channels/102
    Third:   rtsp://[USER]:[PASS]@[IP]:554/Streaming/Channels/103
    ```

### SADP Tool

!!! info "Key Features"
    - Device discovery and management
    - Device activation and password reset
    - Network configuration
    - Firmware upgrades
    - Batch configuration

=== "Installation"
    ```bash
    # Download from:
    https://www.hikvision.com/en/support/tools/hitools/
    
    # System Requirements:
    - Windows 7/10/11 (64-bit)
    - .NET Framework 4.6.1 or later
    - 4GB RAM minimum
    ```

=== "Common Tasks"
    ```bash
    # Find all Hikvision devices on network
    SADP.exe /scan
    
    # Reset IP address
    SADP.exe /ip:192.168.1.100 /newip:192.168.1.101
    
    # Reset password (if enabled)
    SADP.exe /resetpwd /ip:192.168.1.100
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
    
    # Check if device needs activation
    # (Appears in SADP with red highlight)
    ```

=== "Login Issues"
    !!! warning "Security Notice"
        - Try default credentials first
        - If locked out, use SADP for password reset
        - Document new credentials securely
    
    ```yaml
    # Common Defaults:
    admin / 12345
    admin / [Activation Code]
    admin / admin
    admin / 123456
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
    http://[IP]/ISAPI/System/log
    
    # Event Logs
    http://[IP]/ISAPI/Event/notification/alertStream
    
    # Network Configuration
    http://[IP]/ISAPI/System/Network
    ```

## Useful Tools & Resources

### Desktop Applications

=== "iVMS-4200"
    ```yaml
    Purpose: Professional Video Management
    Platform: Windows
    Key Features:
      - Multi-camera monitoring
      - Recording management
      - Playback and export
      - User management
      - Map view
    ```

=== "Batch Configuration Tool"
    ```yaml
    Purpose: Multi-device management
    Platform: Windows
    Key Features:
      - Bulk configuration
      - Firmware updates
      - Password management
      - Network settings
    ```

### Mobile Applications

=== "Hik-Connect"
    ```yaml
    Platforms: iOS, Android
    Key Features:
      - Live view
      - Playback
      - Push notifications
      - PTZ control
      - Two-way audio
      - Cloud storage
    ```

=== "iVMS-4500"
    ```yaml
    Platforms: iOS, Android
    Key Features:
      - Professional monitoring
      - Multi-site support
      - Alarm management
      - Map view
      - Intercom
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
    1. Download firmware from Hikvision Support
    2. Log in to web interface
    3. Go to Maintenance > System > System Maintenance > Upgrade
    4. Upload firmware file
    5. Click "Upgrade" and wait for completion
    ```

=== "SADP Tool"
    ```
    1. Select device in SADP
    2. Right-click > Upgrade
    3. Select firmware file
    4. Click "Start Upgrade"
    5. Monitor progress and wait for reboot
    ```

### Version Compatibility

| Device Type | Minimum Version | Recommended Version |
|-------------|----------------|---------------------|
| DS-2CD2XXX | V5.5.82 | V5.7.3 |
| DS-76XXNI | V4.22.005 | V4.30.085 |
| DS-96XXNI | V4.22.005 | V4.30.085 |

## Mobile & Remote Access

### Hik-Connect Setup

!!! tip "Best Practices"
    - Use Hik-Connect for easy remote access
    - Enable 2FA for security
    - Configure push notifications
    - Set up motion detection alerts

=== "Setup Guide"
    ```markdown
    1. Install Hik-Connect from App Store/Play Store
    2. Create Hik-Connect account
    3. Tap "+" to add device
    4. Choose connection method:
       - Scan QR code (easiest)
       - Manual entry (Serial/QR code)
       - IP/Domain
    5. Enter credentials
    6. Save and connect
    ```

=== "Troubleshooting"
    ```yaml
    # Common Issues:
    - Port forwarding not configured
    - Firewall blocking connection
    - Incorrect credentials
    - Firmware version mismatch
    - Hik-Connect service not enabled
    
    # Test Connection:
    - Verify port is open: telnet [IP] [PORT]
    - Check router logs
    - Test with mobile data (bypass local network)
    ```

## Additional Resources

### Official Documentation

=== "Technical Resources"
    - [Product Manuals](https://www.hikvision.com/en/support/technical-document/)
    - [Firmware Downloads](https://www.hikvision.com/en/support/download/firmware/)
    - [API Documentation](https://www.hikvision.com/en/support/technical-document/)

=== "Learning & Support"
    - [Training Portal](https://www.hikvision.com/en/support/training/)
    - [Video Tutorials](https://www.hikvision.com/en/support/video-tutorials/)
    - [Knowledge Base](https://www.hikvision.com/en/support/faq/)

### Community & Support

=== "Online Communities"
    - [Hikvision Community](https://www.hikvision.com/en/community/)
    - [Reddit r/homedefense](https://www.reddit.com/r/homedefense/)
    - [IPVM Forum](https://ipvm.com/)

=== "Contact Support"
    - [Submit a Ticket](https://www.hikvision.com/en/support/contact-us/)
    - Phone: +1-866-200-6690
    - Email: support.us@hikvision.com

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
      <p>Reach out to Hikvision support for assistance with your CCTV system.</p>
      <a href="https://www.hikvision.com/en/support/contact-us/" class="md-button md-button--primary">
        Contact Support
      </a>
    </div>
  </div>
</div>

*Document last updated: {{ git_revision_date_localized }}*
*For official documentation, visit [Hikvision Support](https://www.hikvision.com/en/support/)*
