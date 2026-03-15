# Bifrost Platform Overview

## Vision

Bifrost is a game federation protocol connecting different game servers and game engines, allowing players to travel between worlds, share content, and experience a unified gaming ecosystem. It is not a single platform but a set of protocols and tools that independent communities can adopt.

This document explains how the components of Bifrost work together. For the protocol specification, see [bifrost-protocol.md](./bifrost-protocol.md). For the Nakama-based architecture exploration, see [with-nakama-and-agones.md](./with-nakama-and-agones.md).

## Core Components

### Bifrost Protocol

The communication protocol for cross-server and cross-game connectivity:
- Defines message formats for chat, presence, and travel
- Provides game-specific integration points that translate between game formats and the protocol
- Implements graceful degradation for incompatible content
- Manages player data transfer and synchronization

See [bifrost-protocol.md](./bifrost-protocol.md) for the protocol specification.

### Just Enough Porting (JEP)

A variant of "Just Enough Process" for game content porting. See (TODO: Later link to the other JEP)

A tiered triage system that makes content porting feasible:

- Tier 0: Graceful degradation (Mystery Box placeholder, data preserved)
- Tier 1: Automated conversion (data-driven assets, simple mappings)
- Tier 2: Compatibility layers (common format support built once, benefits many mods)
- Tier 3: AI-assisted porting (complex mods, community "swarm" development)

The key philosophy is: do the minimum work necessary, always preserve data, and enable future improvements. Full details are in [bifrost-protocol.md](./bifrost-protocol.md).

### Content Registry

A shared database tracking content equivalence mappings (e.g. `terasology:stone` <-> `minecraft:stone`), conversion status, and compatibility layer availability. Grows organically as the community ports content.

## How They Work Together

### Example: Content sharing across games

A player travels from one game to another carrying an item from a mod (e.g. a "Diamond Chest" from the "morechests" mod):

1. The protocol layer detects `morechests:diamond_chest` in the player's inventory.
2. Checks the Content Registry for a mapping. Not found.
3. JEP triage determines: Tier 3 (complex logic, requires porting).
4. Graceful degradation converts it to a "Mystery Box" with all original data preserved.
5. A GitHub issue is generated for community/AI-assisted porting.
6. The player can continue playing. When the port is eventually completed, the Content Registry is updated and future players can travel with the full functionality "Diamond Chest" in their inventory.

### The Contribution Cycle

#### Discovery Phase

**User**: "I want to connect my Minecraft server to a Terasology server. How do I start?"

**Uplifted Mascot** (chatbot backed by RAG system to documentation):

- Explains the Bifrost protocol in friendly terms
- Guides user through setup and configuration
- Points to relevant documentation
- Answers follow-up questions

#### Implementation Phase

**User**: Installs and configures needed components to bridge games.

**Bifrost Protocol**:

- Onboards new game server to the network
- Registers server capabilities
- Begins handling protocol messages (chat, presence, travel)

**Uplifted Mascot (chatbot)**:

- Provides troubleshooting help if connection fails
- Explains error messages
- Suggests configuration improvements

#### Content Sharing Phase

Same flow as the content sharing example above. When a player's item can't be translated, graceful degradation kicks in and a GitHub issue is generated for community porting.

The Uplifted Mascot chatbot can explain to the player why their item became a Mystery Box, describe the conversion process, and link to the GitHub issue if they want to help.

#### Community Contribution Phase

**Community Member**: Sees GitHub issue for chest porting

**JEP Workflow**:

- Member can use AI tools (Claude, Copilot, etc.) with API mapping guide
- Attempts port following JEP Tier 3 workflow
- Submits Pull Request with ported code

**Automated Validation**:

- CI/CD runs tests
- Validates compilation
- Checks against unit tests

**Uplifted Mascot (chatbot)**:

- Can answer questions about the porting process
- Explains API mappings
- Guides through JEP workflow steps

**Result**: 

- Best port is merged
- Content Registry updated
- Future players can travel with full Diamond Chest functionality

AI tools (Claude, Cursor, Copilot, Codex, etc.) amplify what individual contributors can accomplish, making the "swarm" development model practical.

## Infrastructure

Key infrastructure components from the Yggdrasil ecosystem:

- **Nordri**: K8s cluster substrate (Agones, Crossplane, networking)
- **Tafl**: Game server orchestration via Agones
- **Knarr**: Chat bridging and community organizing
- **Nidavellir/Vegvisir**: Gateway routing, Keycloak identity
- **Heimdall**: Observability

## Development Phases

For Bifrost and related components themselves.

### Phase 1: Foundation (current)

- Protocol specification defined
- JEP triage system designed
- Nakama explored as a potential backend (see [with-nakama-and-agones.md](./with-nakama-and-agones.md))
- "First Contact" POC in design: bidirectional chat between Terasology and DestinationSol via Nakama, proving cross-game connectivity without requiring multiplayer in DS

### Phase 2: Minimum Viable Oasis (MVO)

Prove the concept with a tangible "gasp" moment:
- Cross-game chat between Terasology and DestinationSol
- Basic presence/ghost visibility between games
- Simple content transfer demonstrated (item links)

### Phase 3: Expansion

- Cross-server travel within the same game (server-to-server portals)
- Content Registry with initial mappings
- Additional games connected (Minecraft and others)
- AI-assisted porting workflow operational

### Phase 4: Maturity

- Cross-game travel with content translation
- Robust Content Registry with community contributions
- Federated relay infrastructure (multiple independent relay servers)
- Production-ready deployment

## Design Principles

- **Federation**: Not a walled garden. Anyone can integrate with the protocol and join the network. Protocol, not platform.
- **Graceful degradation**: When perfect translation isn't possible, preserve data and enable future improvement rather than failing.
- **Feasibility**: Full porting of all content from every game and mod is impossible. JEP makes the problem tractable by doing "just enough."
- **Community empowerment**: Provide tools, workflows, and documentation that amplify what individuals can contribute.

## Related Projects

- **Terasology**: Open-source voxel game with multiplayer. Primary Bifrost target.
- **DestinationSol**: Open-source 2D space game (single-player). Secondary Bifrost target, proving connectivity without multiplayer.
- **Tafl**: Game server orchestration layer (Agones + Django). Manages server lifecycles for Bifrost-connected games.
- **Knarr**: Planned integration/bridging layer for community communications and agentic workflows. Chat bridging and community organizing responsibilities are migrating here.
- **Uplifted Mascot**: AI-powered project assistant (RAG system). Separate project, may eventually integrate via Knarr. See `components/uplifted-mascot/` for its own docs.
