# Offline Intrusion Early-Warning System

An offline-first intrusion detection and early-warning system designed for remote and isolated zones such as borders, defense outposts, and restricted infrastructure.

## Problem
Conventional intrusion systems generate frequent false alerts due to animals, wind, and environmental noise, leading to alert fatigue and loss of trust. Most systems also depend on continuous internet connectivity, which is unreliable in remote areas.

## Solution
This project implements a confidence-based, multi-sensor intrusion detection system that:
- Works fully offline
- Uses local processing on ESP32
- Observes activity over time instead of reacting instantly
- Generates alerts only when confidence is high

## Key Features
- Multi-sensor fusion (PIR motion + vibration)
- Confidence-based alerting logic
- Offline-first architecture (no cloud dependency)
- Reduced false positives
- Modular and scalable deployment

## Tech Stack
- ESP32
- PIR Motion Sensor
- Vibration Sensor
- Optional: LoRa / GSM for alerts
- Embedded C / Arduino

## Status
Prototype / Simulation stage

## Team – Team KicKoFF
- Parshv Jain – System Design & Integration
- Tushar – AI/ML (Confidence Logic)
- Nivranj – Hardware & IoT
- Disha – Presentation & Documentation

