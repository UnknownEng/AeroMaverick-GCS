# AeroMaverick GCS – User Guide

© 2026 Maverick Technologies. All rights reserved.

## 1. Install

**Windows 10/11 (64-bit)**
1. Download `AeroMaverick-GCS-Windows.zip` from the latest release.
2. Unzip it anywhere, for example `C:\AeroMaverick GCS`.
3. Run `AeroMaverick GCS.exe`. No installation or admin rights are needed.

**Android 8.0+ (64-bit)**
1. Download `AeroMaverick-GCS-1.0.1.apk` on the phone, or copy it over.
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
* **Joystick:** connect a gamepad, then press *Joystick: off* to turn it on. It sends MANUAL_CONTROL (mode 2 layout:
  left stick throttle / yaw, right stick pitch / roll), over MAVLink or MAVROS. Use a stick mode such as *Loiter* /
  *PosHold* (ArduPilot) or *Position* (PX4). ArduPilot shows the joystick as RC overrides on the radio bars. If the
  joystick stops while PX4 is in Position mode, PX4 starts its manual-control-loss failsafe (return or land).
* **KILL** stops the motors immediately, even in the air. Use it only in an emergency.
* On phones, ☰ hides the controls for a full-screen map.
* **Video** shows the camera picture in a corner of the map. ⚙ chooses the source:
  * the stream the vehicle announces (MAVLink camera protocol);
  * UDP H.264 / H.265 RTP on port 5600 (the QGroundControl default);
  * UDP MPEG-TS;
  * an RTSP camera address (Siyi, Herelink, IP cameras);
  * an HTTP / MJPEG / HLS address.

  ⇄ swaps video and map, 📷 saves a photo, ● records an MP4 (the original video, no quality loss) into
  `Documents\AeroMaverick\videos`. On Android the phone plays RTSP and HLS itself. UDP streams need the desktop app.
* **VTOL** aircraft get a **→ Fixed-wing / → Multicopter** transition button.
* **Several vehicles:** a bar at the top of the map lists every connected vehicle. Click one to command it; the others
  stay on the map. **+ Vehicle** connects another one (another radio, UDP port or MAVROS namespace). **All vehicles…**
  sends Hold, RTL, Land or Disarm to all of them. Vehicles that share one radio or UDP port (different system IDs) are
  found automatically.

## 5. Plan

* **Waypoint** tool: click the map to add a waypoint. Drag markers to move them. Expand an item to edit its command and parameters.
* **+ Takeoff / + RTL / + Land** insert those commands.
* **Survey**: click the corners of an area, set spacing, angle and altitude (and optionally a camera trigger distance), then press **Generate**.
* **Fence**: click to draw an inclusion polygon. **Rally**: click to add safe return points.
* **Upload** sends the mission, fence and rally points. **Download** reads them back. **Clear on vehicle** deletes them.
* **Open file… / Save .plan / Save .waypoints** work with QGroundControl and Mission Planner files.
* **VTOL** missions: **+ VTOL takeoff**, **+ Transition** and **+ VTOL land**.
* **Terrain**: choose the *Altitude reference*:
  * *Above home*: normal.
  * *Above terrain, the autopilot follows it* (ArduPilot): waypoints are sent as heights above the ground, and the app
    sends ArduPilot the terrain data it asks for (`TERRAIN_ENABLE = 1`).
  * *Above terrain, fixed heights computed here* (any autopilot, including PX4): the app adds waypoints every
    *Point spacing* metres and sets each one's height above sea level, so the aircraft stays the chosen height above
    the highest ground of every leg.

  **Show terrain profile** draws the ground and your planned altitude along the route. Red means closer than 10 m (or
  into the ground). Terrain data (SRTM, about 30 m) is downloaded once per area and then works offline.

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
* **Radio inputs** and **Servo / motor outputs** show live bars (also through MAVROS `rc/in` and `rc/out`).
* **Calibrate radio** (under Radio inputs; remove the propellers first): move every stick and switch to both ends until
  each channel shows a ✓, then centre the sticks, put the throttle low and press **Save**. This stores `RCx_MIN`,
  `RCx_MAX` and `RCx_TRIM` on ArduPilot and PX4. Do it once for every new transmitter or receiver.
* **Radio trim**: centre the sticks, then press it. ArduPilot stores the stick centres as `RCx_TRIM`; PX4 runs its
  own RC trim calibration.
* **Motor test**: remove the propellers, tick the confirmation box, then spin one motor or all of them in sequence.
* **Safety & quick settings** covers return altitude, speeds, battery and link-loss actions, and the geofence. Press **Write settings**.
* **Reboot autopilot** and **Set home = current**.

The Setup screen has more tabs:

