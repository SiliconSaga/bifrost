# Bifrost Protocol

## Overview

Bifrost is a federated game protocol designed to enable seamless connections between different game servers and even different game engines. Named after the mythical rainbow bridge that connects realms, Bifrost allows players to travel between worlds, share content, and experience a unified metaverse across multiple games.

The protocol is designed with a "Just Enough Porting" (JEP) philosophy, prioritizing graceful degradation and compatibility layers over full rewrites, making it feasible to connect games with minimal modification. It is _not_ intended to require any sort of centralized infrastructure or core organization, leaving it up to enthusiasts to follow suggested patterns to build their piece of the whole.

## Core Vision

### The "OASIS" Ambition

Bifrost aims to create a federated game metaverse where:
- Players can travel between different game servers (even different games)
- Content can be shared and adapted across game boundaries
- Communities can build bridges between their favorite games
- The network grows organically through community contributions

### Historical Context

**Spout & Spoutcraft**: Early attempt at protocol-level game extension, proving that custom networking could enable new features. The project demonstrated the feasibility of bridging game clients and servers.

**Modern Evolution**: With Kubernetes, Agones, and AI-assisted development, the technology is finally ready to realize the full vision of federated game networking.

## Architecture Principles

### 1. Federation Over Centralization

Bifrost is not a single platform but a protocol. Anyone can run a "Bridge Agent" to connect their server to the network, similar to how email (SMTP) or the Fediverse (ActivityPub) work.

### 2. Graceful Degradation

When content can't be perfectly translated between games, the system degrades gracefully rather than failing. A "fancy chest" becomes a "mystery box" that preserves data for future conversion.

### 3. Just Enough Porting (JEP)

Content is triaged into tiers based on complexity:
- **Tier 0**: Graceful degradation (Mystery Box)
- **Tier 1**: Automated conversion (Asset Packs)
- **Tier 2**: Compatibility layers (Techne Models)
- **Tier 3**: Full AI-assisted porting (Complex Mods)

### 4. Community-Driven Growth

The protocol grows through community contributions. AI-assisted tools make it feasible for modders to port content, creating a virtuous cycle of compatibility.

## Protocol Components

### Bridge Agents

A "Bridge Agent" is a lightweight plugin or service that connects a game server to the Bifrost network. It handles:
- **Registration**: Announces the server to the discovery service
- **Handshakes**: Confirms connections from other servers
- **Data Translation**: Converts between game-specific formats and Bifrost protocol
- **Graceful Degradation**: Handles content that can't be perfectly translated

**Deployment Models**:
- **Agones Cluster**: Core infrastructure for managed game servers
- **Self-Hosted**: Side process that connects independent servers to the network
- **Hybrid**: Mix of managed and self-hosted servers

### Relay Server

A central (or federated) relay server that:
- Maintains server registry and discovery
- Routes messages between Bridge Agents
- Handles player transfers and data synchronization
- Provides protocol versioning and compatibility checks

### Content Registry

A shared database that tracks:
- Content equivalence mappings (e.g., `minecraft:stone` ↔ `terasology:CoreAssets:stone`)
- Ported content availability
- Compatibility layer support
- Content conversion tasks

## Minimum Viable Oasis (MVO)

The initial "gasp" moment that proves the concept:

### Phase 1: The Ghost

**Goal**: Two players, one in Minecraft and one in Terasology, can see and chat with each other.

**Implementation**:
- Simple WebSocket relay server
- Bridge Agents in both games
- JSON message protocol for:
  - Chat messages (bi-directional)
  - Player presence updates (position, rotation)
  - Ghost rendering (simple model/hologram in each game)

**Result**: Tangible proof that different game engines can connect in real-time.

This is not exactly new, for instance a presentation was held in-game in "Better Than Minecon" 2017 or 2018 of a video stream from Terasology being shown on a ComputerCraft screen inside Minecraft. But at the time that was done by local video file manipulation on shared server hardware, not any sort of network streaming.

### Phase 2: Cross-Server Travel (ARK Model)

**Goal**: Players can travel between servers within the same game (Minecraft → Minecraft).

**Implementation**:
- Player data serialization
- Shared database for character/inventory data
- Proxy server (BungeeCord/Velocity) for seamless connection transfer
- Data synchronization on transfer

**Result**: Foundation for cross-game travel.

### Phase 3: Cross-Game Travel

**Goal**: Players can travel from Minecraft to Terasology (or other games).

**Implementation**:
- Content mapping registry
- Data transformation pipeline
- Graceful degradation for unsupported content
- Bridge Agent translation layer

**Result**: True federated game metaverse.

## Just Enough Porting (JEP) Triage System

### Tier 0: Graceful Degradation

