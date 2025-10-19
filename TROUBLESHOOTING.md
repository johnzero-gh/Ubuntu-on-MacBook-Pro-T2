# Troubleshooting Guide

This guide covers common issues when running Ubuntu on MacBook Pro T2 and their solutions.

## Table of Contents

- [Boot Issues](#boot-issues)
- [WiFi Problems](#wifi-problems)
- [Audio Issues](#audio-issues)
- [Keyboard and Trackpad](#keyboard-and-trackpad)
- [Display Problems](#display-problems)
- [Suspend and Resume](#suspend-and-resume)
- [Performance Issues](#performance-issues)
- [Bluetooth Problems](#bluetooth-problems)
- [Battery and Power](#battery-and-power)
- [System Stability](#system-stability)

## Boot Issues

### System Won't Boot After Installation

**Symptoms:** Black screen, stuck at loading screen, or boot loops

**Solutions:**

1. **Boot with nomodeset:**
   - At GRUB menu, press 'e' to edit boot entry
   - Add `nomodeset` to kernel parameters
   - Press Ctrl+X to boot
   
   ```bash
   # If successful, make permanent:
   sudo nano /etc/default/grub
   # Add to GRUB_CMDLINE_LINUX_DEFAULT
   sudo update-grub
   ```

2. **Reset NVRAM/PRAM (on Mac side):**
   - Boot into macOS
   - Restart and hold: Command + Option + P + R
   - Hold until you hear startup sound twice
   - Release and boot Ubuntu

3. **Reinstall GRUB:**
   ```bash
   # Boot from USB in recovery mode
   sudo mount /dev/nvme0n1p5 /mnt  # Adjust partition
   sudo mount /dev/nvme0n1p1 /mnt/boot/efi
   sudo grub-install --root-directory=/mnt /dev/nvme0n1
   sudo chroot /mnt
   update-grub
   exit
   sudo reboot
   ```

### GRUB Not Showing macOS

**Solutions:**

```bash
# Detect other operating systems
sudo os-prober

# Update GRUB
sudo update-grub

# If still not showing, manually add entry:
sudo nano /etc/grub.d/40_custom
```

Add:
```
menuentry "macOS" {
    insmod hfsplus
    set root='hd0,gpt2'  # Adjust partition number
    chainloader /System/Library/CoreServices/boot.efi
}
```

```bash
sudo update-grub
```

### Boot Takes Too Long

**Solutions:**

1. **Check systemd services:**
   ```bash
   systemd-analyze blame
   systemd-analyze critical-chain
   ```

2. **Disable slow services:**
   ```bash
   sudo systemctl disable <slow-service>
   ```

3. **Reduce GRUB timeout:**
   ```bash
   sudo nano /etc/default/grub
   # Set: GRUB_TIMEOUT=2
   sudo update-grub
   ```

## WiFi Problems

### WiFi Not Detected

**Solutions:**

1. **Check if device exists:**
   ```bash
   lspci | grep -i network
   ip link show
   ```

2. **Install/reinstall drivers:**
   ```bash
   sudo apt install --reinstall bcmwl-kernel-source
   # OR
   sudo apt install --reinstall firmware-b43-installer
   ```

3. **Check if blocked:**
   ```bash
   rfkill list
   sudo rfkill unblock wifi
   ```

4. **Reload module:**
   ```bash
   sudo modprobe -r brcmfmac
   sudo modprobe brcmfmac
   sudo systemctl restart NetworkManager
   ```

### WiFi Keeps Disconnecting

**Solutions:**

1. **Disable power management:**
   ```bash
   sudo nano /etc/NetworkManager/conf.d/wifi-powersave.conf
   ```
   
   Add:
   ```
   [connection]
   wifi.powersave = 2
   ```
   
   ```bash
   sudo systemctl restart NetworkManager
   ```

2. **Adjust module parameters:**
   ```bash
   sudo nano /etc/modprobe.d/brcmfmac.conf
   ```
   
   Add:
   ```
   options brcmfmac roamoff=1
   ```
   
   ```bash
   sudo modprobe -r brcmfmac && sudo modprobe brcmfmac
   ```

### Slow WiFi Speed

**Solutions:**

1. **Check connection:**
   ```bash
   nmcli device show wlan0 | grep GENERAL.SPEED
   ```

2. **Force 5GHz band:**
   ```bash
   # In Network Settings, set to prefer 5GHz
   nmcli connection modify <connection-name> 802-11-wireless.band a
   ```

3. **Update firmware:**
   ```bash
   sudo apt update
   sudo apt install --reinstall linux-firmware
   ```

## Audio Issues

### No Sound Output

**Solutions:**

1. **Check audio devices:**
   ```bash
   aplay -l
   pactl list sinks short
   ```

2. **Reload ALSA:**
   ```bash
   sudo alsa force-reload
   ```

3. **Restart PulseAudio:**
   ```bash
   pulseaudio -k
   pulseaudio --start
   ```

4. **Check module loading:**
   ```bash
   sudo modprobe -r snd_hda_intel
   sudo modprobe snd_hda_intel
   ```

5. **Configure module options:**
   ```bash
   sudo nano /etc/modprobe.d/t2-audio.conf
   ```
   
   Add:
   ```
   options snd_hda_intel model=auto dmic_detect=0
   ```

### Microphone Not Working

**Solutions:**

1. **Check input devices:**
   ```bash
   arecord -l
   pactl list sources short
   ```

2. **Test microphone:**
   ```bash
   arecord -d 5 test.wav
   aplay test.wav
   ```

3. **Adjust PulseAudio settings:**
   ```bash
   pavucontrol
   # Go to Input Devices tab
   # Unmute and adjust levels
   ```

### Audio Crackling or Distortion

**Solutions:**

1. **Adjust buffer size:**
   ```bash
   sudo nano /etc/pulse/daemon.conf
   ```
   
   Uncomment/modify:
   ```
   default-fragments = 4
   default-fragment-size-msec = 25
   ```

2. **Disable power saving:**
   ```bash
   sudo nano /etc/modprobe.d/t2-audio.conf
   ```
   
   Add:
   ```
   options snd_hda_intel power_save=0
   ```

3. **Restart audio:**
   ```bash
   pulseaudio -k
   sudo alsa force-reload
   ```

## Keyboard and Trackpad

### Keyboard Not Working

**Solutions:**

1. **Check module:**
   ```bash
   lsmod | grep apple
   ```

2. **Reload module:**
   ```bash
   sudo modprobe -r apple_ib_tb
   sudo modprobe apple_ib_tb
   ```

3. **Check kernel parameters:**
   ```bash
   cat /proc/cmdline | grep intel_iommu
   ```

### Function Keys Not Working

**Solutions:**

1. **Change fn mode:**
   ```bash
   echo 2 | sudo tee /sys/module/apple_ib_tb/parameters/fnmode
   ```

2. **Make permanent:**
   ```bash
   sudo nano /etc/modprobe.d/t2-input.conf
   ```
   
   Add:
   ```
   options apple_ib_tb fnmode=2
   ```

3. **Update initramfs:**
   ```bash
   sudo update-initramfs -u
   ```

### Trackpad Too Sensitive/Not Sensitive

**Solutions:**

1. **Adjust with xinput:**
   ```bash
   # List devices
   xinput list
   
   # Get device properties
   xinput list-props <device-id>
   
   # Adjust acceleration
   xinput set-prop <device-id> "libinput Accel Speed" 0.5
   ```

2. **Configure permanently:**
   ```bash
   sudo nano /usr/share/X11/xorg.conf.d/30-touchpad.conf
   ```
   
   Modify `AccelSpeed` value

### Keyboard Backlight Not Working

**Solutions:**

1. **Check sysfs:**
   ```bash
   ls /sys/class/leds/
   cat /sys/class/leds/spi::kbd_backlight/brightness
   ```

2. **Set brightness:**
   ```bash
   echo 100 | sudo tee /sys/class/leds/spi::kbd_backlight/brightness
   ```

3. **Add udev rule:**
   ```bash
   sudo nano /etc/udev/rules.d/99-kbd-backlight.rules
   ```
   
   Add:
   ```
   ACTION=="add", SUBSYSTEM=="leds", KERNEL=="spi::kbd_backlight", RUN+="/bin/chmod g+w /sys/class/leds/%k/brightness"
   ```

## Display Problems

### Display Brightness Control Not Working

**Solutions:**

1. **Check backlight devices:**
   ```bash
   ls /sys/class/backlight/
   ```

2. **Test manually:**
   ```bash
   echo 50 | sudo tee /sys/class/backlight/intel_backlight/brightness
   ```

3. **Add kernel parameter:**
   ```bash
   sudo nano /etc/default/grub
   # Add to GRUB_CMDLINE_LINUX_DEFAULT:
   # acpi_backlight=vendor
   sudo update-grub
   ```

### External Display Not Working

**Solutions:**

1. **Detect displays:**
   ```bash
   xrandr --listproviders
   xrandr
   ```

2. **Force detection:**
   ```bash
   xrandr --output HDMI-1 --auto
   ```

3. **For Thunderbolt displays:**
   ```bash
   sudo apt install bolt
   boltctl list
   boltctl enroll <device-id>
   ```

### Screen Tearing

**Solutions:**

1. **Enable TearFree (Intel):**
   ```bash
   sudo nano /etc/X11/xorg.conf.d/20-intel.conf
   ```
   
   Add:
   ```
   Section "Device"
       Identifier "Intel Graphics"
       Driver "intel"
       Option "TearFree" "true"
   EndSection
   ```

2. **For Wayland users:**
   ```bash
   # Usually not an issue with Wayland
   # Switch to Wayland session if using X11
   ```

## Suspend and Resume

### System Won't Suspend

**Solutions:**

1. **Check suspend method:**
   ```bash
   cat /sys/power/mem_sleep
   ```

2. **Change sleep mode:**
   ```bash
   sudo nano /etc/default/grub
   # Add: mem_sleep_default=deep
   sudo update-grub
   ```

3. **Check for blocking processes:**
   ```bash
   systemd-inhibit --list
   ```

### System Won't Resume

**Solutions:**

1. **Try different sleep state:**
   ```bash
   echo s2idle | sudo tee /sys/power/mem_sleep
   ```

2. **Disable problematic modules:**
   ```bash
   sudo nano /etc/modprobe.d/blacklist.conf
   ```
   
   Add:
   ```
   blacklist thunderbolt
   ```

3. **Check logs:**
   ```bash
   journalctl -b -1 -e
   dmesg | grep -i suspend
   ```

## Performance Issues

### System Feels Slow

**Solutions:**

1. **Check CPU frequency:**
   ```bash
   watch -n 1 "cat /proc/cpuinfo | grep MHz"
   ```

2. **Set performance governor:**
   ```bash
   echo performance | sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor
   ```

3. **Check system load:**
   ```bash
   htop
   iostat -x 1
   ```

4. **Disable swap if you have enough RAM:**
   ```bash
   sudo swapoff -a
   ```

### High CPU Usage

**Solutions:**

1. **Identify process:**
   ```bash
   top
   # Press Shift+P to sort by CPU
   ```

2. **Check for background processes:**
   ```bash
   ps aux | grep -v "0.0.*0.0"
   ```

3. **Disable unnecessary services:**
   ```bash
   systemctl list-units --type=service --state=running
   sudo systemctl disable <service>
   ```

## Bluetooth Problems

### Bluetooth Not Working

**Solutions:**

1. **Check status:**
   ```bash
   rfkill list
   sudo systemctl status bluetooth
   ```

2. **Unblock and restart:**
   ```bash
   sudo rfkill unblock bluetooth
   sudo systemctl restart bluetooth
   ```

3. **Check hardware:**
   ```bash
   lsusb | grep -i bluetooth
   hciconfig -a
   ```

### Can't Pair Devices

**Solutions:**

1. **Reset Bluetooth:**
   ```bash
   sudo systemctl stop bluetooth
   sudo rm -rf /var/lib/bluetooth/*
   sudo systemctl start bluetooth
   ```

2. **Use bluetoothctl:**
   ```bash
   bluetoothctl
   power on
   agent on
   default-agent
   scan on
   pair <device-mac>
   connect <device-mac>
   ```

## Battery and Power

### Battery Drains Too Fast

**Solutions:**

1. **Install TLP:**
   ```bash
   sudo apt install tlp tlp-rdw
   sudo systemctl enable tlp
   sudo systemctl start tlp
   ```

2. **Check battery stats:**
   ```bash
   sudo tlp-stat -b
   upower -i /org/freedesktop/UPower/devices/battery_BAT0
   ```

3. **Monitor power usage:**
   ```bash
   sudo powertop
   # Press Tab to navigate to "Tunables"
   # Press Enter to toggle "Good" settings
   ```

4. **Reduce brightness:**
   ```bash
   echo 30 | sudo tee /sys/class/backlight/*/brightness
   ```

### System Doesn't Detect AC Adapter

**Solutions:**

1. **Check power supply:**
   ```bash
   upower -i /org/freedesktop/UPower/devices/line_power_AC
   ```

2. **Reload power module:**
   ```bash
   sudo modprobe -r battery ac
   sudo modprobe battery ac
   ```

## System Stability

### Random Freezes

**Solutions:**

1. **Check system logs:**
   ```bash
   journalctl -p 3 -b
   dmesg | tail -50
   ```

2. **Check memory:**
   ```bash
   sudo memtest86+  # Reboot required
   ```

3. **Check disk:**
   ```bash
   sudo smartctl -a /dev/nvme0n1
   sudo fsck -n /dev/nvme0n1p5  # Check only, no repair
   ```

4. **Disable PSR (Panel Self Refresh):**
   ```bash
   sudo nano /etc/default/grub
   # Add: i915.enable_psr=0
   sudo update-grub
   ```

### Kernel Panics

**Solutions:**

1. **Boot with older kernel:**
   - Select "Advanced options" in GRUB
   - Choose older kernel version

2. **Check crash logs:**
   ```bash
   ls /var/crash/
   sudo apport-unpack /var/crash/*.crash /tmp/crash-report
   ```

3. **Update kernel:**
   ```bash
   sudo apt update
   sudo apt install --reinstall linux-t2
   ```

## Getting Help

If issues persist:

1. **Gather system information:**
   ```bash
   # Create diagnostic report
   sudo apt install inxi
   inxi -Fxz > ~/system-info.txt
   
   # Gather logs
   journalctl -b > ~/system-journal.txt
   dmesg > ~/dmesg.txt
   lsmod > ~/modules.txt
   ```

2. **Search for similar issues:**
   - [T2 Linux Wiki](https://wiki.t2linux.org/)
   - [Ubuntu Forums](https://ubuntuforums.org/)
   - [GitHub Issues](https://github.com/t2linux/T2-Ubuntu/issues)

3. **Ask for help:**
   - Post in T2 Linux community forums
   - Create GitHub issue with system info
   - Join T2 Linux Discord/Matrix

## Emergency Recovery

If system is completely broken:

1. **Boot into recovery mode:**
   - Hold Shift at GRUB
   - Select "Advanced options"
   - Choose "recovery mode"

2. **Access root shell:**
   - Select "root" from recovery menu

3. **Fix broken packages:**
   ```bash
   mount -o remount,rw /
   apt update
   apt --fix-broken install
   dpkg --configure -a
   ```

4. **Restore from backup:**
   ```bash
   # If you created backups
   cp /path/to/backup/grub /etc/default/grub
   update-grub
   ```

5. **Reinstall system (last resort):**
   - Boot from USB
   - Keep /home partition
   - Reinstall Ubuntu

## See Also

- [CONFIGURATION.md](CONFIGURATION.md) - Configuration reference
- [INSTALLATION.md](INSTALLATION.md) - Installation guide
- [README.md](README.md) - Overview
