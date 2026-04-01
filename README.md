# DigiSpark Prank Lab

A collection of harmless USB pranks for the **Digispark ATtiny85** — the tiny $2 board that disguises itself as a USB stick and acts as a keyboard or mouse the moment it's plugged in. No drivers, no software, no Python. Just plug and chaos.

---

## Boards Supported

| Board | Chip | USB Method |
|-------|------|-----------|
| Digispark (original) | ATtiny85 | V-USB (software) |
| Digispark Pro | ATtiny167 | V-USB (software) |

> For HID pranks (keyboard/mouse), Digispark uses V-USB — it appears as a real USB keyboard or mouse to any Mac, Windows, or Linux machine.

---

## Setup (do this once)

### 1. Add the board package

Open Arduino IDE → **Preferences** → paste into Additional Boards Manager URLs:

```
https://raw.githubusercontent.com/ArminJo/DigistumpArduino/master/package_digistump_index.json
```

> The original `digistump.com` URL is dead. Use the GitHub mirror above.

### 2. Install the boards

**Tools → Boards Manager** → search **Digistump** → install **Digistump AVR Boards**

### 3. Select board

**Tools → Board → Digistump AVR Boards → Digispark (Default - 16.5mhz)**

### 4. Fix libusb on Apple Silicon Mac (M1/M2/M3/M4)

The bundled uploader (`micronucleus`) is an Intel binary and needs x86_64 libusb:

```bash
# Install Rosetta
softwareupdate --install-rosetta --agree-to-license

# Install x86_64 Homebrew
arch -x86_64 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install x86_64 libusb-compat
arch -x86_64 /usr/local/bin/brew install libusb-compat
```

---

## How to Upload (every time)

> **Digispark uploads in reverse — upload first, plug in second.**

1. Click **Upload** in Arduino IDE
2. Wait for: `Please plug in the device ...`
3. **Then** plug the Digispark into USB
4. Done — the sketch is flashed

You have ~8 seconds after plugging in before the bootloader hands off to your sketch.

---

## Pranks

---

### Window Minimiser

**Target: Mac** | Every 5 minutes silently sends `Cmd+M`, collapsing whatever the victim is working in. Completely invisible — no popup, no sound, no indicator. They'll question everything.

```cpp
#include <DigiKeyboard.h>

void setup() {
  DigiKeyboard.delay(5000);
}

void loop() {
  DigiKeyboard.delay(300000); // 5 minutes

  DigiKeyboard.sendKeyStroke(KEY_M, MOD_GUI_LEFT); // Cmd+M
}
```

**Tips:**
- Plug into a hidden USB port (back of iMac, USB hub under desk)
- Works on any windowed app — browser, editor, terminal, Finder
- Fullscreen apps are immune — works best when victim has multiple windows open

**Change the interval:**

| Interval | Value |
|----------|-------|
| 1 min | `60000` |
| 5 min | `300000` |
| 10 min | `600000` |

---

### USB Mouse Teleporter

**Target: Mac / Windows / Linux** | Acts as a USB mouse. Every 10 minutes hurls the cursor a massive random distance — up to 4000px horizontally, 3000px vertically. Cursor flies clean off the screen.

```cpp
#include <DigiMouse.h>

void setup() {
  DigiMouse.begin();
}

void loop() {
  DigiMouse.delay(600000); // 10 minutes

  int dx = random(-4000, 4001);
  int dy = random(-3000, 3001);

  int moved_x = 0;
  int moved_y = 0;
  while (moved_x != dx || moved_y != dy) {
    int sx = constrain(dx - moved_x, -127, 127);
    int sy = constrain(dy - moved_y, -127, 127);
    DigiMouse.move(sx, sy, 0);
    moved_x += sx;
    moved_y += sy;
    DigiMouse.delay(5);
  }
}
```

**Library required:** Search `DigiMouse` in Arduino Library Manager → install.

---

## Troubleshooting

| Error | Fix |
|-------|-----|
| `digispark` not in board list | Add the boards URL from Setup step 1 |
| `digistump.com` returns 404 | Use the GitHub mirror URL — the original site is down |
| `dyld: Library not loaded: libusb-0.1.4.dylib` | Follow the Apple Silicon libusb fix in Setup step 4 |
| `Directory not empty` during brew install | Run `sudo rm -rf /usr/local/opt/libusb-compat` then reinstall |
| Upload times out | Plug in faster after the prompt — you have ~8 seconds |
| Nothing happens on target | Check the board ran setup delay (5s) and the loop delay passed |
| `Mouse` / `Keyboard` not declared | Those only work on ATMEGA32U4 (Leonardo/Micro) — not Digispark. Use DigiMouse / DigiKeyboard |

---

## Legal & Ethics

These sketches are for **educational use, escape rooms, and pranks on people who have given consent**. Do not use on computers you don't own or have explicit permission to access.

---

## Related

- Full Arduino Prank Lab web app (18+ pranks, Uno/Nano/Mega/Leonardo/ESP32): see the companion web app
- IRremote TV-B-Gone sketch (turns off any TV): see `ir_blaster/` folder

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `dyld: Library not loaded: libusb-0.1.4.dylib` | Run: `sudo rm -rf /usr/local/opt/libusb-compat` then `arch -x86_64 /usr/local/bin/brew install libusb-compat` |
| Digispark not in board list | Add the boards URL from Library Setup above |
| Upload times out | Plug in faster after "Plug in device now..." appears — you have ~8 seconds |
| Nothing happens on target Mac | Some fullscreen apps ignore Cmd+M — works best on regular windowed apps |

---

## Adjusting the Interval

Change `300000` (5 minutes in ms) in the `loop()` to whatever you want:

| Time | Value |
|------|-------|
| 1 minute | `60000` |
| 5 minutes | `300000` |
| 10 minutes | `600000` |
| 30 minutes | `1800000` |