**When**: Content has no equivalent and no conversion path exists.

**Action**: 
- Convert to "Mystery Box" placeholder
- Preserve all original data (NBT, metadata)
- Store conversion task for future work

**Example**: Player-locked chest from Minecraft mod → Mystery Box in Terasology

### Tier 1: Automated Conversion

**When**: Content is data-driven with simple 1-to-1 mappings.

**Action**:
- Automated script-based conversion
- Replayable for version upgrades
- No human intervention required

**Example**: Decorative blocks, textures, simple JSON models

**Workflow**:
1. Bridge Agent detects simple asset
2. Runs automated conversion script
3. Injects converted content into target game
4. Logs conversion for future updates

### Tier 2: Compatibility Layers

**When**: Content uses a common format/pattern shared by many mods.

**Action**:
- Build compatibility layer once (community effort)
- All content using that format works automatically
- High-leverage, low-maintenance

**Example**: Techne model format (used by hundreds of Minecraft mods)

**Workflow**:
1. Community identifies common pattern (e.g., Techne models)
2. Builds compatibility layer for target game
3. Any mod using that pattern now works
4. Future mods benefit automatically

### Tier 3: AI-Assisted Porting

**When**: Content has complex, unique logic that requires full porting.

**Action**:
1. Generate "Work Contract" (GitHub issue)
2. AI Swarm (community + AI tools) attempts port
3. Automated testing validates results
4. Best solution is merged

**Example**: Fancy Chest with player locking, custom inventory logic

**Workflow**:
1. Bridge Agent encounters unsupported complex content
2. Creates GitHub issue with:
   - Original source code
   - API mapping guide
   - Failing unit test (definition of "done")
3. Community members use AI tools to attempt port
4. CI/CD validates submissions
5. Best solution is selected and merged
6. Content Registry updated with new mapping

## Protocol Specification (v0.1 Draft)

### Transport

**WebSocket Connection**: Bridge Agents connect to Relay Server via WebSocket for real-time communication.

### Message Types

#### 1. Handshake

**Client → Server**:
```json
{
  "type": "HANDSHAKE",
  "server_id": "minecraft_server_01",
  "game": "minecraft",
  "version": "1.20.1",
  "world": "overworld",
  "capabilities": ["chat", "presence", "travel"]
}
```

**Server → Client**:
```json
{
  "type": "HANDSHAKE_ACK",
  "status": "accepted",
  "protocol_version": "0.1.0",
  "relay_id": "bifrost_relay_01"
}
```

#### 2. Chat Relay

**Bi-Directional**:
```json
{
  "type": "CHAT",
  "player_id": "uuid-1234-abcd",
  "player_name": "Player123",
  "game": "minecraft",
  "server_id": "minecraft_server_01",
  "message": "Hello Terasology!",
  "timestamp": 1234567890
}
```

**Display Format**: `[Minecraft] Player123: Hello Terasology!`

#### 3. Player Presence Update

**Client → Server** (sent ~5 times per second per player):
```json
{
  "type": "PRESENCE_UPDATE",
  "player_id": "uuid-1234-abcd",
  "player_name": "Player123",
  "position": {
    "x": 100.5,
    "y": 64.0,
    "z": -50.2
  },
  "rotation": {
    "yaw": 90.0,
    "pitch": 0.0
  },
  "game": "minecraft",
  "server_id": "minecraft_server_01"
}
```

#### 4. Ghost Broadcast

**Server → Client** (broadcast to other games):
```json
{
  "type": "GHOST_UPDATE",
  "game": "minecraft",
  "server_id": "minecraft_server_01",
  "player_id": "uuid-1234-abcd",
  "player_name": "Player123",
  "position": {
    "x": 100.5,
    "y": 64.0,
    "z": -50.2
  },
  "rotation": {
    "yaw": 90.0,
    "pitch": 0.0
  }
}
```

**Bridge Agent Responsibility**: Spawn or update a "ghost" entity (simple model/hologram) at the specified position.

#### 5. Travel Request

**Client → Server**:
```json
{
  "type": "TRAVEL_REQUEST",
  "player_id": "uuid-1234-abcd",
  "source_game": "minecraft",
  "source_server": "minecraft_server_01",
  "target_game": "terasology",
  "target_server": "terasology_server_01",
  "player_data": {
    "inventory": [...],
    "position": {...},
    "health": 20,
    "metadata": {...}
  }
}
```

#### 6. Travel Response

**Server → Client**:
```json
{
  "type": "TRAVEL_RESPONSE",
  "status": "accepted",
  "target_server_url": "terasology_server_01:25565",
  "player_data_transformed": {
    "inventory": [...], // converted items
    "degraded_items": [...], // items that became Mystery Boxes
    "conversion_tasks": [...] // GitHub issues created for future work
  }
}
```

