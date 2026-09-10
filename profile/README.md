<div align="center">

<br>

# 🌊 WQM Project

### Real-Time River Water Quality Monitoring & Pollution Prediction

**A solar-powered sensor boat patrols Himalayan rivers, streams readings over LoRa,<br>and forecasts pollution 24 hours before it arrives.**

<br>

<p>
  <img src="https://img.shields.io/badge/firmware-STM32F722ZE-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white" alt="STM32F722ZE">
  <img src="https://img.shields.io/badge/comms-LoRa_SX1278-FF6F00?style=for-the-badge" alt="LoRa SX1278">
  <img src="https://img.shields.io/badge/cloud-Firebase-FFCA28?style=for-the-badge&logo=firebase&logoColor=black" alt="Firebase">
  <img src="https://img.shields.io/badge/ML-LSTM_%2B_Prophet-FF6F61?style=for-the-badge&logo=tensorflow&logoColor=white" alt="LSTM and Prophet">
  <img src="https://img.shields.io/badge/dashboard-React_18-61DAFB?style=for-the-badge&logo=react&logoColor=black" alt="React 18">
</p>

<p>
  <a href="https://github.com/WQM-Project/WQM"><img src="https://img.shields.io/github/last-commit/WQM-Project/WQM?style=flat-square&color=0b7285&label=last%20commit" alt="Last Commit"></a>
  <a href="https://github.com/WQM-Project/WQM/stargazers"><img src="https://img.shields.io/github/stars/WQM-Project/WQM?style=flat-square&color=f59f00" alt="Stars"></a>
  <a href="https://github.com/WQM-Project/WQM"><img src="https://img.shields.io/github/repo-size/WQM-Project/WQM?style=flat-square&color=2f9e44" alt="Repo Size"></a>
</p>

<p>
  <a href="https://github.com/WQM-Project/WQM/blob/main/README.md"><b>📖 Full Spec</b></a> &nbsp;·&nbsp;
  <a href="https://github.com/WQM-Project/WQM/tree/main/Sensor_Readme"><b>📡 Hardware Docs</b></a> &nbsp;·&nbsp;
  <a href="https://github.com/WQM-Project/WQM/blob/main/Sensor_Readme/WQM_Sensor_BOM.md"><b>🧾 Bill of Materials</b></a>
</p>

<br>

</div>

---

## 🏔️ The Problem

The **Beas**, **Uhl**, and **Suketi** are losing ground to unmanaged construction runoff, tourism waste, and accelerated glacial melt.

The way we watch them hasn't kept up. Manual sampling catches one point in space and time. Lab turnaround takes days. Deep valleys have no Wi-Fi, patchy cellular, and no grid power for fixed stations.

**So pollution spikes get discovered after they've already moved downstream.**

## 🚀 Our Solution

An IoT boat that goes to the water instead of waiting for the water to reach a lab.

|  | |
|:-:|:--|
| 📡 | **Measures** 10 water-quality parameters with industrial-grade sensors, filtered and calibrated on the edge |
| 📶 | **Transmits** over LoRa — ~2 km line-of-sight to shore, then 4G LTE to the cloud |
| 🤖 | **Predicts** the Water Quality Index 24 hours ahead with an LSTM trained on CPCB data |
| 🚨 | **Alerts** authorities via SMS and WhatsApp the moment thresholds break |

---

## 🏗️ System Architecture

