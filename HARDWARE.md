# Hardware Compatibility

This document provides detailed information about hardware compatibility for Ubuntu on MacBook Pro T2 models.

## Supported Models

### MacBook Pro 13-inch

| Model | Year | Status | Notes |
|-------|------|--------|-------|
| MacBook Pro 13" Two Thunderbolt 3 ports | 2018 | ✅ Fully Supported | All hardware functional with T2 kernel |
| MacBook Pro 13" Four Thunderbolt 3 ports | 2018 | ✅ Fully Supported | All hardware functional with T2 kernel |
| MacBook Pro 13" Two Thunderbolt 3 ports | 2019 | ✅ Fully Supported | All hardware functional with T2 kernel |
| MacBook Pro 13" Four Thunderbolt 3 ports | 2019 | ✅ Fully Supported | All hardware functional with T2 kernel |
| MacBook Pro 13" | 2020 Intel | ✅ Fully Supported | All hardware functional with T2 kernel |

### MacBook Pro 15-inch

| Model | Year | Status | Notes |
|-------|------|--------|-------|
| MacBook Pro 15" | 2018 | ✅ Fully Supported | Dedicated GPU requires additional setup |
| MacBook Pro 15" | 2019 | ✅ Fully Supported | Dedicated GPU requires additional setup |

### MacBook Pro 16-inch

| Model | Year | Status | Notes |
|-------|------|--------|-------|
| MacBook Pro 16" | 2019 | ✅ Fully Supported | Dedicated GPU requires additional setup |
| MacBook Pro 16" | 2020 Intel | ✅ Fully Supported | Dedicated GPU requires additional setup |

**Note:** MacBook Pro with M1/M2 chips (2020 and later with Apple Silicon) are NOT supported and require different methods (Asahi Linux).

## Hardware Component Status

### T2 Security Chip

| Feature | Status | Notes |
|---------|--------|-------|
| Secure Enclave | ⚠️ Not Supported | Apple proprietary, no Linux support |
| Touch ID | ❌ Not Supported | Requires macOS |
| Disk Encryption (T2) | ⚠️ Limited | Use Linux encryption instead (LUKS) |
| Hardware Audio Processing | ⚠️ Partial | Basic audio works, advanced features limited |

### Display

| Feature | Status | Notes |
|---------|--------|-------|
| Internal Display | ✅ Fully Supported | Retina resolution fully supported |
| Brightness Control | ✅ Fully Supported | Function keys work |
| True Tone | ❌ Not Supported | macOS feature only |
| Night Shift | ✅ Supported | Use GNOME Night Light or similar |
| HiDPI Scaling | ✅ Fully Supported | Fractional scaling available |
| Auto-brightness | ❌ Not Supported | Manual control only |

### Audio

| Feature | Status | Notes |
|---------|--------|-------|
| Internal Speakers | ✅ Fully Supported | Both speakers functional |
| Headphone Jack | ✅ Fully Supported | 3.5mm jack works |
| Internal Microphone | ✅ Fully Supported | Array microphone works |
| Volume Control | ✅ Fully Supported | Function keys work |
| Audio Jack Detection | ✅ Fully Supported | Auto-switch works |
| Dolby Audio | ❌ Not Supported | Software equivalent available |

### Input Devices

| Feature | Status | Notes |
|---------|--------|-------|
| Keyboard | ✅ Fully Supported | Butterfly/Magic keyboard works |
| Function Keys | ✅ Fully Supported | All Fn keys functional |
| Keyboard Backlight | ✅ Fully Supported | Brightness control works |
| Trackpad | ✅ Fully Supported | Multi-touch gestures work |
| Force Touch | ⚠️ Partial | Basic click works, pressure sensitivity limited |
| Touch Bar | ⚠️ Limited | Basic function keys, limited customization |

### Wireless

