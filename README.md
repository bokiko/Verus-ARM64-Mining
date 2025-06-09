# Verus Mining Setup Guide for ARM64 Devices

Complete step-by-step guide to set up Verus (VRSC) cryptocurrency mining on ARM64 single board computers with Rockchip RK3588/RK3588S chips.

## Table of Contents

1. [What You Need](#what-you-need)
2. [Compatible Devices](#compatible-devices)
3. [Download Ubuntu Image](#download-ubuntu-image)
4. [Install Ubuntu](#install-ubuntu)
5. [Initial System Setup](#initial-system-setup)
6. [Install Mining Software](#install-mining-software)
7. [Configure Your Miner](#configure-your-miner)
8. [Test Your Miner](#test-your-miner)
9. [Setup Auto-Start](#setup-auto-start)
10. [Monitoring Your Miner](#monitoring-your-miner)
11. [Troubleshooting](#troubleshooting)
12. [Useful Commands](#useful-commands)

---

## What You Need

Before starting, make sure you have:

### Hardware Requirements
- **ARM64 device** with RK3588/RK3588S chipset
- **RAM**: Minimum 4GB (8GB+ recommended)
- **Storage**: MicroSD card 32GB or larger
- **Power Supply**: Quality 5V/3A adapter (very important!)
- **Network**: Ethernet cable or WiFi
- **Cooling**: Heatsink recommended

### Software Requirements
- Computer to flash SD card
- [Balena Etcher](https://www.balena.io/etcher/) for flashing
- Your Verus wallet address
- Mining pool of choice

⚠️ **Important**: Use a quality power supply! Cheap adapters cause random reboots and mining failures.

---

## Compatible Devices

This guide works with any ARM64 device using RK3588/RK3588S chipsets:

**Radxa Rock Series:**
- Rock 5A (RK3588)
- Rock 5B (RK3588) 
- Rock 5C (RK3588S2)

**Orange Pi Series:**
- Orange Pi 5 (RK3588S)
- Orange Pi 5 Plus (RK3588)

**Other Compatible Devices:**
- Khadas Edge2 (RK3588S)
- Firefly ROC-RK3588S-PC
- Banana Pi BPI-M7 (RK3588)
- Any ARM64 device with RK3588/RK3588S chipset

---

## Download Ubuntu Image

Download the Ubuntu image for your specific device:

- **Radxa Rock 5C**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/rock-5c.html
- **Radxa Rock 5A**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/rock-5a.html
- **Radxa Rock 5B**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/rock-5b.html
- **Orange Pi 5**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/orangepi-5.html
- **Orange Pi 5 Plus**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/orangepi-5-plus.html

💡 **Tip**: Download the latest stable version, not beta releases.

---

## Install Ubuntu

### Step 1: Flash Ubuntu to SD Card
1. Download and install [Balena Etcher](https://www.balena.io/etcher/)
2. Insert your microSD card into your computer
3. Open Balena Etcher
4. Select the Ubuntu image file you downloaded
5. Select your SD card
6. Click "Flash" and wait for completion

### Step 2: Boot Your Device
1. Insert the SD card into your ARM64 device
2. Connect ethernet cable (recommended) or setup WiFi later
3. Connect power adapter
4. Your device should boot to Ubuntu

⚠️ **First boot takes 2-3 minutes** - be patient!

---

## Initial System Setup

### Step 1: Login to Ubuntu
```
Username: ubuntu
Password: ubuntu
```

You'll be prompted to change the password on first login.

### Step 2: Change Device Hostname
```bash
sudo nano /etc/hostname
```
- Delete everything and type your preferred hostname
- Press `Ctrl+X`, then `Y`, then `Enter` to save

### Step 3: Update Hosts File
**Important**: Use the same hostname you chose above.

```bash
sudo nano /etc/hosts
```
- After the line `127.0.0.1 localhost`, add a new line:
```
127.0.1.1 your-hostname
```
- Press `Ctrl+X`, then `Y`, then `Enter` to save

### Step 4: Update System
```bash
sudo apt update && sudo apt upgrade -y
```
This will take 5-10 minutes depending on your internet speed.

### Step 5: Install Essential Packages
```bash
sudo apt install lm-sensors nano tmux htop build-essential tree unzip zip net-tools curl wget git -y
```

### Step 6: Setup Temperature Monitoring
```bash
sudo sensors-detect
```
- Press `Enter` to accept defaults when prompted
- Answer "YES" to most questions

Test temperature sensors:
```bash
sensors
```
You should see CPU temperature readings.

### Step 7: Reboot
```bash
sudo reboot
```
After reboot, your prompt should show your new hostname.

---

## Install Mining Software

### Step 1: Download and Install Miner
Run this command to automatically install the optimized Verus miner:

```bash
curl -o- -k https://raw.githubusercontent.com/Oink70/Android-Mining/main/install.sh | bash
```

This installs [Oink70's](https://github.com/Oink70/Android-Mining) optimized ccminer for ARM64 devices.

⏳ **This takes 2-3 minutes** - the script will download and setup everything automatically.

### Step 2: Navigate to Miner Directory
```bash
cd ccminer/
```

---

## Configure Your Miner

### Step 1: Choose Your Mining Pool
Visit https://miningpoolstats.stream/veruscoin to choose a mining pool.

Popular choices include:
- Pool.veruscoin.io
- Luckpool.net
- Zergpool.com

### Step 2: Edit Configuration
```bash
nano config.json
```

Configure these parameters:
- **Pool URL and port** (from your chosen pool)
- **Your wallet address** (your Verus wallet address)
- **Worker name** (any name to identify this miner)

Example format:
```
your-wallet-address.worker-name
```

Save with `Ctrl+X`, then `Y`, then `Enter`.

💡 **Tip**: If you don't have a Verus wallet yet, download one from https://verus.io

---

## Test Your Miner

### Step 1: Start Mining
```bash
~/ccminer/start.sh
```

### Step 2: Check Mining Status
```bash
screen -x CCminer
```

You should see:
- Connection to your pool
- Accepted shares
- Hashrate information

### Step 3: Monitor System Performance
Open a new terminal session and run:
```bash
htop
```
Check CPU usage (should be near 100%).

```bash
sensors
```
Monitor temperature (should stay under 80°C).

### Step 4: Detach from Mining Session
To leave the miner running and return to command prompt:
- Press `Ctrl+A`, then `D`

The miner continues running in the background.

---

## Setup Auto-Start

Make your miner start automatically when the device boots.

### Step 1: Edit Crontab
```bash
crontab -e
```
- Choose option `1` (nano editor)

### Step 2: Add Auto-Start Command
Add this line at the end of the file:
```
@reboot ~/ccminer/start.sh
```

Save with `Ctrl+X`, then `Y`, then `Enter`.

### Step 3: Test Auto-Start
```bash
sudo reboot
```

After reboot, check if mining started automatically:
```bash
screen -x CCminer
```

✅ **Success!** Your miner now starts automatically on boot.

---

## Monitoring Your Miner

### Check Mining Status
```bash
screen -x CCminer
```

### Monitor System Resources
```bash
htop           # CPU and memory usage
sensors        # Temperature monitoring
```

### Check Mining Logs
```bash
tail -f ~/ccminer/ccminer.log
```

### View Temperature Only
```bash
sensors | grep temp
```

---

## Troubleshooting

### Problem: Random Reboots or Crashes

**Solution**: Check power supply
- Ensure you're using a quality 5V/3A power adapter
- Try a different power cable
- Monitor temperature with `sensors`

### Problem: Mining Not Starting on Boot

**Solution**: Check auto-start setup
```bash
crontab -l                    # Verify crontab entry exists
ls -la ~/ccminer/start.sh     # Check script permissions
chmod +x ~/ccminer/start.sh   # Make executable if needed
```

### Problem: Low Hashrate

**Solution**: Optimize performance
```bash
sudo apt install cpufrequtils
sudo cpufreq-set -g performance
```

### Problem: Screen Session Issues

**Solution**: Manage screen sessions
```bash
screen -ls                    # List active sessions
screen -X -S CCminer quit     # Kill stuck sessions
screen -S CCminer             # Create new session
```

### Problem: High Temperature

**Solution**: Improve cooling
- Install heatsink on CPU
- Ensure proper ventilation
- Check that temperature stays under 80°C

---

## Useful Commands

### System Information
```bash
uname -m                          # Verify ARM64 architecture
lscpu | grep "Model name"         # Check CPU information
free -h                           # Check RAM
nproc                             # Check CPU cores
ip addr show                      # Network information
```

### Mining Management
```bash
~/ccminer/start.sh                # Start mining
pkill ccminer                     # Stop mining
screen -x CCminer                 # Check mining status
crontab -l                        # View auto-start settings
crontab -e                        # Edit auto-start settings
```

### System Monitoring
```bash
htop                              # Real-time system monitor
sensors                           # Temperature readings
cat /proc/cpuinfo | grep MHz      # CPU frequency
```

---

## Hardware Specifications

**Supported Chipsets**: RK3588, RK3588S, RK3588S2  
**Architecture**: ARM64 (aarch64)  
**CPU Configuration**: 4x Cortex-A76 + 4x Cortex-A55 (big.LITTLE)

---

## Credits and Resources

- **Ubuntu for Rockchip**: https://github.com/Joshua-Riek/ubuntu-rockchip
- **Android Mining Tools**: https://github.com/Oink70/Android-Mining
- **Verus Community**: https://verus.io

---

**Created by**: Bokiko  
**GitHub**: https://github.com/bokiko  
**Last Updated**: June 2025

> ⚠️ **Warning**: Cryptocurrency mining consumes significant power and generates heat. Ensure proper cooling and power supply. Mining profitability varies with network difficulty and electricity costs.
