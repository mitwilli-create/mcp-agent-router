# Personal OS — Agent Swarm Architecture

A multi-agent system for personal life management using the Model Context Protocol (MCP). Six specialist agents coordinated through a central MCP router with a single conversational interface.

**Status:** Architecture designed. Finance agent spun out as standalone product ([finance-cli](../finance-cli)). Wardrobe agent at ~40% completion.

---

## Problem

Life management across multiple domains — finance, health, career, wardrobe, communications, entertainment — requires constant context-switching between different tools, interfaces, and mental models. Each domain has its own data, its own logic, and its own interface. Nothing talks to anything else.

The goal: a single conversational interface that routes queries to domain-specialist agents, maintains cross-domain memory, and surfaces insights that only emerge when domains are connected.

## Architecture

### Central MCP Router

The router handles:
- **Query classification** — determining which domain(s) a query touches
- **Context assembly** — loading relevant KB context for the target domain
- **Cross-agent requests** — enabling agents to request context from other domains within defined data boundaries
- **Response routing** — returning domain-specialist responses through a unified interface

### Data Boundary Design

Cross-agent context sharing is explicit and permission-based:

| Agent | Can Request From | Cannot Access |
|-------|-----------------|---------------|
| Finance | Location (Voice) | Health records |
| Health | Fitness (Wardrobe) | Financial data |
| Wardrobe | Weather (external) | Career data |
| Career | Communications (Voice) | Medical data |

This prevents the failure mode where agents accumulate more context than they need — which degrades performance and creates privacy risks.

### Domain Agents

**Finance Agent** (spun out as [finance-cli](../finance-cli))
- Transaction categorization and analysis
- Net worth tracking
- Natural language financial queries
- Proactive alerts and pattern detection

**Health Agent** (~30% complete)
- Medical record context and appointment tracking
- Fitness and nutrition coordination
- Supplement and recovery protocol management
- Apple Health integration

**Career Agent** (~25% complete)
- Job search tracking and application status
- Interview preparation
- Referral network mapping
- Market intelligence

**Wardrobe Agent** (~40% complete)
- Photo-based clothing inventory with vision model tagging
- Weather-aware outfit recommendations (OpenWeatherMap)
- Event-context outfit suggestions
- Cost-per-wear tracking
- Garment care intelligence
- Wardrobe gap analysis

**Voice Agent** (~15% complete)
- Voice journaling with transcription
- Communication context for cross-agent routing
- Natural language input layer for the full OS

**Entertainment Agent** (~10% — deprioritized)
- Content recommendations
- Scheduling and discovery

### Single Conversational Interface

All agents are accessible through one interface. The router handles domain detection and context assembly transparently. The user asks a question; the OS figures out which agent(s) to route it to.

Example queries the system handles:
- "What should I wear to an interview tomorrow given the weather?" (Wardrobe + Career + external weather)
- "Am I on track financially this month?" (Finance)
- "What's my schedule this week and do I have anything I need to prepare for?" (Career + Calendar)

## Key Design Decisions

**Why MCP over a custom orchestration layer?**
MCP provides a standardized protocol for context sharing between language model applications. Building on MCP means the architecture is extensible — new agents can be added without redesigning the router.

**Why six specialist agents instead of one general agent?**
Specialist agents outperform general agents on domain-specific tasks. A finance agent with a structured transaction KB and evaluation layer will produce better financial insights than a general agent asked to "help with finances." The routing layer handles the coordination; the specialist agents handle the depth.

**Why explicit data boundaries?**
Agents accumulate context. Without explicit boundaries, agents become aware of more information than they need — which creates privacy risks and degrades retrieval quality. Explicit data boundary rules in the router prevent context leakage between domains.

**Why spin Finance out as a standalone product?**
Scope reduction increases build probability. The Finance agent had the clearest problem definition, the most immediate personal need, and the strongest standalone value. Spinning it out as [finance-cli](../finance-cli) allowed focused execution on one agent rather than diffuse progress across six.

## Tech Stack

- Python
- MCP Protocol
- Claude API
- Gemini API
- Google Apps Script (communications agent components)
- OpenWeatherMap API
- Apple Health (planned)
- Plaid (Finance agent)

## Repo Structure

```
personal-os/
├── README.md
├── architecture/
│   ├── mcp-router-design.md
│   ├── data-boundary-spec.md
│   └── agent-interface-spec.md
├── agents/
│   ├── finance/        → see finance-cli repo
│   ├── wardrobe/
│   ├── health/
│   ├── career/
│   ├── voice/
│   └── entertainment/
├── mcp-protocol/
│   └── router/
└── docs/
    └── cross-agent-context-spec.md
```
