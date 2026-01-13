# Project Bifrost: Protocol for cross-game transportation

## 🚀 The Vision

The Bifrost project is an open-source initiative to connect disparate games and projects into a larger, interconnected network.

Our goal is not to build a single game, but to create the **protocols and tools** that allow independent worlds (like Terasology, Minecraft, Destination Sol, and more) to communicate, share presence, and eventually, content.

At its heart, this project is a **developer enablement platform**, designed to scale the mentorship and community of projects for the AI era. We're building a system that makes it easy for *anyone* to contribute to building this new, open reality.

Possible eventual website: https://bifrost.game (available as of mid-Nov 2025 for $300-400/year)

---

## 🏛️ The Three Pillars

Our architecture is built on three core, interconnected components:

### 1. The "Uplifted Mascot" (The AI Mentor)

This is our community and developer-facing platform. We "uplift" project mascots (like Terasology's "Gooey" or Demicracy's "Bill") with a powerful AI "brain" (a RAG pipeline on GKE + Vertex AI).

* **What it is:** An AI-powered chatbot, website helper, and in-game NPC.
* **Its Job:** To act as a 24/7 mentor. It's the "friendly face" of the project that:
    * Answers contributor questions by drawing from our *actual* design docs and specs.
    * Guides new developers to tasks.
    * Makes the entire ecosystem approachable and welcoming.

### 2. The "Bifrost Protocol"

This is the core "physical" connection. It's the **Federated Game Protocol (FGP)** that allows different game engines to talk to each other.

* **What it is:** A simple, lightweight WebSocket-based protocol.
* **Its Job (v0.1 - The "MVO"):**
    * **Presence ("Ghosting"):** Allow players in Terasology to see a "ghost" of a player in Minecraft, and a "sensor blip" of a player in Destination Sol.
    * **Chat Relay:** Enable seamless, cross-game chat between connected servers.
    * This is the "Minimum Viable Oasis" that proves the connection is real.

### 3. The "JEP" Workflow (The Content Engine)

"JEP" stands for **Just Enough Porting**. This is our intelligent, tiered philosophy for handling content interoperability. 

See the FGP document for further details.

---

##  swarm How It All Works: The "AI Swarm"

This is how the three pillars unite to create our GCI/GSOC-style developer experience:

1.  A player brings a Tier 3 "Mystery Box" (`FancyChest`) to Terasology. The **Bifrost Protocol**'s bridge identifies it.
2.  The **JEP Workflow** automatically generates a "Work Contract" (a GitHub Issue) pre-filled with the source code, API mappings, and a failing unit test.
3.  A new contributor asks the **Uplifted Mascot** ("Gooey") for a task. Gooey finds this "Work Contract" and guides the contributor.
4.  The contributor, acting as an "AI Swarm" agent, uses their AI tools (Gemini, Copilot) to complete the "contract" (i.e., make the test pass).
5.  They submit a PR, which is validated by automated CI, and the `FancyChest` is now a native Terasology item, its "port" facilitated by an AI-human partnership.

This system turns a technical problem (content porting) into a scalable, community-driven mentorship pipeline.