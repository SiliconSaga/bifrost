# Bifrost Protocol

## Overview

Bifrost is a game federation protocol designed to enable connections between different game servers and game engines. Named after the mythical rainbow bridge that connects realms, Bifrost allows players to travel between worlds, share content, and experience a unified metaverse across multiple games.

The protocol uses a "Just Enough Porting" (JEP) philosophy, prioritizing graceful degradation and compatibility layers over full rewrites. It is not intended to require centralized infrastructure, leaving it up to enthusiasts to follow suggested patterns to build their piece of the whole.

## Core Vision

Bifrost aims to create a federated game metaverse where:

- Players can travel between different game servers (even different games)
- Content can be shared and adapted across game boundaries
- Communities can build bridges between their favorite games
- The network grows organically through community contributions

### Historical Context

**Spout & Spoutcraft**: Early attempt at protocol-level game extension (with Terasology as one considered candidate), proving that custom networking could enable new features between game clients and servers.

**Modern Evolution**: With Kubernetes, Agones, and AI-assisted development, the technology is ready to realize the full vision of federated game networking.

## Architecture Principles

### Federation Over Centralization

Bifrost is a protocol, not a platform. Anyone can integrate their server with the network, similar to how email (SMTP) or the Fediverse (ActivityPub) work.

### Graceful Degradation

When content can't be perfectly translated between games, the system degrades gracefully rather than failing. A "fancy chest" becomes a "mystery box" that preserves data for future conversion.

### Just Enough Porting (JEP)

Content is triaged into tiers based on complexity:
- **Tier 0**: Graceful degradation (Mystery Box)
- **Tier 1**: Automated conversion (Asset Packs)
- **Tier 2**: Compatibility layers (Techne Models)
- **Tier 3**: Full AI-assisted porting (Complex Mods)

### Community-Driven Growth

The protocol grows through community contributions. AI-assisted tools make it feasible for modders to port content, creating a virtuous cycle of compatibility.

## Protocol Components

### Game Integration Layer

Each game connects to the Bifrost network through a game-specific integration layer. This handles:

- **Registration**: Announces the server to the discovery service
- **Handshakes**: Confirms connections from other servers
- **Data Translation**: Converts between game-specific formats and the Bifrost protocol
- **Graceful Degradation**: Handles content that can't be perfectly translated

Deployment models:

- **Agones Cluster**: Core infrastructure for managed game servers (via Tafl)
- **Self-Hosted**: Side process that connects independent servers to the network
- **Hybrid**: Mix of managed and self-hosted servers

### Relay/Coordination Server

A central (or federated) coordination layer that:

- Maintains server registry and discovery
- Routes messages between connected games
- Handles player transfers and data synchronization
- Provides protocol versioning and compatibility checks

Nakama is being explored as a candidate for this role. See [with-nakama-and-agones.md](./with-nakama-and-agones.md) for the architectural exploration.

### Content Registry

A shared database that tracks:

- Content equivalence mappings (e.g. `minecraft:stone` <-> `terasology:CoreAssets:stone`)
- Ported content availability
- Compatibility layer support
- Content conversion tasks

## Minimum Viable Oasis (MVO)

The initial "gasp" moment that proves the concept. The primary target games are Terasology (multiplayer voxel world) and DestinationSol (single-player space game).

### Phase 1: First Contact

Goal: Two players, one in Terasology and one in DestinationSol, can chat with each other in real time.

This is notable because DestinationSol has no multiplayer. The connection happens entirely through a shared backend (Nakama), proving that multiplayer is not a prerequisite for metaverse connectivity.

Implementation:
- Nakama server as the coordination layer
- Terasology engine subsystem bridging Gestalt chat events to Nakama
- DestinationSol client connecting to Nakama, using console input and banner display for chat
- Device authentication, shared chat channel

See the First Contact POC design spec for full details.

### Phase 2: Presence and Ghosts

Goal: Players can see each other as "ghost" entities across games.

Implementation:
- Position/rotation updates sent via the protocol
- Each game renders a simple ghost model for players in other games
- Near-real-time updates (~5 per second per player)

### Phase 3: Cross-Server Travel

Goal: Players can travel between servers within the same game.

Implementation:
- Player data serialization
- Shared database for character/inventory data
- Connection handoff (proxy or reconnect)
- Data synchronization on transfer

This builds the foundation for cross-game travel, which adds content translation on top of the same transfer mechanics.

### Phase 4: Cross-Game Travel

Goal: Players can travel from one game to another with content translation.

Implementation:
- Content mapping registry
- Data transformation pipeline
- JEP-based graceful degradation for unsupported content
- Game-specific translation layers

## JEP Triage System

### Tier 0: Graceful Degradation

When content has no equivalent and no conversion path exists:
- Convert to "Mystery Box" placeholder
- Preserve all original data (NBT, metadata)
- Store conversion task for future work

Example: Player-locked chest from a mod becomes a Mystery Box with all data preserved.

### Tier 1: Automated Conversion

When content is data-driven with simple 1-to-1 mappings:
- Automated script-based conversion
- Replayable for version upgrades
- No human intervention required

Example: Decorative blocks, textures, simple JSON models.

### Tier 2: Compatibility Layers

