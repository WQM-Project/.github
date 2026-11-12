<div align="center">

<br>

# 🌊 WQM Project

### Real-Time River Water Quality Monitoring & Pollution Prediction

**A solar-powered sensor boat patrols Himalayan rivers, streams readings directly over 4G LTE,<br>and forecasts pollution 24 hours before it arrives.**

<br>

<p>
  <img src="https://img.shields.io/badge/firmware-STM32F722ZE-03234B?style=for-the-badge&logo=stmicroelectronics&logoColor=white" alt="STM32F722ZE">
  <img src="https://img.shields.io/badge/comms-ESP32_%2B_SIM7600_4G-FF6F00?style=for-the-badge" alt="ESP32 + SIM7600 4G LTE">
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
| 📶 | **Transmits** directly onboard — STM32 sends JSON over 3.3V UART RX–TX to an onboard ESP32 + SIM7600 4G LTE & GNSS module |
| 🤖 | **Predicts** the Water Quality Index 24 hours ahead with an LSTM trained on CPCB data |
| 🚨 | **Alerts** authorities via SMS and WhatsApp the moment thresholds break |

---

## 🏗️ System Architecture

```mermaid
flowchart TB
    RIVER(["🌊 River Environment"])

    subgraph BOAT["📡 Sensor Boat — Edge + Onboard Gateway"]
        direction TB
        SUITE["<b>Sensor Suite</b><br/>pH · TDS · DO · ORP · DS18B20<br/>BMP280 · BNO085 · SIM7600 GNSS"]
        MCU["<b>STM32F722ZE</b><br/>12-bit ADC (2:1 Dividers) · Median Filter<br/>Calibration · JSON Telemetry"]
        ESP["<b>ESP32-WROOM-32</b><br/>validate · attach GPS + UTC · flash queue"]
        LTE["<b>SIM7600 4G LTE + GNSS</b><br/>GPS coordinates · HTTPS upload"]
        SUITE --> MCU
        MCU -->|"UART RX–TX · 115200 8N1 (3.3V)"| ESP
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
    LTE -->|"4G LTE · HTTPS / JSON"| CLOUD
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
| 🧪 pH | robu.in Analog pH Kit (SEN0161 V1) | pH Level (`PH_SCALE=4.942f`, `PH_OFFSET=-2.713f`) | 0–14 pH (±0.1) | `PA3` (`ADC1_CH3`, 2:1 divider, 5V rail) |
| 💧 TDS | DFRobot SEN0244 | Dissolved Solids (`TDS_CAL_K=1.036f`) | 0–1000 ppm | `PA5` (`ADC1_CH5`, 2:1 divider) |
| 🌡️ Temperature | DS18B20 | Water Temp (`temp_w1`) | −55 to +125 °C (±0.5) | `PE6` (1-Wire, 4.7kΩ pull-up) |
| 🫧 Dissolved O₂ | Graylogix / DFRobot SEN0237-A | DO Concentration (7.5% KCl, air-cal `764 mV @ 24°C`) | 0–20 mg/L | `PA4` (`ADC1_CH4`, 2:1 divider) |
| 🔋 ORP | DFRobot SEN0165 | Redox Potential (`2.0V` midpoint, 20-sample trimmed mean) | ±2000 mV | `PC3` (`ADC1_CH13`, 2:1 divider, 5V rail) |
| 🌤️ Environment | BMP280 (`0x76`) | Air Temp + Barometric Pressure (DO comp; no RH) | ±1 hPa | `PB8 / PB9` (`I2C1`) |
| 🧭 IMU | BNO085 (`0x4A`) | Heading / Pitch / Roll | 9-DOF | `PB8 / PB9` (`I2C1`) |
| 📍 Location | SIM7600 GNSS *(+ optional NEO-M8N)* | GPS Coordinates / Fix / Speed / Course | — | Onboard ESP32 + SIM7600 *(optional STM32 `UART4`)* |
| 🌫️ Turbidity *(reserved)* | DFRobot SEN0189 | Suspended Solids (unfitted in current JSON) | 0–3000 NTU | `PC0` (`ADC1_CH10`, 2:1 divider) |
| ⚡ Conductivity *(reserved)* | DFRobot DFR0300 | EC (K=1, unfitted in current JSON) | 0–20 mS/cm | `PA6` (`ADC1_CH6`, 2:1 divider) |

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

`UART RX–TX`
`ESP32-WROOM-32`
`SIM7600 4G + GNSS`
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
┌─ STM32 → Onboard ESP32 UART JSON Line (115200 8N1, \n-terminated) ──────────────┐
│ {"id","ts","pH","tds_ppm","do_mgl","orp_mv","temp_w1","temp_air",               │
│  "pressure_hpa","lat","lon","alt_m","sog_knots","cog_deg","gps_fix",...} + \n   │
└─────────────────────────────────────────────────────────────────────────────────┘

   STM32 → ESP32     3.3V UART RX–TX @ 115200 8N1   Direct onboard wire + ACK/NAK
   ESP32 → Cloud     SIM7600 4G LTE HTTPS POST      Onboard GNSS fix + flash retry queue
```

## 🔋 Power Architecture

```mermaid
flowchart TB
    SUN["☀️ <b>Solar Panel</b><br/>6V 3W monocrystalline"]
    MPPT["<b>CN3791 MPPT</b><br/>Charge Controller"]
    BAT["🔋 <b>LiPo Battery</b><br/>3.7V 3000 mAh · 18650"]
    PM["<b>Power Management</b><br/>sleep between waypoints"]
    LOAD["<b>Loads</b><br/>Sensors · STM32 · ESP32/SIM7600 · Motors"]

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
        A4["🔄 STM32 ↔ ESP32 UART link"]
        A5["🔄 Onboard gateway + cloud"]
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
        C1["Multi-boat coordination"]
        C2["Multi-node 4G fleet telemetry"]
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
| [**`WQM`**](https://github.com/WQM-Project/WQM) | Core firmware, sensor drivers, onboard UART → 4G telemetry, and full project documentation | 🟢 Active |

---

## 🤝 Contributing

Firmware optimization, ML model improvements, dashboard features, or hull design ideas — all of it is welcome. Open an issue or send a PR on the [main repo](https://github.com/WQM-Project/WQM).

---

<div align="center">
<sub>Built with 💙 for Himalayan river conservation</sub>
<br>
<sub><b>Embedded Systems</b> · <b>IoT</b> · <b>AI/ML</b> · <b>Environmental Conservation</b> · <b>Health Tech</b></sub>
</div>
