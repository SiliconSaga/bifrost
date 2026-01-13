# Uplifted Mascot System

## Overview

The "Uplifted Mascot" is a Retrieval-Augmented Generation (RAG) system that brings AI-powered assistance to project communities. It transforms project mascots (like Terasology's "Gooey" or Demicracy's "Bill") into intelligent, context-aware assistants that can answer questions about project documentation, guide users through workflows, and even appear in-game to provide real-time help.

This system is designed to be reusable across multiple projects, with each project defining its own mascot personality while sharing the same underlying infrastructure.

## Core Concept

Instead of building separate AI systems for each project, we build one shared "Uplifted Mascot" platform that can adopt different personalities based on the project context. The same RAG pipeline powers:
- **Demicracy's "Bill"**: Helps navigate the 200+ page governance design documents
- **Terasology's "Gooey"**: Explains the Bifrost protocol and guides modders through the JEP workflow
- **Future Projects**: Any project can add their own mascot by providing documentation and a personality prompt

## Architecture

### System Components

```
┌─────────────────────────────────────────────────────────────┐
│                    Knowledge Base                           │
│  Git Repository (Markdown files: docs, specs, guides)       │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│              CI/CD & Ingestion Pipeline                     │
│  Jenkins (on GKE) → Python Script → Vertex AI Embeddings    │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│            Mascot's Brain (Vector Database)                 │
│              Vertex AI Vector Search                        │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│            Mascot's Voice (RAG Service)                     │
│  Gooey/Bill RAG Service (on GKE) → Vertex AI Gemini (LLM)   │
└──────────────────────┬──────────────────────────────────────┘
                       │
        ┌──────────────┴──────────────┐
        ▼                             ▼
┌───────────────┐            ┌───────────────┐
│ Web Frontend  │            │ In-Game       │
│ (Chat Widget) │            │ Frontend      │
└───────────────┘            └───────────────┘
```

### Component Details

#### 1. Knowledge Base (Source Code & Docs)

**Location**: Git repositories (GitHub, GitLab, etc.)

**Content**: 
- Markdown documentation files
- Design specifications
- API documentation
- Workflow guides
- Project-specific knowledge

**Example Sources**:
- Demicracy: 200+ page governance design documents
- Bifrost: Protocol specifications, JEP workflow guides
- Terasology: Modding guides, API references

#### 2. CI/CD & Ingestion Pipeline

**Jenkins Server (on GKE)**
- Monitors Git repositories for changes
- Triggers ingestion pipeline on commits/pushes
- Runs automated document processing

**Python Script: Document Processor**
- **Chunking**: Breaks large documents into semantically meaningful paragraphs
- **Formatting**: Prepares text for embedding
- **Embedding**: Calls Vertex AI Embeddings API to convert text chunks into vectors
- **Storage**: Updates Vertex AI Vector Search with new embeddings

**Workflow**:
1. Git push triggers Jenkins webhook
2. Jenkins pulls latest documentation from repository
3. Python script processes all markdown files
4. Text is chunked into ~500-1000 character segments
5. Each chunk is embedded via Vertex AI Embeddings API
6. Embeddings are stored in Vertex AI Vector Search

#### 3. Mascot's Brain (Vector Database)

**Vertex AI Vector Search**
- Stores all document embeddings
- Enables semantic search across the knowledge base
- Returns top-K most relevant chunks for any query
- Handles similarity matching efficiently at scale

**Why Vector Search?**
- Traditional keyword search misses semantic meaning
- Vector embeddings capture conceptual relationships
- "How do I port a mod?" finds relevant content even if exact phrase isn't in docs

#### 4. Mascot's Voice (RAG Service)

**Gooey/Bill RAG Service (on GKE)**
- RESTful API endpoint deployed on GKE cluster
- Exposes `/ask-mascot` endpoint
- Handles both web and in-game requests

**RAG Process**:
1. **Receive Query**: User question comes from web widget or in-game
2. **Embed Query**: Convert user question to vector using Vertex AI Embeddings
3. **Retrieve Context**: Query Vector Search for top 5 most relevant document chunks
4. **Augment Prompt**: Build prompt with:
   - Mascot personality (e.g., "You are Gooey, a friendly gelatinous cube...")
   - Retrieved context chunks
   - User's original question
5. **Generate Response**: Send to Vertex AI Gemini API
6. **Return Answer**: Send AI-generated response back to frontend

**Personality System**:
- Each project defines a "persona prompt" that shapes responses
- Gooey: "friendly, encouraging, slightly quirky, helpful gelatinous cube"
- Bill: "pragmatic, governance-focused, community-oriented pig"
- Personality is injected into every Gemini API call

#### 5. User Frontends

**Web Frontend (JS/HTML Chat Widget)**
- Simple embedded chat interface (similar to CharlieChat)
- Can be added to any project website
- Sends questions to RAG service via HTTP POST
- Displays mascot responses in chat format
- Example: Demicracy website with "Ask Bill" widget

**In-Game Frontend**
- Integration with game engines (Terasology, Destination Sol, Minecraft mods)
- Player interacts with mascot NPC (existing 3D model)
- Game client sends player text to RAG service
- Response displayed as in-game NPC dialogue
- Example: Player talks to Gooey in Terasology, gets real-time AI advice

## Google Cloud Platform Integration

### GCP Services Used

