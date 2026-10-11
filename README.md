# KRIVI OS v1.6.0 — touch micro-OS for the Cheap Yellow Display (ESP32-2432S028)

A standalone, landscape (320×240) touch operating system with a 5-second diagnostic boot log, a high-density **4×3 grid launcher** (12 apps per page, 6 pages) with **71 applications**, backed by a microSD card that the dynamic loader pages manifests, icons and assets from.
Made By 11 year old😏👌
**v1.6.0 highlights:** all 19 roadmap placeholders are now **fully implemented** — CHIP-8 interpreter (loads `.ch8`, built-in demo), Space Invaders, Asteroids, Morse keyer (decodes your taps), Voice Pitch tracker (autocorrelation + tuner needle), Ultrasonic range (HC-SR04), World Clock, Currency converter (`/sys/rates.json`), Unit converter, Pi Shell terminal, SSH action shortcuts, live htop monitor, Pi safe shutdown/reboot, Matrix rain, Starfield, System Settings (brightness/volume/Wi-Fi), NVS flash editor, Desk Clock, and Noise Floor logger. The Pi bridge gained `CMD`, `PS` and `POWER` handlers (shell on by default; `--no-shell` disables).

**v1.5.0 highlights:** the desktop is now a **4×3 high-density grid** (12 cells/page, 6 themed pages). 19 roadmap apps were added as **working placeholders** (each shows its planned feature and is being filled in over coming updates): CHIP-8, Space Invaders, Asteroids, Morse Keyer, Voice Pitch, Ultrasonic Range, World Clock, Currency + Unit converters, Pi Shell Keyboard, SSH Shortcuts, htop Monitor, Pi Safe Shutdown, Matrix Rain, Starfield, System Settings, NVS Editor, Desk Clock, Noise Floor Log. All existing apps were kept.

**v1.4.0 highlights:** 4-second boot; removed Maze Runner, Tone Generator, Chiptunes, Metronome, Scorecard and Game of Life; the BLE Slide Presenter is now a full **BLE Keyboard** that types live into a Raspberry Pi or any device; **Sketchpad** no longer streaks on stray touches; **Falling Sand** gained fire, wood, oil, acid, plant, stone and smoke with real interactions; **Virtual Pet** was rebuilt with life stages, five stats, hygiene/poop, sickness and death/rebirth. New limit-pushing apps: **Raycaster FPS**, **2048**, **FFT Spectrum Analyzer**, **Theremin**, **Beat Sequencer**, **Demoscene FX**, and a **3D ISS Globe Radar** that tracks the live space station on a raycast spinning Earth.

