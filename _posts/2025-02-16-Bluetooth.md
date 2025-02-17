---
layout: post
title: 'DIY PowerMeter#2: Bluetooth'
date: '2025-02-16'
categories:
  - PowerMeter
published: true
homepage: false
---
<h3>Establishing Bluetooth using Arduino Ble 33 nano.</h3>\
Prototyping bluetooth code was devoleped as an webapp for ease of code and access. Webpage is linked below. After uploading code#A2 to arduino, and rebooting it, it should pop up under "Arduino".\\
Note this website only supports browsers supported Blueooth Webkit: Chrome or Edge. On an iphone I reccomend using the custom browser "Bluefy". It's free.

<a href="https://webble-8a6e1a.gitlab.io/" class="webble">Web Graph

**A2#Arduino Code for BLE advertising**
```javascript
#include <ArduinoBLE.h>
#include "HX711.h"

#define DOUT_PIN A4  // HX711 Data output pin
#define SCK_PIN A5   // HX711 Serial clock pin

HX711 scale;

BLEService hx711Service("12345678-1234-5678-1234-56789abcdef0");  // Custom BLE service UUID
BLEStringCharacteristic hx711Characteristic("87654321-4321-6789-4321-abcdef987654", 
                                            BLERead | BLENotify, 16);  // Max 16 characters

// Parameters for auto-taring
const int TARE_WINDOW = 5 * 85;  // 5 seconds * 85 samples/sec
const float DEVIATION_THRESHOLD = 0.002;  // If deviation stays below this, tare

float readings[TARE_WINDOW] = {0};  // Store last 5 sec of data
int tareIndex = 0;  // Fix variable name conflict
bool tareScheduled = false;

void setup() {
    Serial.begin(115200);
    while (!Serial);

    if (!BLE.begin()) {
        Serial.println("Starting BLE failed!");
        while (1);
    }

    scale.begin(DOUT_PIN, SCK_PIN);
    scale.set_scale();  // Ensure calibration separately
    scale.tare();       // Set initial value to 0.0000

    BLE.setLocalName("Arduino");  // Shows up as "Arduino" in BLE scanner
    BLE.setAdvertisedService(hx711Service);
    hx711Service.addCharacteristic(hx711Characteristic);
    BLE.addService(hx711Service);

    BLE.advertise();
    Serial.println("BLE ready, advertising...");
}

void loop() {
    BLEDevice central = BLE.central();
    
    if (central) {
        Serial.print("Connected to: ");
        Serial.println(central.address());

        while (central.connected()) {
            float rawValue = scale.get_units();  // Get raw weight reading
            float formattedValue = rawValue * 0.0001;  // Higher resolution

            // Store reading for deviation check
            readings[tareIndex] = formattedValue;
            tareIndex = (tareIndex + 1) % TARE_WINDOW;

            // Calculate deviation over last 5 seconds
            if (tareIndex == 0) {
                float sum = 0, sumSq = 0;
                for (int i = 0; i < TARE_WINDOW; i++) {
                    sum += readings[i];
                    sumSq += readings[i] * readings[i];
                }
                float mean = sum / TARE_WINDOW;
                float variance = (sumSq / TARE_WINDOW) - (mean * mean);
                float stddev = sqrt(variance);

                // If readings are stable (low deviation), tare
                if (stddev < DEVIATION_THRESHOLD) {
                    Serial.println("♻️ Auto-taring due to low deviation...");
                    scale.tare();
                    formattedValue = 0.0000;  // Reset display to 0
                }
            }

            // Convert to string and send over BLE
            String message = String(formattedValue, 4);  // 4 decimal places
            hx711Characteristic.writeValue(message);  // Send over BLE
            Serial.println(message);  // Debugging output

            delay(12);  // Maintain 85 samples per second
        }

        Serial.println("Disconnected.");
    }
}

```

<style>
 .webble {
    display: inline-block;
    padding: 7px 14px;
    font-size: 15px;
    color: white;
    background-color: #007bff;
    text-decoration: none;
    border-radius: 5px;
    transition: background-color 0.3s ease;
}

  .webble:hover {
    background-color: #0056b3;
} 
</style>