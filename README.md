# motor-predictive-maintenance
IoT and machine learning system for predictive maintenance of electric motors — real-time sensor monitoring, Random Forest fault prediction, and live dashboard, fully offline.
# IoT Predictive Maintenance System for Electric Motors

A complete IoT and Machine Learning pipeline that predicts electric motor failures in real time, combining low-cost sensors, an ESP8266 microcontroller, a local TCP server, a Random Forest classification model, and both a desktop dashboard and a mobile application — all running fully offline, without any cloud dependency.

## Overview

Unplanned motor failures cause costly production downtime, safety risks, and reduced industrial efficiency. Existing commercial solutions (Siemens SIMOTICS CONNECT, Schneider Electric EcoStruxure) solve this problem but are expensive, require specialized installation, and depend on cloud platforms.

This project implements a **local, low-cost alternative**: a network of IoT sensors continuously measures key motor parameters, an ESP8266 microcontroller streams the data over Wi-Fi, and a Random Forest model classifies the motor's state in real time — all processed on a local machine with no external dependency, ensuring low latency and full data ownership.

## Key Features

- **Real-time multi-sensor acquisition** — temperature, current, voltage, and rotation speed
- **Wi-Fi data streaming** via an ESP8266 microcontroller using TCP/IP
- **Random Forest classification model** (99.2% accuracy) predicting motor state: `normal`, `degradation`, or `failure`
- **Local desktop dashboard** built with CustomTkinter and Matplotlib, featuring:
  - Live multi-parameter graphs (temperature, current, voltage, rotation)
  - Real-time system status view with color-coded alerts
  - An interactive chatbot assistant for querying motor state via simple commands
- **Companion Android application** (MIT App Inventor) for remote monitoring, displaying live sensor values, connection status, and per-parameter alerts
- **Fully offline architecture** — no cloud, no external API dependency, low latency, full data privacy

## System Architecture

```
Sensors (Temperature, Current, Voltage, Rotation)
        │
        ▼
ESP8266 (Wi-Fi, TCP/IP client)
        │
        ▼
Local Python TCP Server (0.0.0.0:5005)
        │
        ▼
Random Forest ML Model  →  Prediction (normal / degradation / failure)
        │
        ▼
Desktop Dashboard (CustomTkinter + Matplotlib)  +  Mobile App (Android)
```

The system follows a four-layer IoT architecture:

| Layer | Role |
|---|---|
| **Perception** | Sensors measure temperature, current, voltage, and rotation speed |
| **Communication** | ESP8266 streams data over a local Wi-Fi TCP/IP connection |
| **Processing & Intelligence** | Random Forest model classifies motor state in real time |
| **Visualization** | Desktop dashboard and mobile app display live data and alerts |

## Hardware

| Sensor | Connection |
|---|---|
| Temperature (DS18B20) | VCC → 3.3V · GND → GND · DATA → GPIO 4 (D2) |
| Current (ACS712) | VCC → 5V · GND → GND · OUT → A0 |
| Voltage | Voltage divider → A0 |
| Rotation (Hall sensor) | VCC → 5V · GND → GND · Signal → GPIO 5 (D1) |

All sensors connect to an **ESP8266** microcontroller, which transmits readings over the local Wi-Fi network.

## Machine Learning Model

- **Algorithm:** Random Forest Classifier
- **Configuration:** `n_estimators=300`, `max_depth=10`, `random_state=42`
- **Dataset:** 200 labeled samples (Kaggle) with temperature, current, voltage, and rotation features, labeled as `normal`, `degradation`, or `failure`
- **Performance:**

  | Metric | Score |
  |---|---|
  | Accuracy | 99.2% |
  | Precision (weighted avg) | 0.99 |
  | Recall (weighted avg) | 0.99 |
  | F1-score (weighted avg) | 0.99 |

- **Why Random Forest:** chosen for its balance between predictive accuracy and computational lightness, making it suitable for real-time inference on standard hardware without GPU acceleration.

## Tech Stack

**Embedded:** ESP8266, Arduino C++, OneWire / DallasTemperature libraries
**Backend:** Python 3.12+, `socket` (TCP server), threading
**Machine Learning:** scikit-learn (Random Forest), pandas, joblib
**Desktop Interface:** CustomTkinter, Matplotlib
**Mobile App:** MIT App Inventor (Android)
**Communication Protocol:** TCP/IP (local network, no cloud)

## Project Structure

```
predictive-maintenance/
├── server.py                  # TCP server + ML inference + dashboard launcher
├── model_predictive_maintenance.pkl   # Trained Random Forest model
├── data/                      # Training dataset (Kaggle, 200 samples)
├── esp8266/                   # Arduino sketch for sensor acquisition & TCP client
├── app/                       # MIT App Inventor mobile application
└── requirements.txt
```

## Getting Started

### Requirements

- Python 3.12+
- ESP8266 (or compatible Wi-Fi microcontroller)
- Arduino IDE or PlatformIO
- Python packages: `pandas`, `scikit-learn`, `matplotlib`, `customtkinter`, `joblib`

### Installation

```bash
# Clone the repository
git clone https://github.com/hinda22/predictive-maintenance.git
cd predictive-maintenance

# Install Python dependencies
pip install -r requirements.txt

# Upload the ESP8266 sketch (esp8266/sketch.ino) via Arduino IDE or PlatformIO

# Run the local server and dashboard
python server.py
```

### How It Works

1. Sensors continuously measure temperature, current, voltage, and rotation speed.
2. The ESP8266 streams the readings to the local server over a persistent TCP connection (format: `temperature;current;voltage;rotation`).
3. The Python TCP server receives, parses, and stores the latest readings.
4. The Random Forest model classifies the current motor state in real time.
5. The desktop dashboard and mobile app display live measurements, trend graphs, and maintenance alerts.

## Future Improvements

- Automated email/SMS alerts for critical motor states
- On-device inference (TinyML) directly on the ESP8266
- Additional sensors (e.g. vibration) for richer fault detection
- Time-to-failure prediction rather than binary/ternary classification
- Push notifications and remote motor shutdown via the mobile app
- Historical data logging and long-term trend analysis

## Author

**Hind Saada**
Mastère Professionnel EEA2 — Embedded Systems & IoT
Faculty of Sciences of Bizerte, Université de Carthage
[github.com/hinda22](https://github.com/hinda22)

*Developed as a group project for the EEA2 Master's program, in collaboration with two classmates. This repository reflects my own implementation and contributions to the system.*

