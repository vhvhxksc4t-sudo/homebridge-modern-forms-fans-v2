# Modern Forms Homebridge Plugin

Add support for [Modern Forms](https://modernforms.com) fans to HomeKit using Homebridge.

> **This package (`homebridge-modern-forms-fans-v2`) is a community fork updated for Node.js 18+ with Homebridge 1.x/2.x support.**
> Original plugin: [`homebridge-modern-forms-fans`](https://www.npmjs.com/package/homebridge-modern-forms-fans) by davidashman.

## Requirements

- [Homebridge](https://homebridge.io) **1.0 or later** (Homebridge 2.0 supported)
- Node.js **18.0 or later**

## Installation

```bash
npm install -g homebridge-modern-forms-fans-v2
```

Or via the [Homebridge UI](https://github.com/homebridge/homebridge-config-ui-x) — search for **homebridge-modern-forms-fans-v2**.

## Setup

1. Add your fans to your home network using the Modern Forms app ([iOS](https://apps.apple.com/us/app/modern-forms/id1425046298) / [Android](https://play.google.com/store/apps/details?id=com.WAC.PlayStore.ModernForms&hl=en_US)) and verify they respond in the app.

2. Add the platform to your Homebridge `config.json`:

    ```json
    {
        "platform": "ModernForms"
    }
    ```

3. Full example `config.json`:

    ```json
    {
        "bridge": {
            "name": "Homebridge",
            "username": "XX:XX:XX:XX:XX:XX",
            "port": 51826,
            "pin": "000-00-000"
        },
        "platforms": [
            {
                "platform": "ModernForms"
            }
        ]
    }
    ```

4. Restart Homebridge. Fans on your network are discovered automatically and will appear in the Home app.

## Configuration

### Auto-discovery (default)

By default the plugin scans your network, pings every IP in your subnet, and filters by the Modern Forms MAC vendor prefix (`C8:93:46`). No additional configuration required.

### Specifying Fan IP Addresses

If a fan is not found automatically, add it explicitly:

```json
{
    "platform": "ModernForms",
    "fans": [
        { "ip": "192.168.1.10" },
        { "ip": "192.168.1.11", "light": false }
    ]
}
```

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `ip` | string | — | IPv4 address of the fan (required) |
| `light` | boolean | `true` | Expose the fan's light as a Lightbulb accessory |
| `switch` | string | — | MQTT topic suffix for a Tasmota wall switch |

### Disabling Auto-discovery

Useful on large or segmented networks where the subnet scan is slow or unwanted. Requires manual `fans` entries.

```json
{
    "platform": "ModernForms",
    "autoDiscover": false,
    "fans": [
        { "ip": "192.168.1.10" }
    ]
}
```

### Polling Interval

How often (in seconds) the plugin polls each fan for state updates. Default: `5`.

```json
{
    "platform": "ModernForms",
    "pollingInterval": 10
}
```

### MQTT Wall Switch Integration

Pairs a Tasmota-flashed wall switch with a fan so a single-press toggles the fan on/off and syncs the switch LED to fan state.

```json
{
    "platform": "ModernForms",
    "mqttUrl": "mqtt://192.168.1.2",
    "fans": [
        {
            "ip": "192.168.1.10",
            "switch": "tasmota_switch_1"
        }
    ]
}
```

The plugin subscribes to `stat/<switch>/RESULT` and publishes to `cmnd/<switch>/LedPower`.

## Migrating from homebridge-modern-forms-fans

If you have the original [`homebridge-modern-forms-fans`](https://www.npmjs.com/package/homebridge-modern-forms-fans) installed:

> **Before uninstalling the old plugin, note down any manual fan IP addresses from its config.** Uninstalling a Homebridge plugin removes its config entry — those IPs won't carry over automatically.

1. Note any IPs listed under `fans` in the old plugin's config
2. Install `homebridge-modern-forms-fans-v2` via the Homebridge UI
3. Configure it with the same IPs
4. Verify fans are discovered and working
5. Uninstall `homebridge-modern-forms-fans`

Homebridge will automatically migrate cached accessories to the new plugin name on first start.

## Changelog

### 1.1.5
- Fix crash after accessory registration: revert tsconfig target to ES2020 — ES2022 native class field semantics run field initializers before constructor parameter properties are assigned, breaking accessories that reference `this.accessory` in field initializers

### 1.1.4
- Fix crash on network scan: filter ping to alive hosts only, add `catchError` on ARP lookups, add error handlers on subscriptions (rxjs 7 unhandled errors crash the process)

### 1.1.2
- Loosened `peerDependencies` to accept Homebridge 1.x and 2.x

### 1.1.1
- Upgraded `@typescript-eslint` to v8 for full TypeScript 5.x compatibility

### 1.1.0
- **Homebridge 2.0** support (`peerDependencies` updated)
- Node.js minimum raised to **18.0**
- `axios` updated 0.x → 1.x
- `rxjs` updated 6.x → 7.x
- TypeScript updated 4.x → 5.x; build target ES2022
- ESLint updated 7 → 8, `@typescript-eslint` 3 → 8
- CI matrix updated to Node 18 / 20 / 22 / 24

## License

[Apache-2.0](LICENSE)
