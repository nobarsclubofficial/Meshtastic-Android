# No Bars Connect TAK / ATAK Integration Roadmap

This branch extends the official Meshtastic Android TAK implementation without replacing or duplicating upstream TAK networking code.

## Goals

- Make TAK / ATAK setup obvious and approachable for No Bars Connect users.
- Reuse `:core:takserver` and upstream Meshtastic TAK packet handling.
- Keep the fork easy to sync with `meshtastic/Meshtastic-Android`.
- Avoid reviving or depending on the legacy cross-process Meshtastic ATAK plugin path.

## Phase 1 — NBC TAK Hub

Create a dedicated user-facing **TAK / ATAK Integration** screen that summarizes:

- Radio connection state
- Device role (`TAK`, `TAK Tracker`, or other)
- Local TAK server state
- Connected TAK client count
- TAK mesh channel
- Mesh to CoT Converter state
- Firmware compatibility / TAK V2 readiness
- One-tap navigation to the existing upstream configuration screens
- One-tap TAK data-package export

Suggested status treatment:

- `TAK READY` — radio connected, compatible firmware, local TAK server running, and configuration is usable
- `SETUP NEEDED` — one or more required settings are incomplete
- `RADIO DISCONNECTED` — no Meshtastic radio connection
- `UPDATE REQUIRED` — connected firmware is too old for the current TAK path

The hub should remain a presentation/orchestration layer. It must not create a second TAK server or duplicate packet conversion logic.

## Phase 2 — Guided ATAK Setup

Add a guided setup flow that explains and links to the existing upstream settings:

1. Connect a Meshtastic radio.
2. Confirm firmware compatibility.
3. Select `TAK` or `TAK Tracker` when the operator wants a TAK-role node.
4. Configure Team Color and Member Role.
5. Enable Local TAK Server.
6. Select the TAK Mesh Channel.
7. Optionally enable Mesh to CoT Converter so ordinary Meshtastic nodes appear as ATAK contacts.
8. Export the TAK Data Package.
9. Import the package into ATAK and verify connection.

The UI must clearly explain that the legacy Meshtastic ATAK plugin is not required for the current app architecture.

## Phase 3 — Diagnostics

Add a lightweight diagnostic panel using existing state exposed by upstream components:

- Local server running / stopped
- Number of connected TAK clients
- Radio connected / disconnected
- Node role
- Firmware version and V2 eligibility
- TAK mesh channel
- Mesh to CoT enabled / disabled
- Last inbound/outbound TAK activity when an upstream observable is available

Do not log private message content or precise location data merely for diagnostics.

## Phase 4 — NBC UX / Branding

Once functionality is stable:

- Apply No Bars Connect naming and visual treatment to the new hub only.
- Keep protocol terminology (`TAK`, `ATAK`, `CoT`, `TAK Tracker`) intact so documentation and troubleshooting remain compatible with upstream.
- Add an NBC help section that points users to the relevant official Meshtastic TAK concepts rather than duplicating protocol documentation.

## Upstream Components to Preserve

The implementation should continue using upstream components including:

- `:core:takserver`
- `TAKServerManager`
- `TAKMeshIntegration`
- `MeshToCotBroadcaster`
- `TakPrefs`
- `TAKPacketV2` conversion / compression paths
- Existing TAK data-package generation and export

Any feature that can be added as a thin UI layer over these components should be implemented that way.

## Maintenance Rule

No Bars Connect-specific changes should be isolated wherever practical. Avoid edits to TAK wire-format code unless required by an upstream API change. This keeps future upstream merges small and reduces the risk of incompatibility with Meshtastic firmware, ATAK, iTAK, or WinTAK.

## Initial Acceptance Criteria

The first usable NBC TAK integration is complete when a user can open one TAK hub and determine, without hunting through multiple screens:

- whether their radio is connected,
- whether their firmware is TAK-ready,
- whether their node role is appropriate,
- whether the local TAK server is running,
- whether ATAK is connected,
- whether ordinary mesh nodes are being exposed through Mesh to CoT,
- and exactly which action to take next if any item is not ready.