* **Airframe**: the most important settings for *your* vehicle type, with descriptions and dropdowns:
  * **Multicopter**: frame, limits, return, failsafes.
  * **Plane**: airspeed, throttle, attitude limits, TECS climb/sink, take-off, landing, navigation, failsafes.
  * **VTOL**: QuadPlane / tiltrotor / tailsitter settings, transition speeds and times, plus the plane settings.
  * **Rover and boat**: frame, cruise speed and throttle, steering, navigation, failsafes.
  * **Sub**: frame, pilot gains, leak / pressure / temperature failsafes.

  ArduPilot and PX4 each get their own list. An ArduPlane that is not a VTOL yet can be turned into a QuadPlane with
  one button.
* **Outputs**: what every output pin drives (motor, aileron, elevator, rudder, steering, throttle, gear …), its
  minimum / trim / maximum, reversing, and a live bar. Move the sticks (props off) to check that every control surface
  moves the right way.
* **Frame geometry** (custom multirotors): drag the motors on a top-view drawing or type their positions, pick each
  motor's spin direction, and check the balance. Start from Quad X / + / Hexa X / Octa X or build any layout.
  * PX4: the layout is written to the control-allocation parameters (`CA_ROTOR…`).
  * ArduPilot: the app generates a *scripted frame* (a Lua motor matrix), uploads it to the SD card and sets
    `FRAME_CLASS = 15`.

  Reboot, then confirm every motor with **Motor test** before flying.
* **CAN / DroneCAN**: CAN port and protocol settings (ArduPilot `CAN_P*` / `CAN_D*`, PX4 `UAVCAN_*`). On ArduPilot
  **Start** joins the CAN bus through the autopilot and lists every DroneCAN device: GPS, compass, airspeed, power
  module, ESCs, with health, uptime and versions. Click a device to read, change and **Save** its own parameters, or
  restart it.
* **Firmware** (desktop): install or update ArduPilot or PX4.
  1. **Detect board**: plug the flight controller in by USB; a board that is already connected is rebooted into its
     bootloader for you.
  2. Pick the vehicle and board variant (latest official ArduPilot), a PX4 release file, or your own `.apj` / `.px4`.
  3. **Flash firmware**.

  The board ID is checked before anything is erased, and the result is verified.

## 8. Analyze

* **Live messages & inspector**: every status text from the vehicle, colour-coded by severity, and every MAVLink
  message type with its live rate (click one to see its fields).
* Telemetry logs (`.tlog`) are recorded automatically:
  * Windows: `Documents\AeroMaverick\logs`, or `%APPDATA%\AeroMaverick\files\logs` if Windows blocks Documents.
  * Android: `Android/data/com.aeromaverick.gcs/files/logs`.
* **Flight logs & graphs** opens ArduPilot `.bin`, PX4 `.ulg` and telemetry `.tlog` files:
  * **From vehicle…** lists the logs on the flight controller and downloads them (a USB cable is much faster than a
    radio). **Open log file…** opens any log on this computer.
  * The **summary** shows flight time, maximum altitude, distance, speed, battery and vibration.
  * The **automatic check** rates battery, vibration (and clipping), GPS, failsafes, errors, compass interference,
    board power, motor balance and saturation, attitude tracking (tuning) and the navigation filter.
  * The **map** shows the flight path with the flight-mode changes.
  * **Graphs**: pick a ready-made graph (attitude, altitude, battery, vibration, GPS, motors, RC …) or type any field
    (e.g. `ATT.Roll`). Drag across the graph to zoom in, double-click to zoom out, hover for values. Blue lines mark
    mode changes, red lines errors. **CSV** exports the plotted data. **Parameters** saves the parameters stored in
    the log.

## 9. Scripts

**GCS Python** scripts run in the app and control the connected vehicle. Write one in the editor (or **Open file…**,
or start from an example) and press **▶ Run**. Output appears below. **■ Stop** interrupts a script at any time,
even inside a loop. If a script fails, the error and its **line number** are shown and the line is highlighted.
Ctrl+Enter runs, Ctrl+S saves.

```python
vehicle.takeoff(10)
vehicle.wait_altitude(10)
vehicle.goto(vehicle.lat + 0.0003, vehicle.lon, 10)
vehicle.wait_arrival(vehicle.lat + 0.0003, vehicle.lon)
vehicle.land()
```

The **Script API** list (left) shows everything `vehicle` offers:
* telemetry (`lat`, `alt`, `battery`, `mode`, `state` …) and the last MAVLink messages;
* arm, take-off, go to, mode, RTL / land / hold, speed and altitude;
* parameters, missions and any `MAV_CMD`;
* waits: `wait_altitude`, `wait_arrival`, `wait_until`;
* `vehicles()` for every connected vehicle;
* `wait()`, `print()` and `log()`.

Scripts are saved in `Documents\AeroMaverick\scripts`.

**Autopilot Lua** (ArduPilot) scripts run on the flight controller itself. Write or open a `.lua` file and press
**⇧ Upload to autopilot & run**. The app copies it to the SD card (MAVFTP), checks it, restarts Lua scripting and
shows the autopilot's messages. Lua errors are shown with their line. The list shows the scripts on the SD card
(view, delete) and the examples. Lua needs `SCR_ENABLE = 1` and a reboot; the app offers to set it.

