---
layout: post
title: "Hangmanduino"
image:
  path: /images/hangmanduino/hero.jpg
  thumbnail: /images/hangmanduino/1.jpg
date: 2009-08-31
category: arduino
tags: [arduino, electronics, hangman]
description: Hangman is one of those simple word games that almost everyone knows — which makes it perfect for turning into a physical, interactive Arduino build. I wanted a quick project that blended analog input, digital input, display output, and small-scale game logic. The result was **Hangmanduino**, a tactile version of Hangman where you play using a knob and a button.
---

Hangman is one of those simple word games that almost everyone knows — which makes it perfect for turning into a physical, interactive Arduino build. I wanted a quick project that blended analog input, digital input, display output, and small-scale game logic. The result was **Hangmanduino**, a tactile version of Hangman where you play using a knob and a button.

Code repo: <https://github.com/danwagoner/hangmanduino>

<figure class="align-center">
  <a href="#"><img src="{{ '/images/hangmanduino/schematic.jpg' | absolute_url }}" alt=""></a>
</figure> 

---

## What the Project Does
The Arduino sketch loads a small word list and randomly selects one each game. The interaction is intentionally simple:

- Turn a **potentiometer** to scroll through the alphabet  
- Press a **button** to guess a letter  
- The display shows:
  - The word with blanks  
  - Which letters you’ve already picked  
  - How many wrong guesses you have left  
- Letters you select get replaced with `*` in the letter-selector display so you can’t choose them again  
- Winning or losing triggers a reset  
- Holding the button for ~2 seconds also forces a reset

Everything is self-contained — no serial input, no keyboard, just hardware interaction.

---

## Hardware Setup
To build this project, you’ll need:

- **Arduino Uno** (or similar)  
- **Potentiometer** (connected as an analog input)  
- **Tactile push-button** (used to confirm letter selections)  
- **Display** (as shown in the repo’s schematic)  
- Jumper wires and a breadboard
  
The potentiometer provides an analog value from 0–1023. The sketch maps this range to the 26 letters of the alphabet. The button is debounced and handles both short presses (letter selection) and long presses (reset).

The display shows the evolving game state: the hidden word, selected letters, and wrong-guess counter.

---

## Key Software Concepts

### Random Word Selection
A small array of words lives in the code. On startup or reset, one is chosen randomly.

### Analog-to-Letter Mapping
The potentiometer reading is converted to a letter index (0–25), letting the player scroll through the alphabet.

### Letter-Selection Logic
When the button is pressed, the code checks:

- Has this letter been guessed before?
- Is it in the target word?
- Should the wrong-guess counter increase?
- Has the player won or lost?

Used letters are visually marked with `*` so they can’t be picked twice.

### Game-State Tracking
The program keeps track of:
- The current word
- Correct and incorrect guesses
- Remaining wrong attempts
- Win/lose conditions
- Reset timing for long button presses

### Reset Behavior
After a win or loss — or after holding the button — the Arduino resets to a fresh game.

---

## How to Try It Yourself

1. Clone the repo:`git clone https://github.com/danwagoner/hangmanduino`
2. Build the circuit using a pot, a button, and a display
3. Upload the .ino file to your Arduino
4. Adjust pin assignments if your hardware setup differs
5. Modify the word list or add features to personalize it
6. Play!