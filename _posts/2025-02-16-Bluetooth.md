---
layout: post
title: 'DIY PowerMeter#2: Bluetooth'
date: '2025-02-16'
categories:
  - PowerMeter
published: true
homepage: false
---
### Establishing Bluetooth using Arduino Ble 33 Nano.

Prototyping bluetooth code was devoleped as an webapp for ease of code and access. Webpage is linked below. After uploading code#A2 to the Arduino, and rebooting it, it should pop up under "Arduino".

This is the first step of introducing mobility to the PowerMeter. Adding a battery would be the next, however I'm currently missing some modules that are required for charging and discharging Li-po batteries.

Lithium Polymer batteries are particularly delicate and need a a CC/CV chip to prevent overcharging (above 4.2V) and to supply a constant current.

I'm using a 500maH lipo with a TP4056 module which would limit current to 200mA.

Since Li-po batteries also do not supply voltage at a constant rate while discharging, a buck boost converter is needed. I've ordered a couple XL63802 modules off taobao.

The efficacy will be revealed next post.

_Note this website only supports browsers with Blueooth Webkit support: Chrome or Edge. On an iPad/iPhone I reccomend using the custom browser "Bluefy". It's free._

[Web Graph](https://webble-8a6e1a.gitlab.io/)

[A2#Arduino Code for BLE advertising](https://webble-8a6e1a.gitlab.io/)

```python
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