## 10. Swarm (Enterprise)

The **Swarm** screen shows every connected vehicle on one map. Tick the vehicles to use.

* **Group commands**: **Take off (staggered)** arms and launches them all, each a few metres higher than the last.
  **Hold / RTL / Land** for the whole group.
* **Formation, centralised**: the app is the brain. Choose the leader, a shape (V, line, column, grid, circle),
  spacing and height step, and press **Start formation**. Fly the leader (Fly screen, a mission, or right-click the
  Swarm map); twice a second the app sends every follower to its slot. It needs a working link to every vehicle.
  If the leader is lost, the followers hold.
* **Formation, decentralised**: the vehicles do it themselves. **Set up onboard follow** sets ArduPilot followers to
  `FOLLOW` mode (`FOLL_*` parameters: leader system ID, offsets), or PX4 followers to Follow-Me.
  * On a mesh radio they hear the leader directly and keep the formation even if the GCS link drops.
  * On separate links the app relays the leader's position to them.
* **Shared survey**: draw an area; each vehicle gets its own strip at its own height. **Upload & start all**.
* **Safety**: a separation monitor warns (or makes both vehicles hold) when two vehicles come closer than the limit.
* Every vehicle needs its own MAVLink **system ID**; the app warns about duplicates and can change one
  (`SYSID_THISMAV` / `MAV_SYS_ID`, then reboot).
* ROS swarms: connect each vehicle's MAVROS namespace (`/uav1/mavros`, `/uav2/mavros` …) through rosbridge as an extra
  vehicle.

## 11. Updates

From version 1.0.1 AeroMaverick updates itself. It checks for a newer release a few seconds after it starts and every
6 hours while it runs (when the device is online). If there is one, an **Update to vX.Y.Z** button appears in the top
bar. You can also press **Check for updates** in the 🔑 licence dialog, or untick **Check for updates automatically**
there.

Press the button and confirm. The app downloads the update with a progress bar and checks it, then:

* **Windows:** closes, installs the new version in the same folder (the old one is kept as a backup), and starts
  again by itself.
* **Android:** opens the Android installer. Tap **Update**. The first time, Android may ask you to allow AeroMaverick to
  install apps.

**Safety.** An update is installed only if it is signed by Maverick Technologies and its checksum matches, so a
damaged or fake download is refused. Updating is refused while the vehicle is armed: land and disarm first. Your
licence, settings, missions and logs are kept.

The check reads only the public release page; nothing about you, your licence or your device is sent. If the app
folder is not writable (for example under `Program Files`), the button opens the download page instead. Version 1.0.0
has no update check, so 1.0.0 users install 1.0.1 by hand once.

## 12. Troubleshooting

| Problem | Fix |
|---|---|
| "Waiting for vehicle…" never changes | Check the port and baud rate; another program (QGC / Mission Planner) may be using the port. |
| UDP shows nothing | Allow AeroMaverick through Windows Firewall, and check the vehicle sends to this PC's IP on port 14550. |
| USB-OTG says permission requested | Tap **Allow** in the Android prompt, then press **Connect** again. |
| MAVROS: cannot reach rosbridge | Start rosbridge on the robot and check the IP / port 9090 and the firewall. |
| Joystick does nothing through MAVROS (ArduPilot) | ArduPilot only takes stick input from its GCS system ID (255). MAVROS sends as system 1. On ArduPilot 4.7+ set `MAV_GCS_SYSID` = 1 and `MAV_GCS_SYSID_HI` = 255 so both count as GCS (older firmware: `SYSID_MYGCS` = 1). The app shows this hint the first time you use the joystick. |
| Arming refused with "RC calibrating" (ArduPilot) | Caused by Radio trim in AeroMaverick 1.0.0. Press **Cancel cal** in Setup or reboot the autopilot, then update to 1.0.1. |
| Script: "is not connected" / command refused | The script line that failed is highlighted; the message says what the vehicle refused (e.g. not armable in this mode). |
| Lua script does not start (ArduPilot) | Set `SCR_ENABLE` = 1 (Scripts → Autopilot Lua offers it) and reboot. An SD card must be fitted. |
| Video: "No video" | Check the address / port, that the camera streams to this computer, and the Windows Firewall (UDP 5600). On the phone use the RTSP address. |
| Terrain profile empty | Terrain tiles are downloaded the first time an area is used – go online once. |
| DroneCAN inspector finds nothing | Enable the CAN port (`CAN_P1_DRIVER` = 1), reboot, and check the bus wiring / termination. |
| Firmware: "No bootloader found" | Unplug the board, press **Detect**, plug it in within 20 s. Close other programs using the port. |
| Swarm: vehicles have the same system ID | Swarm → Vehicles → **Give V2 a new ID**, then reboot that vehicle. |
| Licence is locked to another computer | Each device needs its own licence; send the Machine ID shown on this device. |

---
AeroMaverick GCS · © 2026 Maverick Technologies. All rights reserved. · https://maverick-technologies.vercel.app
