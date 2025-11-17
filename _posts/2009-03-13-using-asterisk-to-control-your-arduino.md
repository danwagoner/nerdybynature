---
layout: post
title: "Using Asterisk PBX to Control an Arduino"
image:
  path: /images/arduino-asteriskcontrol/hero.png
  thumbnail: /images/arduino-asteriskcontrol/thumbnail.png
date: 2009-03-13
category: arduino
tags: [arduino, electronics, asterisk]
description: 'In many "Internet of Things" setups, microcontrollers handle sensors and actuators. Telephony systems like Asterisk are often kept separate. But I wanted to merge the two: letting a phone call trigger a physical piece of hardware.'
---

If you’ve ever wondered how to bridge the world of telephony (via Asterisk) with the physical world of microcontrollers (via Arduino), this project is for you. I built a mini system where a call to Asterisk triggers an LED on the Arduino.

In many “Internet of Things” setups, microcontrollers handle sensors and actuators. Telephony systems like Asterisk are often kept separate. But I wanted to merge the two: letting a phone call trigger a physical piece of hardware.

Code repo: <https://github.com/danwagoner/arduino-asteriskcontrol>

---
## High-Level Overview

- A call (or an IVR option) in Asterisk triggers an AGI script.  
- The AGI script communicates over TCP to the Arduino with an Ethernet shield.  
- The Arduino receives a command and turns an LED on, off or blink.  

So effectively: call → AGI script → TCP message to Arduino.

---

## How It Works: Step by Step  
Here’s the flow of operations from a call to LED action.  

### 1. Asterisk Call/AGI  
You configure Asterisk to call `arduino_asteriskcontrol.php` via AGI. This script runs when the call reaches a certain extension or context.

### 2. The PHP Script Connects to Arduino  
Within `arduino_asteriskcontrol.php`:  
- It opens a TCP connection to the Arduino’s IP and port.
- The user is voice-prompted to choose LED mode (0 = off, 1 = on, 2 = blink).
- It then sends the corresponding ASCII char to the Arduino over the socket.

### 3. Arduino Sketch Receives Commands  
In `arduino_asteriskcontrol.pde`:  
- The Arduino sets up an Ethernet server listening (port 23 in the code) via a WIZnet Ethernet shield.
- It listens for a connection (`server.available()`) and then reads a byte `x = client.read();`.  
- Based on the byte value:
  - `0` → LED pin LOW (off)  
  - `1` → LED pin HIGH (on)  
  - `2` → blinking: HIGH, delay, LOW, delay in loop. :contentReference[oaicite:9]{index=9}  

### 4. Result: Hardware Action  
When the Arduino receives the command it executes the appropriate LED state change.

---

## Hardware & Network Requirements  
- An Arduino board (e.g., Uno or similar)  
- Ethernet shield (WIZnet-based, configured with MAC + static IP in sketch)
- LED (pin defined in sketch as `LEDpin = 4`) and appropriate resistor.
- A network connection between the Arduino and the Asterisk server so that the TCP socket can be opened.
- Asterisk server configured to run the PHP AGI script and permitted to reach the Arduino.

---

## Get Started 
1. **Clone the repo**: `git clone https://github.com/danwagoner/arduino-asteriskcontrol.git`
2. **Set up hardware**: Install Arduino plus Ethernet shield, assign it IP `192.168.15.15` in the sketch.
3. **Wire the LED**: LED to pin 4, set up `pinMode(LEDpin, OUTPUT)` in `setup()`.
4. **Flash the sketch** to the Arduino.
5. **Set up Asterisk**: In `extensions.conf`, add a context where calls hit the AGI script `arduino_asteriskcontrol.php`.
6. **Edit `arduino_asteriskcontrol.php`**: Update `$arduino_ip`, `$arduino_port`, prompts, and TTS engine settings to match my system.
7. **Test call**: Place a call, navigate the prompt, and watch the LED light up or blink.
8. **Debug**: Check the Arduino serial output and network connectivity if things didn’t trigger. The sketch includes `Serial.begin(9600)` for troubleshooting.