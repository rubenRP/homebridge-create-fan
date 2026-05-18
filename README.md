
![ceiling-fan.jpg](readme/ceiling-fan.jpg)

# CREATE Ceiling Fan (LAN)

Control your CREATE Ceiling Fan from HomeKit. Fork of [homebridge-create-ceiling-fan](https://github.com/moifort/homebridge-create-fan) with **optional static LAN IP** when UDP discovery fails.

- Turn fan on/off
- Adjust fan speed (6 steps, shown as a slider in the Home app)
- Reverse rotation direction (summer/winter mode)
- Turn light on/off
- Momentary toggle tiles (tap to flip the fan/light state, automation-friendly)
- Connect by **static IP** (skips `tuyapi.find()` UDP discovery)

![homekit-1.png](readme/homekit-1.png)
![homekit-2.png](readme/homekit-2.png)

## Installation

Install from npm (recommended):

```bash
sudo npm install -g homebridge-create-ceiling-fan-lan
```

Or use the Homebridge UI → Plugins → search for `homebridge-create-ceiling-fan-lan`.

**Important:** Uninstall the original `homebridge-create-ceiling-fan` plugin if you had it installed, to avoid duplicate platform accessories.

In `config.json`, reference this package under `plugins`:

```json
"plugins": [
  "homebridge-create-ceiling-fan-lan"
]
```

The platform key stays `HomebridgeCreateCeilingFan` (unchanged from upstream).

## Static IP

Use a static IP when Homebridge logs `find() timed out` or when `tinytuya scan` returns no devices, but `tinytuya get` / `tuya-cli` work with a known IP.

1. Reserve a **DHCP reservation** for each fan in your router.
2. Verify id/key and protocol with tinytuya before adding to config:

   ```bash
   python3 -m tinytuya get --ip 192.168.1.128 --id YOUR_ID --key YOUR_KEY --version 3.5
   ```

3. Add `"ip": "192.168.1.128"` to each device entry (see example below).

When `ip` is set, the plugin connects directly via TCP (port 6668) and does **not** call UDP discovery. Without `ip`, behavior matches upstream (`find()` then `connect()`).

After changing `ip`, `id`, or `key` on a cached accessory, restart Homebridge — config is merged into cached context on startup.

## Protocol 3.5

Many recent CREATE fans (e.g. Windcalm) use Tuya protocol **3.5**, not 3.4. If you see `ECONNRESET` right after `Connected!`, try `"version": "3.5"`.

## Configuration example

```json
{
  "platform": "HomebridgeCreateCeilingFan",
  "name": "Create Ceiling Fan",
  "devices": [
    {
      "id": "bf7c2ad326d040a7afwalm",
      "key": "YOUR_LOCAL_KEY",
      "name": "Ventilador Dormitorio",
      "version": "3.5",
      "ip": "192.168.1.128"
    },
    {
      "id": "YOUR_STUDIO_ID",
      "key": "YOUR_STUDIO_KEY",
      "name": "Ventilador Estudio",
      "version": "3.5",
      "ip": "192.168.1.129"
    }
  ]
}
```

Replace `id`, `key`, and `ip` per fan. After re-pairing in Smart Life, refresh credentials with [tinytuya wizard](https://github.com/jasonacox/tinytuya/tree/master#setup-wizard---getting-local-keys).

## Getting your keys

To get your device `id` and `key`, follow [Getting your keys](https://github.com/jasonacox/tinytuya/tree/master#setup-wizard---getting-local-keys).

### Optional: Toggle tiles

The plugin exposes two extra momentary toggles, named `Toggle` and rendered with the Apple Home
fan and light-bulb icons. Tap the tile in the Home app (or trigger it from an automation) and the
plugin flips the fan or light state, then resets the tile back to OFF.

Set `toggles: false` on the device to hide them. Default is `true`.

```json
{
  "platform": "HomebridgeCreateCeilingFan",
  "devices": [
    { "id": "…", "key": "…", "name": "Ceiling Fan", "toggles": false }
  ]
}
```

## Publish (maintainers)

```bash
npm run build
npm run lint
npm publish --access public
```

## Thanks

- [homebridge-create-ceiling-fan](https://github.com/moifort/homebridge-create-fan) by Thibaut Mottet
- [tuyapi](https://github.com/codetheweb/tuyapi)
- @marsuboss
