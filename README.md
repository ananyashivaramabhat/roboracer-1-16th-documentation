# 🏎️ E116 Autonomous Race Car — Hardware Bring-Up & Software Setup

A 1/16-scale autonomous race car built on a **Traxxas E-Revo** chassis, powered by an **NVIDIA Jetson Orin Nano** running Ubuntu Linux. This repo documents the full bring-up process — from inspecting the hardware and measuring power rails to booting into Ubuntu and getting the first Python script running on the Jetson.

![Lab Setup Banner](Lehigh_Lab1.jpg)
<!-- Replace: wide shot of the car on the bench with the NVIDIA boot screen on the monitor -->

---

## What This Project Is

The E116 is a small-scale autonomous vehicle platform designed for experimenting with GPU-accelerated perception, motor control, and embedded Linux. The car uses:

- **NVIDIA Jetson Orin Nano (8 GB)** for onboard compute
- **Intel RealSense D435** stereo camera for depth perception
- **Velineon 380 brushless motor** with an ESC for propulsion
- **TQi 2.4 GHz RC handheld** for manual override
- A **custom carrier board (LU v2.1)** that handles power management, sensor interfaces, drive-train PWM, and status monitoring

This write-up covers Week 1: getting the car assembled, powered on, and the Jetson booted into a usable Linux environment.

---

## Hardware Overview

### The Platform

The base is a **Traxxas 1/16 E-Revo VXL** — a small all-wheel-drive car with Ackerman steering geometry. The stock Traxxas electronics (ESC, receiver) are retained for manual control, while the Jetson and carrier board sit on top for autonomous operation.

![Car Top View](assets/images/car_top_view.jpg)
<!-- Replace: top-down photo of the fully assembled car -->

![Car Side View](assets/images/car_side_view.jpg)
<!-- Replace: side-angle photo showing the wheel/suspension and mounted boards -->

### Bill of Materials

| Component | Model | Est. Cost | Role |
|-----------|-------|-----------|------|
| Chassis | Traxxas 1/16 E-Revo VXL TSM | $ ___ | AWD platform with Ackerman steering |
| Motor | Velineon® 380 (brushless) | $ ___ | Propulsion — no brushes, higher efficiency |
| RC Controller | TQi 2.4 GHz TSM | $ ___ | Manual override / safety kill |
| LiPo Battery | OVONIC | $ ___ | Main power, high energy density |
| NiMH Battery | Traxxas | $ ___ | Backup / RC receiver power |
| LiPo Charger | OVONIC X1, 200 W | $ ___ | Charges LiPo packs |
| Dual Charger | Traxxas EZ-Peak 8A 3S | $ ___ | Charges NiMH and LiPo |
| Stereo Camera | Intel RealSense D435 | $ ___ | Depth + RGB, stereo baseline for 3D |
| Compute Module | NVIDIA Jetson Orin Nano 8 GB | $ ___ | GPU edge compute, runs Ubuntu |
| Carrier Board | LU v2.1 | ~$105 | Power mgmt, I2C/UART, drive control, OLED |

> The full-scale E116 platform uses a **LiDAR** sensor and a **VESC** motor controller instead of the stereo camera and stock Traxxas ESC. The trade-offs are interesting — LiDAR gives precise range data but costs significantly more, and the VESC allows fine-grained current-based torque control that the stock ESC can't do.

---

## Step 1 — Power Rail Verification

Before powering anything on the car, I verified the power supply chain with a **Digital Multimeter** (Fluke 8800A / GwInstek GDM-8245).

### AC Side

Plugged the power cord into the 120 V wall outlet, left the converter disconnected. Measured across the cord terminals in **AC V** mode.

![AC Measurement](assets/images/ac_measurement.jpg)
<!-- Replace: photo of DMM probes measuring the AC power cord voltage -->

**Reading:** ~120 V RMS at 60 Hz — as expected for a US wall outlet.

### DC Side

Connected the AC-DC converter and switched the DMM to **DC V** mode. Measured the barrel connector output.

![DC Measurement](assets/images/dc_measurement.jpg)
<!-- Replace: photo of DMM probes on the barrel connector output -->

