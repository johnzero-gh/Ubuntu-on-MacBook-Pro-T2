# Ubuntu on MacBook Pro T2

This repository contains comprehensive documentation and configuration standards for installing and running Ubuntu on MacBook Pro models with the Apple T2 Security Chip.

## Table of Contents

- [Overview](#overview)
- [System Requirements](#system-requirements)
- [Pre-Installation](#pre-installation)
- [Installation](#installation)
- [Post-Installation Configuration](#post-installation-configuration)
- [Hardware Configuration](#hardware-configuration)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

## Overview

MacBook Pro models with the T2 chip (2018 and later) require specific configurations to run Ubuntu Linux. This guide provides standardized configuration procedures to ensure optimal compatibility and performance.

### Supported Models

- MacBook Pro 13" (2018, 2019, 2020 - Intel)
- MacBook Pro 15" (2018, 2019)
- MacBook Pro 16" (2019, 2020)

## System Requirements

### Minimum Requirements

- MacBook Pro with T2 Security Chip (2018 or later)
- 8GB RAM (16GB recommended)
- 128GB free disk space (256GB recommended)
- USB drive (16GB or larger) for installation media
- macOS 10.13 or later (for initial setup)

### Software Requirements

- Ubuntu 20.04 LTS or later
- Custom T2 kernel modules
- Modified boot configuration

## Pre-Installation

### 1. Backup Your Data

**Critical**: Always backup all important data before proceeding.

```bash
# Use Time Machine or your preferred backup solution
```

### 2. Disable Secure Boot

1. Restart your Mac
2. Hold `Command + R` during boot to enter Recovery Mode
3. Go to Utilities → Startup Security Utility
4. Set Secure Boot to "No Security"
5. Set External Boot to "Allow booting from external media"

### 3. Partition Your Drive

You can dual-boot with macOS or use Ubuntu exclusively.

**For Dual Boot:**
- Keep at least 50GB for macOS
- Allocate remaining space for Ubuntu
- Use Disk Utility in macOS to create partitions

**For Ubuntu Only:**
- You will format the entire drive during installation

## Installation

### Step 1: Create Installation Media

1. Download Ubuntu ISO (22.04 LTS or later recommended)
2. Create bootable USB drive:

```bash
# On Linux/macOS
sudo dd if=ubuntu-22.04-desktop-amd64.iso of=/dev/sdX bs=4M status=progress && sync
```

### Step 2: Boot from USB

1. Insert USB drive
2. Restart Mac
3. Hold `Option` key during boot
4. Select "EFI Boot" option

### Step 3: Install Ubuntu

Follow the standard Ubuntu installation process with these notes:

- Choose "Erase disk and install Ubuntu" OR "Something else" for manual partitioning
- For dual-boot, select the partition you created earlier
- Set up your user account and preferences

## Post-Installation Configuration

### 1. Install T2 Kernel Modules

The T2 chip requires custom drivers for full hardware support.

```bash
# Add T2 repository
sudo apt-add-repository -y ppa:t2-project/t2-kernel
sudo apt update

# Install T2-enabled kernel
sudo apt install -y linux-generic-t2
```

### 2. Configure GRUB Bootloader

Edit GRUB configuration for T2 compatibility:

```bash
sudo nano /etc/default/grub
```

Add/modify these lines:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash intel_iommu=on iommu=pt pcie_ports=compat"
```

Update GRUB:

```bash
sudo update-grub
```

### 3. WiFi Configuration

The T2 WiFi chip requires specific firmware:

```bash
# Install WiFi firmware
sudo apt install -y firmware-b43-installer
```

### 4. Audio Configuration

Configure audio for T2 speakers and microphone:

```bash
# Install audio drivers
sudo apt install -y t2-audio-config

# Apply audio configuration
sudo t2-audio-setup
```

## Hardware Configuration

### Keyboard and Trackpad

The keyboard and trackpad should work out of the box with T2 kernel modules, but fine-tuning may be needed:

```bash
# Install additional input drivers
sudo apt install -y xserver-xorg-input-libinput

# Configure trackpad settings
# Use GNOME Settings or your DE's settings manager
```

### Display Configuration

```bash
# For HiDPI displays, enable fractional scaling
gsettings set org.gnome.mutter experimental-features "['scale-monitor-framebuffer']"
```

### Fan Control

Install fan control for temperature management:

```bash
sudo apt install -y mbpfan
sudo systemctl enable mbpfan
sudo systemctl start mbpfan
```

### Battery Management

```bash
# Install TLP for better battery life
sudo apt install -y tlp tlp-rdw
sudo systemctl enable tlp
sudo systemctl start tlp
```

## Configuration Files

### Standard Directory Structure

```
/etc/t2/
├── audio.conf
├── keyboard.conf
└── wifi.conf

/usr/share/t2/
├── firmware/
└── scripts/
```

### Example Configuration: Audio

`/etc/t2/audio.conf`:

```ini
[Audio]
Device=T2-Audio
Driver=snd_hda_intel
OutputChannels=2
InputChannels=1
```

### Example Configuration: WiFi

`/etc/t2/wifi.conf`:

```ini
[WiFi]
Device=brcmfmac
Driver=broadcom-wl
PowerManagement=on
```

## Troubleshooting

### WiFi Not Working

```bash
# Check WiFi status
lspci | grep -i network

# Reload WiFi drivers
sudo modprobe -r brcmfmac
sudo modprobe brcmfmac

# Check firmware installation
dmesg | grep brcm
```

### No Audio

```bash
# Verify audio modules are loaded
lsmod | grep snd

# Reload audio modules
sudo alsa force-reload

# Check PulseAudio
pulseaudio --check
pulseaudio -k && pulseaudio --start
```

### Keyboard Not Working

```bash
# Check keyboard modules
lsmod | grep apple

# Reload keyboard module
sudo modprobe -r apple_ib_tb
sudo modprobe apple_ib_tb
```

### Boot Issues

1. Boot into recovery mode
2. Reinstall GRUB:

```bash
sudo grub-install /dev/sda
sudo update-grub
```

### Suspend/Resume Problems

```bash
# Disable problematic PM features
sudo nano /etc/default/grub

# Add to GRUB_CMDLINE_LINUX_DEFAULT:
# ... mem_sleep_default=deep
```

## Performance Optimization

### SSD TRIM Support

```bash
# Enable TRIM for SSD
sudo systemctl enable fstrim.timer
sudo systemctl start fstrim.timer
```

### Swap Configuration

```bash
# Adjust swappiness for better performance
sudo sysctl vm.swappiness=10

# Make permanent
echo "vm.swappiness=10" | sudo tee -a /etc/sysctl.conf
```

## Contributing

We welcome contributions! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

### Reporting Issues

When reporting issues, please include:

- MacBook Pro model and year
- Ubuntu version
- Kernel version (`uname -r`)
- Relevant log output
- Steps to reproduce

## Resources

- [T2 Linux Wiki](https://wiki.t2linux.org/)
- [T2 Linux GitHub](https://github.com/t2linux)
- [Ubuntu Documentation](https://help.ubuntu.com/)

## License

This documentation is provided as-is under the MIT License.

## Acknowledgments

- T2 Linux community
- Ubuntu community
- All contributors

---

**Disclaimer**: Modifying your MacBook's configuration may void your warranty. Proceed at your own risk. Always backup your data before making system changes.