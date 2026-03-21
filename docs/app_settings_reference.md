# Settings Reference

Complete reference for every setting in MeshMapper, organized by section.

!!! warning
    Some settings are locked while auto-ping is running. A yellow banner appears at the top of the Settings screen when this is the case. Stop auto-ping to change locked settings.

---

## General

### Theme

- Toggle between **Dark mode** (default) and **Light mode**
- Dark mode recommended for wardriving at night to reduce glare

### Units

- **Metric** (km, m) or **Imperial** (mi, ft)
- Affects distance displays throughout the app: zone distances, ping distances, GPS simulator speed

### Top Repeaters on Map

- Shows the **Top Heard** overlay on the map with the best repeaters by SNR from your most recent ping
- Disabled by default

### Background Mode (Mobile only)

- Shown on both **Android** and **iOS** (not available on Web)
- Prompts for "Always" location permission for continuous GPS tracking when the app is backgrounded
- **Android**: Background operation uses a foreground service. Enabling this grants "Always" location permission for improved GPS consistency.
- **iOS**: Required for reliable background wardriving. Without it, iOS may throttle or stop GPS updates when the app is not in the foreground.

---

## Ping Settings

### Anonymous Mode

- Renames your companion device to **"Anonymous"** on the mesh
- Changing while connected triggers a brief reconnection
- Confirmation dialog when enabling or disabling while connected
- Cannot change while auto-ping is running

### Auto-Ping Interval

How frequently pings are sent in Active, Hybrid, and Trace modes:

- **15 seconds** — Fast, good for walking or slow driving
- **30 seconds** — Default, good balance for most wardriving
- **60 seconds** — Slower, good for highway driving
- Cannot be changed during auto-ping
- Regional admin may enforce a minimum interval (you cannot choose faster)

!!! note
    Does not affect Passive Mode (fixed 30-second discovery interval). In Hybrid Mode, the effective interval between channel messages is doubled since discovery requests alternate with TX.

### Min Ping Distance

Minimum distance you must move before the next auto-ping. Prevents spamming from a stationary position.

- Enter any value in meters (free-text input)
- **Default**: 25m
- **Minimum**: 25m (enforced by the app)

!!! note
    Does not apply to manual pings. Cannot be changed during auto-ping.

### Sound Notifications

- Plays a sound when TX/Discovery sent and when repeater echo/RX received
- Coexists with other audio (transient focus with ducking on Android, ambient on iOS)
- Useful when wardriving with phone mounted out of view

### Auto-Stop After Idle

- Automatically stops auto-ping after **30 minutes without GPS movement**
- Prevents unnecessary transmissions and saves battery
- Enabled by default
- Cannot be changed during auto-ping

---

## Modes

### Hybrid Mode

- Alternates between TX channel messages and discovery requests each interval
- Produces richer data with **50% fewer channel messages** (less mesh flooding)
- Enabled by default across all regions
- May be locked by regional admin (shown in amber when enforced)
- Cannot be changed during auto-ping

### Discovery Drop

- Counts discovery requests with **no response** as "failed pings" reported to the API
- Helps identify dead zones for network planning
- Requires repeater firmware **1.10+** to respond to discovery requests
- Disabled by default
- May be enforced by regional admin (shown in amber when set)
- Confirmation dialog before enabling
- Cannot be changed during auto-ping

---

## Filtering

### CARpeater Filter

If you're wardriving with a repeater mounted in your vehicle or nearby ("CARpeater"), enable this filter so that direct echoes from your own repeater are stripped from results. Without it, every ping would show your co-located repeater as "heard" with an extremely strong signal, polluting the coverage map with false data.

- Enter the full **6-digit hex ID** (3 bytes) of your co-located repeater (e.g., "A3B2FF")
- The app automatically truncates to match your region's hop byte size (1, 2, or 3 bytes)
- Multi-hop packets through your CARpeater are stripped to report the underlying repeater
- Cannot be changed during auto-ping

### Disable RSSI Filter

By default, the app drops any packet with RSSI ≥ -30 dBm because a signal that strong almost certainly came from a co-located repeater, not meaningful coverage. Only disable this if you are certain no co-located repeater is within range. If disabled while a CARpeater is present, your device will report false coverage data to the MeshMapper community map, degrading accuracy for everyone.

