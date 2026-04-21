# ORKA — Omnidirektionaler Roboter mit Kameragestützter Autonomie

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo/ORKA_logo_white.png">
    <source media="(prefers-color-scheme: light)" srcset="assets/logo/ORKA_logo_black.png">
    <img src="assets/logo/ORKA_logo_black.png" width="500" alt="ORKA Logo">
  </picture>
</p>

> Autonomous mobile robot platform with computer vision, modular chassis design, and a 6-DOF robotic arm.
> Built as an engineering portfolio project. Currently in active development.

---

<details>
<summary><h3><strong><u>📸 Fotos anzeigen</u></strong></h3></summary>
<br>
<p align="center">
<img src="assets/images/2026-04-14_ORKA_assembled.JPG" width="600" alt="ORKA-Base">
<br>
<em>ORKA-Base</em>
</p>

<img src="assets/images/2026-04-10_cabling_step_8.JPG" width="600" alt="Elektronik-Innenansicht">
<br>
<em>ORKA interior: Raspberry Pi 5 (8GB), ESP32, 2× TB6612FNG motor drivers, Hailo-8L M.2 HAT (13 TOPS)</em>
</p>

<img src="assets/images/2026-04-18_J0+J5_drive.JPG" width="600" alt="J0 & J5 drives">
<br>
<em>J0 & J5 Harmonic Drive Tests</em>
</p>

</details>


---

## Demo

▶ [ORKA — First Drive + Live Camera](https://www.linkedin.com/feed/update/urn:li:activity:7443994345415983104/?originTrackingId=DYWwwzkaQbOAQfCaBuinaQ%3D%3D)
▶ [ORKA — YOLOv8 Object Detection](https://www.linkedin.com/feed/update/urn:li:activity:7443994691827814400/?originTrackingId=r31xf84IT7KFzjLIR9R%2BbQ%3D%3)

## What is ORKA?

ORKA is a self-built autonomous robot designed for outdoor object detection and collection using YOLOv8 and a robotic arm. The platform is intentionally modular — the mission payload (arm, sensors, gripper) can be swapped independently of the drive platform.

Current status: **driving, camera live, YOLOv8 running on-device, 6-DOF arm in development.**

---

## Architecture

```
Raspberry Pi 5 (8GB)  ──UART──►  ESP32
   │                                │
   │ picamera2 / CSI                └──► 2× TB6612FNG ──► 4× DC Motors
   │ YOLOv8 (orca-env)
   │
   └──► Hailo-8L M.2 HAT (13 TOPS — active)
        [planned] 2nd ESP32 — sensors (GPS, IMU, ultrasonic)
        [planned] Robotic Arm (Robotastic 6R, NEMA steppers, SimpleFOC)
        [planned] GPS · IMU · LiDAR
        [long-term] Legged platform — quadruped
```
**Key design decisions:**
- ESP32 handles real-time motor control (deterministic, no Linux overhead)
- Pi 5 handles vision and high-level logic
- Chassis uses a Gridfinity + DIN-rail hybrid system (Robofinity) for modular electronics mounting
- Robotic arm uses modular NEMA 17 + Harmonic Drive joints for rapid prototyping & iteration

---

## Tech Stack

| Layer | Technology |
|---|---|
| Vision / AI | YOLOv8 (ultralytics), picamera2, Raspberry Pi Camera Module 3, Hailo-8L M.2 HAT (13 TOPS) |
| Compute | Raspberry Pi 5 8GB, ESP32 DevKit |
| Motor Control | TB6612FNG × 2 (drive), MKS SERVO42D CAN-Bus × 6 (arm) |
| Arm Design | NEMA 17 steppers, Harmonic Drives |
| Chassis | 3D-printed PETG, Gridfinity + DIN-rail hybrid (Robofinity) |
| CAD | Siemens NX |
| 3D Printing | Bambu Lab P1S with AMS 2 Pro |

---

## Roadmap

- [x] Drive platform — 4-wheel, ESP32-controlled, UART interface to Pi
- [x] Camera live — Raspberry Pi Camera Module 3 via CSI
- [x] YOLOv8 running on Pi 5 (8GB)
- [x] Hailo-8L M.2 HAT integration — 13 TOPS hardware-accelerated inference
- [x] Robotic arm J0 & J5 — NEMA 17 + Harmonic Drives tested ✅
- [ ] Complete 6-DOF arm assembly (J1–J6)
- [ ] CAN-Bus integration — MKS SERVO42D full control
- [ ] Sensor expansion — GPS, IMU, ultrasonic
- [ ] TACO dataset fine-tuning — outdoor trash detection
- [ ] Autonomous pick-and-place mission
- [ ] Legged platform — long-term vision: ORKA as a quadruped robot

---

## Engineering Log

Development decisions, hardware failures, and lessons learned are documented in [`DEVLOG.md`](DEVLOG.md).

**Recent:** Motor-gearbox validation (04.2026), Robofinity electronics integration, 6-DOF arm rapid prototyping with NEMA 17 + Harmonic Drives.

---

*Built by [Lenny Kossyk](mailto:lenkossyk@gmail.com) · [LinkedIn](https://www.linkedin.com/in/lenny-kossyk-2a2033234) · TH Nürnberg · Energieprozesstechnik*

---

## Related Projects

- **[Robofinity](https://github.com/lennykossyk/Robofinity)** — open-source modular robot chassis system (Gridfinity + DIN-rail hybrid)