# macOS-WiFi-Band-Force
**A shell-based automation workaround for "Sticky 2.4GHz" issues on legacy macOS hardware in unified SSID (Band Steering) environments.**

## 1. The Problem: "The macOS Roaming Gap"
In modern mesh networks utilizing **Band Steering** (a single SSID for both 2.4GHz and 5GHz), macOS roaming logic often prioritizes signal stability (RSSI) over raw throughput (MCS Index). 

### Key Challenges:
* **Hardware Limitations:** Older MacBook Pro models (pre-Wi-Fi 6E) lack 6GHz capabilities and often struggle to "handshake" with the 5GHz band if a 2.4GHz signal is significantly stronger.
* **Driver Restrictions:** Unlike Windows-based Intel adapters, which allow for a "5GHz Preferred" flag in device properties, macOS offers no native GUI toggle to prioritize bands.
* **The Roaming Trigger:** macOS typically begins background scanning for a new candidate AP at **-70 dBm**. In home offices with wall attenuation, the 2.4GHz band often stays above this threshold, causing the Mac to "stick" to a slow connection even when a 400+ Mbps 5GHz signal is available.

---

## 2. The Solution: Physical & Logical Optimization

### Phase 1: Physical Layer (Layer 1)
* **Attenuation Audit:** Identified that external monitors and peripheral placement acted as RF shields.
* **Antenna Orientation:** MacBook Pro antennas are located in the "clutch" (hinge) area. Rotating the device toward the signal source improved RSSI from **-71 dBm** to **-65 dBm**.

### Phase 2: The Automation Script (Layer 2)
The `fixwifi.sh` script performs a "hard-kick" to the wireless interface, bypasses the standard roaming logic, and forces an immediate re-association with a high-bandwidth 5GHz channel.

---

## 3. Implementation

### `fixwifi.sh`

#!/bin/bash
# Force macOS to drop 2.4GHz and rejoin on 5GHz Channel 36

echo "Kicking WiFi..."
sudo /System/Library/PrivateFrameworks/Apple80211.framework/Resources/airport -z

sleep 3

echo "Forcing Channel 36 (5GHz)..."
sudo /System/Library/PrivateFrameworks/Apple80211.framework/Resources/airport --channel=36

sleep 2

echo "Rejoining SSID..."
# Replace 'YOUR_SSID_NAME' with your actual network name
networksetup -setairportnetwork en0 YOUR_SSID_NAME

4. Installation
Create the script: Run nano ~/fixwifi.sh and paste the code above.

Permissions: Make it executable with chmod +x ~/fixwifi.sh.

Alias: Add alias fixwifi='~/fixwifi.sh' to your ~/.bash_profile.

Initialize: Run source ~/.bash_profile to enable the command.

5. Performance Benchmarks
6. Tools Used
wdutil: Real-time monitoring of Wi-Fi diagnostics and RSSI.

airport: Private framework utility for low-level wireless management.

networksetup: macOS configuration tool for network service settings.

```bash
