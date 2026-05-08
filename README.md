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

The project is divided into 5 specialized repositories, working in harmony:

### 1. [📱 Smart Cushion App (Frontend)](https://github.com/MuoiVung/smart-cushion-app)
A modern React + Vite + TailwindCSS dashboard.
- Real-time posture visualization.
- Gacha reward system & Sticker collection.
- Live session tracking and history.
- **Live Demo**: [https://smart-cushion-app.vercel.app/](https://smart-cushion-app.vercel.app/)

### 2. [☁️ Smart Cushion Cloud (Backend)](https://github.com/MuoiVung/smart-cushion-cloud)
Serverless backend built with AWS SAM (Lambda, DynamoDB, API Gateway, IoT Core).
- Manages user profiles, gem balances, and collections.
- Processes session summaries and handles WebSocket relay.

### 3. [🖥️ Smart Cushion Fog Node](https://github.com/MuoiVung/smart-cushion-fog-node)
A Python-based processing hub that runs locally.
- Receives raw sensor data from the Edge device.
- Runs the AI model for real-time classification.
- Broadcasts updates to the App via WebSockets.

### 4. [🧠 Smart Cushion AI (Models)](https://github.com/MuoiVung/smart-cushion-AI)
The "brain" of the project.
- Contains the training scripts and datasets for posture classification.
- Hosts the trained models (HDF5/PKL) used by the Fog Node.

### 5. [🔌 Smart Cushion Edge (Firmware)](https://github.com/MuoiVung/smart-cushion-edge)
The hardware interface (ESP32).
- Collects data from the 3x3 pressure sensor matrix.
- Transmits raw data to the Fog Node via UDP/WebSocket for low-latency processing.

---

## 🚀 Getting Started

To explore or deploy the system, it is recommended to start with the **[Cloud Repository](https://github.com/MuoiVung/smart-cushion-cloud)** to set up the infrastructure, followed by the **[App](https://github.com/MuoiVung/smart-cushion-app)** for the interface.

## 🤝 Authors
- **Vincent Nam Tran** - Lead Developer & AI Engineer

---

*Made with ❤️ for a healthier spine.*
