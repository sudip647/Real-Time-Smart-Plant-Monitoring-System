# Plant Monitoring Edge AI with Arduino UNO R4 and Raspberry Pi 4

This project monitors soil moisture, predicts watering needs, and tracks light conditions using an **Arduino UNO R4** with BLE and a **Raspberry Pi 4** as the Edge AI platform. Data is displayed on an **LCD**, logged locally, and sent to **ThingSpeak** for cloud monitoring.

---

## Features

- Real-time soil moisture monitoring (Wet, Moist, Dry)
- LED and buzzer alerts for low moisture
- Light detection (Day/Night) using digital light sensor
- Predictive "Time-to-Water" calculation using drying rate
- Local display on Grove RGB LCD
- BLE communication between Arduino and Raspberry Pi
- Cloud logging to ThingSpeak
- Python-based Edge AI for predictive analytics

---

## Hardware

| Component | Description |
|-----------|------------|
| Arduino UNO R4 | Reads soil, light, and outputs alerts via LEDs/buzzer |
| Soil Moisture Sensor | Analog input to Arduino |
| Light Barrier / LDR | Digital input for Day/Night detection |
| LEDs | Green, Yellow, Red for soil status |
| Buzzer | Alert when soil is dry |
| Grove RGB LCD | Displays real-time soil & water info |
| Raspberry Pi 4 | BLE client, predictive Edge AI, ThingSpeak integration |
| USB / Power | For Arduino and Pi |

---

## Arduino Libraries

Install in **Arduino IDE**:

- [ArduinoBLE](https://www.arduino.cc/en/Reference/ArduinoBLE)
- [rgb_lcd](https://github.com/Seeed-Studio/Grove_LCD_RGB_Backlight)
- [Wire](https://www.arduino.cc/en/reference/wire)

---

## Raspberry Pi Dependencies

```bash
sudo apt update
sudo apt install python3-pip
pip3 install bleak requests
```

- **bleak** → BLE communication with Arduino  
- **requests** → Sending data to ThingSpeak

---

## ThingSpeak Channel Setup

1. Create a new channel on [ThingSpeak](https://thingspeak.com/)  
2. Enable the following fields:

| Field | Description |
|-------|------------|
| Field1 | Soil % |
| Field2 | Soil Status (Wet=3, Moist=2, Dry=1) |
| Field3 | LED Status (Green=3, Yellow=2, Red=1) |
| Field4 | Buzzer (ON=1, OFF=0) |
| Field5 | Light Status (Day=1, Night=0) |
| Field6 | Predicted Water Time (hours) |

3. Copy your **Write API Key** for use in Python  

---

## Arduino Code

- Connect soil sensor to `A0`  
- Connect light sensor digital output to `D3`  
- LEDs to `D11` (Green), `D12` (Yellow), `D13` (Red)  
- Buzzer to `D8`  
- Grove LCD via I2C (SDA/SCL)

**Features:**
- BLE characteristic `2A58` for Raspberry Pi  
- Predictive water countdown with minimum hours  
- Corrected Day/Night detection: LOW = Day, HIGH = Night

---

## Python (Raspberry Pi) Code

- Scans and connects to `PlantMonitor_R4` via BLE  
- Reads soil %, status, LED, buzzer, light, and water prediction  
- Logs data locally (`ble_plant_log.txt`)  
- Sends numeric values to ThingSpeak fields every 15 seconds

**Mapping for ThingSpeak fields:**

```python
status_map = {"Wet":3, "Moist":2, "Dry":1}
led_map = {"GREEN":3, "YELLOW":2, "RED":1}
buzzer_map = {"ON":1, "OFF":0}
light_map = {"Day":1, "Night":0}
```

---

## Usage

1. Upload the Arduino sketch to **UNO R4**  
2. Connect all sensors, LEDs, buzzer, and LCD as per pin configuration  
3. Power on the Arduino  
4. On Raspberry Pi, run:

```bash
python3 soil.py
```

5. Terminal will show live readings:

```
04:44:12 | Soil:49% | Moist | LED:YELLOW | Buzzer:OFF | Light:Day | Water: 2.0h
```

6. ThingSpeak channel will receive numeric data for all 6 fields  
7. Use ThingSpeak **Private View** or **Charts** to visualize soil status, light, buzzer alerts, and predictive water time

---

## Notes

- BLE range depends on environment, keep Arduino and Pi within 5–10 meters  
- ThingSpeak free API allows **1 update every 15 seconds**  
- Grove RGB LCD requires `rgb_lcd` library installed  
- Prediction is for demo; drying rates can be tuned for real plants

---

## Files

- `plant_monitor.ino` → Arduino code  
- `soil.py` → Raspberry Pi BLE & ThingSpeak integration  
- `ble_plant_log.txt` → Log file generated on Raspberry Pi

---

## References

- [ArduinoBLE Library](https://www.arduino.cc/en/Reference/ArduinoBLE)  
- [Grove LCD RGB Backlight](https://github.com/Seeed-Studio/Grove_LCD_RGB_Backlight)  
- [Bleak Python Library](https://github.com/hbldh/bleak)  
- [ThingSpeak API](https://thingspeak.com/docs/channels/)

