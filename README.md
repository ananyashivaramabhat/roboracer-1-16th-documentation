# 🏎️ E116 Autonomous Race Car README

This repository documents the bring-up, testing, and early software setup of the **E116 autonomous race car** over **Week 1** and **Week 2**. The platform is built on a **Traxxas 1/16 E-Revo** chassis and powered by an **NVIDIA Jetson Orin Nano** running Ubuntu Linux.

![Lab Setup Banner](Lehigh_Lab1.jpg)
<!-- Replace with a wide shot of the car on the bench -->

---

## Platform Overview

The E116 platform combines embedded compute, sensing, and actuation hardware for robotics and autonomous vehicle experimentation.

Main components:

- **Traxxas 1/16 E-Revo VXL chassis**
- **NVIDIA Jetson Orin Nano (8 GB)**
- **Intel RealSense D435 stereo camera**
- **Velineon 380 brushless motor**
- **TQi 2.4 GHz RC handheld**
- **LU v2.1 custom carrier board**
- **LiPo battery**
- **NiMH battery**
- **Ovonic LiPo charger**
- **Traxxas EZ-Peak charger**

![Car Top View](assets/images/car_top_view.jpg)
<!-- Replace with top view of the car -->

![Car Side View](assets/images/car_side_view.jpg)
<!-- Replace with side view of the car -->

---

## Bill of Materials

| Component | Model | Est. Cost | Role |
|-----------|-------|-----------|------|
| Chassis | Traxxas 1/16 E-Revo VXL TSM | $___ | AWD race car platform |
| Motor | Velineon® 380 | $___ | Brushless propulsion motor |
| RC Handheld | TQi 2.4 GHz TSM | $___ | Manual control and safety override |
| LiPo Battery | OVONIC | $___ | Main power source |
| NiMH Battery | Traxxas | $___ | Drive-train / auxiliary power |
| LiPo Charger | OVONIC X1, 200 W | $___ | LiPo charging |
| Dual Charger | Traxxas EZ-Peak 8A 3S | $___ | NiMH charging |
| Stereo Camera | Intel RealSense D435 | $___ | RGB and depth sensing |
| Compute Module | Jetson Orin Nano 8 GB | $___ | Embedded compute |
| Carrier Board | LU v2.1 | ~$105 | Power, control, and interfaces |

---

# Week 1 — Hardware Bring-Up and Ubuntu Setup

## 1. Lab Familiarization and Power Verification

The first week focused on becoming familiar with the lab environment, major hardware components, the Digital Multimeter, and the power delivery path used by the race car.

### AC Power Check

The power cord was connected to the wall outlet and measured using the DMM in **AC voltage mode**.

![AC Measurement](assets/images/ac_measurement.jpg)
<!-- Replace with photo of AC measurement -->

Expected observation:

- approximately **120 V AC**

### DC Power Check

The AC-DC converter was connected and the barrel output was measured using the DMM in **DC voltage mode**.

![DC Measurement](assets/images/dc_measurement.jpg)
<!-- Replace with photo of DC measurement -->

Expected observation:

- approximately **19 V DC**

![DMM Closeup](assets/images/dmm_closeup.jpg)
<!-- Replace with DMM close-up -->

---

## 2. Hardware Component Identification

The major hardware components of the E116 platform were identified and studied:

- Traxxas chassis
- brushless motor
- steering servo
- RC handheld
- RealSense stereo camera
- LiPo battery
- NiMH battery
- battery chargers
- Jetson Orin Nano
- LU v2.1 carrier board

This stage also involved understanding each component’s role in the vehicle system.

---

## 3. Car Inspection Before Power-On

Before booting the system, the assembled car was checked for:

- loose screws
- loose wires
- poor connector seating
- incorrect polarity
- poor cable routing
- mechanical assembly issues

![Inspection — Wiring](assets/images/inspection_wiring.jpg)
<!-- Replace with wiring inspection image -->

![Inspection — Mounting](assets/images/inspection_mounting.jpg)
<!-- Replace with Jetson/carrier board mounting image -->

Any loose components were tightened before proceeding.

---

## 4. First Power-On and RC Link Test

A monitor, keyboard, and mouse were connected to the Jetson. The AC-DC adapter was then connected to the **Main Barrel** input on the carrier board.

### Power-On Sequence

The expected sequence was:

1. **Main LED** turns on
2. **Jetson Power** button is pressed
3. **Drive-train Power Switch** is turned on
4. **Status LEDs 1–3** illuminate
5. Jetson green LED turns on
6. Jetson fan starts spinning

![Power On — LEDs](assets/images/power_on_leds.jpg)
<!-- Replace with carrier board LED photo -->

### RC Link Check

The RC handheld was powered on near the car.

Expected result:

- RC receiver LED turns **green**
- **Status LED 4** turns on

![RC Link](assets/images/rc_link.jpg)
<!-- Replace with RC link photo -->

### Ubuntu Boot

After boot, the display showed:

- NVIDIA splash screen
- Ubuntu login window

![NVIDIA Boot Screen](assets/images/nvidia_boot_screen.jpg)
<!-- Replace with boot screen image -->

---

## 5. Ubuntu Login and Wi-Fi Setup

The Jetson was logged into using the assigned team credentials and connected to lab Wi-Fi.

| Network | Password |
|---------|----------|
| `PinkPig` | `GetLost2022` |
| `ECE_Lab` | `ECElab332` |

After login, **Software & Updates** was opened and automatic updates were disabled by setting:

- **Automatically check for updates → Never**

![Ubuntu Desktop](assets/images/ubuntu_desktop.jpg)
<!-- Replace with Ubuntu desktop screenshot -->

---

## 6. Linux Terminal Practice

A terminal was opened and added to the favorites bar. Basic Linux commands for navigation, file management, and permissions were practiced.

### Commands Used

```bash
mkdir ~/Documents/week1
cd ~/Documents/week1

cp ~/Downloads/game.py .

ls -l game.py

chmod a+x game.py

python3 game.py
