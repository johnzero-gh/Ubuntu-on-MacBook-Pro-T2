# Installation Guide

This document provides detailed step-by-step instructions for installing Ubuntu on MacBook Pro with T2 chip.

## Prerequisites Checklist

- [ ] MacBook Pro with T2 chip (2018 or later)
- [ ] 16GB or larger USB drive
- [ ] External keyboard (recommended as backup)
- [ ] Complete backup of all data
- [ ] Internet connection for post-installation
- [ ] Power adapter connected

## Detailed Installation Steps

### Phase 1: Preparation

#### 1.1 Create Bootable USB

**On macOS:**

```bash
# Download Ubuntu ISO
curl -O https://releases.ubuntu.com/22.04/ubuntu-22.04.3-desktop-amd64.iso

# Find USB drive
diskutil list

# Create bootable USB (replace diskN with your USB drive)
sudo diskutil unmountDisk /dev/diskN
sudo dd if=ubuntu-22.04.3-desktop-amd64.iso of=/dev/rdiskN bs=1m
```

**On Linux:**

```bash
# Find USB drive
lsblk

# Create bootable USB (replace sdX with your USB drive)
sudo dd if=ubuntu-22.04.3-desktop-amd64.iso of=/dev/sdX bs=4M status=progress oflag=sync
```

**On Windows:**

