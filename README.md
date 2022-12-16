# ESP32_WiFi_Bring-Up
My step-by-step tutorial for flashing the latest firmware onto the Espressif [ESP32](https://www.espressif.com/en/products/socs/esp32) WiFi and Bluetooth chip

## Introduction
For the final project of my Stanford grad class EE256 "Board Level Design", we had to design, assemble, and bring-up a sub-system module for an "Uber Radio", which is basically just a radio with a rediculous amount of functionality. The module my partner Kevin and I chose to reproduce was the WiFi module, enabling the radio to stream songs from the internet and update local time and weather.

Our starting place was the Adafruit [Airlift Breakout Board](https://www.adafruit.com/product/4201), which enables a microcontroller, like the [Feather M4 Express](https://www.adafruit.com/product/3857), with intetnet-acessing cabalibities. The Airlft interfaces with the Espressif [ESP32-WROOM-32](https://www.adafruit.com/product/3320) module, which contains the actually microprocessor, flash memory, antenna, etc. For this project we reproduced bith the circuitry of the Airlift and the ESP32-WROOM-32 module, essentially flattening it all out onto one board, including our own copper trace meandering inverted-F antenna. Our Github repository for this project can be found [here](https://github.com/EE156/kmarx-kmarx-kmarx-joaquin-castillo).

Because we did not use the off-the-shelf module from Espressif, we bought an ESP32 chip straight from the factory, with no firmware loaded onto it. The process for flashing this was not trivial, but not too complicated either. I will share my experience to hopefully expedite yours in the future!

## What You'll Need
- Arduino compatable microcontroller
- Jumper wires
- USB cable for the microcontroller
- And your computer!

## Guide
I started by following this helpful [tutorial](https://learn.adafruit.com/upgrading-esp32-firmware/upgrade-external-esp32-airlift-firmware-2) by Adafruit. Unfortunately, I ran into a couple issues along the way, but the guide helped me through those as well. It's also a bit more general of a guide than just for the Airlift Breakout Board, so here is a whittled down version:

### Summary
- Download and install the Arduino IDE
- Install approriate board cores
- Program the Feather with the following code
- Make the following electrical connections
- Download the NINA firmware
- Install esptool.py
- Flash firmware with esptool.py on command line

### Download and install the Arduino IDE
In order to configure the Feather as a USB-serial bridge, you need to program it to do so. A simple way to do this s through the Arduino IDE. Download the latest verion of the IDE [here](https://www.arduino.cc/en/software). Launch the installer and follow its prompts to complete installation.

### Install approriate board cores
Next you must include the proper board cores in order for the Feather to be recognized by the IDE. Follow the steps on [this page](https://learn.adafruit.com/adafruit-feather-m4-express-atsamd51/setup) and the [page after](https://learn.adafruit.com/adafruit-feather-m4-express-atsamd51/using-with-arduino-ide) until you've installed the SAMD boards in the Board Manager.

### Program the Feather with the following code
Now when you plug the Feather into you computer you should see it pop up in the Port menu (Tools > Port:). Copy and paste the following code into the IDE and upload.

```
// SPDX-FileCopyrightText: 2018 Arduino SA 
//
// SPDX-License-Identifier: LGPL-2.1-or-later
/*
  SerialNINAPassthrough - Use esptool to flash the ESP32 module
  For use with PyPortal, Metro M4 WiFi...

  Copyright (c) 2018 Arduino SA. All rights reserved.

  This library is free software; you can redistribute it and/or
  modify it under the terms of the GNU Lesser General Public
  License as published by the Free Software Foundation; either
  version 2.1 of the License, or (at your option) any later version.

  This library is distributed in the hope that it will be useful,
  but WITHOUT ANY WARRANTY; without even the implied warranty of
  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
  Lesser General Public License for more details.

  You should have received a copy of the GNU Lesser General Public
  License along with this library; if not, write to the Free Software
  Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA
*/

#include <Adafruit_NeoPixel.h>

unsigned long baud = 115200;

#if defined(ADAFRUIT_FEATHER_M4_EXPRESS) || \
  defined(ADAFRUIT_FEATHER_M0_EXPRESS) || \
  defined(ARDUINO_AVR_FEATHER32U4) || \
  defined(ARDUINO_NRF52840_FEATHER) || \
  defined(ADAFRUIT_ITSYBITSY_M0) || \
  defined(ADAFRUIT_ITSYBITSY_M4_EXPRESS) || \
  defined(ARDUINO_AVR_ITSYBITSY32U4_3V) || \
  defined(ARDUINO_NRF52_ITSYBITSY) || \
  defined(ARDUINO_PYGAMER_M4_EXPRESS)
  // Configure the pins used for the ESP32 connection
  #define SerialESP32   Serial1
  #define SPIWIFI       SPI  // The SPI port
  #define SPIWIFI_SS    13   // Chip select pin
  #define ESP32_RESETN  12   // Reset pin
  #define SPIWIFI_ACK   11   // a.k.a BUSY or READY pin
  #define ESP32_GPIO0   10
  #define NEOPIXEL_PIN   8
#elif defined(ARDUINO_AVR_FEATHER328P)
  #define SerialESP32   Serial1
  #define SPIWIFI       SPI  // The SPI port
  #define SPIWIFI_SS     4   // Chip select pin
  #define ESP32_RESETN   3   // Reset pin
  #define SPIWIFI_ACK    2   // a.k.a BUSY or READY pin
  #define ESP32_GPIO0   -1
  #define NEOPIXEL_PIN   8
#elif defined(TEENSYDUINO)
  #define SerialESP32   Serial1
  #define SPIWIFI       SPI  // The SPI port
  #define SPIWIFI_SS     5   // Chip select pin
  #define ESP32_RESETN   6   // Reset pin
  #define SPIWIFI_ACK    9   // a.k.a BUSY or READY pin
  #define ESP32_GPIO0   -1
  #define NEOPIXEL_PIN   8
#elif defined(ARDUINO_NRF52832_FEATHER )
  #define SerialESP32   Serial1
  #define SPIWIFI       SPI  // The SPI port
  #define SPIWIFI_SS    16  // Chip select pin
  #define ESP32_RESETN  15  // Reset pin
  #define SPIWIFI_ACK    7  // a.k.a BUSY or READY pin
  #define ESP32_GPIO0   -1
  #define NEOPIXEL_PIN   8
#elif !defined(SPIWIFI_SS)  // if the wifi definition isnt in the board variant
  // Don't change the names of these #define's! they match the variant ones
  #define SerialESP32   Serial1
  #define SPIWIFI       SPI
  #define SPIWIFI_SS    -1   // Chip select pin
  #define SPIWIFI_ACK   -1   // a.k.a BUSY or READY pin
  #define ESP32_RESETN  12   // Reset pin
  #define ESP32_GPIO0   10   // Not connected
  #define NEOPIXEL_PIN   8
#endif

#if defined(ADAFRUIT_PYPORTAL)
  #define PIN_NEOPIXEL   2
#elif defined(ADAFRUIT_METRO_M4_AIRLIFT_LITE)
  #define PIN_NEOPIXEL   40
#endif

Adafruit_NeoPixel pixel = Adafruit_NeoPixel(1, PIN_NEOPIXEL, NEO_GRB + NEO_KHZ800);

void setup() {
  Serial.begin(baud);
  pixel.begin();
  pixel.setPixelColor(0, 10, 10, 10); pixel.show();

  while (!Serial);
  pixel.setPixelColor(0, 50, 50, 50); pixel.show();

  delay(100);
  SerialESP32.begin(baud);

  pinMode(SPIWIFI_SS, OUTPUT);
  pinMode(ESP32_GPIO0, OUTPUT);
  pinMode(ESP32_RESETN, OUTPUT);
  
  // manually put the ESP32 in upload mode
  digitalWrite(ESP32_GPIO0, LOW);

  digitalWrite(ESP32_RESETN, LOW);
  delay(100);
  digitalWrite(ESP32_RESETN, HIGH);
  pixel.setPixelColor(0, 20, 20, 0); pixel.show();
  delay(100);
}

void loop() {
  while (Serial.available()) {
    pixel.setPixelColor(0, 10, 0, 0); pixel.show();
    SerialESP32.write(Serial.read());
  }

  while (SerialESP32.available()) {
    pixel.setPixelColor(0, 0, 0, 10); pixel.show();
    Serial.write(SerialESP32.read());
  }
}
```

### Make the following electrical connections
![wiring_diagram_flashing_esp32](wiring_diagram_flashing_esp32.png)
- Board Pin 12 to ESP32_ResetN
- Board Pin 10 to ESP GPIO0
- Board TX to RXI
- Board RX to TXO

Enusre you wiring matches these pin definitions and vice versa.
```
#if defined(ADAFRUIT_FEATHER_M4_EXPRESS) || \
  defined(ADAFRUIT_FEATHER_M0_EXPRESS) || \
  defined(ARDUINO_AVR_FEATHER32U4) || \
  defined(ARDUINO_NRF52840_FEATHER) || \
  defined(ADAFRUIT_ITSYBITSY_M0) || \
  defined(ADAFRUIT_ITSYBITSY_M4_EXPRESS) || \
  defined(ARDUINO_AVR_ITSYBITSY32U4_3V) || \
  defined(ARDUINO_NRF52_ITSYBITSY) || \
  defined(ARDUINO_PYGAMER_M4_EXPRESS)
  // Configure the pins used for the ESP32 connection
  #define SerialESP32   Serial1
  #define SPIWIFI       SPI  // The SPI port
  #define SPIWIFI_SS    13   // Chip select pin
  #define ESP32_RESETN  12   // Reset pin
  #define SPIWIFI_ACK   11   // a.k.a BUSY or READY pin
  #define ESP32_GPIO0   10
  #define NEOPIXEL_PIN   8
```

### Download the NINA firmware
The latest version of the firmware should be [here](https://github.com/adafruit/nina-fw/releases/tag/1.7.4). The file you want will look something like `NINA_W102-1.7.4.bin`. Download and save it into a directory that maybe isn't your Downloads forlder, but it can be if you want to I guess. That's what I did, but it seems a little lazy.

### Install esptool.py
Hop onto your favorite terminal emulator. Install esptool.py by copying and running the following command into the command line.
```
pip install esptool
```

### Flash firmware with esptool.py on command line
Once esptool is installed, navigate to the directory you saved the NINA firmware file in. Copy and run the following command into th ecommand line, replacing the COM port with the one for your Feather (you can find this in Device Manager > Ports (COM & LPT)) and the version number of the firmware file. If this runs sucessfully, congrats! You've just flashed the ESP32 chip. If not, you can find some great troubleshooting guidance at this [website](https://www.google.com/).

