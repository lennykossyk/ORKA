# Engineering Devlog

> Hardware:  
> Raspberry Pi 5 (8GB) · ESP32 · TB6612FNG · YOLOv8 · PETG-Chassis (3D-Druck)

---

## 2026-03-06 – Erste Motorsteuerungsversuche & Architekturentscheidung

**Ziel**
- ESP32 einrichten, erste Motorsteuerung mit TB6612FNG aufbauen
- Systemarchitektur für die Motorsteuerung festlegen.

**Vorgehensweise**
- Arduino IDE installiert, ESP32 DevKit eingerichtet, ersten Testcode geflasht
- Breadboard-Prototyp mit TB6612FNG aufgebaut
- Stromversorgung für ESP32 DevKit über Powerpack (USB-A auf Micro USB)
- Stromversorgung für DC Gear Motors über Powerpack: altes USB-A Kabel aufgeschnitten und direkt mit Breadboard verbunden

<br>
<details>
<summary><h3><strong><u>📸 Fotos anzeigen</u></strong></h3></summary>
<br>
<p align="center">
<img src="assets/schematics/2026-03-06_schaltplan.svg" width="600" alt="Schaltplan erste Version">
<br>
<em>Erste Verkabelungsidee: 5V an VM, alte Pin-Belegung (G25/26/32)</em>
</p>
<p align="center">
<img src="assets/images/2026-03-06_powerpack_front.JPG" width="600" alt="Powerpack Frontansicht">
<br>
<em>Powerpack Frontalansicht — AC-Ausgang 200W + 2 x USB-A Port (5V/3A) </em>
</p>
<br>
<p align="center">
<img src="assets/images/2026-03-06_powerpack_side.jpeg" width="600" alt="Powerpack Seitenansicht">
<br>
<em>Powerpack Seitenansicht — USB-C Port (5-20V/3A)</em>
</p>
<p align="center">
<img src="assets/images/2026-03-06_usb_kabel_aufgeschnitten.png" width="400" alt="USB-A Kabel aufgeschnitten">
<br>
<em>USB-A Kabel aufgeschnitten: rot = 5V, schwarz = GND — direkt ans Breadboard angeschlossen</em>
</p>
<br>

</details>

<br>

**Probleme**
- Der Motor ließ sich nicht ansteuern
  
  > Vermutung: Entweder liegt ein Fehler in der Verkabelung des Motortreibers vor, oder die gewünschte Spannung wird erst nach einer entsprechenden Anfrage über USB Power Delivery bereitgestellt
- Powerpack liefert über USB-C PD erst nach expliziter Spannungsanforderung die gewünschte Spannung – kein automatischer 12-V-Output

**Lösungen**
- Referenz für die Verkabelung des TB6612FNG-Motortreibers auf GitHub gesucht
- Mehrere YouTube Tutorials zur TB6612FNG Verkabelung angesehen  
- USB-C PD Trigger Kabel bestellt (fordert automatisch 15V an → Step-Down auf 12V für Motoren)
- Expansion Board für das ESP32 DevKit bestellt, um das Breadboard zu ersetzen
- Für den Fall, dass weitere Sensoren angeschlossen werden sollen, wurde ein zweites ESP32 DevKit bestellt

- Architekturentscheidung: Motorsteuerung läuft auf ESP32 DevKit, nicht direkt auf Raspberry Pi
  - Begründung: Mikrocontroller reagiert deterministischer als Linux-System bei zeitkritischer Motorsteuerung
  - ESP32 kann Arduino-Sensoren direkt anbinden
  - Raspberry Pi bleibt für Bildverarbeitung und YOLOv8 zuständig


**Erkenntnis**
- USB-C PD verhält sich nicht wie eine einfache Spannungsquelle — Protokoll-Handshake notwendig
- Klare Aufgabentrennung ESP32/Pi von Anfang an sinnvoll

**Ausblick**
- PD Trigger Kabel + Expansion Board abwarten
- Motor-Test mit sauberer Verkabelung wiederholen

---


