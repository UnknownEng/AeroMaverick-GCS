# AeroMaverick GCS – User Guide

© 2026 Maverick Technologies. All rights reserved.

## 1. Install

**Windows 10/11 (64-bit)**
1. Download `AeroMaverick-GCS-Windows.zip` from the latest release.
2. Unzip it anywhere, for example `C:\AeroMaverick GCS`.
3. Run `AeroMaverick GCS.exe`. No installation or admin rights are needed.

**Android 8.0+ (64-bit)**
1. Download `AeroMaverick-GCS-1.0.0.apk` on the phone, or copy it over.
2. Tap it and allow *Install unknown apps* when asked.
3. Open **AeroMaverick GCS**. The first start can take up to 30 seconds.

## 2. Activate your licence

1. On first start the licence screen shows your **Machine ID**, for example `AMG-XXXXX-XXXXX-XXXXX-XXXXX`.
2. Tap **Copy** and send the ID to Maverick Technologies.
3. Paste the licence key you receive (`AMGCS1.…`), or use **Load .lic file…**, then press **Activate**.

Each computer and each phone needs its own licence. A phone keeps its Machine ID across app updates and reinstalls; a
factory reset changes it. The 🔑 button in the top bar shows your licence details (edition, expiry).

## 3. Connect to a vehicle

Press **Connect**:

| Setup | Choose |
|---|---|
| PX4 SITL / a companion computer sending MAVLink to the GCS | MAVLink → **UDP – listen**, port 14550 |
| ArduPilot SITL | MAVLink → **TCP client**, 127.0.0.1 : 5760 |
| Pixhawk / Cube on USB (Windows) | MAVLink → **Serial**, COMx, 115200 |
| SiK / RFD telemetry radio (Windows) | MAVLink → **Serial**, COMx, 57600 |
| Telemetry radio or autopilot on a USB-OTG cable (Android) | MAVLink → **USB OTG**. Tap *Allow*, then Connect again |
| Wi-Fi telemetry (ESP8266, Herelink, companion) | MAVLink → **UDP – listen**, 14550 |
| Network serial bridge (ser2net …) | **Serial**, port `socket://host:port` or `rfc2217://host:port` |
| Robot running **MAVROS** | **MAVROS** tab → robot IP, port 9090, namespace `mavros` |

**MAVROS setup on the robot.** Run MAVROS, and next to it run rosbridge:

```bash
roslaunch rosbridge_server rosbridge_websocket.launch                 # ROS 1
ros2 launch rosbridge_server rosbridge_websocket_launch.xml           # ROS 2
```

The GCS detects ROS 1 or ROS 2 automatically. It uses only MAVROS topics and services; it never opens a MAVLink socket.

## 4. Fly

* The top bar shows link, autopilot and firmware, flight mode (selectable), armed state, GPS, battery and message rate.
* **Arm / Disarm**, **Takeoff** to the chosen altitude, **Start Mission**, **Pause / Hold**, **RTL**, **Land**.
* **Set speed** and **Change alt** while flying.
* **Right-click** (desktop) or **long-press** (phone) the map: *Go to here*, *Point camera (ROI) here*, *Set home here*.
* **Joystick:** connect a gamepad, then press *Joystick: off* to turn it on. It sends MANUAL_CONTROL (mode 2 layout).
* **KILL** stops the motors immediately, even in the air. Use it only in an emergency.
* On phones, ☰ hides the controls for a full-screen map.

## 5. Plan

* **Waypoint** tool: click the map to add a waypoint. Drag markers to move them. Expand an item to edit its command and parameters.
* **+ Takeoff / + RTL / + Land** insert those commands.
* **Survey**: click the corners of an area, set spacing, angle and altitude (and optionally a camera trigger distance), then press **Generate**.
* **Fence**: click to draw an inclusion polygon. **Rally**: click to add safe return points.
* **Upload** sends the mission, fence and rally points. **Download** reads them back. **Clear on vehicle** deletes them.
* **Open file… / Save .plan / Save .waypoints** work with QGroundControl and Mission Planner files.

## 6. Parameters

* Search by name or description, filter by group, or show *Modified only*.
* Click a parameter to see its full documentation, range, units, default and bitmask checkboxes.
* Edited values are highlighted. Press **Write** to send them. Whole-number and out-of-range values are refused before
  anything is sent, and parameters that need a reboot are flagged.
* **Load file…** compares a `.params` / `.param` file with the vehicle and lets you choose what to write.
* **Save .params** (QGroundControl) and **Save .param** (Mission Planner) export everything.
* **Update docs** downloads the newest PX4 / ArduPilot documentation. ArduPilot 4.5 / 4.6 / 4.7 names are all covered offline.

## 7. Setup

* **Sensor calibration**: Gyroscope, Accelerometer, Level horizon, Compass, Barometer (ArduPilot only; PX4 calibrates
  it automatically from GPS), Airspeed, Radio trim, ESC. Follow the messages in the log under the buttons.
  * ArduPilot accelerometer: place the vehicle as asked and press **Done – next position** six times. ArduPilot cannot
    stop an accelerometer calibration halfway, so **Cancel** reboots the autopilot.
  * ArduPilot compass: progress bars appear. Rotate the vehicle, then **Accept**. After any compass calibration
    ArduPilot will not arm until it is rebooted: press **Reboot autopilot**.
  * PX4 accelerometer / compass: hold the vehicle still in each position PX4 asks for. PX4 only notices a cancel
    between positions, so it can take a few seconds.
* **Radio inputs** and **Servo / motor outputs** show live bars.
* **Motor test**: remove the propellers, tick the confirmation box, then spin one motor or all of them in sequence.
* **Safety & quick settings** covers return altitude, speeds, battery and link-loss actions, and the geofence. Press **Write settings**.
* **Reboot autopilot** and **Set home = current**.

## 8. Analyze

* **Messages**: every status text from the vehicle, colour-coded by severity.
* **MAVLink inspector**: every message type with its live rate. Click one to see its fields.
* Telemetry logs (`.tlog`) are recorded automatically:
  * Windows: `Documents\AeroMaverick\logs`, or `%APPDATA%\AeroMaverick\files\logs` if Windows blocks Documents
  * Android: `Android/data/com.aeromaverick.gcs/files/logs`

## 9. Troubleshooting

| Problem | Fix |
|---|---|
| "Waiting for vehicle…" never changes | Check the port and baud rate; another program (QGC / Mission Planner) may be using the port. |
| UDP shows nothing | Allow AeroMaverick through Windows Firewall, and check the vehicle sends to this PC's IP on port 14550. |
| USB-OTG says permission requested | Tap **Allow** in the Android prompt, then press **Connect** again. |
| MAVROS: cannot reach rosbridge | Start rosbridge on the robot and check the IP / port 9090 and the firewall. |
| Licence is locked to another computer | Each device needs its own licence; send the Machine ID shown on this device. |

---
AeroMaverick GCS · © 2026 Maverick Technologies. All rights reserved. · https://maverick-technologies.vercel.app