```mermaid
flowchart TB
    RIVER(["🌊 River Environment"])

    subgraph BOAT["📡 Sensor Boat — Edge Node"]
        direction TB
        SUITE["<b>Sensor Suite</b><br/>pH · Turbidity · TDS · DO · EC · ORP · Depth<br/>DS18B20 ×2 · BME280 · NEO-6M GPS"]
        MCU["<b>STM32F722ZE</b><br/>12-bit ADC → Median Filter → Calibration"]
        SUITE --> MCU
    end

    subgraph SHORE["🏠 Shore Gateway"]
        direction TB
        ESP["<b>ESP8266 ESP-12E</b><br/>validate · timestamp · serialize"]
        LTE["<b>SIM7600E 4G LTE</b><br/>upload"]
        ESP --> LTE
    end

    CLOUD[("☁️ <b>Firebase Firestore</b><br/>Data Bucketing")]

    subgraph BRAIN["Analytics + Delivery"]
        direction LR
        ML["🤖 <b>ML Pipeline</b><br/>LSTM → 24 h WQI<br/>Prophet → trends"]
        DASH["📊 <b>Dashboard</b><br/>React 18 SPA<br/>Leaflet · Recharts"]
    end

    ALERT["🚨 <b>Alert Engine</b><br/>Twilio SMS / WhatsApp"]
    GOV(["🏛️ Local Authorities"])

    RIVER --> SUITE
    MCU -.->|"LoRa SX1278 · 868/915 MHz · ~2 km LoS"| ESP
    LTE -->|"HTTPS / JSON"| CLOUD
    CLOUD --> ML
    CLOUD -->|"real-time"| DASH
    ML -->|"threshold breach"| ALERT
    ALERT --> GOV

    classDef endpoint fill:#0b7285,stroke:#095c6b,stroke-width:2px,color:#ffffff
    classDef edge fill:#1971c2,stroke:#1249a0,color:#ffffff
    classDef relay fill:#e8590c,stroke:#b34700,color:#ffffff
    classDef store fill:#6741d9,stroke:#4c2fa8,color:#ffffff
    classDef action fill:#2f9e44,stroke:#1f7a30,color:#ffffff
    classDef alarm fill:#e03131,stroke:#b02525,color:#ffffff

    class RIVER,GOV endpoint
    class SUITE,MCU edge
    class ESP,LTE relay
    class CLOUD store
    class ML,DASH action
    class ALERT alarm
```

---

## 📡 Sensor Suite

| Sensor | Model | Parameter | Range | Interface |
|:-------|:------|:----------|:------|:----------|
| 🧪 pH | DFRobot SEN0161 | pH Level | 0–14 pH (±0.1) | Analog |
| 🌫️ Turbidity | DFRobot SEN0189 | Suspended Solids | 0–3000 NTU | Analog |
| 💧 TDS | DFRobot SEN0244 | Dissolved Solids | 0–1000 ppm | Analog |
| 🌡️ Temperature | DS18B20 ×2 | Water Temp | −55 to +125 °C (±0.5) | OneWire |
| 🫧 Dissolved O₂ | DFRobot SEN0237-A | DO Concentration | 0–20 mg/L | Analog |
| ⚡ Conductivity | DFRobot DFR0300 | EC (K=1) | 0–20 mS/cm | Analog |
| 🔋 ORP | DFRobot SEN0165 | Redox Potential | ±2000 mV | Analog |
| 📏 Depth | JSN-SR04T | Water Level | 20–600 cm | Trig/Echo |
| 🌤️ Environment | BME280 | Air Temp + Humidity + Pressure | — | I²C |
| 📍 Location | NEO-6M / M8N | GPS Coordinates | — | UART |

---

## 🛠️ Tech Stack

<table>
<tr>
<td align="center" valign="top" width="20%">

**Edge / Firmware**

`STM32F722ZE`
`STM32CubeIDE`
`HAL Drivers`
`FreeRTOS`

</td>
<td align="center" valign="top" width="20%">

**Communication**

`LoRa SX1278`
`SIM7600E 4G`
`ESP8266`
`AT Commands`

</td>
<td align="center" valign="top" width="20%">

**Cloud**

`Firebase`
`Firestore`
`Cloud Functions`
`Data Bucketing`

</td>
<td align="center" valign="top" width="20%">

**ML / Analytics**

`TensorFlow`
`Keras LSTM`
`FB Prophet`
`Python 3.10`

</td>
<td align="center" valign="top" width="20%">

**Frontend**

`React 18`
`Vite`
`Leaflet.js`
`Recharts`

</td>
</tr>
</table>

---

## 📊 Communication Protocol

```
┌─ LoRa Packet Format ────────────────────────────────────────────────────────────┐
│ node_id │ timestamp │ lat │ lon │  pH  │ turb │ TDS  │ temp │  DO  │ cond │ CRC │
│  1 B    │    4 B    │ 4 B │ 4 B │  2 B │  2 B │  2 B │  2 B │  2 B │  2 B │ 2 B │
└─────────────────────────────────────────────────────────────────────────────────┘

   Edge → Gateway    LoRa SX1278 @ 868/915 MHz    ~2 km LoS  ·  ~500 m NLoS
   Gateway → Cloud   4G LTE, HTTPS + JSON         low bandwidth (~1.5 GB/day)
```