**Reading:** matched the label on the converter (typically 19 V DC for Jetson power supplies). This is the voltage that feeds the carrier board's main power input.

![DMM Closeup](assets/images/dmm_closeup.jpg)
<!-- Replace: closeup of the DMM display showing the DC voltage reading -->

---

## Step 2 — Car Assembly Inspection

Before first power-on, I went over the car checking for:

- Loose screws on the carrier board standoffs and Jetson mounting
- Secure wire connections — battery leads, motor wires, servo cable
- Proper polarity on all DC connections (red → V+, black → GND)
- Camera mounted and cable seated in the USB port
- No metal debris or loose parts that could short anything

![Inspection — Wiring](assets/images/inspection_wiring.jpg)
<!-- Replace: photo showing the wiring harness / connections on the car -->

![Inspection — Mounting](assets/images/inspection_mounting.jpg)
<!-- Replace: photo of the Jetson + carrier board mounted on the chassis -->

Everything looked solid. A couple of screws on the carrier board standoffs were slightly loose — tightened those before proceeding.

---

## Step 3 — First Power-On

### Boot Sequence

1. Connected a monitor (DisplayPort), USB keyboard, and mouse to the Orin Nano.
2. Plugged the AC-DC converter barrel into the **Main Barrel** on the carrier board.
3. **Main LED** turned on immediately — good, power is reaching the board.
4. Pressed the **Jetson Power** button and flipped the **Drive-train Power Switch**.

What I saw:
- ✅ Status LEDs 1–3 lit up
- ✅ Green LED on the Orin Nano module
- ✅ Fan spun up

![Power On — LEDs](assets/images/power_on_leds.jpg)
<!-- Replace: photo of the carrier board with status LEDs lit -->

### RC Link Test

5. Powered on the **TQi RC handheld** near the car.
6. RC receiver LED turned **green**, and **Status LED 4** came on — link established.
7. Turned off RC power immediately after confirming the link.

![RC Link](assets/images/rc_link.jpg)
<!-- Replace: photo showing the RC handheld and the green receiver LED on the car -->

### Ubuntu Boot

8. Waited ~2 minutes for the Jetson to boot.
9. Switched the Dell monitor input to **DisplayPort**.
10. NVIDIA splash screen appeared, followed by the Ubuntu login window.

![NVIDIA Boot Screen](assets/images/nvidia_boot_screen.jpg)
<!-- Replace: your Lehigh_Lab1.jpg — the car next to the monitor showing the NVIDIA splash -->

<!-- VIDEO: Full boot sequence from power button to Ubuntu login -->
https://github.com/user-attachments/assets/VIDEO_ID_HERE
<!-- Replace: upload boot sequence video and paste the GitHub video link -->

---

## Step 4 — Ubuntu Setup on the Jetson

### Login & Wi-Fi

Logged into Ubuntu with the team credentials. Connected to the lab Wi-Fi network and confirmed internet access via Firefox.

| Network | Password |
|---------|----------|
| `PinkPig` | `GetLost2022` |
| `ECE_Lab` | `ECElab332` |

### Disabling Auto-Updates

Opened **Software & Updates** → Updates tab → set "Automatically check for updates" to **Never**. On an embedded platform like this, you don't want background updates eating bandwidth or breaking packages mid-project.

![Ubuntu Desktop](assets/images/ubuntu_desktop.jpg)
<!-- Replace: screenshot of the Ubuntu desktop on the Jetson -->

---

## Step 5 — Terminal & Linux Basics

Opened a terminal and pinned it to the Favorites bar. This is where most of the real work happens on the Jetson — no need for a GUI for autonomous driving software.

### Commands I Used

```bash
# Created a working directory
mkdir ~/Documents/week1
cd ~/Documents/week1

# Copied the game script over
cp ~/Downloads/game.py .

# Checked permissions
ls -l game.py
# Output: -rw-r--r-- ... game.py

# Made it executable
chmod a+x game.py

# Ran it
python3 game.py
```

![Terminal — File Permissions](assets/images/terminal_permissions.jpg)
<!-- Replace: screenshot showing ls -l and chmod output -->

### Quick Reference

