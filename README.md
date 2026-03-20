# FixWifi
A shell-based automation workaround for "Sticky 2.4GHz" issues on legacy macOS hardware in unified SSID (Band Steering) environments.
1. The Problem: "The macOS Roaming Gap" In modern mesh networks utilizing Band Steering (a single SSID for both 2.4GHz and 5GHz), macOS roaming logic often prioritizes signal stability (RSSI) over raw throughput (MCS Index).Key Challenges: Hardware Limitations: Older MacBook Pro models (pre-Wi-Fi 6E) lack 6GHz capabilities and often struggle to "handshake" with the 5GHz band if a 2.4GHz signal is significantly stronger.Driver Restrictions: Unlike Windows-based Intel adapters, which allow for a "5GHz Preferred" flag in device properties, macOS offers no native GUI toggle to prioritize bands.The Roaming Trigger: macOS typically begins background scanning for a new "candidate" AP at -70 dBm. In home offices with wall attenuation, the 2.4GHz band often stays above this threshold, causing the Mac to "stick" to a slow connection even when a 400+ Mbps 5GHz signal is available. 2. The Solution: Physical & Logical Optimization. This project uses a two-pronged approach to maintain high-performance connectivity without the complexity of splitting SSIDs (which can break IoT device ecosystems).Phase 1: Physical Layer (Layer 1)Attenuation Audit: Identified that external monitors and peripheral placement acted as RF shields.Antenna Orientation: MacBook Pro antennas are located in the "clutch" (hinge) area. Rotating the device toward the signal source improved RSSI from -71 dBm (Roaming Trigger Zone) to -65 dBm (Stable 5GHz Zone).Phase 2: The Automation Script (Layer 2)The fixwifi.sh script performs a "hard-kick" to the wireless interface, bypasses the standard roaming logic, and forces an immediate re-association with a high-bandwidth 5GHz channel. 3. Implementationfixwifi.shBash#!/bin/bash
# Force macOS to drop 2.4GHz and rejoin on 5GHz Channel 36

echo "Kicking WiFi..."
# Disassociate from current network
sudo /System/Library/PrivateFrameworks/Apple80211.framework/Resources/airport -z

sleep 3

echo "Forcing Channel 36 (5GHz)..."
# Explicitly tune radio to 5GHz band to bypass 2.4GHz auto-join
sudo /System/Library/PrivateFrameworks/Apple80211.framework/Resources/airport --channel=36

sleep 2

echo "Rejoining SSID..."
# Force handshake with saved credentials
networksetup -setairportnetwork en0 YOUR_SSID_NAME
Installation: Move the script to your home directory: mv fixwifi.sh ~/fixwifi.shMake it executable: chmod +x ~/fixwifi.shAdd an alias to your .bash_profile or .zshrc:alias fixwifi='~/fixwifi.sh'4. Performance BenchmarksMetricBefore (Sticky 2.4GHz)After (Forced 5GHz)RSSI-71 dBm-65 dBmDownload80 Mbps428 MbpsUpload1.37 Mbps164 MbpsLatency99 ms7 ms5. Tools Used: wdutil: Used for real-time monitoring of Wi-Fi diagnostics and RSSI.airport: A private framework utility for low-level wireless management. networksetup: macOS configuration tool for network service settings. 6. Usage Note: This tool is specifically designed for mobile users who move between rooms. While macOS logic is "sticky," running fixwifi ensures the hardware utilizes maximum available bandwidth upon returning to a primary workstation or "Power User" environment.