Use [Rufus](https://rufus.ie/) or [Etcher](https://www.balena.io/etcher/):
1. Select Ubuntu ISO
2. Select USB drive
3. Choose "DD Image" mode
4. Click "Start"

#### 1.2 Adjust Mac Settings

1. **Disable FileVault** (if enabled):
   - System Preferences → Security & Privacy → FileVault
   - Turn Off FileVault
   - Wait for decryption to complete (this may take several hours)

2. **Adjust Startup Security**:
   - Restart and hold Command + R
   - Utilities → Startup Security Utility
   - Authenticate with admin password
   - Set "Secure Boot" to "No Security"
   - Set "External Boot" to "Allow booting from external media"

#### 1.3 Partition Disk (for Dual Boot)

**In macOS Disk Utility:**

1. Open Disk Utility
2. View → Show All Devices
3. Select main disk (not partition)
4. Click "Partition"
5. Add new partition:
   - Name: "Ubuntu"
   - Format: "MS-DOS (FAT)"
   - Size: At least 100GB recommended
6. Click "Apply"

### Phase 2: Ubuntu Installation

#### 2.1 Boot from USB

1. Shutdown Mac completely
2. Insert USB drive
3. Power on and immediately hold Option key
4. Select "EFI Boot" from boot menu
5. Wait for Ubuntu live session to load

#### 2.2 Try Ubuntu First

Before installing, verify hardware works in live session:

1. Test WiFi connectivity
2. Test keyboard and trackpad
3. Test audio (if available)
4. Test display brightness controls

#### 2.3 Begin Installation

1. Double-click "Install Ubuntu" icon on desktop
2. Select language → Continue
3. Choose keyboard layout → Continue
4. Select "Normal installation"
5. Check "Download updates while installing Ubuntu"
6. Check "Install third-party software" → Continue

#### 2.4 Partition Setup

**For Dual Boot:**

1. Choose "Something else"
2. Select the partition you created earlier (should be free space)
3. Click "+" to create partitions:
   - **EFI System Partition** (if not exists):
     - Size: 512 MB
     - Type: EFI System Partition
     - Mount: /boot/efi
   - **Root Partition**:
     - Size: 50-100 GB
     - Type: ext4
     - Mount: /
   - **Swap Partition**:
     - Size: Equal to RAM (for hibernate support)
     - Type: swap area
   - **Home Partition** (optional):
     - Size: Remaining space
     - Type: ext4
     - Mount: /home

**For Single OS:**

1. Choose "Erase disk and install Ubuntu"
2. Select installation disk
3. Confirm → Install Now

#### 2.5 Complete Installation

1. Select timezone → Continue
2. Create user account:
   - Your name
   - Computer name
   - Username
   - Password
3. Wait for installation to complete
4. Click "Restart Now"
5. Remove USB drive when prompted
6. Press Enter

### Phase 3: First Boot

#### 3.1 Boot Selection

After restart, you should see GRUB bootloader:
- Select "Ubuntu" to boot into Ubuntu
- Select "macOS" to boot into macOS (if dual boot)

If you don't see GRUB:
1. Hold Option during boot
2. Select "EFI Boot" or "Ubuntu"

#### 3.2 Initial Login

1. Enter your password
2. Complete initial setup wizard
3. Connect to WiFi (may not work yet - this is normal)

### Phase 4: Post-Installation Configuration

#### 4.1 Update System

```bash
# Update package lists
sudo apt update

# Upgrade all packages
sudo apt upgrade -y
```

#### 4.2 Install T2 Kernel

```bash
# Add T2 repository
curl -s --compressed "https://adnrw.github.io/apt/KEY.gpg" | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/t2-ubuntu-repo.gpg >/dev/null
sudo curl -s --compressed -o /etc/apt/sources.list.d/t2.list "https://adnrw.github.io/apt/t2.list"

# Update and install
sudo apt update
sudo apt install -y linux-t2
```

#### 4.3 Configure Boot Parameters

```bash
# Edit GRUB configuration
sudo nano /etc/default/grub

# Modify this line:
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash intel_iommu=on iommu=pt pcie_ports=compat"

# Save and exit (Ctrl+X, Y, Enter)

# Update GRUB
sudo update-grub
```

#### 4.4 Install WiFi Drivers

```bash
# Install WiFi firmware
sudo apt install -y firmware-b43-installer

# If above doesn't work, try:
sudo apt install -y bcmwl-kernel-source
```

#### 4.5 Reboot

```bash
sudo reboot
```

After reboot, WiFi should work.

## Verification

After installation, verify everything works:

```bash
# Check kernel version (should show t2)
uname -r

# Check WiFi
nmcli device status

# Check audio devices
aplay -l

# Check graphics
glxinfo | grep "OpenGL renderer"
```

## Common Installation Issues

### Issue: Stuck at Black Screen

**Solution:**
1. Reboot and select "Advanced options" in GRUB
2. Add `nomodeset` to boot parameters
3. Boot and install proper graphics drivers

### Issue: Cannot Boot into USB

**Solution:**
1. Verify USB was created correctly
2. Check Startup Security Utility settings
3. Try different USB port
4. Recreate bootable USB

### Issue: WiFi Not Showing

**Solution:**
This is normal during installation. WiFi will work after installing T2 kernel modules.

### Issue: Keyboard Not Working During Installation

**Solution:**
1. Connect external USB keyboard
2. Complete installation
3. Keyboard will work after T2 modules are installed

### Issue: GRUB Not Showing macOS

**Solution:**
```bash
sudo os-prober
sudo update-grub
```

## Next Steps

After successful installation:
1. Read [CONFIGURATION.md](CONFIGURATION.md) for hardware optimization
2. Read [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for common issues
3. Join the T2 Linux community for support

## Rollback Procedure

If you need to remove Ubuntu:

### For Dual Boot:

1. Boot into macOS
2. Open Disk Utility
3. Select Ubuntu partition
4. Delete partition
5. Expand macOS partition (optional)
6. Remove GRUB:
   ```bash
   sudo diskutil list
   sudo diskutil mount disk0s1
   cd /Volumes/EFI/EFI
   sudo rm -rf ubuntu BOOT
   ```

### For Single Boot:

Use macOS Recovery to reinstall macOS:
1. Boot into Recovery Mode (Command + R)
2. Disk Utility → Erase disk
3. Reinstall macOS

## Support

- [T2 Linux Forums](https://github.com/t2linux/discussions)
- [Ubuntu Forums](https://ubuntuforums.org/)
- [GitHub Issues](https://github.com/johnzero-gh/Ubuntu-on-MacBook-Pro-T2/issues)