| Feature | Status | Notes |
|---------|--------|-------|
| WiFi (802.11ac) | ✅ Fully Supported | Broadcom WiFi works with drivers |
| WiFi 6 (802.11ax) | ✅ Fully Supported | On supported models |
| Bluetooth 5.0/5.1 | ✅ Fully Supported | All BT devices work |
| AirDrop | ❌ Not Supported | Apple proprietary |
| Handoff | ❌ Not Supported | Apple proprietary |

### Ports and Expansion

| Feature | Status | Notes |
|---------|--------|-------|
| Thunderbolt 3/USB-C | ✅ Fully Supported | All ports functional |
| USB-A (via adapter) | ✅ Fully Supported | Works through USB-C |
| HDMI (via adapter) | ✅ Fully Supported | 4K@60Hz supported |
| DisplayPort | ✅ Fully Supported | Via USB-C |
| eGPU | ⚠️ Partial | Some eGPUs work, varies by model |
| SD Card (via adapter) | ✅ Fully Supported | Standard SD card readers work |

### Storage

| Feature | Status | Notes |
|---------|--------|-------|
| Internal NVMe SSD | ✅ Fully Supported | Full speed access |
| TRIM Support | ✅ Fully Supported | Enabled by default |
| APFS Partitions | ⚠️ Read-only | Can read macOS partitions |

### Camera

| Feature | Status | Notes |
|---------|--------|-------|
| FaceTime HD Camera | ✅ Fully Supported | 720p video works |
| Camera Privacy LED | ✅ Fully Supported | LED indicates camera use |

### Power Management

| Feature | Status | Notes |
|---------|--------|-------|
| Battery Status | ✅ Fully Supported | Accurate readings |
| AC Adapter Detection | ✅ Fully Supported | Charge status works |
| Battery Health | ⚠️ Partial | Basic info available |
| Power Management | ✅ Fully Supported | Sleep/wake works |
| Battery Cycle Count | ✅ Supported | Readable via upower |

### Graphics

#### Intel Integrated Graphics

| Feature | Status | Notes |
|---------|--------|-------|
| Intel UHD Graphics 630 | ✅ Fully Supported | Full acceleration |
| Intel Iris Plus Graphics | ✅ Fully Supported | Full acceleration |
| OpenGL | ✅ Fully Supported | Full support |
| Vulkan | ✅ Fully Supported | Via Mesa drivers |
| Hardware Acceleration | ✅ Fully Supported | Video decode/encode works |

#### AMD Discrete Graphics (15"/16" models)

| Feature | Status | Notes |
|---------|--------|-------|
| Radeon Pro 555X | ✅ Fully Supported | AMDGPU driver |
| Radeon Pro 560X | ✅ Fully Supported | AMDGPU driver |
| Radeon Pro 5300M | ✅ Fully Supported | AMDGPU driver |
| Radeon Pro 5500M | ✅ Fully Supported | AMDGPU driver |
| GPU Switching | ⚠️ Manual | Use `prime-select` |
| PowerXpress | ⚠️ Limited | Manual switching recommended |

### Sensors

| Feature | Status | Notes |
|---------|--------|-------|
| Ambient Light Sensor | ⚠️ Partial | Detected but auto-brightness not working |
| Accelerometer | ⚠️ Limited | Detected but limited functionality |
| Gyroscope | ⚠️ Limited | Detected but limited functionality |
| SMC Sensors | ✅ Partial | Temperature sensors work |

## Hardware Specifications by Model

### MacBook Pro 13" (2018-2020)

**Processor:**
- Intel Core i5/i7/i9 (8th-10th gen)
- 2-4 cores, 4-8 threads

**Memory:**
- 8GB/16GB/32GB LPDDR3/LPDDR4X
- Soldered, non-upgradeable

**Storage:**
- 128GB to 4TB NVMe SSD
- Upgradeable on some models (special tools required)

**Display:**
- 13.3" Retina (2560x1600)
- IPS, True Tone (not available in Linux)
- 500 nits brightness

**Ports:**
- 2 or 4 Thunderbolt 3 (USB-C)
- 3.5mm headphone jack

