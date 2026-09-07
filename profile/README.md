<div align="center">

# 🎈 Serendib HAB
### *Ground Station Pipeline & Multi-Station Cloud Synchronization Platform*

[![Documentation](https://img.shields.io/badge/docs-serendib--docs-blue.svg?style=for-the-badge&logo=gitbook&logoColor=white)](https://github.com/serendib-hab)
[![Architecture](https://img.shields.io/badge/architecture-offline--first-success.svg?style=for-the-badge&logo=satellite&logoColor=white)](https://github.com/serendib-hab)
[![License](https://img.shields.io/badge/license-MIT-informational.svg?style=for-the-badge)](https://github.com/serendib-hab)
[![Status](https://img.shields.io/badge/missions-active-orange.svg?style=for-the-badge&logo=githubactions&logoColor=white)](https://github.com/serendib-hab)

<p align="center">
  <b>Serendib HAB</b> is an open-source, high-reliability software and avionics ecosystem engineered for <b>High-Altitude Balloon (HAB) missions and near-space exploration</b>. Designed with an offline-first philosophy, Serendib enables autonomous local ground stations, real-time trajectory prediction, multi-station consensus, and mission control telemetry distribution.
</p>

---

[Explore Docs](https://github.com/serendib-hab) • [Architecture Guide](https://github.com/serendib-hab) • [Packet Specs](https://github.com/serendib-hab) • [Get Involved](#-contributing--community)

</div>

---

## 🌌 Key Highlights

<table>
  <tr>
    <td width="50%" valign="top">
      <h3>📡 Offline-First Ground Station</h3>
      <ul>
        <li><b>Zero-Dependency Field Operations:</b> Autonomous edge pipeline running full LoRa/FSK radio ingest, telemetry decoding, and command safety interlocks without internet connectivity.</li>
        <li><b>Append-Only Persistence:</b> Embedded SQLite in WAL mode ensuring every raw frame and demodulated metric is preserved non-destructively.</li>
        <li><b>Dynamic Trajectory Prediction:</b> Real-time ascent/burst/descent models integrating live atmospheric wind soundings and Kalman filtering.</li>
      </ul>
    </td>
    <td width="50%" valign="top">
      <h3>☁️ Multi-Station Cloud Mesh</h3>
      <ul>
        <li><b>High-Throughput Messaging:</b> NATS JetStream event broker distributing live telemetry frames with sub-millisecond latencies.</li>
        <li><b>Consensus & Deduplication:</b> Real-time packet reconciliation across globally distributed listener stations to maximize payload coverage.</li>
        <li><b>Downstream Gateways:</b> Real-time 3D web flight tracking, broadcast live stream overlays, and automated recovery alerts via Telegram.</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td width="50%" valign="top">
      <h3>📦 Avionics & Science Payloads</h3>
      <ul>
        <li><b>Modular Payload Architecture:</b> Support for multi-band RF (LoRa, FSK, APRS), GNSS tracking, and secondary payload buses (I2C/SPI/UART).</li>
        <li><b>Atmospheric Sensing:</b> High-resolution barometric altitude, thermal gradients, cosmic radiation monitoring, and onboard cameras.</li>
        <li><b>Power & Cutdown Systems:</b> Energy-efficient power budgeting with thermal management and failsafe geo-fenced cutdown mechanisms.</li>
      </ul>
    </td>
    <td width="50%" valign="top">
      <h3>🛡️ Safety, Security & Telemetry Specs</h3>
      <ul>
        <li><b>Compact 32-Byte Binary Framing:</b> Maximizes RF link budget, coding gain, and airtime efficiency over long-range LoRa links.</li>
        <li><b>Deterministic Command Interlocks:</b> Cryptographically verified command uplink with multi-stage armed confirmations and hardware failsafes.</li>
        <li><b>Audit Logs:</b> End-to-end immutability for post-flight incident analysis and scientific dataset extraction.</li>
      </ul>
    </td>
  </tr>
</table>

---

## 🏛️ System Architecture

```mermaid
flowchart TB
    subgraph Payload ["🎈 Flight Avionics & Sensors"]
        SENS["Sensors (GNSS, Baro, IMU, Temp)"]
        FC["Flight Computer (Avionics)"]
        TX["RF Transmitter (LoRa / APRS)"]
        CUT["Safety Cutdown Engine"]

        SENS --> FC
        FC --> TX
        FC -.-> CUT
    end

    subgraph LocalStation ["📡 Local Ground Station (Offline-First)"]
        RF["Radio Receiver (LoRa / SDR)"]
        ING["Ingest Service (Go)"]
        STO[("SQLite DB (WAL Mode)")]
        PROC["Processing & Kalman Prediction"]
        REC["Station Reconciliation"]
        TUI["Field Operator TUI"]
        WEB["Mission Control Web UI"]
        CMD["Command Interlock & Uplink"]

        RF --> ING
        ING --> STO
        STO --> PROC
        PROC --> REC
        REC --> TUI
        REC --> WEB
        WEB -->|Command| CMD
        TUI -->|Command| CMD
        CMD --> RF
    end

    subgraph CloudLayer ["☁️ Cloud Telemetry Hub (NATS JetStream)"]
        C_ING["Sync Ingress Gateway"]
        BROKER["NATS JetStream Broker"]
        C_REC["Global Multi-Station Consensus"]
        C_STO[("Central Fleet DB")]
        C_TRACK["Public 3D Web Tracker"]
        C_NOTIF["Recovery Team Alert Bot"]

        C_ING --> BROKER
        BROKER --> C_REC
        C_REC --> C_STO
        BROKER --> C_TRACK
        BROKER --> C_NOTIF
    end

    TX ==>|Sub-GHz RF Link| RF
    STO <-.->|Intermittent Sync| C_ING
```

---

## 📂 Core Ecosystem Repositories

| Repository | Description | Primary Stack | Status |
|---|---|---|---|
| [`serendib-docs`](https://github.com/serendib-hab) | Architectural blueprints, mathematical flight models, specs & interactive guides | VitePress, Markdown, Mermaid | 🟢 Active |
| [`serendib-station`](https://github.com/serendib-hab) | Offline-first edge ground station, binary radio ingest & SQLite storage engine | Go, SQLite (WAL), Python, C++ | 🟢 Active |
| [`serendib-cloud`](https://github.com/serendib-hab) | Multi-station telemetry sync, NATS JetStream pub/sub & recovery dispatcher | Go, NATS, TypeScript, Docker | 🟡 In Development |
| [`serendib-tracker`](https://github.com/serendib-hab) | Real-time 3D flight visualization, mission control UI & chase vehicle dashboard | Vue / Vite, Cesium / Mapbox, TS | 🟡 In Development |
| [`serendib-avionics`](https://github.com/serendib-hab) | Flight computer firmware, power management, sensor drivers & cutdown controllers | C / C++, FreeRTOS, Embedded C | 🔵 Planned |

---

## 🛠️ Technology Stack

<div align="center">

| Domain | Technologies & Protocols |
|---|---|
| **Core & Edge Ingest** | `Go` • `Python` • `C++` • `SQLite (WAL)` • `LoRa / SX127x / SX126x` |
| **Cloud & Messaging** | `NATS JetStream` • `PostgreSQL / TimescaleDB` • `Docker` • `gRPC / Protobuf` |
| **Frontend & Visualization** | `TypeScript` • `Vue.js` • `Vite` • `TailwindCSS` • `Mapbox / OpenLayers` |
| **Avionics & Embedded** | `STM32 / ESP32` • `FreeRTOS` • `SPI / I2C / UART` • `GNSS (NMEA / UBX)` |
| **Documentation & CI/CD** | `VitePress` • `GitHub Actions` • `Mermaid.js` |

</div>

---

## 🤝 Contributing & Community

We welcome contributions from ballooning enthusiasts, radio amateurs, aerospace engineers, and software developers!

- 📖 **Read the Docs:** Explore architecture specifications and binary protocol definitions.
- 🐛 **Report Issues:** Found a bug or have a suggestion? Open an issue on any of the respective repositories.
- 💡 **Discussions:** Join technical discussions on telemetry protocols, flight dynamics, and RF links.

---

<div align="center">

<sub>Built with ❤️ by the Serendib HAB Engineering Team • Dedicated to open near-space exploration and scientific research.</sub>

</div>