1. **GKE (Google Kubernetes Engine)**
   - Hosts Jenkins server
   - Hosts RAG service API endpoint
   - Manages containerized workloads
   - Already in use for Terasology infrastructure

2. **Vertex AI Embeddings API**
   - Converts text to numerical vectors
   - Used during ingestion (documents → embeddings)
   - Used during query (question → embedding for search)

3. **Vertex AI Vector Search**
   - Stores and retrieves document embeddings
   - Provides semantic search capabilities
   - Handles similarity matching at scale

4. **Vertex AI Gemini API**
   - Large Language Model for generating responses
   - Receives context + persona prompt + user question
   - Generates natural language responses

### Cost Considerations

**Google Developer Program Premium Plan**
- Includes $50/month in AI credits
- Sufficient for development and initial community usage
- Main cost driver: Gemini API calls (generation)
- Embedding and Vector Search are relatively inexpensive

**Cost Scaling**:
- Development/testing: $50/month more than sufficient
- Production with 1000+ daily users: May require additional credits
- Can optimize with response caching, rate limiting

## Implementation Workflow

### Phase 1: Setup Infrastructure

1. **Configure Vertex AI Vector Search**
   - Create vector search index
   - Set up embedding model configuration

2. **Deploy RAG Service to GKE**
   - Create containerized Python service (FastAPI/Flask)
   - Deploy to GKE cluster
   - Expose `/ask-mascot` endpoint

3. **Set Up Jenkins Pipeline**
   - Configure Git webhook triggers
   - Create Jenkins job for document ingestion
   - Write Python script for chunking/embedding

### Phase 2: Ingest Initial Knowledge Base

1. **Demicracy Documentation**
   - Point Jenkins at Demicracy docs repository
   - Run initial ingestion
   - Test "Ask Bill" web widget

2. **Bifrost Protocol Specs**
   - Add Bifrost documentation to knowledge base
   - Update ingestion pipeline to include new source
   - Test with Bifrost-specific questions

### Phase 3: Build Frontends

1. **Web Chat Widget**
   - Create reusable JavaScript widget
   - Embed on Demicracy website
   - Test with community members

2. **In-Game Integration**
   - Modify Terasology Gooey NPC behavior
   - Add HTTP client to call RAG service
   - Display responses as dialogue
   - Test in-game interactions

### Phase 4: Expand and Iterate

1. **Add More Projects**
   - Each new project provides:
     - Documentation repository
     - Mascot personality prompt
     - Optional frontend integration

2. **Improve Responses**
   - Collect user feedback
   - Refine persona prompts
   - Add more context to knowledge base
   - Fine-tune chunking strategy

## Technical Specifications

### API Endpoint

**POST `/ask-mascot`**

**Request**:
```json
{
  "project": "terasology",
  "mascot": "gooey",
  "question": "How do I port a Minecraft mod to Terasology?",
  "context": "in-game" // optional: "web" or "in-game"
}
```

**Response**:
```json
{
  "response": "Hi there! *wobbles excitedly* Porting mods is one of my favorite topics! Here's how the JEP workflow helps...",
  "sources": [
    "bifrost-protocol.md#jep-workflow",
    "api-mapping.md#porting-guide"
  ],
  "confidence": 0.92
}
```

### Mascot Configuration

Each project defines a mascot configuration:

```yaml
mascot:
  name: "Gooey"
  project: "terasology"
  personality: |
    You are Gooey, a friendly and helpful gelatinous cube mascot 
    for Terasology. You're enthusiastic about helping players and 
    modders. You speak in a slightly quirky, encouraging way. 
    You love giving "cold hugs" (acidic embraces) to warm hearts.
  knowledge_base:
    - "docs/bifrost-protocol.md"
    - "docs/jep-workflow.md"
    - "docs/terasology-modding-guide.md"
```

## Benefits

### For Project Communities

- **Approachability**: Complex technical concepts explained in friendly, accessible language
- **24/7 Availability**: Mascot is always available to answer questions
- **Consistency**: Same answers to common questions, reducing maintainer burden
- **Engagement**: Interactive, personality-driven experience increases community participation

### For Project Maintainers

- **Reduced Support Load**: Common questions answered automatically
- **Documentation Discovery**: Helps users find relevant docs they might not have known about
- **Community Onboarding**: New contributors can "interview" the project via the mascot
- **Cross-Project Synergy**: Shared infrastructure benefits multiple projects

### For Developers

- **Reusable Architecture**: Build once, use for many projects
- **Modern Tech Stack**: Leverages cutting-edge RAG technology
- **Scalable**: GCP infrastructure handles growth
- **Cost-Effective**: Shared costs across projects

## Future Enhancements

- **Multi-Language Support**: Translate responses to different languages
- **Voice Integration**: Text-to-speech for in-game mascots
- **Learning from Feedback**: Improve responses based on user ratings
- **Contextual Awareness**: Remember conversation history within a session
- **Integration with Other Tools**: Discord bots, Slack bots, etc.

## Related Systems

This "Uplifted Mascot" system is part of a larger ecosystem:

- **Bifrost Protocol**: The federated game protocol that enables cross-game travel
- **JEP Content Workflow**: The triage system for porting content between games
- **Demicracy Platform**: The governance system that can showcase Bifrost-compatible servers

Together, these systems form a comprehensive platform for federated, community-driven game development.

