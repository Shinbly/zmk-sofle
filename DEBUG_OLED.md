# OLED Debug Info

## Findings

### Hardware
- Eyelash Sofle keyboard with SH1106 OLED (I2C)
- Display module: blue PCB, pins GND VCC SCL SDA
- Works with original firmware

### Firmware Issue
The ZMK firmware (a741725193/zmk-sofle) is configured for:
- **SPI nice!view display** (nice_view_spi: &spi0)
- SPI pins: SCK=P0.20, MOSI=P0.17, MISO=P0.25, CS=P0.6

The "version OLED" from AliExpress has an **I2C OLED**, not SPI nice!view.
This is a hardware mismatch.

### Current I2C Config
- SDA=P0.26, SCL=P0.27
- Address=0x3c
- Compatible: solomon,ssd1306fb

### Problem
The I2C OLED doesn't display anything. Possible causes:
1. Wrong I2C pins (P0.26/P0.27 may not be wired to the OLED)
2. Wrong I2C address (0x3c vs 0x3d)
3. Missing initialization

### Tried I2C Pin Combinations (ALL FAILED)
- P0.26/P0.27 - FAILED
- P0.04/P0.05 - FAILED  
- P0.25/P0.24 - FAILED
- P0.03/P0.02 - CURRENT TEST

### Important Info
- Display not working on either side
- Build compiles but no display output
- Sound crackling on computer when keyboard turns on (may be related)

### Theory
The "version OLED" from AliExpress may use a different pin assignment
than the standard Eyelash Sofle firmware. The stock firmware might
have been QMK-based with different I2C configuration.