- Default: Drops packets with RSSI ≥ -30 dBm (carpeater threshold)
- Enabling allows **all signal strengths** through
- Confirmation dialog warns about community map impact before enabling
- Cannot be changed during auto-ping

---

## Radio

### TX Bytes

Repeater ID size in TX/RX path hops:

- **1 byte** — 256 unique IDs, standard for most networks
- **2 bytes** — ~65K unique IDs, reduces collisions
- **3 bytes** — ~16M unique IDs, maximum resolution

!!! warning
    Requires both your companion device and repeaters to be on firmware **1.14+**. Only change this if you understand what you're doing, or if your regional admin has enforced a value. RX always auto-detects the sender's byte size regardless of your TX setting.

**States:**

- "Set by Regional Admin" (amber) when region enforces a value
- "Firmware 1.14+ required" (amber) if firmware is too old
- "Connect to radio to configure" (amber) when disconnected
- Cannot be changed during auto-ping

!!! note
    Applied to your radio during connection, restored on clean disconnect.

### Trace Bytes

Repeater ID size for trace path requests:

- **1 byte**, **2 bytes**, or **4 bytes**

This is separate from TX Bytes because traces use a different encoding in the MeshCore protocol (bitshift encoding vs simple counter). The size options don't match 1:1:

| TX/RX (simple counter) | Trace (bitshift encoding) |
|---|---|
| Mode 0 → 1 byte | Mode 0 → 1 byte |
| Mode 1 → 2 bytes | Mode 1 → 2 bytes |
| Mode 2 → 3 bytes | Mode 2 → 4 bytes |

!!! warning
    3-byte traces are not supported by the MeshCore protocol. When your region uses 3-byte TX paths, set Trace Bytes to 4.

!!! warning
    Requires companion firmware **1.14+**. Only change this if you understand what you're doing.

**States:**

- "Firmware 1.14+ required" (amber) if firmware is too old
- "Connect to radio to configure" (amber) when disconnected
- Cannot be changed during auto-ping

### Delete Channel on Disconnect

- **Enabled** (default): Removes #wardriving channel from your radio on disconnect. Keeps radio clean.
- **Disabled**: Channel remains. Slightly faster reconnection since it doesn't need to be recreated.

---

## Data

### Queued Pings

Shows items waiting to be uploaded. Two actions:

- **Force Upload**: Immediately upload all queued items
- **Clear Queue**: Permanently delete all queued pings. Confirmation dialog included.

### Clear Map Markers

- Removes all TX and RX markers from the map display
- Does not affect uploaded data or the upload queue

---

## Offline Sessions

Sessions recorded in Offline Mode. Each session shows:

- Filename (date-based)
- Creation timestamp
- Ping count
- Device info

**Actions per session:**

- **Upload**: Send to MeshMapper servers
- **Download**: Save raw JSON to your device
- **Delete**: Remove from local storage

---

## About

### App Name and Version

- Shows "MeshMapper" and current version
- Tap version to copy to clipboard
- **Hidden feature**: Tap version **7 times** to enable Developer Mode (countdown appears after 3 taps)

### Submit Feedback

- Opens bug report dialog to report bugs or request features
- Can attach debug log files if logging is enabled (select which files to include)

### Links

- **GitHub**: View issues and source code
- **Discord**: Join the community chat
- **Community**: Built with contributions from the Greater Ottawa Mesh Radio Enthusiasts community
- **Buy us a coffee**: Support MeshMapper development

---

## Exit (Android only)

### Close App After Disconnect

- App automatically exits after disconnecting from your radio

### Close App

- Immediately exits the app
- Confirmation dialog. Warns if connected.

---

## Debug (Mobile only)

### Debug Logs

- Writes detailed debug logs to files on your device
- Orange "LOGGING" badge when active
- Timestamped entries, auto-rotate (max 10 files, max 4.5 MB per file)
- Always disabled on app start (must re-enable each session)

**When logs exist:**

- **Log file list**: Date, size, active status
- **View**: In-app log viewer
- **Share**: System share sheet
- **Upload**: Send to MeshMapper team for analysis
- **Delete All**: Remove all stored log files
