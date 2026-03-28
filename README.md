# Bifrost

A game federation protocol connecting worlds, even across different games, into a larger interconnected network.

## Vision

Bifrost is not a game but a set of protocols and tools that allow coordination between independent worlds within games like Terasology or DestinationSol, even between worlds from different games, for communication, sharing presence, and eventually sharing content.

As part of this quest it also envisions a developer enablement platform designed to scale mentorship, tooling, process, and community for the AI era — making it easier for anyone to contribute to building this open, federated gaming network. That's part of the overarching goals for https://github.com/SiliconSaga/yggdrasil and the greater related ecosystem.

## Prerequisites

Both target games (Terasology and DestinationSol) are Java-based. You need:

- **JDK 17** (covers both games — Terasology requires exactly 17, DestinationSol needs 11+)
- **Git** (for cloning and contributing)

On Windows with Chocolatey:

```bash
choco install temurin17
```

On Linux/macOS, use your package manager or [Adoptium](https://adoptium.net/).

Each game includes a Gradle wrapper (`./gradlew`) so no separate Gradle install is needed. See individual game READMEs for runtime requirements.

## Documentation

- [overview.md](./overview.md) — Platform overview, components, and development phases
- [bifrost-protocol.md](./bifrost-protocol.md) — Protocol specification, JEP triage system, and message formats
- [with-nakama-and-agones.md](./with-nakama-and-agones.md) — Architectural exploration using Nakama as the coordination layer with Agones for game server orchestration
