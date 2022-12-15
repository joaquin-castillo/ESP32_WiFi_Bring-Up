# ESP32_WiFi_Bring-Up
My step-by-step tutorial for flashing the latest firmware onto the Espressif ESP32 WiFi and Bluetooth chip

## Introduction
For the final project of my Stanford Grad class EE256 "Board Level Design", we had to design, assemble, and bring-up a sub-system module for an "Uber Radio", which is basically just a radio with a rediculous amount of functionality. The module my partner Kevin and I chose to reproduce was the WiFi module, enabling the radio to stream songs from the internet and update local time and weather.
Our starting place was the Adafruit Airlift Breakout Board, which enables a microcontroller, like the Feather Express M4, with intetnet-acessing cabalibities. The Airlft interfaces with the Espressif ESP32-WROOM-32 module, which contains the actually microprocessor, flash memory, antenna, etc. For this project we reproduced bith the circuitry of the Airlift and the ESP32-WROOM-32 module, essentially flattening it all out onto one board, including our own copper trace meandering inverted-F antenna. Our Github repository for this project can be found here.
Because we did not use the off-the-shelf module from Espressif, we bought an ESP32 chip straight from the factory, with no firmware loaded onto it. The process for flashing this was not trivial, but not too complicated either. I will share my experience to hopefully expedite yours in the future!

## Section
