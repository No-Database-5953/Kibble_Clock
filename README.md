## Dogs Fed Indicator with Arduino Uno R4

### Overview
This sketch turns an Arduino Uno R4, a push-button, two LEDs, and a 16×2 I²C LCD into a “Dogs Fed” indicator.  
- Press the button to toggle between **Fed** and **Not Fed** states.  
- The green LED lights when **Fed**; the red LED lights when **Not Fed**.  
- A timestamp on the LCD shows the last toggle time, synchronized via NTP (Central Time with DST).  
- After **10 hours**, the status automatically resets to **Not Fed**.

### Features
- **WiFi + NTP** time sync every hour  
- **Central Time** conversion with DST rules  
- **Debounced** button using `INPUT_PULLUP`  
- **Auto-reset** after configurable interval (default 10 hours)  
- **I²C LCD** display, 16×2 characters

### Hardware Requirements
- **Arduino Uno R4** (ensure compatibility with libraries)  
- Push-button (to ground, with internal pull-up)  
- 2 × 5 mm LEDs (green & red)  
- 2 × 220 Ω resistors (for LEDs)  
- 1 × 16×2 I²C LCD module  
- Breadboard & jumper wires  
- USB cable for power & programming

#### Pin Connections

| Arduino Pin | Device               | Notes                             |
|-------------|----------------------|-----------------------------------|
| D2          | Button → GND         | `INPUT_PULLUP`                    |
| D3          | Green LED → 220 Ω → GND | Lights when **Fed**               |
| D4          | Red LED → 220 Ω → GND   | Lights when **Not Fed**           |
| SDA, SCL    | LCD SDA, SCL         | I²C bus (e.g. A4/A5 on AVRs)      |

### Software Requirements
- **Arduino IDE** ≥ 1.8.x  
- **Libraries**:  
  - `WiFi.h` (built-in)
  - `LiquidCrystal_I2C` by Frank de Brabander (modified to support `architecture=*`)
  - `NTPClient`  
  - `Timezone`

> **Library Note:** In `LiquidCrystal_I2C/library.properties`, change  
> ```ini
> architecture=avr
> ```  
> to  
> ```ini
> architecture=*
> ```  
> so it compiles for the Renesas-based Uno R4.

### Installation & Configuration

1. **Clone** or **download** this repository.  
2. **Install** required libraries via **Library Manager** (`Sketch → Include Library → Manage Libraries…`).  
3. **Modify** `ssid` and `password` in the sketch:  
   ```cpp
   const char* ssid     = "your_SSID";
   const char* password = "your_PASSWORD";
   ```  
4. (Optional) Adjust `resetInterval` in milliseconds (default 10 h):  
   ```cpp
   const unsigned long resetInterval = 10UL * 60UL * 60UL * 1000UL;
   ```

### Usage

1. Upload the sketch to your **Arduino Uno R4**.  
2. Open the **Serial Monitor** at 115200 baud to watch WiFi/NTP status.  
3. After **WiFi connects**, the LCD will display:  
   ```
   Status: Not Fed
   Time:   HH:MM:SS
   ```  
4. **Press** the button to set **Fed**:  
   - Green LED on, red off  
   - LCD shows “Status: Fed” and timestamp  
5. After **10 hours**, the indicator auto-resets to **Not Fed**.

### Timezone & DST

Uses the [Timezone library] with rules for US Central:  
```cpp
TimeChangeRule usCDT = {"CDT", Second, Sun, Mar, 2, -300};
TimeChangeRule usCST = {"CST", First,  Sun, Nov, 2, -360};
Timezone      usCentral(usCDT, usCST);
```  
Local time is derived via `usCentral.toLocal( timeClient.getEpochTime() )`.

### Troubleshooting

- **LCD won’t initialize**: Check I²C address (`0x27` default) and wiring.  
- **WiFi fails**: Confirm SSID/password and network availability.  
- **Button bounce**: If toggles multiple times, add software debounce or a small capacitor.

### License

This project is licensed under the Creative Commons Zero v1.0 Universal. See [LICENSE](LICENSE) for details.

Enjoy your reliably timed Dog Fed indicator! 😊
