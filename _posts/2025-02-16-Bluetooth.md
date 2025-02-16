---
layout: post
title: 'DIY PowerMeter#2: Bluetooth'
date: '2025-02-16'
categories:
  - PowerMeter
published: true
---
Establishing Bluetooth using Arduino Ble 33 nano

<style>

button {
    background: linear-gradient(135deg, #e02904);
    color: white;
    border: none;
    padding: 10px 20px;
    font-size: 15px;
    border-radius: 50px;
    cursor: pointer;
    transition: 0.3s;
    outline: none;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

/* Button Hover Effect */
button:hover {
    background: linear-gradient(135deg, #21a698, #21a698);
}

/* Status & Weight Display */
.status, .weight {
    font-size: 20px;
    margin-top: 20px;
}

/* Large Weight Display */
#weight {
    font-size: 48px;
    font-weight: bold;
    color: #28a745;
    background: rgba(255, 255, 255, 0.1);
    padding: 10px 20px;
    border-radius: 10px;
    display: inline-block;
    margin-top: 10px;
}

/* Device List Styling */
#deviceList {
    margin-top: 20px;
    list-style: none;
    padding: 0;
}

#deviceList li {
    padding: 10px;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 10px;
    cursor: pointer;
    transition: 0.3s;
    margin: 5px;
}

#deviceList li:hover {
    background: rgba(255, 255, 255, 0.3);
}
</style>

<div class="container">
    <h2>HX711 BLE Scanner</h2>

    <button onclick="scanBLE()">🔍 Scan for BLE Devices</button>

    <p class="status">Status: <span id="status">Not connected</span></p>

    <h3>Discovered Devices:</h3>
    <ul id="deviceList"></ul>

    <h3>Value:</h3>
    <p id="weight">0.0000</p>
</div>

<script>
    const SERVICE_UUID = "12345678-1234-5678-1234-56789abcdef0";
    const CHARACTERISTIC_UUID = "87654321-4321-6789-4321-abcdef987654";
    let bleDevice, bleServer, bleCharacteristic;

    async function scanBLE() {
        try {
            document.getElementById("status").innerText = "Scanning...";
            
            // Scan for ANY BLE device
            let device = await navigator.bluetooth.requestDevice({
                acceptAllDevices: true,  // ✅ Scan for ALL devices
                optionalServices: [SERVICE_UUID] // Try to filter for Arduino HX711
            });

            console.log("✅ Found Device:", device.name || "Unnamed Device");

            // Add device to list
            let listItem = document.createElement("li");
            listItem.textContent = device.name || "Unnamed Device";
            listItem.onclick = () => connectBLE(device);
            document.getElementById("deviceList").appendChild(listItem);

            document.getElementById("status").innerText = `Found: ${device.name || "Unnamed Device"}`;

        } catch (error) {
            console.error("BLE Error:", error);
            document.getElementById("status").innerText = "❌ Connection Failed";
        }
    }

    async function connectBLE(device) {
        try {
            document.getElementById("status").innerText = "Connecting...";
            
            bleServer = await device.gatt.connect();
            const service = await bleServer.getPrimaryService(SERVICE_UUID);
            bleCharacteristic = await service.getCharacteristic(CHARACTERISTIC_UUID);

            // Subscribe to weight updates
            bleCharacteristic.addEventListener("characteristicvaluechanged", handleData);
            await bleCharacteristic.startNotifications();
            
            document.getElementById("status").innerText = `✅ Connected to ${device.name || "Unnamed Device"}`;
            console.log("✅ Connected to BLE Device!");

        } catch (error) {
            console.error("BLE Connection Error:", error);
            document.getElementById("status").innerText = "❌ Connection Failed";
        }
    }

    function handleData(event) {
        let value = new TextDecoder().decode(event.target.value);
        document.getElementById("weight").innerText = value;
        console.log("📡 weight:", value);
    }
</script>