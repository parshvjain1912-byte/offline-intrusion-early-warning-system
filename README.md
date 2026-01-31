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
- ESP32 CODE--

- #include <HardwareSerial.h>

HardwareSerial gsm(1);

int confidence = 0;
unsigned long lastDecay = 0;

void setup() {
  Serial.begin(9600);

  pinMode(PIR_PIN, INPUT);

  pinMode(LDR1_PIN, INPUT);
  pinMode(LDR2_PIN, INPUT);
  pinMode(LDR3_PIN, INPUT);
  pinMode(LDR4_PIN, INPUT);

  pinMode(BUZZER_PIN, OUTPUT);
  pinMode(STATUS_LED, OUTPUT);

  digitalWrite(BUZZER_PIN, LOW);

  // GSM init
  gsm.begin(9600, SERIAL_8N1, GSM_RX, GSM_TX);

  Serial.println("System Initialized (Offline Mode)");
}

void loop() {
  int pir = digitalRead(PIR_PIN);

  int ldr1 = analogRead(LDR1_PIN);
  int ldr2 = analogRead(LDR2_PIN);
  int ldr3 = analogRead(LDR3_PIN);
  int ldr4 = analogRead(LDR4_PIN);

  // -------- CONFIDENCE LOGIC --------
  if (pir == HIGH) {
    confidence += 3;  // Human heat = strong signal
  }

  int lightChangeCount = 0;

  if (ldr1 < 1500) lightChangeCount++;
  if (ldr2 < 1500) lightChangeCount++;
  if (ldr3 < 1500) lightChangeCount++;
  if (ldr4 < 1500) lightChangeCount++;

  if (lightChangeCount >= 2) {
    confidence += 2;  // Multiple LDRs confirm movement
  }

  // -------- CONFIDENCE DECAY --------
  if (millis() - lastDecay > 3000) {
    if (confidence > 0) confidence--;
    lastDecay = millis();
  }

  Serial.print("Confidence: ");
  Serial.println(confidence);

  // -------- ALERT DECISION --------
  if (confidence >= 6) {
    triggerAlert();
  } else {
    digitalWrite(BUZZER_PIN, LOW);
  }

  delay(1000);
}

void triggerAlert() {
  digitalWrite(BUZZER_PIN, HIGH);

  Serial.println("ALERT: Intrusion Detected");

  // GSM Alert (optional, minimal)
  gsm.println("AT");
  delay(500);
  gsm.println("AT+CMGF=1");
  delay(500);
  gsm.println("AT+CMGS=\"+91XXXXXXXXXX\"");
  delay(500);
  gsm.println("ALERT: Intrusion detected at monitored zone");
  gsm.write(26);  // CTRL+Z
}
