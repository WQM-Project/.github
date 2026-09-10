<h1 align="center">
  <br>
  🌊 WQM Project
  <br>
</h1>

<h3 align="center">
  <em>Real-Time River Water Quality Monitoring & Pollution Prediction</em>
</h3>

<p align="center">
  <a href="https://github.com/WQM-Project/WQM"><img src="https://img.shields.io/badge/firmware-STM32F722-00599C?style=for-the-badge&logo=stmicroelectronics&logoColor=white" alt="STM32"></a>
  <a href="#"><img src="https://img.shields.io/badge/comms-LoRa_SX1278-FF6F00?style=for-the-badge&logo=semver&logoColor=white" alt="LoRa"></a>
  <a href="#"><img src="https://img.shields.io/badge/cloud-Firebase-FFCA28?style=for-the-badge&logo=firebase&logoColor=black" alt="Firebase"></a>
  <a href="#"><img src="https://img.shields.io/badge/ML-LSTM_+_Prophet-FF6F61?style=for-the-badge&logo=tensorflow&logoColor=white" alt="ML"></a>
  <a href="#"><img src="https://img.shields.io/badge/dashboard-React_18-61DAFB?style=for-the-badge&logo=react&logoColor=black" alt="React"></a>
</p>

<p align="center">
  <a href="https://github.com/WQM-Project/WQM"><img src="https://img.shields.io/github/last-commit/WQM-Project/WQM?style=flat-square&color=blue" alt="Last Commit"></a>
  <a href="https://github.com/WQM-Project/WQM/stargazers"><img src="https://img.shields.io/github/stars/WQM-Project/WQM?style=flat-square&color=yellow" alt="Stars"></a>
  <a href="https://github.com/WQM-Project/WQM"><img src="https://img.shields.io/github/repo-size/WQM-Project/WQM?style=flat-square&color=green" alt="Repo Size"></a>
  <a href="https://github.com/WQM-Project/WQM/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-purple?style=flat-square" alt="License"></a>
</p>

---

## 🏔️ The Problem

Himalayan river ecosystems — the **Beas**, **Uhl**, and **Suketi** — face escalating ecological degradation from unmanaged construction runoff, tourism waste, and accelerated glacial melt. Manual sampling and lab analysis can't catch sudden pollution events. Communities downstream deserve better.

## 🚀 Our Solution

An **autonomous IoT boat** that patrols rivers, collecting real-time water quality data with industrial-grade sensors, transmitting it over **LoRa** to a cloud backend that predicts pollution **24 hours ahead** using deep learning — and alerts authorities instantly.

---

## 🏗️ System Architecture

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         🌊  RIVER ENVIRONMENT                          │
└──────────────────────┬───────────────────────────────────────────────────┘
                       │
        ┌──────────────▼──────────────┐
        │    📡 SENSOR BOAT (Edge)    │
        │  ┌────────────────────────┐ │
        │  │   STM32F722ZE MCU     │ │
        │  │  ┌──────┐ ┌────────┐  │ │
        │  │  │ ADC  │ │Median  │  │ │
        │  │  │12-bit│ │Filter  │  │ │
        │  │  └──┬───┘ └───┬────┘  │ │
        │  └─────┼─────────┼───────┘ │
        │   pH ──┘  Turb  TDS  DO    │
        │   EC   ORP  Temp  BME280   │
        │   JSN-SR04T  DS18B20×2     │
        │   NEO-6M GPS              │
        └────────────┬───────────────┘
                     │ LoRa SX1278
                     │ 868/915 MHz RF
              ≈≈≈≈≈≈≈▼≈≈≈≈≈≈≈
        ┌────────────────────────────┐
        │   🏠 SHORE GATEWAY        │
        │   ESP8266 (ESP-12E)       │
        │   + SIM7600E 4G LTE      │
        └────────────┬───────────────┘
                     │ HTTPS / JSON
              ┌──────▼──────┐
              │  ☁️  CLOUD  │
              │  Firebase   │
              │  Firestore  │
              └──┬──────┬───┘
        ┌────────▼──┐ ┌─▼───────────┐
        │ 🤖 ML     │ │ 📊 Dashboard│
        │ LSTM WQI  │ │ React SPA   │
        │ Prophet   │ │ Leaflet.js  │
        │ Trends    │ │ Recharts    │
        └─────┬─────┘ └─────────────┘
              │
        ┌─────▼─────────────┐
        │ 🚨 ALERT ENGINE   │
        │ Twilio SMS/WhatsApp│
        │ → Authorities     │
        └───────────────────┘
