# 🚗 Arduino Distance Monitoring - LED & Buzzer Indicator

<p>Projekt edukacyjno-demonstracyjny pokazujący, jak połączyć elektronikę, Arduino, Node-RED, MQTT, InfluxDB i Grafanę w jeden kompletny system monitorowania odległości. Układ działa jak miniaturowy system SCADA: mierzy odległość czujnikiem ultradźwiękowym, steruje diodami LED oraz buzzerem, a następnie wysyła dane w formacie JSON do dalszej wizualizacji.</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/aa4ad5e0-3580-4082-85e6-ca1586369436" alt="Schemat układu Arduino" width="500"/>
</p>

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Hardware](#hardware)
- [Libraries](#libraries)
- [Program logic](#program-logic)
- [Installation](#installation)
- [Usage](#usage)
- [Media](#media)


---

## Overview

<p>
Projekt powstał w celach edukacyjnych i hobbystycznych. Łączy on fizyczny układ pomiarowy z pełnym torem przetwarzania danych: Arduino zbiera pomiary, Node-RED je odbiera i rozdziela, MQTT publikuje je jako telemetrię, InfluxDB zapisuje jako dane historyczne, a Grafana wizualizuje je w czasie rzeczywistym.  
Czujnik ultradźwiękowy reaguje na zmianę odległości obiektu (np. modelu bolidu F1), a system LEDów i buzzer pełnią funkcję sygnalizacyjną.
</p>

---

## Features

### 🟢 Feature 1 - Dynamiczne sterowanie LED i buzzerem
<p>System sygnalizuje odległość obiektu za pomocą trzech stref: zielonej, pomarańczowej i czerwonej. W strefie krytycznej buzzer uruchamia alarm.</p>

### 📡 Feature 2 - Przesył danych JSON → Node-RED → MQTT → InfluxDB → Grafana
<p>Arduino generuje ramkę JSON zawierającą odległość oraz stany LEDów i buzzera. Ramka jest parsowana w Node-RED i zapisywana w InfluxDB oraz wysyłana przez MQTT.</p>

---

## Hardware

- Arduino UNO R3  
- Czujnik ultradźwiękowy HC-SR04  
- 6× dioda LED (2× zielone, 2× pomarańczowe, 2× czerwone)  
- Buzzer aktywny  
- Rezystory 220 Ω  
- Płytka stykowa  
- Mosquitto MQTT Broker (software)  
- InfluxDB 2.0  
- Grafana  

---
## Instalation
<p>Instructions to follow when first using the code</p>

- Pobierz plik .ino i otwórz w Arduino IDE

- Wybierz płytkę Arduino UNO

- Wybierz port COM Twojego Arduino

- Kliknij Upload

- Uruchom Node-RED

- Skonfiguruj kolejno:
- Serial In
- JSON Parser
- Function nodes (MQTT + InfluxDB)
- MQTT Out
- InfluxDB Out

- Uruchom Mosquitto MQTT

- W Grafanie dodaj InfluxDB jako Data Source

- Otwórz dashboard i obserwuj pomiary
## Libraries

### Arduino:
- `<ArduinoJson.h>`  
- Standardowe biblioteki Arduino (`pulseIn`, `digitalWrite`, `pinMode`)

### Node-RED:
- node-red-node-serialport  
- node-red-contrib-influxdb  
- węzły MQTT  

---

## Program logic

<p>Arduino mierzy czas powrotu impulsu ultradźwiękowego, przelicza go na odległość i w zależności od wartości aktywuje odpowiednie LEDy oraz buzzer. Następnie generuje ramkę JSON do wysłania przez port szeregowy.</p>

### Pomiar odległości

```cpp
digitalWrite(TRIG_PIN, LOW);
delayMicroseconds(2);
digitalWrite(TRIG_PIN, HIGH);
delayMicroseconds(10);
digitalWrite(TRIG_PIN, LOW);

long duration = pulseIn(ECHO_PIN, HIGH);
float distance = (duration * 0.0343) / 2.0;
```
## Logika stref 

```cpp
if (distance > 20) {
  greenOn();
  orangeOff();
  redOff();
  buzzerOff();
} else if (distance > 10) {
  greenOff();
  orangeOn();
  redOff();
  buzzerOn();
} else {
  greenOff();
  orangeOff();
  redBlink();
  buzzerOn();
}
```
## Tworzenie JSON

```cpp
StaticJsonDocument<200> doc;

doc["distance"] = distance;
doc["led_green1"] = digitalRead(G1);
doc["led_green2"] = digitalRead(G2);
doc["led_orange1"] = digitalRead(O1);
doc["led_orange2"] = digitalRead(O2);
doc["led_red1"] = digitalRead(R1);
doc["led_red2"] = digitalRead(R2);
doc["buzzer"] = buzzerState;

serializeJson(doc, Serial);
Serial.println();
```


## Media
<p align="center">
<img width="660" height="800" alt="project" src="https://github.com/user-attachments/assets/eeed2c7a-10b3-4b96-934f-bae14dbbbeba" />
<img width="661" height="654" alt="project_yellow_led" src="https://github.com/user-attachments/assets/2249203c-4416-487e-b3ec-b9954e4f5bd6" />
<img width="669" height="760" alt="redled" src="https://github.com/user-attachments/assets/9230f90e-af74-4cba-8c3b-36d5bf576385" />
<img width="1642" height="784" alt="node-red" src="https://github.com/user-attachments/assets/8eae242e-957b-4b98-8c86-87a6ba5ca321" />
<img width="1859" height="842" alt="Grafana" src="https://github.com/user-attachments/assets/049ed83c-5460-41bc-a9d4-537fd1aea005" />
















