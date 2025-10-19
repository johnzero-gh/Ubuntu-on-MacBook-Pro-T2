# Configuration Reference

This document provides comprehensive configuration details for Ubuntu on MacBook Pro T2.

## Configuration Overview

All T2-specific configurations should be stored in a consistent location:

```
/etc/t2/                    # T2 configuration files
/usr/share/t2/             # T2 shared resources
/var/log/t2/               # T2 logs
~/.config/t2/              # User-specific T2 configs
```

## System Configuration

### 1. Kernel Parameters

Location: `/etc/default/grub`

```bash
# Essential T2 parameters
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash intel_iommu=on iommu=pt pcie_ports=compat"

# Additional options for specific issues:

# For better suspend/resume:
# Add: mem_sleep_default=deep

# For graphics issues:
# Add: i915.enable_psr=0

# For audio issues:
# Add: snd_hda_intel.dmic_detect=0

# Example with all options:
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash intel_iommu=on iommu=pt pcie_ports=compat mem_sleep_default=deep"
```

After changes:
```bash
sudo update-grub
```

### 2. Module Configuration

#### 2.1 Audio Modules

Location: `/etc/modprobe.d/t2-audio.conf`

```conf
# T2 Audio Configuration
options snd_hda_intel model=auto
options snd_hda_intel dmic_detect=0
options snd_hda_intel power_save=0

# Enable all codecs
options snd_hda_codec_cs8409 index=0
```

Apply changes:
```bash
sudo modprobe -r snd_hda_intel
sudo modprobe snd_hda_intel
```

#### 2.2 WiFi Modules

Location: `/etc/modprobe.d/t2-wifi.conf`

```conf
# T2 WiFi Configuration
options brcmfmac debug=0

# Power management
options brcmfmac roamoff=1

# Alternative driver configuration
# options wl ignore_probe_fail=1
```

Reload WiFi:
```bash
sudo modprobe -r brcmfmac
sudo modprobe brcmfmac
sudo systemctl restart NetworkManager
```

#### 2.3 Keyboard and Trackpad

Location: `/etc/modprobe.d/t2-input.conf`

```conf
# T2 Keyboard Configuration
options apple_ib_tb fnmode=2
options apple_ib_tb iso_layout=0

# Trackpad configuration
options hid_apple fnmode=2
options hid_apple iso_layout=0
```

### 3. UDEV Rules

#### 3.1 Keyboard Backlight

Location: `/etc/udev/rules.d/99-t2-keyboard-backlight.rules`

```udev
# Allow users to control keyboard backlight
ACTION=="add", SUBSYSTEM=="leds", KERNEL=="spi::kbd_backlight", RUN+="/bin/chmod g+w /sys/class/leds/%k/brightness", RUN+="/bin/chgrp video /sys/class/leds/%k/brightness"
```

#### 3.2 Display Backlight

Location: `/etc/udev/rules.d/99-t2-display-backlight.rules`

```udev
# Allow users to control display backlight
ACTION=="add", SUBSYSTEM=="backlight", RUN+="/bin/chgrp video /sys/class/backlight/%k/brightness", RUN+="/bin/chmod g+w /sys/class/backlight/%k/brightness"
```

Apply rules:
```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

## Hardware-Specific Configuration

### 1. Audio Configuration

#### 1.1 PulseAudio Configuration

Location: `/etc/pulse/daemon.conf`

```conf
# T2 Audio optimizations
default-sample-rate = 48000
alternate-sample-rate = 44100
default-fragments = 4
default-fragment-size-msec = 25
```

#### 1.2 ALSA Configuration

Location: `/etc/asound.conf`

```conf
pcm.!default {
    type hw
    card 0
    device 0
}

ctl.!default {
    type hw
    card 0
}
```

Test audio:
```bash
speaker-test -c 2 -t wav
```

### 2. Display Configuration

#### 2.1 Enable HiDPI Scaling

For GNOME:
```bash
# Enable fractional scaling
gsettings set org.gnome.mutter experimental-features "['scale-monitor-framebuffer']"

# Set scaling factor (1.5x example)
gsettings set org.gnome.desktop.interface scaling-factor 2
```

For KDE:
```bash
# Use System Settings → Display Configuration
# Set scaling to 150% or 200%
```

#### 2.2 Night Light/Blue Light Filter

```bash
# Enable night light in GNOME
gsettings set org.gnome.settings-daemon.plugins.color night-light-enabled true

