---
layout: post
title: "Off-Center"
date: 2024-09-04
image:
  path: /images/offcenter/hero.png
  thumbnail: /images/offcenter/thumbnail.png
category: arduino
description: "Off-Center is a sculptural lamp built from a stacked series of handmade ceramic forms, each illuminated by its own LED ring. At first glance, it looks like a tower of floating discs—soft, balanced, architectural. But the true experience lies in its shifting pulsing lights."
tags: [arduino, ceramics, lighting, sculpture]
---

Off-Center is a sculptural lamp built from a stacked series of handmade ceramic forms, each illuminated by its own LED ring. At first glance, it looks like a tower of floating discs—soft, balanced, architectural. But the true experience lies in its shifting pulse behavior.

Controlled by an Arduino, every LED ring “breathes” at a different rhythm. These rhythms are randomly recalculated over time, then slowly drawn together until eventuallyall plates begin pulsing in perfect unison. After this brief moment of harmony, the system releases again into gentle chaos, beginning the cycle anew.

The effect is meditative and organic, like watching a group of fireflies drift together and apart.

Code repo: <https://github.com/danwagoner/arduino-off_center>

<figure style="width: 300px" class="align-right">
  <img src="{{ '/images/offcenter/full.png' | absolute_url }}" alt="">
</figure>

---

## **Physical Design**  
The lamp is composed of:

- A wide ceramic base that houses the arduino and power supply  
- A series of central ceramic columns supporting each of the clay plates  
- A large top plate that caps the form
- LED rings mounted beneath or within each plate, emitting soft, diffused light  

Each plate feels like a stepping stone hovering in space, connected yet independent. The clay surface diffuses the LEDs with a warm, tactile glow.

---

## **How the Light Behaves**  

The Arduino sketch that drives the lamp uses a simple pulse waveform, but wraps it in a generative timing system. Here's how the behavior unfolds:

### **1. Randomized Pulse Patterns**  
At set intervals, the Arduino:

- Selects a new random pulse speed for each plate  
- Selects a new random pulse depth (brightness swing)  
- Applies these new values immediately  

This causes each plate to drift into its own pattern—fast, slow, shallow, deep—producing a pleasantly asynchronous field of motion.

### **2. Gradual Narrowing Toward Order**  
After a full phase of randomness, the code begins to tighten the allowable range of both speed and depth:

- Maximum and minimum speeds slowly approach one another  
- Depth variation shrinks as well  

With each recalculation, the differences between plates shrink a bit, creating a slow gravitational pull toward harmony.

### **3. The Moment of Unison**  
When the range becomes small enough:

- All pulse speeds match
- All brightness depths sync  
- Every LED ring fades up and down together  

This is the calmest moment of the cycle—a synchronized breath shared across all plates.

### **4. Expansion Back to Random**  
Once unison is achieved, the process reverses:

- The speed and depth ranges gradually widen again  
- New random values become more different  
- The plates drift away from each other into loose, organic rhythms  

The lamp cycles endlessly between divergence and union, like inhaling and exhaling at the scale of minutes.

---

## **The Experience**  
From across the room, Off-Center appears alive:

- Plates glow at varied tempos  
- Brightness rises and falls like breathing  
- Rhythms lean toward each other, then wander apart  

When unison occurs, it’s subtle—an emergent alignment that feels discovered rather than programmed.

The combination of earthy ceramics and algorithmic motion creates a warm, contemplative presence. It’s a kinetic sculpture disguised as a lamp.