When content uses a common format shared by many mods:
- Build compatibility layer once (community effort)
- All content using that format works automatically
- High-leverage, low-maintenance

Example: Techne model format (used by hundreds of mods).

### Tier 3: AI-Assisted Porting

When content has complex, unique logic that requires full porting:
1. Generate "Work Contract" (GitHub issue) with source code, API mapping guide, and a failing unit test as the definition of "done"
2. Community members use AI tools to attempt the port
3. Automated testing validates results
4. Best solution is merged and Content Registry is updated

## Protocol Specification (v0.1 Draft)

### Transport

Connected games communicate with the coordination server via gRPC or WebSocket, depending on the SDK used. The Nakama Java SDK uses gRPC.

### Message Types

#### Handshake

Client to Server:
```json
{
  "type": "HANDSHAKE",
  "server_id": "terasology_server_01",
  "game": "terasology",
  "version": "6.0.0",
  "world": "overworld",
  "capabilities": ["chat", "presence", "travel"]
}
```

Server to Client:
```json
{
  "type": "HANDSHAKE_ACK",
  "status": "accepted",
  "protocol_version": "0.1.0",
  "relay_id": "bifrost_relay_01"
}
```

#### Chat Relay

```json
{
  "type": "CHAT",
  "player_id": "uuid-1234-abcd",
  "player_name": "Alice",
  "game": "terasology",
  "server_id": "terasology_server_01",
  "message": "Hello from the voxel world!",
  "timestamp": 1234567890
}
```

Display format: `[Terasology] Alice: Hello from the voxel world!`

#### Player Presence Update

Sent ~5 times per second per player:
```json
{
  "type": "PRESENCE_UPDATE",
  "player_id": "uuid-1234-abcd",
  "player_name": "Alice",
  "position": { "x": 100.5, "y": 64.0, "z": -50.2 },
  "rotation": { "yaw": 90.0, "pitch": 0.0 },
  "game": "terasology",
  "server_id": "terasology_server_01"
}
```

#### Ghost Broadcast

Server broadcasts to other connected games:
```json
{
  "type": "GHOST_UPDATE",
  "game": "terasology",
  "server_id": "terasology_server_01",
  "player_id": "uuid-1234-abcd",
  "player_name": "Alice",
  "position": { "x": 100.5, "y": 64.0, "z": -50.2 },
  "rotation": { "yaw": 90.0, "pitch": 0.0 }
}
```

The receiving game spawns or updates a ghost entity (simple model/hologram) at the specified position.

#### Travel Request

```json
{
  "type": "TRAVEL_REQUEST",
  "player_id": "uuid-1234-abcd",
  "source_game": "terasology",
  "source_server": "terasology_server_01",
  "target_game": "destinationsol",
  "target_server": "destinationsol_01",
  "player_data": {
    "inventory": [],
    "position": {},
    "health": 20,
    "metadata": {}
  }
}
```

#### Travel Response

```json
{
  "type": "TRAVEL_RESPONSE",
  "status": "accepted",
  "target_server_url": "destinationsol_01:25565",
  "player_data_transformed": {
    "inventory": [],
    "degraded_items": [],
    "conversion_tasks": []
  }
}
```

## Integration with Agones

### Tafl Orchestration

Tafl (the game server orchestration layer) manages Agones-based game server lifecycles. When a player requests travel to a server that isn't running, Tafl can spin one up on demand. See the Tafl design document for details.

### Self-Hosted Integration

Game-specific integration can run as a side process on self-hosted servers:
- Connects to a public or private relay/coordination server
- Registers server capabilities
- Handles local data translation
- Enables federation without requiring an Agones cluster

## Content Conversion Pipeline

### Discovery

When the protocol layer encounters unknown content:
1. Check Content Registry for existing mapping
2. If not found, determine content tier (0-3)
3. Execute appropriate JEP workflow

### Task Generation

For Tier 3 content, automatically create a GitHub issue with:
- Source code and original data
- API mapping guide for the target game
- Failing unit test as the definition of "done"
- Tags for AI-assisted attention

### Conversion Tracking

Content Registry tracks:
- Original and converted content IDs
- Conversion method used (Tier 0-3)
- Conversion quality/status
- Related GitHub issues

## Technical Challenges

### Coordinate System Differences

Different games use different coordinate systems, block sizes, and world scales. Integration layers maintain transformation matrices for each game pair.

### Content Licensing

Porting content creates derivative works with licensing implications. Only permissive licenses (MIT, Apache 2.0) are supported, with clear documentation of requirements. Graceful degradation preserves original content when licensing is unclear.

### Version Compatibility

Games and mods update frequently. Mitigations: version-aware Content Registry, automated re-conversion for Tier 1 content, and community-maintained compatibility layers.

## Future Possibilities

- Cross-platform support beyond Java-based games
- Advanced rendering (complex 3D models, animations)
- Cross-game economic systems
- Social features spanning games (friends lists, guilds)
- Visual portals via game streaming (Sunshine/Moonlight/Wolf) — see [with-nakama-and-agones.md](./with-nakama-and-agones.md)

## Related Systems

- **Tafl**: Game server orchestration via Agones
- **Knarr**: Integration and bridging layer for community communications
- **Uplifted Mascot**: AI assistant (separate project, see `components/uplifted-mascot/`)
