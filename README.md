# Lime Unlocker

A single-file Web Bluetooth tool for Lime e-scooters. No app, no install, no
developer account. Runs in **Bluefy** on iPhone and in Chrome/Edge on desktop
or Android.

It connects straight to the scooter over Bluetooth LE and fires unlock commands
at it — no Lime servers involved.

## Why this works

Lime scooters broadcast BLE under the name `lime-XXXXXXXX` (the CC2540 radio in
the IoT/tracker module). That BLE channel is a transparent UART into the
scooter's own management app, and the app parses the same commands whether they
arrive from Lime's servers or from your phone. So you can talk to the hardware
directly.

## Deploy to GitHub Pages (2 minutes)

1. Create a new **public** repo, e.g. `lime-unlocker`.
2. Add `index.html`, `README.md`, and `LICENSE` (contents below).
3. Repo → **Settings → Pages**.
4. **Source:** *Deploy from a branch* → **Branch:** `main` → **Folder:** `/ (root)` → **Save**.
5. Wait ~1 minute. Your URL is `https://YOURNAME.github.io/lime-unlocker/`.

Must be HTTPS — GitHub Pages gives you that for free.

## Use it on iPhone

1. Install **Bluefy** from the App Store (free).
2. Open your Pages URL in Bluefy.
3. Make sure Bluetooth is on and you're standing next to the scooter.
4. **Connect** → pick `lime-XXXXXXXX` from the list.
5. **Unlock**.

The scooter should beep and/or the wheel should release.

## Protocols

The tool ships four ways to talk to the scooter. **Auto** fires all of them,
paced 350 ms apart — that's the "just make it work" setting.

| Protocol | Payload for *Unlock* | Used by |
|---|---|---|
| App ASCII | `unlock` | Custom IoT / ESP32 replacement firmware |
| Controller HEX | `46 43 16 61 00 01 F1 F2 8F` | Raw UART-level controller protocol |
| AT command | `AT+CMD,<password>,<time>,<seq>,{...}$` | Stock Lime IoT app |

Other presets: `lock`, `alarm` / beep, `unlockforever`, plus a 500 ms heartbeat.

## When Auto doesn't work

Open **Advanced**.

- The **Services & characteristics** table shows exactly what your scooter
  exposes. If nothing writable shows up, paste more UUIDs into *Extra service
  UUIDs* and reconnect.
- Use **Send raw** to throw arbitrary hex/ASCII at the write characteristic.
- Use the **AT builder** if you're on stock Lime firmware. BLE commands on stock
  scooters are ignored until the scooter authenticates you — that's what the
  password field is for. The command form is
  `AT+type,password,time,sequence,data$` with `type` ∈ `ATH`, `QRY`, `CMD`, `DBG`.

## IMEI recovery (beta)

The 8 hex digits in the BLE name are the last 4 bytes of `DES(IMEI, 0x1122334455667788)`.
Enter the first N digits of your IMEI, hit **Recover IMEI**, and the tool
brute-forces the rest against the connected scooter's name.

- 8 digits → 1,000,000 candidates (~10–25 s on a phone)
- 12 digits → 1,000 candidates (instant)

## Notes

- Works best when standing within a couple of metres of the scooter.
- The scooter's IoT module still talks to Lime's servers, so whether you get
  charged is a separate question from whether it unlocks.
- Generations differ. `lime-…` naming and BLE channel are the stable parts;
  the command vocabulary is not.

## Credits

Protocol details pieced together from Matthew Garrett's Lime scooter reversing
write-up, the scooterhacking.org wiki and forums, and the
`lime-scooter-reversing` / `Lime_Gen3_IoT_Replacement` projects.


