# Configuration Files

This directory contains example configuration files for Ubuntu on MacBook Pro T2.

## Directory Structure

```
configs/
├── grub/              # GRUB bootloader configurations
├── modprobe.d/        # Kernel module configurations
├── systemd/           # Systemd service configurations
├── tlp/               # TLP power management configurations
└── udev/              # UDEV rules for hardware permissions
```

## Installation Instructions

### GRUB Configuration

```bash
# Backup existing configuration
sudo cp /etc/default/grub /etc/default/grub.bak

# Copy T2 configuration
sudo cp grub/grub-t2-defaults /etc/default/grub

# Update GRUB
sudo update-grub

# Reboot to apply changes
sudo reboot
```

### Modprobe Configuration

```bash
# Install audio configuration
sudo cp modprobe.d/t2-audio.conf /etc/modprobe.d/

# Install WiFi configuration
sudo cp modprobe.d/t2-wifi.conf /etc/modprobe.d/

# Install input configuration
sudo cp modprobe.d/t2-input.conf /etc/modprobe.d/

# Update initramfs to include new configurations
sudo update-initramfs -u

# Reboot to apply changes
sudo reboot
```

### UDEV Rules

```bash
# Install keyboard backlight rules
sudo cp udev/99-t2-keyboard-backlight.rules /etc/udev/rules.d/

# Install display backlight rules
sudo cp udev/99-t2-display-backlight.rules /etc/udev/rules.d/

# Add your user to video group
sudo usermod -aG video $USER

# Reload UDEV rules
sudo udevadm control --reload-rules
sudo udevadm trigger

# Log out and back in for group changes to take effect
```

### TLP Configuration

```bash
# Install TLP if not already installed
sudo apt install tlp tlp-rdw

# Backup existing configuration (if any)
sudo cp /etc/tlp.conf /etc/tlp.conf.bak 2>/dev/null || true

# Copy T2-optimized configuration
sudo cp tlp/tlp-t2.conf /etc/tlp.conf

# Enable and start TLP
sudo systemctl enable tlp
sudo systemctl start tlp

# Check status
sudo tlp-stat -s
```

## Configuration Overview

### GRUB (grub-t2-defaults)

Contains essential kernel parameters for T2 compatibility:
- `intel_iommu=on iommu=pt` - Required for T2 chip
- `pcie_ports=compat` - PCIe compatibility mode
- Optional parameters for specific issues (commented)

### Audio (t2-audio.conf)

Configures the Cirrus Logic CS8409 audio codec:
- Disables problematic auto-detection
- Prevents audio dropouts
- Optimizes power management

### WiFi (t2-wifi.conf)

Configures Broadcom WiFi chip:
- Improves connection stability
- Disables roaming
- Reduces power-related disconnects

### Input (t2-input.conf)

Configures keyboard and trackpad:
- Sets function key behavior
- Configures keyboard layout
- Enables proper key mapping

### Backlight Rules

Allows non-root users to control brightness:
- Keyboard backlight control
- Display backlight control
- Adds permissions for video group

### TLP (tlp-t2.conf)

Optimizes power management:
- Battery health settings
- CPU governor configuration
- GPU power management
- WiFi power saving
- USB autosuspend

## Customization

Feel free to modify these configurations for your needs:

1. **Copy the file** you want to customize
2. **Edit parameters** as needed
3. **Test changes** on your hardware
4. **Share improvements** via pull request

## Verification

After installing configurations, verify they work:

### Check GRUB Parameters

```bash
cat /proc/cmdline
```

Should show: `intel_iommu=on iommu=pt pcie_ports=compat`

### Check Loaded Modules

```bash
# Audio
lsmod | grep snd_hda_intel

# WiFi
lsmod | grep brcmfmac

# Input
lsmod | grep apple
```

### Check Module Parameters

```bash
# Audio parameters
systool -v -m snd_hda_intel

# Input parameters
systool -v -m apple_ib_tb
```

### Test Backlight Control

```bash
# Keyboard backlight
echo 100 | sudo tee /sys/class/leds/spi::kbd_backlight/brightness

# Display backlight (should work without sudo after adding to video group)
echo 50 | tee /sys/class/backlight/intel_backlight/brightness
```

### Check TLP Status

```bash
sudo tlp-stat -s
sudo tlp-stat -b  # Battery status
sudo tlp-stat -p  # Processor status
```

## Troubleshooting

### Configuration Not Applied

```bash
# For modprobe configs
sudo update-initramfs -u
sudo reboot

# For GRUB
sudo update-grub
sudo reboot

# For UDEV rules
sudo udevadm control --reload-rules
sudo udevadm trigger
```

### Conflicting Configurations

```bash
# Check for conflicting modprobe configs
ls -la /etc/modprobe.d/

# Check for conflicting UDEV rules
ls -la /etc/udev/rules.d/

# Remove conflicts if found
sudo rm /etc/modprobe.d/conflicting-file.conf
```

### Reverting Changes

```bash
# Restore GRUB backup
sudo cp /etc/default/grub.bak /etc/default/grub
sudo update-grub

# Remove modprobe configs
sudo rm /etc/modprobe.d/t2-*.conf
sudo update-initramfs -u

# Remove UDEV rules
sudo rm /etc/udev/rules.d/99-t2-*.rules
sudo udevadm control --reload-rules

# Restore TLP defaults
sudo rm /etc/tlp.conf
sudo apt install --reinstall tlp
```

## Contributing

Have improved configurations? Please share them:

1. Test on your hardware
2. Document what you changed and why
3. Submit a pull request
4. Include your hardware model and test results

See [CONTRIBUTING.md](../CONTRIBUTING.md) for details.

## Support

For help with configurations:
- Check [TROUBLESHOOTING.md](../TROUBLESHOOTING.md)
- Review [CONFIGURATION.md](../CONFIGURATION.md)
- Open an issue on GitHub
- Ask in T2 Linux community forums

## Additional Resources

- [T2 Linux Wiki](https://wiki.t2linux.org/)
- [Arch Wiki - MacBookPro](https://wiki.archlinux.org/title/MacBookPro)
- [Ubuntu Wiki - MacBook](https://help.ubuntu.com/community/MacBook)
