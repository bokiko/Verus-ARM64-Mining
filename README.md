# Verus Mining Setup Guide for RK3588/RK3588S ARM64 Devices

Complete guide to set up Verus (VRSC) mining on ARM64 single board computers with Rockchip RK3588/RK3588S chips using Ubuntu.

## Compatible Devices

**Primary Focus**: Radxa Rock 5C (RK3588S2)

**Also Compatible With**:
- Radxa Rock 5A/5B (RK3588)
- Orange Pi 5/5 Plus (RK3588S)
- Khadas Edge2 (RK3588S)
- Firefly ROC-RK3588S-PC
- Banana Pi BPI-M7 (RK3588)
- Any ARM64 device with RK3588/RK3588S chipset

## System Requirements

- **CPU**: Rockchip RK3588 or RK3588S (ARM64 architecture)
- **RAM**: Minimum 4GB (8GB+ recommended for optimal performance)
- **Storage**: MicroSD card (32GB+ recommended) or eMMC
- **Power**: 5V/3A minimum (quality power supply crucial!)
- **Network**: Ethernet or WiFi connection
- **Architecture**: ARM64 (aarch64)



## Step 1: Install Ubuntu

### Download Ubuntu Images for RK3588/RK3588S Devices:

**Radxa Rock 5C**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/rock-5c.html  
**Radxa Rock 5A**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/rock-5a.html  
**Radxa Rock 5B**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/rock-5b.html  
**Orange Pi 5**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/orangepi-5.html  
**Orange Pi 5 Plus**: https://joshua-riek.github.io/ubuntu-rockchip-download/boards/orangepi-5-plus.html

### Installation Steps:
1. Download appropriate Ubuntu image for your specific board
2. Flash to microSD card using [Balena Etcher](https://www.balena.io/etcher/)
3. Insert SD card into your device and boot
4. Ensure your device has adequate cooling (heatsink recommended)

## Step 2: Initial Setup

### Login to the system
```bash
Username: ubuntu
Password: ubuntu
```

### Change hostname (add your hostname)
```bash
sudo nano /etc/hostname
```
Delete everything and type your preferred hostname.

Press `Ctrl+X`, then `Y`, then `Enter`

### Update hosts file
**Note**: Use the same hostname you entered in the previous step.

```bash
sudo nano /etc/hosts
```
After the line `127.0.0.1 localhost`, add (with your chosen hostname):
```
127.0.1.1 your-hostname
```
Press `Ctrl+X`, then `Y`, then `Enter`

### Update the system
```bash
sudo apt update && sudo apt upgrade -y
```

### Install essential packages
```bash
sudo apt install lm-sensors nano tmux htop build-essential tree unzip zip net-tools curl wget git -y
```

### Configure and test sensors
```bash
sudo sensors-detect
```
Press `Enter` to accept defaults when prompted.

Test sensors:
```bash
sensors
```

### Reboot to apply hostname changes
```bash
sudo reboot
```

## Step 3: Install Verus Miner

### Install miner using [Oink70's](https://github.com/Oink70/Android-Mining) automated script
```bash
curl -o- -k https://raw.githubusercontent.com/Oink70/Android-Mining/main/install.sh | bash
```

### Navigate to ccminer directory
```bash
cd ccminer/
```

### Configure mining pool and wallet
```bash
nano config.json
```

Edit the configuration file with your mining pool URL and wallet address. Configure the following parameters:
- Pool URL and port
- Your wallet address.Worker name (same line)


Choose your preferred pool from: https://miningpoolstats.stream/veruscoin

Save with `Ctrl+X`, then `Y`, then `Enter`

## Step 4: Test Mining

### Start mining manually
```bash
~/ccminer/start.sh
```

### Check mining status
```bash
screen -x CCminer
```

To detach from screen session: `Ctrl+A`, then `D`

### Monitor system performance
```bash
htop
sensors
```

## Step 5: Setup Auto-Start on Boot

### Add auto-start to crontab
```bash
crontab -e
```

Choose nano as editor (option 1), then add this line at the end:
```
@reboot ~/ccminer/start.sh
```

Save with `Ctrl+X`, then `Y`, then `Enter`

### Test auto-start
```bash
sudo reboot
```

After reboot, check if mining started automatically:
```bash
screen -x CCminer
```

## Step 6: Monitoring and Management

### Check mining status
```bash
screen -x CCminer
```

### Monitor system resources
```bash
htop
sensors
```

### Check system temperature
```bash
sensors | grep temp
```

### View mining logs
```bash
tail -f ~/ccminer/ccminer.log
```

### Restart mining if needed
```bash
pkill ccminer
~/ccminer/start.sh
```

## Troubleshooting

### Random reboots or crashes
- **Check power supply**: Ensure you're using a quality 5V/3A power adapter
- **Monitor temperature**: Run `sensors` to check CPU temperature
- **Check for adequate cooling**: Ensure proper ventilation

### Mining not starting on boot
- Verify crontab entry: `crontab -l`
- Check script permissions: `ls -la ~/ccminer/start.sh`
- Make executable if needed: `chmod +x ~/ccminer/start.sh`

### Low hashrate
- Monitor CPU usage with `htop`
- Check CPU frequency: `cat /proc/cpuinfo | grep MHz`
- Adjust CPU governor for performance:
```bash
sudo apt install cpufrequtils
sudo cpufreq-set -g performance
```

### Screen session issues
- List active sessions: `screen -ls`
- Kill stuck sessions: `screen -X -S CCminer quit`
- Create new session: `screen -S CCminer`

## Hardware Specifications

**Supported Chipsets**: RK3588, RK3588S, RK3588S2  
**Architecture**: ARM64 (aarch64)  
**CPU Cores**: 4x Cortex-A76 + 4x Cortex-A55 (big.LITTLE configuration)

## Important Notes

- **Power Supply**: Use a quality 5V/3A (or higher) power adapter - insufficient power causes random reboots
- **Cooling**: Install heatsinks and ensure proper ventilation - RK3588/RK3588S chips generate significant heat
- **RAM**: 4GB minimum, 8GB+ recommended for stable operation
- **Network**: Stable internet connection required for pool mining
- **Architecture**: This guide is specifically for ARM64 devices with RK3588/RK3588S chipsets
- **Performance**: Hashrate varies significantly with cooling, power supply quality, and thermal management

## Useful Commands

### System monitoring
```bash
### Check CPU architecture and specs
```bash
# Verify ARM64 architecture
uname -m

# Check CPU information (should show RK3588/RK3588S)
lscpu | grep "Model name"

# Verify RAM (should be 4GB+)
free -h

# Check available CPU cores
nproc
```

# Monitor real-time performance
htop

# Check temperatures
sensors

# Network status
ip addr show
```

### Mining management
```bash
# Start mining
~/ccminer/start.sh

# Stop mining
pkill ccminer

# Check mining status
screen -x CCminer

# View crontab
crontab -l

# Edit crontab
crontab -e
```

## Credits

- **Ubuntu for Rockchip**: https://github.com/Joshua-Riek/ubuntu-rockchip
- **Android Mining Tools**: https://github.com/Oink70/Android-Mining
- **Verus Community**: https://verus.io

---

**Created by**: Bokiko  
**GitHub**: https://github.com/bokiko  
**Last Updated**: June 2025

> ⚠️ **Warning**: Cryptocurrency mining consumes significant power and generates heat. Ensure proper cooling and power supply. Mining profitability varies with network difficulty and electricity costs.
