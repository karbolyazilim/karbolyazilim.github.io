# Essential ADB Power Commands for Mobile Engineers

A curated cheat-sheet of battle-tested Android Debug Bridge (`adb`) commands for physical device testing, tablet layout simulation, ad debugging, network control, and screen capture.

---

## 📱 1. Tablet & Screen Simulation (Without Buying Hardware)

Change your connected phone's display resolution and density in real-time to simulate any device layout:

- **Simulate 7-inch Tablet (Portrait 16:10 / 9:16)**
  ```bash
  adb shell wm size 1200x1920 && adb shell wm density 240
  ```
  *Instantly re-renders the Android system and apps into a 7-inch tablet viewport (`width: ~600dp`).*

- **Simulate 10-inch Tablet (Landscape / Portrait)**
  ```bash
  adb shell wm size 1600x2560 && adb shell wm density 280
  ```
  *Simulates a modern large 10-inch to 11-inch tablet (iPad / Galaxy Tab S size).*

- **Reset Screen Back to Factory Defaults**
  ```bash
  adb shell wm size reset && adb shell wm density reset
  ```
  *Immediately restores your phone's native hardware resolution and pixel density.*

- **Check Current Screen Size & Density**
  ```bash
  adb shell wm size && adb shell wm density
  ```
  *Displays both the physical hardware resolution and the active override resolution.*

---

## 📸 2. Screen Capture & Video Recording

Capture flawless, lossless screenshots and screen recordings directly to your Mac:

- **Capture High-Res Screenshot (Stream Directly to Mac File)**
  ```bash
  adb exec-out screencap -p > assets/screenshots/tablet1.png
  ```
  *Takes a lossless PNG screenshot and pipes it directly to your current terminal folder without touching phone storage.*

- **Record Screen Video (Up to 3 minutes)**
  ```bash
  adb shell screenrecord /sdcard/promo_demo.mp4
  ```
  *Records the phone screen in 1080p MP4. Press `Ctrl + C` when finished.*

- **Pull Recorded Video to Mac**
  ```bash
  adb pull /sdcard/promo_demo.mp4 ./promo_demo.mp4 && adb shell rm /sdcard/promo_demo.mp4
  ```
  *Downloads the video file to your Mac and cleans up the temporary file from the phone.*

---

## 🌐 3. Private DNS & AdMob Testing

Control Android's Private DNS settings via ADB (crucial for toggling AdGuard/NextDNS when testing AdMob ads):

- **Check Active Private DNS Mode**
  ```bash
  adb shell settings get global private_dns_mode
  ```
  *Returns `off` (disabled), `opportunistic` (automatic), or `hostname` (custom provider).*

- **Disable Private DNS (Allow AdMob Ads to Load)**
  ```bash
  adb shell settings put global private_dns_mode off
  ```
  *Disables private DNS so Google AdMob servers are not blocked by ad-blocking DNS providers.*

- **Set Custom Private DNS Provider (e.g. AdGuard for Ad-Block Testing)**
  ```bash
  adb shell settings put global private_dns_mode hostname
  adb shell settings put global private_dns_specifier dns.adguard.com
  ```
  *Sets DNS to AdGuard to test how your app behaves when ad networks are unreachable.*

- **Set Cloudflare Private DNS (1.1.1.1)**
  ```bash
  adb shell settings put global private_dns_mode hostname
  adb shell settings put global private_dns_specifier 1dot1dot1dot1.cloudflare-dns.com
  ```
  *Fast, secure DNS without ad blocking.*

- **Reset Private DNS to Automatic (Opportunistic)**
  ```bash
  adb shell settings put global private_dns_mode opportunistic
  ```
  *Restores Android's default automatic DNS mode.*

---

## 🧹 4. App Lifecycle, Cache & Permission Management

Package name: `com.karbolyazilim.caropan`

- **Force Stop the App**
  ```bash
  adb shell am force-stop com.karbolyazilim.caropan
  ```
  *Instantly kills the running process.*

- **Clear All App Data & Cache (Cold Clean Slate)**
  ```bash
  adb shell pm clear com.karbolyazilim.caropan
  ```
  *Wipes AsyncStorage, cached images, and local databases as if the app was freshly installed.*

- **Grant Storage / Photo Permission via CLI**
  ```bash
  adb shell pm grant com.karbolyazilim.caropan android.permission.READ_MEDIA_IMAGES
  ```
  *Grants image access without having to click through the Android permission popup.*

- **Revoke Permission via CLI**
  ```bash
  adb shell pm revoke com.karbolyazilim.caropan android.permission.READ_MEDIA_IMAGES
  ```
  *Revokes image permission to test runtime permission rejection handling.*

- **Launch App via ADB**
  ```bash
  adb shell am start -n com.karbolyazilim.caropan/.MainActivity
  ```
  *Launches the app's main activity from the terminal.*

---

## 🔍 5. Logcat & Debugging

Filter real-time device logs without getting overwhelmed by system noise:

- **Filter React Native & JavaScript Console Logs**
  ```bash
  adb logcat "*:S" ReactNative:V ReactNativeJS:V
  # Or alternatively via -s flag:
  adb logcat -s ReactNative ReactNativeJS
  ```
  *Filters logs exclusively to `console.log`, `console.warn`, and React Native runtime messages. Always quote `*:S` in zsh (macOS) to prevent shell glob expansion.*

- **Filter Google AdMob Logs**
  ```bash
  adb logcat -s Ads:V
  ```
  *Tracks AdMob SDK ad requests, fill rates, click events, and errors.*

- **Clear Logcat Buffer**
  ```bash
  adb logcat -c
  ```
  *Flushes old log history so you only see new logs starting from this instant.*

---

## 🔌 6. Port Forwarding & Network Utilities

- **Reverse Port Forward Metro Bundler (Port 8081)**
  ```bash
  adb reverse tcp:8081 tcp:8081
  ```
  *Enables the physical device to communicate with the Mac's Metro dev server over USB.*

- **Verify Connected Devices**
  ```bash
  adb devices -l
  ```
  *Lists all connected USB/wireless devices along with their model name (e.g. `SM_S731B`).*

- **Toggle Wi-Fi On / Off**
  ```bash
  adb shell svc wifi disable
  adb shell svc wifi enable
  ```
  *Quickly test offline mode handling and retry mechanisms.*

- **Toggle Mobile Data On / Off**
  ```bash
  adb shell svc data disable
  adb shell svc data enable
  ```
  *Test behavior when cellular data is unavailable.*

---

## ⌨️ 7. Keyboard & Physical Key Event Simulation

- **Send Text Input Directly to Focused Field**
  ```bash
  adb shell input text "test@example.com"
  ```
  *Types text into the currently focused input without using the on-screen keyboard.*

- **Simulate Hardware Back Button**
  ```bash
  adb shell input keyevent 4
  ```
  *Triggers the Android physical Back button (useful for testing modals and navigation pop).*

- **Simulate Home Button**
  ```bash
  adb shell input keyevent 3
  ```
  *Minimizes the app to test backgrounding (`AppState: background`).*

- **Wake Up & Unlock Screen**
  ```bash
  adb shell input keyevent 26 && adb shell input keyevent 82
  ```
  *Presses the Power button and unlocks the lock screen.*
