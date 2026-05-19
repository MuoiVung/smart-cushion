# 🦫 CapyCushion: AI-Powered Smart Posture Assistant

Welcome to **CapyCushion**, an end-to-end IoT solution designed to improve spinal health through real-time posture monitoring, AI-driven feedback, and a fun gamified reward system.

![CapyCushion Logo](https://smart-cushion-app.vercel.app/assets/capybara/logo.png)

## 🌟 Overview

CapyCushion is more than just a smart seat; it's a personal posture coach. By combining pressure sensors, Edge computing, and Cloud AI, the system detects your sitting posture and provides immediate feedback. To keep users engaged, we've integrated a **Capy Gacha** system where users earn Gems for maintaining a healthy posture, which can be used to collect adorable Capybara stickers.

### Key Features
- **Real-time Monitoring**: 9 pressure sensors track your posture with high precision.
- **AI Classification**: Advanced machine learning models classify 9 distinct sitting postures.
- **Gamified Rewards**: Earn 1 Gem for every 10 seconds of proper sitting.
- **Capy Gacha**: Spend Gems to roll for rare Capybara stickers and build your collection.
- **Cloud-Fog-Edge Architecture**: Optimized data processing for low latency and high scalability.

---

## 🏗️ System Architecture

CapyCushion follows a **Cloud-Fog-Edge** paradigm, distributing intelligence across three computing layers to achieve both low-latency real-time feedback and robust cloud-scale data management.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                          CLOUD LAYER (AWS)                              │
│                                                                         │
│   ┌──────────────┐   ┌──────────────┐   ┌──────────────┐              │
│   │  Login / Auth │   │ Gacha / Gems │   │Process Summary│              │
│   │    Lambda    │   │    Lambda    │   │    Lambda    │              │
│   └──────┬───────┘   └──────┬───────┘   └──────┬───────┘              │
│          └──────────────────┴──────────────────┘                       │
│                              │ DynamoDB                                  │
│                     ┌────────▼────────┐                                 │
│                     │  User Profiles  │                                 │
│                     │  Gem Balances   │                                 │
│                     │  Session Logs   │                                 │
│                     │  Collections    │                                 │
│                     └─────────────────┘                                 │
└───────────────────────────┬─────────────────────────────────────────────┘
                    REST API │ (HTTPS)              ▲ Session Summary
                             │                      │ (on session end)
┌───────────────────────────▼─────────────────────-┴───────────────────┐
│                          APP LAYER (Vercel)                           │
│                                                                       │
│   React + Vite + TailwindCSS                                          │
│   ┌─────────────────┐  ┌──────────────┐  ┌──────────────────────┐   │
│   │   Live Monitor  │  │  Capy Gacha  │  │   Session History    │   │
│   │  (posture viz)  │  │ (gems/roll)  │  │  (past sessions)     │   │
│   └────────┬────────┘  └──────────────┘  └──────────────────────┘   │
└────────────┼──────────────────────────────────────────────────────────┘
     WebSocket│ (real-time updates)
┌────────────▼──────────────────────────────────────────────────────────┐
│                        FOG LAYER (Local PC / Laptop)                  │
│                                                                       │
│   Python Fog Node                                                     │
│   ┌──────────────┐     ┌─────────────────────────┐                   │
│   │ UDP Receiver │────▶│    AI Posture Classifier │                   │
│   │  (ESP32 raw) │     │  Model: HDF5 / SKLearn   │                   │
│   └──────────────┘     │  9 Classes (NUP, LF, LB, │                   │
│                         │  LFSR, LFSL, CRL, CLL,   │                   │
│   ┌──────────────────┐  │  CRLL, CLLL)             │                   │
│   │ WebSocket Server │◀─└─────────────────────────┘                   │
│   │ (broadcasts to   │                                                 │
│   │    App layer)    │                                                 │
│   └──────────────────┘                                                 │
└────────────▲──────────────────────────────────────────────────────────┘
      UDP    │ (raw sensor values, ~10 Hz)
┌────────────┼──────────────────────────────────────────────────────────┐
│                        EDGE LAYER (ESP32 Hardware)                    │
│                                                                       │
│   ┌──────────────────────────────────────────────────┐               │
│   │           3 × 3 Pressure Sensor Matrix           │               │
│   │                                                  │               │
│   │    [FL]  [FM]  [FR]                              │               │
│   │    [ML]  [MM]  [MR]    ──▶  ESP32 ADC reads      │               │
│   │    [BL]  [BM]  [BR]         9 analog channels    │               │
│   │                                                  │               │
│   └──────────────────────────────────────────────────┘               │
│                                                                       │
│   ESP32 packages 9 sensor values → sends via Wi-Fi UDP               │
└───────────────────────────────────────────────────────────────────────┘
```

### Data Flow — Step by Step

| Step | Layer | Action |
|------|-------|--------|
| 1 | **Edge** | 9 pressure sensors detect weight distribution across the cushion surface |
| 2 | **Edge → Fog** | ESP32 samples ADC values at ~10 Hz and transmits them as UDP packets over Wi-Fi |
| 3 | **Fog** | Python Fog Node receives raw values and feeds them into the trained AI model |
| 4 | **Fog** | Model classifies one of 9 posture labels (e.g. `NUP`, `LF`, `CRLL`…) in < 50 ms |
| 5 | **Fog → App** | Result is broadcast via WebSocket as a `realtime_update` JSON message |
| 6 | **App** | Live Monitor reacts instantly — Capybara avatar and sensor heatmap update |
| 7 | **Fog → Cloud** | At session end, Fog sends a summary payload to AWS (durations, posture breakdown) |
| 8 | **Cloud** | `ProcessSummaryFn` Lambda calculates Gems earned: **1 Gem per 10 s of NUP posture** |
| 9 | **App ↔ Cloud** | User interacts with Gacha: rolls stickers, checks collection — all via REST API |

> ℹ️ **For detailed technical specifications regarding sensor calibration, raw signal quality filtering, feature engineering, and cross-subject validation, please refer to the [AI Data Pipeline & Processing Flow](./DATA_PIPELINE.md) guide.**

### Why Cloud-Fog-Edge?

| Concern | Solution |
|---------|----------|
| **Low Latency** | AI inference runs on Fog (local), not Cloud — response in < 50 ms |
| **Privacy** | Raw sensor data never leaves the local network |
| **Cost** | Only summary data hits AWS, reducing Lambda invocations significantly |
| **Scalability** | Cloud handles multi-user state, history and reward management independently |

### Repository Map

| Repo | Layer | Tech Stack | Link |
|------|-------|------------|------|
| `smart-cushion-edge` | Edge | C++ / Arduino / ESP32 | [GitHub](https://github.com/MuoiVung/smart-cushion-edge) |
| `smart-cushion-AI` | Fog (Model) | Python / TensorFlow / Scikit-learn | [GitHub](https://github.com/MuoiVung/smart-cushion-AI) |
| `smart-cushion-fog-node` | Fog (Runtime) | Python / asyncio / WebSocket | [GitHub](https://github.com/MuoiVung/smart-cushion-fog-node) |
| `smart-cushion-cloud` | Cloud | AWS SAM / Lambda / DynamoDB | [GitHub](https://github.com/MuoiVung/smart-cushion-cloud) |
| `smart-cushion-app` | App | React / Vite / TailwindCSS | [GitHub](https://github.com/MuoiVung/smart-cushion-app) · [Live Demo](https://smart-cushion-app.vercel.app/) |

---

## 🚀 Getting Started

To explore or deploy the system, it is recommended to start with the **[Cloud Repository](https://github.com/MuoiVung/smart-cushion-cloud)** to set up the infrastructure, followed by the **[App](https://github.com/MuoiVung/smart-cushion-app)** for the interface.

## 🤝 Team Members

We are a group of engineers and designers dedicated to building the future of ergonomic sitting.

- **To Nguyen Tan Phuong** – *Hardware & Edge Engineer*  
  Hardware architecture specialist focusing on sensor integration and robust edge systems.
- **Tran Viet Nam** – *Fog & Hardware Integration*  
  Architects scalable local infrastructures and bridges the gap between hardware and high-level software.
- **Nguyen Thao Huong** – *AI Consultant & Model Designer*  
  AI expert specializing in custom model design for human posture classification.
- **Dong Boi Thi** – *Cloud & Dashboard Developer*  
  Specializes in cloud backend frameworks and real-time data processing pipelines.
- **Hoang Mai Vu** – *Real-time Dashboard Architect*  
  Architects high-performance real-time dashboards and interactive user experiences.

---

*Made with ❤️ for a healthier spine.*
