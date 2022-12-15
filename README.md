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
I started by following this helpful tutorial by Adafruit. Unfortunately I ran into a couple issues along the way, but the guide helped me through those as well. it's also a bit more general of a guide than just for the Airlift Breakout Board, so here is a whittled down version:

1. 
