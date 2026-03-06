# Personal OS

Six specialist agents, one conversational interface, coordinated through a central MCP router. A personal life management system that actually talks to itself.

**Status:** Architecture complete. Finance agent spun out as a standalone product → [finance-cli](https://github.com/mitwilli-create/finance-cli). Wardrobe agent at ~40%.

## The problem

Managing life across multiple domains — finance, health, career, wardrobe, communications — means constant context-switching between different tools that don't talk to each other. Each domain has its own data, its own logic, its own interface.

The useful insights live across domains. Your health affects your wardrobe constraints. Your calendar affects your financial decisions. Your career stage affects everything. No single tool sees all of it.

## Architecture

### Central MCP router

The router handles four things:
- **Query classification** — which domain(s) does this touch?
- **Context assembly** — load the right KB context for the target agent
- **Cross-agent requests** — agents can request context from other domains within defined data boundaries
- **Response routing** — single interface, specialist output

### Data boundary spec

Agents share context explicitly, permission-based. They only get what they need.

| Agent | Can request from | Can't access |
|---|---|---|
| Finance | Location (Voice agent) | Health records |
| Health | Fitness data (Wardrobe) | Financial data |
| Wardrobe | Weather (external API) | Career data |
| Career | Communications (Voice) | Medical data |

Without this, agents accumulate more context than they need — degrading retrieval quality and creating privacy risks.

### The six agents

**Finance** → spun out as [finance-cli](https://github.com/mitwilli-create/finance-cli)  
Transaction categorization, net worth tracking, natural language financial queries, proactive alerts.

**Health** (~30% complete)  
Medical context, appointment tracking, fitness and nutrition coordination, Apple Health integration.

**Career** (~25% complete)  
Job search tracking, application status, interview prep, referral network mapping, market intelligence.

**Wardrobe** (~40% complete — most advanced)  
Photo-based clothing inventory via vision models, weather-aware outfit recommendations, event-context suggestions, cost-per-wear tracking, gap analysis.

**Voice** (~15% complete)  
Voice journaling, cross-agent routing, natural language input layer for the full OS.

**Entertainment** (~10% — deprioritized)  
Content recommendations and scheduling. On the backburner.

### How a query works

You ask a question. The router classifies which domain(s) it touches, assembles relevant context from the appropriate agent KBs, routes to the specialist agent(s), and returns a unified response. You don't manage which agent handles what — the router does.

Example queries:
- "What should I wear to an interview tomorrow given the weather?" → Wardrobe + Career + OpenWeatherMap
- "Am I on track financially this month?" → Finance
- "What do I have coming up this week?" → Career + external calendar

## Why spin Finance out separately

Scope is the enemy of shipped. Finance had the clearest problem definition, the most immediate personal need, and the strongest standalone value. Focused execution beats diffuse progress. The Finance CLI is real and building. The Personal OS architecture is real and waiting.

## Tech stack

- Python
- MCP Protocol
- Claude API
- Gemini API
- OpenWeatherMap API
- Plaid (Finance agent)
- Apple Health (Health agent, planned)

## Repo structure (planned)

```
personal-os/
├── architecture/
│   ├── mcp-router-design.md
│   ├── data-boundary-spec.md
│   └── agent-interface-spec.md
├── agents/
│   ├── finance/        # see finance-cli repo
│   ├── wardrobe/
│   ├── health/
│   ├── career/
│   ├── voice/
│   └── entertainment/
└── mcp-protocol/
    └── router/
```
