# Air Quality Monitor — Wiring Reference

**Hardware:** Seeed XIAO ESP32-C6 · Sensirion SEN66 · Winsen ZE08-CH2O · WeAct 2.9" e-Paper  
**Firmware:** ESPHome · esp-idf framework · board: `esp32-c6-devkitc-1`

---

## I²C Bus — `bus_a` (SEN66)

| Signal | XIAO Pin | GPIO   | SEN66 Pin | Notes                        |
|--------|----------|--------|-----------|------------------------------|
| SDA    | D4       | GPIO22 | Pin 2     | Pull-up enabled · 100 kHz    |
| SCL    | D5       | GPIO23 | Pin 3     | Pull-up enabled · 100 kHz    |
| VDD    | 3V3      | —      | Pin 1     | 3.3 V · max 75 mA            |
| GND    | GND      | —      | Pin 4     |                              |

> I²C address: `0x6B` (default) · Update interval: `30 s` · Sliding avg window: 5 samples

---

## UART Bus — `uart_ze08` (ZE08-CH2O)

| Signal      | XIAO Pin | GPIO   | ZE08 Pin | Notes                              |
|-------------|----------|--------|----------|------------------------------------|
| RX ← TX     | D7       | GPIO17 | Pin 3    | ESP32 RX ← ZE08 TX (cross-wired)  |
| TX → RX     | D6       | GPIO16 | Pin 4    | ESP32 TX → ZE08 RX (cross-wired)  |
| VCC         | 5V       | —      | Pin 1    | 5 V required · use VBUS pin        |
| GND         | GND      | —      | Pin 2    |                                    |

> Baud rate: `9600` · 8N1 · ZE08 TX output is 3.3 V compatible — no level shifter needed  
> Update interval: `20 s`

---

## SPI Bus — `bus1` (WeAct 2.9" e-Paper)

| Signal | XIAO Pin | GPIO   | e-Paper Pin | Direction     | Notes                        |
|--------|----------|--------|-------------|---------------|------------------------------|
| CLK    | D8       | GPIO19 | SCLK        | MCU → display | SPI clock                    |
| MOSI   | D10      | GPIO18 | DIN         | MCU → display | Data in                      |
| CS     | D1       | GPIO01 | CS          | MCU → display | Active low chip select       |
| DC     | D3       | GPIO21 | DC          | MCU → display | Data/Command select          |
| BUSY   | D2       | GPIO02 | BUSY        | display → MCU | High = busy, wait before TX  |
| RST    | D0       | GPIO00 | RST         | MCU → display | Active low reset             |
| VCC    | 3V3      | —      | VCC         | —             | 3.3 V                        |
| GND    | GND      | —      | GND         | —             |                              |

> Model: `waveshare_epaper 2.90inv2-r2` · 128×296 px · Rotation: 90°  
> Update interval: `60 s` · Full refresh every 10 partial updates · SPI mode 0

---

## Power Summary

| Rail  | XIAO Pin | Consumers                     | Max current |
|-------|----------|-------------------------------|-------------|
| 3.3 V | 3V3      | SEN66, e-Paper                | ~80 mA      |
| 5 V   | 5V       | ZE08-CH2O                     | ~150 mA     |
| GND   | GND      | SEN66, ZE08-CH2O, e-Paper     | shared      |

> The XIAO 3V3 pin can supply up to ~700 mA from the onboard regulator.  
> The 5V pin is VBUS (USB power) — not available when running on battery alone.

---

## GPIO Usage Summary

| GPIO   | XIAO Pin | Function     | Bus       | Connected to     |
|--------|----------|--------------|-----------|------------------|
| GPIO00 | D0       | RST          | SPI       | e-Paper RST      |
| GPIO01 | D1       | CS           | SPI       | e-Paper CS       |
| GPIO02 | D2       | BUSY (input) | SPI       | e-Paper BUSY     |
| GPIO16 | D6       | UART TX      | UART      | ZE08 RX (pin 4)  |
| GPIO17 | D7       | UART RX      | UART      | ZE08 TX (pin 3)  |
| GPIO18 | D10      | SPI MOSI     | SPI       | e-Paper DIN      |
| GPIO19 | D8       | SPI CLK      | SPI       | e-Paper SCLK     |
| GPIO20 | D9       | SPI MISO     | SPI       | —  (unused)      |
| GPIO21 | D3       | DC           | SPI       | e-Paper DC       |
| GPIO22 | D4       | I²C SDA      | I²C       | SEN66 pin 2      |
| GPIO23 | D5       | I²C SCL      | I²C       | SEN66 pin 3      |
