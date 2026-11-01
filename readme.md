# MIDI MONITOR Control Panel

A single-page MIDI control surface built with **Vue 3** and the **Web MIDI API**.  
Runs entirely in the browser — no build step, no dependencies beyond a CDN.

![status](https://img.shields.io/badge/status-stable-brightgreen)
![vue](https://img.shields.io/badge/vue-3-42b883)
![web-midi](https://img.shields.io/badge/Web%20MIDI-API-blue)

## Features

- **Slider** — sends CC 0–127 with configurable CC number.
- **Button** — momentary control: CC 127 on press, 0 on release.
- **Text Display** — up to 127 characters, sent via:
  - **CC stream** — one character per CC message, terminated by `0`.
  - **SysEx** — full UTF-8 string in a single message (supports Unicode / Cyrillic / emoji).
- **MIDI Monitor** — real-time log of IN/OUT messages with timestamp, direction, type, channel, controller and value. Auto-scroll, clear, capped at 1000 events.
- **Device management** — multiple MIDI IN/OUT devices, hot-plug detection via `onstatechange`.
- **Incoming CC sync** — slider and button reflect incoming CC messages with matching numbers.
- **Channel selector** — 1–16 for outgoing messages.

## Requirements

- **Chrome** or **Edge** with Web MIDI API support.
- Served over **HTTPS** or **localhost** (Web MIDI is not available on `file://` or insecure origins).
- For SysEx text mode: permission to access SysEx devices must be granted when prompted.

## Usage

```bash
# From the project directory
python -m http.server 8000
# or
npx serve .
```

Then open http://localhost:8000/midi-panel.html in Chrome.

1. Select a MIDI Input and/or MIDI Output.
2. Click Connect.
3. Move the slider, press the button, or type text — outgoing messages appear in the monitor.
4. Adjust CC numbers under each control as needed.

## SysEx text format
```text
F0 7D <UTF-8 bytes…> F7
```

* `F0` / `F7` — SysEx start / end.
* `7D` — Non-commercial / educational manufacturer ID.
* Payload is UTF-8 encoded with `TextEncoder`.

The CC-stream mode is kept for compatibility with gear that doesn't accept SysEx:
```text
CC 20, 72   // 'H'
CC 20, 101  // 'e'
CC 20, 108  // 'l'
CC 20, 108  // 'l'
CC 20, 111  // 'o'
CC 20, 0    // End of text
```
> Note: CC stream uses charCodeAt(i) & 0x7F, so non-ASCII characters are truncated. Use SysEx mode for full Unicode.


## Project structure
```text
midi_monitor.html   # the entire app — HTML + CSS + Vue 3 via CDN
```

## Notes

* Default CC numbers: slider 1, button 2, text 20 — all editable in the UI.
* The MIDI Monitor keeps a rolling window of the last 1000 events.
* Auto-scroll pauses if you scroll up manually, so your inspection isn't interrupted.

How to enable IAC Driver in MacOS:
1. Go to `Audio MIDI Setup` utility
2. Then `Window` -> `Show MIDI Studio` or `⌘ 2`
3. Double click on `IAC Driver`
4. Enable `Device is online` checkbox and `Apply`

And don't forget to restart the browser after.

## License
MIT
