# The "Bifrost" Protocol

## 1. Overview: The "Minimum Viable Oasis"

Bifrost is the code name for the **Federated Game Protocol (FGP)**. It is an open, lightweight, game-agnostic protocol for connecting disparate game servers into a federated network.

The v0.1 goal is to create a live, tangible connection between two different games, such as Terasology and Destination Sol. It is *not* yet for rich features like inventory or asset transfer. It is for **Presence and Chat**.

## 2. v0.1 Architecture: The Relay Model

* **Central Relay Server:** A simple, lightweight WebSocket server. Its only job is to "relay" JSON messages. It holds no state.

* **Bridge Agent (Plugin):** A game-specific plugin (a Terasology module, a Destination Sol module) that:
    1.  Connects to the Relay Server via WebSocket.
    2.  Sends local player data *to* the Relay.
    3.  Receives data *from* the Relay and renders it in-game.

## 3. FGP v0.1 Spec: JSON Message Payloads

### `HANDSHAKE` (Client-to-Server)
* **When:** Sent by the Bridge Agent immediately upon connection.
* **Purpose:** Identifies the server to the network.
```json
{
  "type": "HANDSHAKE",
  "server_id": "terasology_main",
  "game_id": "Terasology",
  "world_id": "overworld",
  "display_name": "Terasology (Main)"
}
CHAT (Client-to-Server)
When: Sent by the Bridge Agent when a player sends a "global" chat message (e.g., /global hello all!).

JSON

{
  "type": "CHAT",
  "player_name": "Player123",
  "message": "Hello all!"
}
CHAT_BROADCAST (Server-to-Client)
When: The Relay broadcasts this to all other connected Bridge Agents.

JSON

{
  "type": "CHAT_BROADCAST",
  "source_game": "Terasology",
  "source_server": "Terasology (Main)",
  "player_name": "Player123",
  "message": "Hello all!"
}
In-Game Result: The Minecraft client sees: [Terasology] Player123: Hello all!

PRESENCE_UPDATE (Client-to-Server)
When: Sent by the Bridge Agent 5 times per second for every local player.

Purpose: To power the "ghosting" feature.

JSON

{
  "type": "PRESENCE_UPDATE",
  "player_id": "uuid-abcd-1234",
  "player_name": "Player123",
  "position": { "x": 10.5, "y": 64.0, "z": -22.1 },
  "rotation": { "yaw": 180.0, "pitch": 0.0 }
}
GHOST_BROADCAST (Server-to-Client)
When: The Relay broadcasts this to all other connected Bridge Agents.

Purpose: The Bridge Agent uses this data to render a "ghost" or "hologram" of the remote player.

JSON

{
  "type": "GHOST_BROADCAST",
  "source_game": "Terasology",
  "player_id": "uuid-abcd-1234",
  "player_name": "Player123",
  "position": { "x": 10.5, "y": 64.0, "z": -22.1 },
  "rotation": { "yaw": 180.0, "pitch": 0.0 }
}
In-Game Result: A player in Destination Sol might see a "sensor blip" named "Player123" at a mapped coordinate, while a Minecraft player sees a semi-transparent "ghost" model.