**Battery:**
- 58Wh (2-port) or 58Wh (4-port)
- Up to 10 hours (varies in Linux)

### MacBook Pro 15" (2018-2019)

**Processor:**
- Intel Core i7/i9 (8th-9th gen)
- 6-8 cores, 12-16 threads

**Memory:**
- 16GB/32GB DDR4
- Soldered, non-upgradeable

**Storage:**
- 256GB to 4TB NVMe SSD
- Upgradeable (special tools required)

**Display:**
- 15.4" Retina (2880x1800)
- IPS, True Tone
- 500 nits brightness

**Graphics:**
- Intel UHD Graphics 630
- Radeon Pro 555X/560X/Vega 16/20 (4GB)

**Ports:**
- 4 Thunderbolt 3 (USB-C)
- 3.5mm headphone jack

**Battery:**
- 83.6Wh
- Up to 10 hours (varies in Linux)

### MacBook Pro 16" (2019-2020)

**Processor:**
- Intel Core i7/i9 (9th-10th gen)
- 6-8 cores, 12-16 threads

**Memory:**
- 16GB/32GB/64GB DDR4
- Soldered, non-upgradeable

**Storage:**
- 512GB to 8TB NVMe SSD
- Upgradeable (special tools required)

**Display:**
- 16" Retina (3072x1920)
- IPS, True Tone
- 500 nits brightness

**Graphics:**
- Intel UHD Graphics 630
- Radeon Pro 5300M/5500M (4GB/8GB)

**Ports:**
- 4 Thunderbolt 3 (USB-C)
- 3.5mm headphone jack

**Battery:**
- 99.8Wh
- Up to 11 hours (varies in Linux)

## Detailed Component Information

### WiFi Chipsets

| Model | Chipset | Driver | Status |
|-------|---------|--------|--------|
| 2018-2020 | Broadcom BCM4377 | brcmfmac | ✅ Working |
| Alternative | Broadcom BCM4364 | brcmfmac | ✅ Working |

**Installation:**
```bash
sudo apt install firmware-b43-installer
```

### Bluetooth Chipsets

| Model | Chipset | Driver | Status |
|-------|---------|--------|--------|
| All T2 Models | Broadcom | btbcm | ✅ Working |

### Audio Codec

| Model | Codec | Driver | Status |
|-------|-------|--------|--------|
| All T2 Models | Cirrus Logic CS8409 | snd_hda_intel | ✅ Working |

### Keyboard/Trackpad Controller

| Model | Controller | Driver | Status |
|-------|------------|--------|--------|
| All T2 Models | Apple iBridge | apple_ib_tb | ✅ Working |

### Touch Bar Controller

| Model | Controller | Driver | Status |
|-------|------------|--------|--------|
| Touch Bar Models | Apple iBridge | apple_ib_tb | ⚠️ Limited |

## Performance Benchmarks

### CPU Performance

Expect near-native performance (95-100% of macOS):
- Compilation: ~95% of macOS speed
- Scientific computing: ~98% of macOS speed
- General tasks: 100% (sometimes faster than macOS)

### GPU Performance

**Intel Graphics:**
- OpenGL: ~90-95% of macOS
- Vulkan: ~95-100% of macOS
- Video encoding: ~90% of macOS

**AMD Graphics:**
- OpenGL: ~85-90% of macOS
- Vulkan: ~90-95% of macOS
- Gaming: Varies by title

### Battery Life

Expect 70-85% of macOS battery life:
- Web browsing: 6-8 hours
- Video playback: 7-9 hours
- Development work: 5-7 hours
- Gaming: 2-3 hours

**Tips to improve:**
- Use TLP for power management
- Lower display brightness
- Disable unused hardware (Bluetooth, etc.)
- Use CPU power-saving governor

## Known Hardware Issues

### Critical Issues
None with latest T2 kernel and drivers.

### Minor Issues

1. **Automatic Brightness:** Ambient light sensor detected but auto-brightness not implemented
2. **Touch Bar:** Limited functionality compared to macOS
3. **True Tone:** Not available (Apple proprietary)
4. **Touch ID:** Not available (requires macOS)