## 🔋 Power Architecture

```mermaid
flowchart TB
    SUN["☀️ <b>Solar Panel</b><br/>6V 3W monocrystalline"]
    MPPT["<b>CN3791 MPPT</b><br/>Charge Controller"]
    BAT["🔋 <b>LiPo Battery</b><br/>3.7V 3000 mAh · 18650"]
    PM["<b>Power Management</b><br/>sleep between waypoints"]
    LOAD["<b>Loads</b><br/>Sensors · MCU · LoRa · Motors"]

    SUN -->|"harvest"| MPPT
    MPPT -->|"charge"| BAT
    BAT -->|"regulated rail"| PM
    PM -->|"gated supply"| LOAD

    classDef src fill:#f59f00,stroke:#c47f00,stroke-width:2px,color:#000000
    classDef mid fill:#1971c2,stroke:#0d4a8a,stroke-width:2px,color:#ffffff
    classDef sink fill:#2f9e44,stroke:#1a6b2a,stroke-width:2px,color:#ffffff
    class SUN src
    class MPPT,BAT,PM mid
    class LOAD sink
```

Entirely off-grid. Non-critical modules power down between waypoints.

---

## 🗺️ Roadmap

```mermaid
flowchart LR
    subgraph S1["🛥️ STAGE 1 · RC Boat"]
        direction TB
        A1["✅ Sensor suite selected"]
        A2["✅ STM32 firmware base"]
        A3["✅ ADC + filtering"]
        A4["🔄 LoRa telemetry"]
        A5["🔄 Gateway + cloud"]
        A1 ~~~ A2 ~~~ A3 ~~~ A4 ~~~ A5
    end

    subgraph S2["🧭 STAGE 2 · Autonomous"]
        direction TB
        B1["GPS waypoint navigation"]
        B2["Auto-stabilize on arrival"]
        B3["Motor control"]
        B4["Solar MPPT power"]
        B5["Hull design"]
        B1 ~~~ B2 ~~~ B3 ~~~ B4 ~~~ B5
    end

    subgraph S3["🛰️ STAGE 3 · Fleet"]
        direction TB
        C1["Multi-boat mesh"]
        C2["LoRa mesh network"]
        C3["On-device TinyML"]
        C4["Public API"]
        C5["Municipal portal"]
        C1 ~~~ C2 ~~~ C3 ~~~ C4 ~~~ C5
    end

    S1 ==> S2 ==> S3

    classDef done fill:#2f9e44,stroke:#1a6b2a,stroke-width:2px,color:#ffffff
    classDef wip fill:#e8590c,stroke:#a83e08,stroke-width:2px,color:#ffffff
    classDef planned fill:#495057,stroke:#2b3035,stroke-width:2px,color:#ffffff
    class A1,A2,A3 done
    class A4,A5 wip
    class B1,B2,B3,B4,B5,C1,C2,C3,C4,C5 planned
```

<div align="center">
<sub>✅ done · 🔄 in progress · ⬜ planned</sub>
</div>

> [!NOTE]
> **Hull design is open.** The mechanical platform for both boat stages is still under active development — hydrodynamic stability, a rigid probe mast, and battery volume for propulsion. This is a great place to jump in.

---

## 📦 Repositories

| Repository | Description | Status |
|:-----------|:------------|:-------|
| [**`WQM`**](https://github.com/WQM-Project/WQM) | Core firmware, sensor drivers, LoRa telemetry, and full project documentation | 🟢 Active |

---

## 🤝 Contributing

Firmware optimization, ML model improvements, dashboard features, or hull design ideas — all of it is welcome. Open an issue or send a PR on the [main repo](https://github.com/WQM-Project/WQM).

---

<div align="center">
<sub>Built with 💙 for Himalayan river conservation</sub>
<br>
<sub><b>Embedded Systems</b> · <b>IoT</b> · <b>AI/ML</b> · <b>Environmental Conservation</b> · <b>Health Tech</b></sub>
</div>
