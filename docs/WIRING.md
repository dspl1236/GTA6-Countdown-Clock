# Wiring & Troubleshooting

## Parts

- **ESP32 WROOM-32** dev board (30 or 38-pin). Any common variant works. Must be the 2.4 GHz WROOM — the sketch uses WiFi.
- **2.42" OLED display, SSD1309 driver, 128×64, SPI (7-pin).** These are sold as "2.42 inch OLED SSD1309" and usually have a 7-pin header. Make sure it's in **SPI** mode (some modules have solder jumpers to select SPI vs I²C — SPI is the default on the 7-pin ones).

## Pinout

```
   OLED (SSD1309, SPI)            ESP32 WROOM-32
  ┌───────────────────┐
  │ GND ──────────────┼──────────► GND
  │ VCC ──────────────┼──────────► 3V3
  │ SCK / CLK / D0 ───┼──────────► GPIO 18  (VSPI SCK)
  │ SDA / MOSI / D1 ──┼──────────► GPIO 23  (VSPI MOSI)
  │ RES / RST ────────┼──────────► GPIO 4
  │ DC ───────────────┼──────────► GPIO 15
  │ CS ───────────────┼──────────► GPIO 5
  └───────────────────┘
```

| OLED label (varies by board) | Function | ESP32 |
|------------------------------|----------|-------|
| GND               | Ground             | GND  |
| VCC / VDD         | Power (3.3 V)      | 3V3  |
| SCK / CLK / SCL / D0 | SPI clock       | GPIO 18 |
| SDA / MOSI / DIN / D1 | SPI data       | GPIO 23 |
| RES / RST         | Reset              | GPIO 4  |
| DC / A0           | Data/Command       | GPIO 15 |
| CS                | Chip select        | GPIO 5  |

> Pin **labels** differ between vendors. Match by **function**, not by the silkscreen letter. On these 7-pin OLEDs the order is commonly: `GND, VCC, SCK, SDA, RES, DC, CS`.

### Why these GPIOs?

The sketch uses the ESP32's **hardware VSPI** peripheral, so **SCK is fixed at GPIO 18** and **MOSI at GPIO 23**. Those two can't be moved without switching SPI peripherals. **CS (5), DC (15), RST (4)** are just regular GPIOs and can be changed at the top of the sketch:

```cpp
#define PIN_CS    5
#define PIN_DC   15
#define PIN_RST   4
```

Avoid the ESP32's input-only pins (34–39) and the strapping pins (0, 2, 12, 15) where you can — GPIO 15 is used for DC here and works fine, but if you repurpose pins, keep that in mind.

## Power

- For development, USB into the dev board is plenty.
- The 3V3 pin on the dev board powers the OLED fine (these draw ~10–25 mA).
- For battery use, see the **Power & battery** section in the main README.

## Troubleshooting

**Screen stays blank / black:**
- Double-check GND and VCC first.
- Verify the module is in **SPI** mode (check solder jumpers).
- Confirm CS/DC/RST match the defines; a swapped DC/CS is the most common cause.
- Make sure you selected the **SSD1309** constructor (it's already set in the sketch). An SSD1306 constructor won't drive this panel correctly.

**Garbled / shifted / partial image:**
- Usually SPI wiring — reseat SCK (18) and MOSI (23).
- Try lowering `SPI_CLOCK` from `8000000` to `4000000` if you have long/messy jumper wires.

**Clock shows frozen fallback numbers (e.g. `143 0 0 0`) and never updates:**
- It never got the time. Open Serial Monitor at 115200 and reset:
  - `WiFi FAILED` → wrong SSID/password, or you're on a **5 GHz** network. The WROOM-32 is **2.4 GHz only**. Make sure your 2.4 GHz band is reachable (some routers hide it under the same name — that can still work, but a 5 GHz-only SSID will not).
  - `WiFi OK` then `NTP FAILED` → firewall/DNS blocking NTP, or a slow connection. It will retry.

**Time is off by an hour:**
- Daylight saving. Adjust `GMT_OFFSET_SEC` / `DAYLIGHT_OFFSET` for your zone and the current DST state.

**Wrong day count:**
- Check `GTA6_RELEASE_UTC` and your `GMT_OFFSET_SEC`. The target epoch is in UTC; the display applies your offset.

**Colors/brightness:**
- These are monochrome panels (single color, usually blue-white or yellow). `CONTRAST` controls brightness (0–255), not color.