```
KRIVI_OS/
├── KriviOS/                     ← Arduino IDE sketch (open KriviOS.ino)
│   ├── KriviOS.ino              setup()/loop()
│   ├── krivi.h                  kernel API, pin map, layout constants
│   ├── hal_touch_audio.cpp      XPT2046 driver (your exact map() code) + I2S/DAC audio engine
│   ├── sys_services.cpp         settings.json, logs, clock/NTP, alarm, clap latch, LED
│   ├── ui.cpp                   touch events, header + EXIT box, widgets, file picker, keyboard
│   ├── boot.cpp                 5-second diagnostic boot sequence
│   ├── launcher.cpp             5-page home screen + 43-app registry
│   ├── loader.cpp               dynamic parameter/binary loader
│   ├── app_nes.cpp              App 01 (NES emulator)
│   ├── app_games.cpp            Apps 02–08
│   ├── app_board.cpp            App 09 (Chess / Tic-Tac-Toe) 
│   ├── app_audio.cpp            Apps 10–17
│   ├── app_utils.cpp            Apps 18–26
│   ├── app_link.cpp             Apps 27–34 (Pi link, BLE HID, Wi-Fi, ESP-NOW)
│   ├── app_sims.cpp             Apps 35–39, 41–43
│   ├── app_media.cpp            App 40 (Video & MP4 player) + App 44 (MP3 player)
│   ├── app_wireless.cpp         Apps 45–50 (Wireless Lab: passive RF diagnostics)
│   ├── app_weather.cpp          App 51 (Weather - Open-Meteo, icons from SD)
│   ├── app_iss.cpp              App 52 (3D ISS Globe Radar, embedded world map)
│   ├── app_newgames.cpp         Apps 53-54 (Raycaster FPS, 2048)
│   ├── app_newaudio.cpp         Apps 55-57 (Spectrum, Theremin, Beat Sequencer)
│   ├── app_demofx.cpp           App 58 (Demoscene FX)
│   ├── app_games3.cpp          Apps 59-61 (CHIP-8, Space Invaders, Asteroids)
│   ├── app_office.cpp          Apps 65,66,67,76 (World Clock, Currency, Units, Desk Clock)
│   ├── app_sensors.cpp         Apps 62,63,64,77 (Morse, Pitch, Ultrasonic, Noise Log)
│   ├── app_pi2.cpp             Apps 68-71 (Pi Shell, SSH, htop, Pi Power)
│   ├── app_system.cpp          Apps 74,75 (Settings, NVS Editor)
│   ├── app_screensavers.cpp    Apps 72,73 (Matrix Rain, Starfield)
│   ├── src/helix/               Helix fixed-point MP3 decoder (bundled, nothing to install)
│   └── partitions.csv           custom 4 MB layout (picked up automatically)
├── TFT_eSPI_User_Setup.h        display config for the CYD → copy into the TFT_eSPI library
├── sd_card/                     copy its CONTENTS to the root of a FAT32 microSD
├── prebuilt/                    ready-to-flash merged images (no compiling needed)
└── tools/
    ├── make_sd.py               regenerates sd_card/ (icons, manifests, manual, demo media)
    ├── convert_media.py         MP4/MOV/MKV → playable .avi, FLAC/WAV/M4A → .mp3 (uses ffmpeg)
    ├── krivi_return.h           for external .bin apps launched in binary mode
    └── pi/                      Raspberry Pi bridge daemon, systemd unit, example scripts
```

---
Usually go to the sd card folder in the zip file provided and copy the contents inside and paste it in a sd card.
## 1. Arduino IDE setup (one time)

1. **Board package** — *Tools → Board → Boards Manager*, search **esp32 by Espressif Systems**, install **version 2.0.17**.
   Core 3.x will not work: it removed the built-in-DAC I2S mode that drives the speaker (the sketch stops with a clear `#error` if 3.x is selected).
2. **Libraries** — *Sketch → Include Library → Manage Libraries*:
   | Library | Version |
   |---|---|
   | TFT_eSPI (Bodmer) | 2.5.43 |
   | TJpg_Decoder (Bodmer) | 1.1.0 or newer |
   | ArduinoJson (Benoit Blanchon) | 7.x |
   | NimBLE-Arduino (h2zero) | **1.4.3** (not 2.x — its API changed) |
