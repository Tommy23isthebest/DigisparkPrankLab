# Window Minimiser — Prank README

## What It Does

Plugs in as a USB keyboard. Every **5 minutes** it silently sends **Cmd+M**, collapsing whatever window the victim is actively working in. They have no idea why. Unplug to stop.

---

## Parts Needed

| Part | Notes |
|------|-------|
| Digispark (ATtiny85) | Tiny, cheap, looks like a USB stick |
| USB port on target Mac | Direct port — avoid hubs |

---

## Library Setup

1. Arduino IDE → **Preferences** → Additional Boards Manager URLs, add:
   ```
   https://raw.githubusercontent.com/ArminJo/DigistumpArduino/master/package_digistump_index.json
   ```
2. **Tools → Boards Manager** → search **Digistump** → install **Digistump AVR Boards**
3. **Tools → Board → Digistump AVR Boards → Digispark (Default - 16.5mhz)**

No extra library needed — DigiKeyboard is built in.

---

## The Sketch

```cpp
#include <DigiKeyboard.h>

void setup() {
  DigiKeyboard.delay(5000); // wait for USB enumeration
}

void loop() {
  DigiKeyboard.delay(300000); // 5 minutes

  // Cmd+M = minimise current window (Mac)
  DigiKeyboard.sendKeyStroke(KEY_M, MOD_GUI_LEFT);
}
```

---

## How to Upload

> **DigiSpark uploads differently from every other Arduino board.**

1. Open the sketch in Arduino IDE
2. Click **Upload**
3. Wait until the IDE says: **"Plug in device now..."**
4. **Then** plug the DigiSpark into the Mac's USB port
5. Upload completes automatically

Do NOT plug it in before clicking upload.

---

## Deployment Tips

- Works best plugged into the **back** of an iMac or a hidden USB port
- The 5-second startup delay means it won't fire immediately — no suspicion on plug-in
- First trigger happens after 5 minutes of sitting idle
- If caught, it just looks like a USB stick

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
