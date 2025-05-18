# 📊 Raspberry Pi Kibana Dashboard Kiosk (Firefox-Based)

Turn your Raspberry Pi into a full-screen kiosk that auto-launches a saved Kibana dashboard using Firefox in kiosk mode. This setup assumes you already have a Kibana instance deployed (e.g., via T-Pot on Linode) and are pointing to that remote dashboard.

---

## 🛠️ Requirements

- Raspberry Pi 4 (or equivalent)
- Highly recommend to use a Raspberry Pi 5 to fix the lag, the Raspberry Pi 4 is the absolute minimum
- Raspberry Pi OS (Desktop)
- A monitor connected to the Pi
- An existing Kibana instance with Basic Auth enabled (e.g., via T-Pot)
- Firefox installed (default on RPi Desktop)

---

## 🧱 Setup Instructions

### 1. **Boot into GUI automatically**
Ensure the Pi is set to boot into desktop:

```bash
sudo raspi-config
```
Navigate to:
```
  → System Options
    → Boot / Auto Login
      → Desktop Autologin
```

---

### 2. **Create a Firefox profile and save credentials**

```bash
firefox -P
```

1. Create a new profile named `kiosk`
2. Launch it
3. Navigate to your Kibana instance (e.g., `https://kibana.com:64297`)
4. Login manually
5. When prompted to save credentials, accept
6. Exit Firefox cleanly

---

### 3. **Get the profile path**

```bash
cat ~/.mozilla/firefox/profiles.ini
```

Look for the `[Profile]` block matching `Name=kiosk` and note the `Path=` value (e.g., `d37yp5qh.kiosk`).

---

### 4. **Create the startup script**

```bash
nano /home/elliot/firefox-kiosk.sh
```

Paste:

```bash
#!/bin/bash
firefox --kiosk --profile /home/elliot/.mozilla/firefox/d99pyp5qh.kiosk https://kibana.com:64297
```

Make it executable:
```bash
chmod +x /home/elliot/firefox-kiosk.sh
```

---

### 5. **Autostart the script on boot**

Edit the LXDE autostart config:
```bash
nano /home/elliot/.config/lxsession/LXDE-pi/autostart
```

Append:
```bash
@xset s off
@xset -dpms
@xset s noblank
@/home/elliot/firefox-kiosk.sh
```

> ⚠️ Avoid using `sudo` in autostart, or Firefox will fail to connect to the X session.

---

### ✅ Result
After rebooting, the Pi will:

- Boot directly into the desktop
- Wait ~5 seconds
- Launch Firefox in fullscreen kiosk mode
- Use your saved profile with credentials
- Load Kibana dashboard directly

If Firefox still shows the login prompt but with credentials pre-filled, consider using a Firefox extension like [AutoLogin](https://addons.mozilla.org/en-US/firefox/addon/autologin/) to automatically submit the form.

---

## 📓 Notes
- `EGLContext` errors can safely be ignored as long as Firefox loads.
- This setup uses `Wayland=0` and software rendering to avoid crashes.
- All credentials are saved in Firefox’s encrypted profile.

---

## 🔐 Optional: Harden Access
- Configure Kibana reverse proxy with basic auth passthrough
- Restrict Pi's outbound access to Kibana only
- Lock UI access with Openbox restrictions

---

## 🧠 Credit
This kiosk mode deployment was built to support real-time threat intel visualizations from a T-Pot honeypot running in the cloud.