```

---

## 📡 Sensor Suite

| Sensor | Model | Parameter | Range | Interface |
|:-------|:------|:----------|:------|:----------|
| 🧪 pH | DFRobot SEN0161 | pH Level | 0–14 pH (±0.1) | Analog |
| 🌫️ Turbidity | DFRobot SEN0189 | Suspended Solids | 0–3000 NTU | Analog |
| 💧 TDS | Gravity Analog | Dissolved Solids | 0–1000 ppm | Analog |
| 🌡️ Temperature | DS18B20 ×2 | Water Temp | -55 to +125 °C (±0.5) | OneWire |
| 🫧 Dissolved O₂ | DFRobot SEN0237-A | DO Concentration | 0–20 mg/L | Analog |
| ⚡ Conductivity | DFRobot DFR0300 | EC (K=1) | 0–20 mS/cm | Analog |
| 🔋 ORP | DFRobot SEN0165 | Redox Potential | ±2000 mV | Analog |
| 📏 Depth | JSN-SR04T | Water Level | 20–600 cm | Trig/Echo |
| 🌤️ Environment | BME280 | Air Temp + Humidity + Pressure | – | I²C |
| 📍 Location | NEO-6M/M8N | GPS Coordinates | – | UART |

---

## 🛠️ Tech Stack

<table>
<tr>
<td align="center" width="20%">

**Edge / Firmware**
<br><br>
`STM32F722ZE`
<br>
`STM32CubeIDE`
<br>
`HAL Drivers`
<br>
`FreeRTOS`

</td>
<td align="center" width="20%">

**Communication**
<br><br>
`LoRa SX1278`
<br>
`SIM7600E 4G`
<br>
`ESP8266`
<br>
`AT Commands`

</td>
<td align="center" width="20%">

**Cloud**
<br><br>
`Firebase`
<br>
`Firestore`
<br>
`Cloud Functions`
<br>
`Data Bucketing`

</td>
<td align="center" width="20%">

**ML / Analytics**
<br><br>
`TensorFlow`
<br>
`Keras LSTM`
<br>
`FB Prophet`
<br>
`Python 3.10`

</td>
<td align="center" width="20%">

**Frontend**
<br><br>
`React 18`
<br>
`Vite`
<br>
`Leaflet.js`
<br>
`Recharts`

</td>
</tr>
</table>

---

## 🗺️ Project Roadmap

```
  ╔══════════════════════╗     ╔══════════════════════╗     ╔══════════════════════╗
  ║   STAGE 1 — RC Boat ║     ║  STAGE 2 — Autonomous║     ║  STAGE 3 — Fleet     ║
  ║  ──────────────────  ║     ║  ──────────────────  ║     ║  ──────────────────  ║
  ║  ✅ Sensor suite     ║     ║  ⬜ GPS waypoint nav ║     ║  ⬜ Multi-boat mesh  ║
  ║  ✅ STM32 firmware   ║ ──▶ ║  ⬜ Auto-stabilize   ║ ──▶ ║  ⬜ LoRa mesh net    ║
  ║  ✅ ADC + filtering  ║     ║  ⬜ Motor control     ║     ║  ⬜ Edge ML (TinyML) ║
  ║  🔄 LoRa telemetry  ║     ║  ⬜ Solar MPPT power  ║     ║  ⬜ Public API       ║
  ║  🔄 Gateway + cloud ║     ║  ⬜ Hull design       ║     ║  ⬜ Municipal portal ║
  ╚══════════════════════╝     ╚══════════════════════╝     ╚══════════════════════╝
```

---

## 📦 Repository

| Repository | Description | Status |
|:-----------|:------------|:-------|
| [`WQM`](https://github.com/WQM-Project/WQM) | Core firmware, sensor drivers, LoRa telemetry, and project documentation | 🟢 Active |

---

## ⚡ Quick Links

<p align="center">
  <a href="https://github.com/WQM-Project/WQM/blob/main/README.md">📖 Full Specification Report</a> •
  <a href="https://github.com/WQM-Project/WQM/tree/main/Sensor_Readme">📡 Sensor Documentation</a> •
  <a href="https://github.com/WQM-Project/WQM/tree/main/Core/Src">💻 Source Code</a>
</p>

---

## 🤝 Contributing

We welcome contributions! Whether it's firmware optimization, ML model improvements, dashboard features, or hull design ideas — open an issue or submit a PR on the [main repo](https://github.com/WQM-Project/WQM).

---

## 📊 Communication Protocol

```
┌─ LoRa Packet Format ──────────────────────────────────────────────────┐
│ node_id │ timestamp │ lat │ lon │ pH │ turb │ TDS │ temp │ DO │ cond │ checksum │
│  1 byte │  4 bytes  │ 4B  │ 4B  │ 2B │  2B  │ 2B  │  2B  │ 2B │  2B  │  2 bytes │
└────────────────────────────────────────────────────────────────────────┘

  Edge → Gateway:  LoRa SX1278 @ 868/915 MHz  │  ~2 km LoS, ~500 m NLoS
  Gateway → Cloud: 4G LTE HTTPS/JSON           │  Low bandwidth (~1.5 GB/day max)
```

---

## 🔋 Power Architecture

```
  ☀️ Solar Panel (6V 3W)
        │
        ▼
  ┌─────────────┐     ┌──────────────┐
  │  CN3791 MPPT │────▶│  LiPo Battery │
  │  Controller  │     │  3.7V 3000mAh│
  └──────────────┘     └──────┬───────┘
                              │
                    ┌─────────▼─────────┐
                    │  Power Management │
                    │  Sleep between    │
                    │  waypoints        │
                    └───────────────────┘
```

---

<p align="center">
  <sub>Built with 💙 for Himalayan river conservation</sub>
  <br>
  <sub>
    <strong>Domains:</strong> Embedded Systems • IoT • AI/ML • Environmental Conservation • Health Tech
  </sub>
</p>