### Workarounds

Most issues have software workarounds:
- Manual brightness control works perfectly
- Touch Bar shows function keys
- Night Light can replace True Tone
- Use password/PIN instead of Touch ID

## eGPU Compatibility

### Tested eGPUs

| eGPU Enclosure | GPU | Status | Notes |
|----------------|-----|--------|-------|
| Razer Core X | Various | ✅ Working | Best compatibility |
| Akitio Node | AMD RX 580 | ✅ Working | Good performance |
| Sonnet Breakaway | NVIDIA GTX 1080 | ⚠️ Limited | Requires nvidia drivers |
| Any TB3 | AMD RX 5700 | ✅ Working | Good performance |

### eGPU Setup

```bash
# For AMD GPUs (recommended)
sudo apt install amdgpu-dkms

# For NVIDIA GPUs
sudo apt install nvidia-driver-<version>

# Authorize Thunderbolt device
sudo apt install bolt
boltctl list
boltctl enroll <device-id>
```

## Upgrading Hardware

### What Can Be Upgraded

| Component | Upgradeable | Difficulty | Notes |
|-----------|-------------|------------|-------|
| SSD | ✅ Yes | Hard | Requires special tools, some models only |
| RAM | ❌ No | N/A | Soldered to motherboard |
| WiFi Card | ❌ No | N/A | Soldered to motherboard |
| Battery | ✅ Yes | Medium | Apple service recommended |

### SSD Upgrade

**Compatible SSDs:**
- OWC Aura Pro X2
- Samsung 970 EVO Plus (with adapter)
- Crucial P5 (with adapter)

**Requirements:**
- Pentalobe P5 screwdriver
- T5 Torx screwdriver
- SSD adapter (if needed)
- Patience and care

**Note:** Upgrading SSD may affect warranty. Professional installation recommended.

## Thermal Performance

### Temperature Ranges (Normal Operation)

| Component | Idle | Load | Throttling |
|-----------|------|------|------------|
| CPU | 40-50°C | 80-90°C | >95°C |
| GPU | 35-45°C | 70-80°C | >85°C |
| SSD | 35-40°C | 50-60°C | >70°C |

### Thermal Management

```bash
# Install fan control
sudo apt install mbpfan

# Monitor temperatures
watch -n 1 sensors

# Check throttling
watch -n 1 "cat /proc/cpuinfo | grep MHz"
```

## Hardware Testing Commands

### Comprehensive Hardware Check

```bash
# CPU info
lscpu
cat /proc/cpuinfo

# Memory info
free -h
sudo dmidecode -t memory

# Storage info
lsblk
sudo smartctl -a /dev/nvme0n1

# PCI devices
lspci -v

# USB devices
lsusb -v

# Graphics
glxinfo | grep "OpenGL"
vulkaninfo

# Audio
aplay -l
pactl list

# Network
nmcli device status
iwconfig

# Bluetooth
hciconfig -a

# Battery
upower -i /org/freedesktop/UPower/devices/battery_BAT0

# Sensors
sensors

# All hardware
sudo lshw -short
```

## Firmware Updates

### Updating T2 Firmware

T2 firmware can only be updated through macOS:
1. Boot into macOS
2. Install macOS updates
3. Reboot into Ubuntu

### Updating Thunderbolt Firmware

```bash
sudo apt install fwupd
fwupdmgr get-devices
fwupdmgr refresh
fwupdmgr get-updates
fwupdmgr update
```

## Resources

- [T2 Linux Wiki](https://wiki.t2linux.org/)
- [Linux Hardware Database](https://linux-hardware.org/)
- [Ubuntu Hardware Support](https://ubuntu.com/certified)

## Contributing Hardware Information

To help improve this documentation:
1. Test your specific model
2. Report compatibility status
3. Share configuration that works
4. Submit pull requests with updates

---

**Last Updated:** 2025-10-19
**Maintainer:** T2 Linux Community