| Command | What It Does |
|---------|-------------|
| `ls -al` | List everything including hidden files, with permissions |
| `cd` / `cd ..` / `cd ~` | Navigate directories |
| `mkdir` | Create a directory |
| `cp` / `mv` / `rm -r` | Copy, move, remove |
| `grep pattern file` | Search inside a file |
| `chmod 755 file` | Set permissions (owner rwx, others rx) |
| `Tab` | Auto-complete paths and commands |
| `Ctrl+C` | Kill a running process |

---

## Step 6 — First Python Script on the Jetson

To confirm the Python environment is working, I wrote a simple script using `gedit`:

```bash
gedit compute.py
```

```python
# simple computation by python
a = 10
b = 5
c = a * b
print("c = " + str(c))
```

Saved, closed, and ran it:

```bash
python3 compute.py
# Output: c = 50
```

Small thing, but it confirms the Python 3 toolchain is intact and the Jetson's user environment is properly set up. From here, you can start installing ROS 2, OpenCV, or whatever the autonomous stack needs.

![Running compute.py](assets/images/compute_py_output.jpg)
<!-- Replace: screenshot of the terminal showing the python3 compute.py output -->

<!-- VIDEO: Writing, saving, and running compute.py -->
https://github.com/user-attachments/assets/VIDEO_ID_HERE
<!-- Replace: screen recording of the editor + terminal workflow -->

---

## Carrier Board — LU v2.1

The carrier board is the glue between the Jetson and the Traxxas chassis. It's a custom PCB that handles everything the Jetson can't do on its own.

![Carrier Board](assets/images/carrier_board.jpg)
<!-- Replace: annotated photo of the carrier board -->

| Block | What It Does |
|-------|-------------|
| **Main Power** | Barrel connector input, voltage regulation, battery level checker |
| **Jetson Signal Interface** | Routes GPIO, I2C, UART between the carrier and the Orin Nano |
| **Drive Control** | PWM signals out to the motor ESC and steering servo |
| **Sensing & Control** | Connectors for external sensors |
| **Comm Ports** | I2C and UART breakout headers for peripherals |
| **External IMU Interface** | Dedicated connector for an inertial measurement unit |
| **Status LEDs 1–4** | Power, drive-train active, Jetson alive, RC link |
| **Mode Switch** | Selects between operating modes |
| **0.92″ OLED Screen** | Displays real-time status info on the board |

---

## Safety Notes

A few things I learned to be careful about during bring-up:

- **Always put the car on a stand** when the drive-train is powered — the wheels will spin and the car will launch off the bench if it's not secured.
- **Turn off power before disconnecting anything.** Hotplugging DC connections can arc and damage connectors.
- **Match polarity every time.** Red is positive, black is ground. Reversing a LiPo connection can destroy the battery and the board.
- **Unplug batteries when done.** LiPo cells are damaged by deep discharge — don't leave them connected overnight.
- **Hold connectors, not wires** when unplugging. The cable strain relief on these small connectors is minimal.

---

## What's Next

With the hardware verified and Ubuntu running, the next steps are:

- Install **ROS 2** and set up the catkin/colcon workspace
- Calibrate the **Intel RealSense D435** and test depth streaming
- Write a basic **motor control node** using PWM through the carrier board
- Set up **SSH access** so the car can run headless without a monitor

---

## Repo Structure

```
.
├── README.md
├── assets/
│   ├── images/
│   │   ├── lab_setup_banner.jpg
│   │   ├── car_top_view.jpg
│   │   ├── car_side_view.jpg
│   │   ├── ac_measurement.jpg
│   │   ├── dc_measurement.jpg
│   │   ├── dmm_closeup.jpg
│   │   ├── inspection_wiring.jpg
│   │   ├── inspection_mounting.jpg
│   │   ├── power_on_leds.jpg
│   │   ├── rc_link.jpg
│   │   ├── nvidia_boot_screen.jpg
│   │   ├── ubuntu_desktop.jpg
│   │   ├── terminal_permissions.jpg
│   │   ├── compute_py_output.jpg
│   │   └── carrier_board.jpg
│   └── videos/
│       ├── boot_sequence.mp4
│       └── compute_py_demo.mp4
└── src/
    ├── game.py
    └── compute.py
```
