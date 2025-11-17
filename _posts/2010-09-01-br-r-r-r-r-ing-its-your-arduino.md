---
layout: post
title: "Br-r-r-r-r-ing…It’s your Arduino"
date: 2010-09-10
category: arduino
image:
  path: /images/arduino-phone/hero.png
  thumbnail: /images/arduino-phone/thumbnail.png
tags: [arduino, asterisk, electronics, php]
---

If you’ve ever wanted a sensor, button, or random Arduino event to kick off a real phone call, this project does exactly that. It uses an Arduino to talk to an Asterisk PBX over the network and tell it, “Hey, go call this number.”

The full source lives here:  
<https://github.com/danwagoner/arduino-outboundcall>

---

## High-Level Idea

Instead of wiring relays into a phone line, this project uses Asterisk’s call file mechanism. It does this by making an HTTP request to a PHP script running on the Asterisk server. That script:

1. Verifies the request came from the Arduino’s IP  
2. Creates a `.call` file with your call parameters  
3. Drops the file into Asterisk’s `outgoing` spool directory  
4. Asterisk immediately originates the outbound call

This means the integration is actually network-based, not relay-based.

---

## What’s in the Repo

The repository contains three main components:

- `arduino-outboundcall.pde` — the Arduino sketch  
- `arduino-outboundcall.php` — the PHP script that generates the call file  
- `extensions_custom.conf` — Asterisk dialplan configuration  

These pieces work together to trigger outbound calls through Asterisk.

---

## Arduino: Trigger → HTTP Request → Call

On the Arduino side, the logic is intentionally simple:

- You attach any trigger mechanism you want:
  - Button
  - Motion sensor
  - Distance sensor
  - Any digital/analog event
- When the trigger condition is met, the Arduino sends an HTTP request to the PHP script sitting on the asterisk server.

The sequence:

1. Wait for the trigger (e.g. a button press)  
2. Open a TCP connection to the Asterisk server  
3. Send a `GET /arduino-outboundcall.php` request  
4. Close the connection and wait for the next event  

The Arduino doesn’t dial numbers, doesn’t know SIP, and doesn’t talk to phone lines. It just notifies the server.

---

## The PHP Script on the Asterisk Server

The `arduino-outboundcall.php` script is the real workhorse. It does the following:

### 1. IP Address Verification
Checks `$_SERVER['REMOTE_ADDR']` to ensure the request is coming from your Arduino’s configured IP.  
If not, it exits.

### 2. Building the `.call` File
A `.call` file looks something similar to this:

```
Channel: PJSIP/yourtrunk/18005551212
CallerID: "Alert" <1000>
Context: outbound-arduino
Extension: s
Priority: 1
```

The PHP script writes this data to a temporary file.

### 3. Dropping It Into Asterisk’s Outgoing Directory
After creating the temporaryfile, the script moves it into `/var/spool/asterisk/outgoing/`, which Asterisk monitors.

As soon as the file hits that directory, Asterisk originates the call automatically.

---

## Asterisk Dialplan (`extensions_custom.conf`)

This file defines what Asterisk should do when the `.call` file is processed.

Example behaviors:

- Dial a specific phone number  
- Play an audio message  
- Route the call through an IVR  
- Send it to a queue  

Your `.call` file references a context (e.g., `[outbound-arduino]`), and the dialplan defines the steps to execute.

Because all logic lives in Asterisk, you can change what the call does without editing the Arduino at all.

---

## End-to-End Flow

1. Arduino detects an event
2. Arduino sends an HTTP request to the PHP script  
3. PHP script authenticates, creates a `.call` file, and drops it into the spool  
4. Asterisk sees the call file and originates the call  
5. Your phone rings

---

## Why This Method 

- No relays or analog hacking — everything is done over the network  
- Secure — IP filtering is simple and effective  
- Flexible — Asterisk dialplan can take the call anywhere you want  
- Extensible — any Arduino event can trigger a call  

---

## Final Thoughts

This project isn’t a hardware hack on phone lines—it’s a **network-integrated trigger system** for Asterisk. The Arduino just calls a URL, and the Asterisk server takes care of the telephony side through its powerful call file system.