# Set color temperature (default: 4000K)
gsettings set org.gnome.settings-daemon.plugins.color night-light-temperature 3500
```

### 3. Power Management

#### 3.1 TLP Configuration

Location: `/etc/tlp.conf`

```conf
# T2 Power Management Configuration

# CPU Settings
CPU_SCALING_GOVERNOR_ON_AC=performance
CPU_SCALING_GOVERNOR_ON_BAT=powersave
CPU_ENERGY_PERF_POLICY_ON_AC=performance
CPU_ENERGY_PERF_POLICY_ON_BAT=power

# Intel GPU
INTEL_GPU_MIN_FREQ_ON_AC=300
INTEL_GPU_MIN_FREQ_ON_BAT=300
INTEL_GPU_MAX_FREQ_ON_AC=1200
INTEL_GPU_MAX_FREQ_ON_BAT=800
INTEL_GPU_BOOST_FREQ_ON_AC=1200
INTEL_GPU_BOOST_FREQ_ON_BAT=1000

# WiFi Power Saving
WIFI_PWR_ON_AC=off
WIFI_PWR_ON_BAT=on

# PCIe Runtime Power Management
RUNTIME_PM_ON_AC=on
RUNTIME_PM_ON_BAT=auto

# Battery Care (for long-term battery health)
START_CHARGE_THRESH_BAT0=75
STOP_CHARGE_THRESH_BAT0=80
```

Apply TLP configuration:
```bash
sudo tlp start
```

#### 3.2 Fan Control

Location: `/etc/mbpfan.conf`

```conf
[general]
# Temperature thresholds
min_fan_speed = 2000
max_fan_speed = 6200
low_temp = 55
high_temp = 68
max_temp = 86
polling_interval = 1
```

Restart fan service:
```bash
sudo systemctl restart mbpfan
```

### 4. Input Device Configuration

#### 4.1 Trackpad Configuration

Location: `/usr/share/X11/xorg.conf.d/30-touchpad.conf`

```conf
Section "InputClass"
    Identifier "T2 Touchpad"
    MatchDriver "libinput"
    MatchIsTouchpad "on"
    
    # Enable tap to click
    Option "Tapping" "on"
    
    # Natural scrolling
    Option "NaturalScrolling" "on"
    
    # Two-finger scrolling
    Option "ScrollMethod" "twofinger"
    
    # Disable while typing
    Option "DisableWhileTyping" "on"
    
    # Pointer acceleration
    Option "AccelProfile" "adaptive"
    Option "AccelSpeed" "0.5"
EndSection
```

#### 4.2 Keyboard Configuration

Location: `/usr/share/X11/xorg.conf.d/20-keyboard.conf`

```conf
Section "InputClass"
    Identifier "T2 Keyboard"
    MatchDriver "libinput"
    MatchIsKeyboard "on"
    
    # Function key behavior
    Option "XkbOptions" "apple:badmap"
EndSection
```

Restart X server to apply:
```bash
sudo systemctl restart gdm
```

## Network Configuration

### 1. WiFi Optimization

Location: `/etc/NetworkManager/conf.d/wifi-powersave.conf`

```conf
[connection]
wifi.powersave = 2

# 2 = disable power saving
# 3 = enable power saving
```

Restart NetworkManager:
```bash
sudo systemctl restart NetworkManager
```

### 2. Bluetooth Configuration

Location: `/etc/bluetooth/main.conf`

```conf
[General]
# Enable experimental features for better device support
Experimental = true

# Class of device
Class = 0x000100

# Discoverable timeout (0 = never timeout)
DiscoverableTimeout = 0

[Policy]
AutoEnable=true
```

Restart Bluetooth:
```bash
sudo systemctl restart bluetooth
```

## Boot Configuration

### 1. GRUB Appearance

Location: `/etc/default/grub`

```bash
# Timeout before default boot
GRUB_TIMEOUT=5

# Hide GRUB menu (press Shift/Esc to show)
GRUB_TIMEOUT_STYLE=hidden

# Default boot entry (0 = first entry)
GRUB_DEFAULT=0

# Boot in text mode for faster boot (optional)
# GRUB_CMDLINE_LINUX_DEFAULT="text"

