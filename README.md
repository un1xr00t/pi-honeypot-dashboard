
# Raspberry Pi Honeypot Dashboard Viewer

This project turns a Raspberry Pi 4 into a dedicated, full-screen threat intelligence dashboard for viewing live attacks from a T-Pot honeypot instance hosted on Linode.

## Initial Setup: T-Pot Honeypot (Cloud Requirement)

This project assumes you already have a working T-Pot honeypot deployment in the cloud (e.g., Linode, DigitalOcean, etc.) that exposes Kibana on a public IP and port (default: 64297).

**If you don’t have that yet, follow this guide to get your cloud instance online first:**
👉 [T-Pot Threat Intel Lab Setup](https://github.com/un1xr00t/tpot-threat-intel-lab)

---

## Overview

- Chromium auto-launches in kiosk mode
- Displays Kibana dashboard from remote T-Pot instance
- Auto-boots to GUI with no input required
- Uses HTTP basic auth embedded in URL
- Clean, reliable boot with LightDM

---

## Requirements

- Raspberry Pi 4 (4GB or 8GB recommended)
- Raspberry Pi OS (with Desktop)
- Access to a running T-Pot honeypot (Kibana endpoint)
- HDMI display, keyboard/mouse (for setup)
- Local IP or public IP of T-Pot Kibana (`http://your-ip:64297`)

---

## Setup Instructions

### 1. Flash Raspberry Pi OS with Desktop

Use Raspberry Pi Imager and select **Raspberry Pi OS (32-bit with Desktop)**. Enable SSH and Wi-Fi in the advanced settings if needed.

### 2. Boot and Update

```bash
sudo apt update && sudo apt full-upgrade -y
sudo apt install chromium-browser unclutter -y
```

### 3. Edit /etc/hosts (Optional)

To map a friendly domain to your Kibana IP:

```bash
sudo nano /etc/hosts
```

Add:

```
your-ip    kibana.com
```

Replace with your actual IP.

### 4. Configure Chromium Autostart

```bash
mkdir -p ~/.config/lxsession/LXDE-pi
nano ~/.config/lxsession/LXDE-pi/autostart
```

Paste:

```
@xset s off
@xset -dpms
@xset s noblank
@unclutter -idle 1 -root
@chromium-browser --kiosk --noerrdialogs --disable-infobars --disable-gpu --app=http://username:password@kibana.com:64297
```

Replace `username:password@kibana.com:64297` with your Kibana credentials and URL.

### 5. Fix Half-Screen or Cut-Off Display

Edit:

```bash
sudo nano /boot/config.txt
```

Ensure:

```
hdmi_force_hotplug=1
disable_overscan=1
```

Comment out or remove any `overscan_left/right/top/bottom` lines.

### 6. Enable GUI Autostart via LightDM

Enable the display manager:

```bash
sudo systemctl enable lightdm
sudo systemctl start lightdm
```

Configure auto-login:

```bash
sudo nano /etc/lightdm/lightdm.conf
```

Set:

```
[Seat:*]
autologin-user=elliot
autologin-user-timeout=0
user-session=LXDE-pi
```

Replace `elliot` with your username.

### 7. Reboot and Enjoy

```bash
sudo reboot
```

Your Pi will boot directly into the LXDE desktop, auto-launch Chromium, and display your live Kibana honeypot dashboard in full-screen kiosk mode.

---

## Credits

Built by William using Raspberry Pi OS, Chromium, and T-Pot honeypot.

---

## License

MIT License