## Integration with Agones

### Shulker

Shulker is a Minecraft-specific Agones operator that provides:
- `MinecraftServerFleet` custom resource
- `ProxyFleet` custom resource for BungeeCord/Velocity
- Automatic server lifecycle management

### Bifrost + Agones Workflow

1. **Server Allocation**: Player requests travel to Terasology server
2. **Agones Allocation**: Relay Server requests Agones to allocate/find Terasology server
3. **Bridge Agent Activation**: Target server's Bridge Agent prepares to receive player
4. **Data Translation**: Bridge Agent converts player data using Content Registry
5. **Connection Transfer**: Player seamlessly moves to new server

### Self-Hosted Integration

Bridge Agents can run as side processes on self-hosted servers:
- Connects to public Relay Server
- Registers server capabilities
- Handles local data translation
- Enables federation without requiring Agones cluster

## Content Conversion Pipeline

### Discovery

When a Bridge Agent encounters unknown content:
1. Check Content Registry for existing mapping
2. If not found, determine content tier (0-3)
3. Execute appropriate JEP workflow

### Task Generation

For Tier 3 content, automatically create:
- GitHub issue in "Content Conversion" project
- Includes: source code, API mapping, test requirements
- Tagged for AI Swarm attention
- Links to relevant documentation

### Conversion Tracking

Content Registry tracks:
- Original content ID and metadata
- Converted content ID and location
- Conversion method used (Tier 0-3)
- Conversion quality/status
- Related GitHub issues/tasks

## Community Workflow

### For Modders

1. **Submit Content**: Upload open-source mod to conversion portal
2. **AI Analysis**: System analyzes code and suggests conversion tier
3. **Community Review**: AI Swarm reviews and attempts conversion
4. **Validation**: Automated tests verify conversion
5. **Registry Update**: Successful conversions update Content Registry

### For Server Operators

1. **Install Bridge Agent**: Deploy agent plugin/service
2. **Configure**: Point to Relay Server, set server capabilities
3. **Register**: Agent announces server to network
4. **Monitor**: Track connections, conversions, degradation events

### For Players

1. **Discover**: Find Bifrost-compatible servers
2. **Travel**: Use portals/commands to move between servers
3. **Experience**: Seamless gameplay across game boundaries
4. **Contribute**: Report issues, suggest content for conversion

## Technical Challenges

### 1. Coordinate System Differences

Different games use different coordinate systems, block sizes, and world scales.

**Solution**: Bridge Agents maintain transformation matrices for each game pair.

### 2. Rendering Compatibility

Games have different rendering engines (OpenGL versions, shader systems, etc.).

**Solution**: Compatibility layers for common formats (Techne, OBJ, etc.) and graceful degradation for unsupported features.

### 3. Networking Protocols

Each game has its own networking protocol.

**Solution**: Bridge Agents translate between game protocols and Bifrost protocol. Proxy servers (BungeeCord/Velocity) handle connection management.

### 4. Content Licensing

Porting content creates derivative works with licensing implications.

**Solution**: 
- Only work with permissive licenses (MIT, Apache 2.0)
- Clear documentation of license requirements
- Graceful degradation preserves original content when possible

### 5. Version Compatibility

Games and mods update frequently, breaking compatibility.

**Solution**:
- Version-aware Content Registry
- Automated re-conversion for Tier 1 content
- Community-maintained compatibility layers

## Future Enhancements

- **Cross-Platform Support**: Extend beyond Java-based games
- **Advanced Rendering**: Support for complex 3D models, animations
- **Economic Systems**: Cross-game currency, trading
- **Social Features**: Friends lists, guilds that span games
- **Content Marketplace**: Community-driven content sharing platform
- **Cloud Gaming**: Seamlessly transfer to games with no local installation

## Related Systems

- **Uplifted Mascot**: AI assistant that explains Bifrost to users
- **JEP Workflow**: The triage system that makes Bifrost feasible
- **Demicracy Platform**: Governance system that can showcase Bifrost servers

## Getting Started

### For Developers

1. Review protocol specification (this document)
2. Choose a game to build a Bridge Agent for
3. Implement WebSocket client for Relay Server
4. Implement message handlers for protocol messages
5. Add graceful degradation for unsupported content

### For Server Operators

1. Deploy Bridge Agent plugin/service
2. Configure connection to Relay Server
3. Test with MVO (ghost/chat functionality)
4. Enable travel features as ready

### For Community

1. Join Bifrost Discord/Forum
2. Test early implementations
3. Report bugs and suggest features
4. Contribute to content conversion efforts