# High resolution console
GRUB_GFXMODE=1920x1080x32
GRUB_GFXPAYLOAD_LINUX=keep
```

### 2. SystemD Boot Timeout

Location: `/etc/systemd/system.conf`

```conf
[Manager]
# Reduce boot timeout
DefaultTimeoutStartSec=15s
DefaultTimeoutStopSec=15s
```

## System Optimization

### 1. File System Configuration

Location: `/etc/fstab`

```fstab
# Root partition with optimal mount options for SSD
UUID=xxxxx / ext4 defaults,noatime,nodiratime,discard 0 1

# Swap partition
UUID=yyyyy none swap sw 0 0

# Tmpfs for /tmp
tmpfs /tmp tmpfs defaults,noatime,mode=1777 0 0
```

### 2. Swap Configuration

Location: `/etc/sysctl.d/99-swappiness.conf`

```conf
# Reduce swappiness for better performance
vm.swappiness=10

# Improve cache management
vm.vfs_cache_pressure=50
```

Apply immediately:
```bash
sudo sysctl -p /etc/sysctl.d/99-swappiness.conf
```

### 3. I/O Scheduler

Location: `/etc/udev/rules.d/60-ioschedulers.rules`

```udev
# Set optimal I/O scheduler for SSD
ACTION=="add|change", KERNEL=="nvme[0-9]n[0-9]", ATTR{queue/scheduler}="none"
ACTION=="add|change", KERNEL=="sd[a-z]", ATTR{queue/rotational}=="0", ATTR{queue/scheduler}="mq-deadline"
```

## Security Configuration

### 1. Firewall

```bash
# Enable UFW firewall
sudo ufw enable

# Allow common services
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https

# Check status
sudo ufw status
```

### 2. AppArmor

```bash
# Ensure AppArmor is enabled
sudo systemctl enable apparmor
sudo systemctl start apparmor

# Check status
sudo aa-status
```

## Backup Configuration Files

Create a backup script for all T2 configurations:

```bash
#!/bin/bash
# Save as: /usr/local/bin/t2-config-backup

BACKUP_DIR="$HOME/t2-config-backup-$(date +%Y%m%d)"
mkdir -p "$BACKUP_DIR"

# Backup configuration files
sudo cp -r /etc/t2/ "$BACKUP_DIR/" 2>/dev/null
sudo cp /etc/default/grub "$BACKUP_DIR/"
sudo cp -r /etc/modprobe.d/t2-*.conf "$BACKUP_DIR/" 2>/dev/null
sudo cp -r /etc/udev/rules.d/99-t2-*.rules "$BACKUP_DIR/" 2>/dev/null
sudo cp /etc/tlp.conf "$BACKUP_DIR/" 2>/dev/null
sudo cp /etc/mbpfan.conf "$BACKUP_DIR/" 2>/dev/null

echo "Backup created at: $BACKUP_DIR"
```

Make it executable:
```bash
sudo chmod +x /usr/local/bin/t2-config-backup
```

## Verification Commands

After configuration changes, verify with these commands:

```bash
# Check kernel parameters
cat /proc/cmdline

# List loaded modules
lsmod | grep -E "apple|brcm|snd"

# Check system services
systemctl status tlp mbpfan NetworkManager

# Test hardware
# WiFi
nmcli device status

# Audio
pactl list sinks short

# Bluetooth
bluetoothctl show

# Battery
upower -i /org/freedesktop/UPower/devices/battery_BAT0
```

## Configuration Profiles

### Profile: Battery Saver

```bash
# Maximize battery life
sudo tlp-stat -s  # Check current state
sudo tlp bat      # Force battery mode

# Reduce brightness
echo 30 | sudo tee /sys/class/backlight/*/brightness

# Disable Bluetooth
sudo systemctl stop bluetooth
```

### Profile: Performance

```bash
# Maximum performance
sudo tlp ac      # Force AC mode

# Set CPU governor
echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Maximum brightness
echo 100 | sudo tee /sys/class/backlight/*/brightness
```

## Troubleshooting Configuration Issues

If configuration changes cause problems:

1. Boot into recovery mode (hold Shift during GRUB)
2. Select "Advanced options"
3. Choose "recovery mode"
4. Select "root" for root shell
5. Revert changes:
   ```bash
   # Restore GRUB
   cp /etc/default/grub.bak /etc/default/grub
   update-grub
   
   # Remove problematic config
   rm /etc/modprobe.d/t2-problematic.conf
   
   # Reboot
   reboot
   ```

## See Also

- [INSTALLATION.md](INSTALLATION.md) - Installation guide
- [TROUBLESHOOTING.md](TROUBLESHOOTING.md) - Troubleshooting guide
- [README.md](README.md) - Overview and quick start