3. **Display config** — copy `TFT_eSPI_User_Setup.h` over `Documents/Arduino/libraries/TFT_eSPI/User_Setup.h` (keep a backup).
4. **Tools menu**
   - Board: **ESP32 Dev Module**
   - Partition Scheme: **Huge APP (3MB No OTA/1MB SPIFFS)** — only sets the size limit the IDE checks; the real layout comes from `KriviOS/partitions.csv` (2.3 MB launcher slot + 1.56 MB slot for external apps).
   - CPU 240 MHz, Flash 80 MHz / QIO, PSRAM Disabled, **Upload Speed 460800** (921600 is too fast for the CYD's CH340 on many Macs; use 115200 if uploads still fail)
5. Open `KriviOS/KriviOS.ino` → **Upload**. The firmware is ~1.58 MB (the HTTPS stack for Weather adds ~150 KB).

**No IDE?** Flash the prebuilt image instead (pick the panel variant, see troubleshooting):
```
esptool.py --chip esp32 --baud 460800 write_flash 0x0 prebuilt/KRIVI_OS_v1.6.0_ILI9341_merged.bin
```
(or any ESP Web Flasher at offset 0x0).

## 2. microSD card

Format as **FAT32**, copy everything *inside* `sd_card/` to the card root, then edit `sys/settings.json`:
```json
"wifi": { "ssid": "YourNetwork", "pass": "YourPassword" },
"time": { "tz": "EST5EDT,M3.2.0,M11.1.0" }
```
Wi-Fi is only used for NTP time, the Wi-Fi analyzer and ping monitor. Put your own legally dumped `mario.nes` / `tetris.nes` in `/roms` (Super Mario Bros is mapper 0, NES Tetris is mapper 1 — both supported).

SD layout:
```
sys/  settings.json · apps/app_01..44.json · boot/ (logs)
icons/ app_01..44.raw          roms/ *.nes          apps/ optional external .bin
data/ music/ *.mp3   movies/ *.avi (converted MP4s)   video/ *.mjpeg (Bad Apple style)
      chiptunes/ *.mod   reader/ *.txt   paint/ *.bmp
```
The Wireless Lab tools need no SD files. The card ships with an original demo song (`data/music/krivi_demo.mp3`) and test movie (`data/movies/krivi_test_pattern.avi`).

## 2b. MP3 and MP4

- **MP3 (app 44, page 2)** — copy `.mp3` files straight into `data/music/`. Any bitrate, 32–48 kHz, mono/stereo; decoded on the ESP32 by the bundled Helix decoder, mixed to mono and played through the 8-bit DAC (so expect clear but lo-fi "radio" quality). Shows ID3 title/artist, elapsed/total time and a level visualizer; tap the bar to seek, swipe for next/previous, `SH/RA/1x` = shuffle / repeat all / play once.
- **MP4 (app 40, page 5)** — the ESP32 has no video decoder and is far too slow for H.264, so an `.mp4` can't be played directly. Convert it on your Mac once:
  ```bash
  brew install ffmpeg
  python3 tools/convert_media.py ~/Movies/clip.mp4 --sd /Volumes/<SDCARD>
  ```
  This writes `data/movies/clip.avi` — Motion-JPEG at up to 320×240, 15 fps, with 22 kHz audio — which plays **with sound**, audio-synced (late frames are dropped rather than drifting). Tap to pause, swipe down for the file list. The same tool turns `.flac/.wav/.m4a` into MP3s, and `--bad-apple` additionally writes a silent `.mjpeg` clip to `data/video/`.

## 2c. Wireless Lab (page 6)

Six passive / defensive radio tools. They only **listen** to public Wi-Fi management traffic and BLE advertisements, or probe **your own** network — none of them transmit attack frames, inject packets, capture payloads, log other people's device addresses, or impersonate a network. They were written from scratch for KRIVI, not ported from attack firmware.

| # | App | What it does |
|---|---|---|
| 45 | Channel Congestion | Hops channels 1–13 in monitor mode and graphs how busy each is by **counting frames only** (never contents). Pick the clearest channel for your router. |
| 46 | BLE Scanner | Passive list of nearby Bluetooth LE devices from the public advertisements they broadcast (name, RSSI, address type, appearance). Find your own beacons/trackers. |
| 47 | Signal Mapper | Pick one of **your** access points, then walk around: live RSSI meter + rolling graph reveal dead spots. SAMPLE records a labelled reading per room. |
| 48 | LAN Discovery | Joins **your** network and pings the /24 subnet to inventory the devices that are online, RTT and gateway flagged. A mini Fing. |
| 49 | Net Health | Times DNS lookups and measures latency / jitter / loss to your gateway and a host you choose (set in `sys/apps/app_49.json`). Tells you whether a bad connection is your Wi-Fi, router or the internet. |
| 50 | Airtime Monitor | Stays on one channel and plots frames/sec over time, split into management / data / control **counts** (no payloads). Spot congestion and interference. |
| 52 | 3D ISS Globe Radar | A raycast spinning Earth tracking the live ISS from wheretheiss.at (no API key). Pulsing blip, orbit trail, lat/lon/alt/velocity HUD; drag to spin. Needs Wi-Fi. |

Apps 45, 47, 50 use monitor mode (no connection needed). Apps 48–49 join the Wi-Fi network from `sys/settings.json`. App 46 uses Bluetooth LE.

## 2d. Weather (app 51, page 4)

Current conditions and a 5-day forecast from **Open-Meteo** — free and needs no API key. The condition icons live on the SD card at `icons/weather/*.raw` (10 conditions × two sizes, day/night variants), so you can restyle them by replacing those files, no reflash needed; if one is missing the firmware draws a vector fallback. Set your location and units in `sys/apps/app_51.json`:

```json
{ "params": { "city": "Austin", "units": "us" } }
```

- `city` is geocoded automatically; or give explicit `lat`/`lon` and a `name`.
- `units`: `us` → °F and mph, `metric` → °C and km/h.
- Needs Wi-Fi (credentials in `sys/settings.json`). Tap the screen to refresh; it also auto-refreshes every 10 minutes while open.

To design your own icon set, drop 80×80 (`_lg`) and 40×40 (`_sm`) 16-bit RGB565 `.raw` files into `icons/weather/` named `clear_day`, `clear_night`, `partly_day`, `partly_night`, `cloudy`, `fog`, `drizzle`, `rain`, `snow`, `thunder`. `tools/make_sd.py` regenerates the default set.

## 3. Hardware hookups (all optional)

| Feature | Connection |
|---|---|
| Speaker (apps 10, 13, 15–17, 40, 44, alarms, clicks) | 8 Ω 1–2 W speaker on the **SPEAK** connector (GPIO26 DAC → on-board amp) |
| Microphone (apps 11, 12, 14) | analog mic module (MAX4466 / MAX9814) **OUT → IO35** on P3, VCC 3.3 V, GND |
| Clap relay output (app 14) | relay module input on **IO22** (CN1); the RGB LED mirrors the latch |
| Raspberry Pi (apps 27–29) | USB cable Pi ↔ CYD, run `tools/pi/krivi_bridge.py` on the Pi |
| ESP-NOW (app 34) | a second board running KRIVI OS on the same channel |

## 4. Raspberry Pi bridge

```bash
sudo apt install python3-serial python3-psutil python3-evdev
sudo usermod -aG dialout,input $USER     # then log out/in
mkdir -p ~/krivi ~/krivi_scripts
cp tools/pi/krivi_bridge.py ~/krivi/ && cp tools/pi/example_scripts/*.sh ~/krivi_scripts/ && chmod +x ~/krivi_scripts/*.sh
python3 ~/krivi/krivi_bridge.py          # or install krivi-bridge.service for autostart
```
Only executables inside `~/krivi_scripts` can run — the CYD sends a script *name*, never a command. The bridge opens the port with DTR/RTS low so it doesn't reset the CYD.

## 5. Using the OS

- **Boot**: ~5 s kernel-style log. Every line is a live probe (chip, flash, partitions, heap, SPI buses, SD mount + directory repair, settings parse, icon/ROM counts, I2S/DAC, XPT2046 probe + map, IO35/IO34 ADC, RGB LED test, MAC, NTP) with an escalating load bar. Saved to `/sys/boot/boot_last.log`.
- **Home**: 3×3 grid of 95×55 tiles, 5 pages. Swipe or use `<` `>`. **Long-press** a tile for what it needs and how it works.
- **Exit**: the red EXIT box at X 260–315, Y 4–22 closes any app.
- **Alarms** ring anywhere in the OS; tap the red banner to stop.

## 6. How the "dynamic loader" works (and what it doesn't do)

The ESP32 can't dynamically link native code from an SD card into RAM, so KRIVI uses two real mechanisms:

1. **Parameter mode (all 44 apps).** App code sits in memory-mapped flash and costs no RAM until it runs. On tap the loader frees the launcher's icon page cache (only the 9 visible icons are ever in RAM), pages `/sys/apps/app_NN.json` from SD, checks `min_heap_kb` against the largest free block and `require_dir`, hands the app its `params`, then reclaims everything on exit and logs heap before/after to `/sys/boot/session.log`. Apps allocate their big buffers (ROM images, sprites, samples, grids) on entry and free them on exit.
2. **Binary mode (optional).** Add `"bin": "/apps/foo.bin"` to a manifest and the loader streams that firmware from SD into the `ota_1` slot and reboots into it. The external firmware returns with `tools/krivi_return.h`.

Tunable parameters live in the manifests, e.g. metronome tempo (`app_16.json`), ping host (`app_33.json`), BLE macro keys (`app_30.json`, syntax `CTRL+ALT+T`, `F5`, `text:hello`, `media:VOLUP`).

## 7. Touch calibration

Your exact mapping is in `hal_touch_audio.cpp → touch::read()`:
```cpp
int touchX = map(p.x, 300, 3600, 0, 320);   // Perfect horizontal landscape calibration
int touchY = map(p.y, 500, 3700, 0, 240);   // Perfect vertical landscape calibration
touchX = constrain(touchX, 0, 319);
touchY = constrain(touchY, 0, 239);
```
The XPT2046 is read by a bit-banged driver on the CYD's dedicated touch pins (CLK 25, MOSI 32, MISO 39, CS 33, IRQ 36) using the same measurement sequence as `XPT2046_Touchscreen` with `setRotation(1)`, so `p.x`/`p.y` match those constants, and the SD card keeps the hardware VSPI bus to itself. App 42 shows raw ADC values and measures tap error at five targets.

## 8. Troubleshooting

| Symptom | Fix |
|---|---|
| Wrong colours / mirrored / garbled screen | Some dual-USB CYDs use an ST7789 panel: in `User_Setup.h` comment `ILI9341_2_DRIVER` and enable the three ST7789 lines (or flash the `ST7789` prebuilt image) |
| Touch moves the wrong way | `"touch": {"invertX": true}` / `invertY` in `settings.json`; upside-down mounting: `"display": {"flip": true}` |
| "Sketch too big" | Partition Scheme → Huge APP (step 1.4) |
| Upload fails: `Invalid head of packet` | Upload Speed 460800 or 115200; short data cable, no hub; hold BOOT while it says `Connecting...` |
| Touch does nothing | Fixed in v1.1 (the soft-SPI driver sent the XPT2046 command byte in the wrong half of the 16-bit transfer). Open Serial Monitor @115200: every press prints `touch: press at x,y` |
| Movie stutters | Re-convert with `--fps 12` or `--quality 12` (smaller JPEG frames decode faster) |
| `#error ... core 2.0.x` | Downgrade the esp32 board package to 2.0.17 |
| SD shows FAIL at boot | FAT32, ≤32 GB card recommended; the OS still boots in degraded mode |
| NES ROM refused | Supports mappers 0–4 (NROM, MMC1, UxROM, CNROM, **MMC3**). The whole ROM loads into RAM and the CYD has **no PSRAM**, so only games whose PRG+CHR fit in ~180 KB run — **SMB3 (~384 KB) is too large** and reports the exact size needed. MMC3 bank-switching and the scanline-IRQ status-bar split work; the strict blargg sub-scanline IRQ-timing tests do not all pass (scanline-level PPU). No APU sound. |
| Stuck in an external binary app | `esptool.py --chip esp32 erase_region 0xe000 0x2000` boots KRIVI OS again |

## 9. What was verified

Compiled cleanly with the esp32 core 2.0.17 + the libraries above (1.57 MB flash, 76 KB static RAM) for both panel variants. Host-side tests: the NES CPU matches the `nestest` reference log for all 5,038 official-opcode instructions (cycle-exact) and the emulator renders nestest's menu and runs its test suite to "OK"; the chess move generator passes standard perft counts (start position to depth 4, Kiwipete to depth 3); the MOD tracker engine renders the demo tune; the bundled Helix decoder decodes MP3s without errors and the ID3 reader extracts title/artist; MP4s converted by `convert_media.py` parse with the firmware's AVI reader (all frames valid JPEG, audio length correct); the Pi bridge was exercised over a virtual serial port. The six Wireless Lab tools (page 6) compile but exercise the ESP32 Wi-Fi monitor mode, BLE scan and ICMP stacks directly, so they can only be tried on real hardware. The Weather app's Open-Meteo request is blocked by the build sandbox's egress proxy, so its live fetch is also hardware-only; it uses the documented Open-Meteo v1 field names. It has **not** been run on a physical CYD yet, so expect some tuning (timing feel, colours on your exact panel) on first flash.
