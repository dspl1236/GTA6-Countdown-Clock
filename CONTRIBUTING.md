# Contributing

Thanks for your interest! This is a small hobby project, but improvements and forks are very welcome.

## Ways to help

- **Bug reports** — open an issue with your board, display, and what you see. Serial Monitor output (115200 baud) is gold.
- **Wiring/board compatibility** — got it working on a different ESP32 variant or a different SSD1309 module? Note it in an issue or PR.
- **Features** — see the roadmap in the README. Some good starter ideas:
  - A `secrets.h` / config split so WiFi credentials aren't in the main sketch.
  - Configurable target date without recompiling (captive portal or serial command).
  - Timezone/DST via an NTP timezone string instead of raw offsets.
  - Additional clock faces or boot variations.
- **Case designs** — 3D-printable enclosures (STL/STEP) are welcome in `hardware/`.

## Ground rules

- **Never commit real WiFi credentials.** The `.gitignore` already excludes `secrets.h` / `wifi_config.h`. Keep the placeholders in the committed sketch.
- **Keep it monochrome-friendly.** Graphics are 1-bit XBM for the SSD1309. If you add art, hand it in as XBM bitmaps or code that generates them.
- **Test-compile before you PR.** Confirm it builds for **ESP32 Dev Module** with the U8g2 library. Note flash/RAM usage if you can.
- **Original art only.** Recreated logos/scenes here are homage pixel art. Don't add copyrighted image files or ripped assets.

## Making a change

1. Fork and branch.
2. Make your change; keep the coding style consistent with the existing sketch.
3. Compile-check for ESP32.
4. Open a PR describing what changed and why, with a photo/gif if it's visual.

## A note on the theme

This is a *Grand Theft Auto* fan tribute. Please keep contributions in that spirit and don't do anything that implies official affiliation with Rockstar Games or Take-Two. See the Legal section of the README.
