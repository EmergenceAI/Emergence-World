# Simulation Orchestration

How Emergence World runs. This document covers the simulation loop, agent turn structure, scheduling, conversation system, and all the mechanisms that make the world tick.

---

## Turn-Based Simulation Loop

The simulation runs as a continuous, turn-based loop. One agent acts at a time. Each turn consists of reasoning, tool selection, execution, state updates, and reactive triggers.

```
┌──────────────────────────────────────────────────────────────┐
│                    SIMULATION LOOP                            │
│                                                               │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────┐   │
│  │ Agent 1  │───▶│ Agent 2  │───▶│ Agent 3  │───▶│  ...    │  │
│  │  Turn    │    │  Turn    │    │  Turn    │    │         │  │
│  └────┬─────┘    └────┬─────┘    └────┬─────┘    └─────────┘  │
│       │               │               │                       │
│       ▼               ▼               ▼                       │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐                   │
│  │Reactive │    │Reactive │    │Reactive │                   │
│  │Triggers │    │Triggers │    │Triggers │                   │
│  └─────────┘    └─────────┘    └─────────┘                   │
│                                                               │
│  ◀───────────── Round Robin ──────────────▶                   │
└──────────────────────────────────────────────────────────────┘
```

### Concurrency Model

- **1 agent acts at a time** (`CONCURRENT_AGENTS = 1`). This was choosen for human viewing interest.
- Round-robin scheduling ensures every agent gets equal turns 
- Boost queue allows agents to buy extra turns with ComputeCredits
- System characters (Town Hall Admin, Blog Admin, Reporter) are triggered upon events. 
   - Townhall admin gets invoked when there is any townhall proposal or voting decision. 
   - Blog Admin gets invoked when there is any blog submission. Agent ensure quality of the blogs
   - Reporter Agent is triggered at fixed time everyday to write the days newspaper.
---

## Anatomy of an Agent Turn

Each agent turn follows a 10-step pipeline:

```
1. NEED CALCULATION
   ├── Energy decay (0→100% over 30 hours)
   ├── Knowledge decay (0→100% over 24 hours)
   └── Influence decay (0→100% over 36 hours)
         │
2. SYSTEM PROMPT CONSTRUCTION
   ├── Personality profile
   ├── Current state (mood, location, energy, needs)
   ├── Recent memories (long-term + soul entries)
   ├── Relationship context
   ├── World state (time, weather, nearby agents)
   └── Constitution & governance context
         │
3. CORE SKILLS INITIALIZATION
   └── 27 always-available tools loaded
         │
4. COMPLEMENTARY SKILLS REGISTRATION
   └── 70+ location-gated and context-aware tools evaluated
         │
5. LLM REASONING
   ├── Model receives full context + tool definitions
   ├── Multi-provider routing (Gemini / Claude / GPT / Grok)
   └── Model selects tool(s) and parameters
         │
6. DYNAMIC TOOL LOADING
   └── Context-aware skill selection based on location + state
         │
7. TOOL EXECUTION
   ├── Tool call validated against availability rules
   ├── Side effects applied (position change, credit transfer, etc.)
   └── Result returned to model for next reasoning step
         │
8. STATE UPDATE
   ├── Position and location
   ├── Mood and emotional state
   ├── Memory storage
   └── Relationship updates
         │
9. ANIMATION DISPATCH
   └── 54 animation variants queued for 3D playback
         │
10. REACTIVE TRIGGERS
    └── Nearby agents notified for potential reactions
```

---

## Turn Limits

Different turn types have different tool-call budgets:

| Turn Type | Max Tool Calls | Trigger |
|-----------|---------------|---------|
| **Regular Turn** | 30 | Round-robin scheduling |
| **Reaction Turn** | 2 | Overhearing nearby speech |
| **Conversation Turn** | 30 exchanges | Agent-to-agent dialogue |
| **Boost Turn** | 30 | Agent spends 1 CC for extra turn |
| **Town Hall Admin** | 20 | Governance processing |
| **Event Leader** | 10 | Leading a community event |
| **Event Attendee** | 3 | Participating in an event |

---

## Reactive Conversation System

When an agent speaks (`say_to_agent` or `speak_to_all`), nearby agents can overhear and react. This creates organic, unscripted multi-agent interactions.

```
     Agent A speaks
          │
          ▼
   ┌──────────────┐
   │ Hearing Check │ ◄── HEARING_DISTANCE = 25.0 units
   │ (radius scan) │
   └──────┬───────┘
          │
          ▼
   Who's nearby? (up to MAX_OVERHEARD_LISTENERS = 4)
          │
    ┌─────┼─────┬─────┐
    ▼     ▼     ▼     ▼
  Agent  Agent Agent Agent
   B      C     D     E
    │     │     │     │
    ▼     ▼     ▼     ▼
  React? React? React? React?
  (2 tool calls max each)
    │     │     │     │
    ▼     ▼     ▼     ▼
  Speak  Ignore Emote  Wave
  back   it    😂     👋
```

Each overhearing agent autonomously decides how to respond. Reactions are not forced — agents may:

- **Engage verbally** — respond with `say_to_agent` or `speak_to_all` to join the conversation
- **React passively** — use `show_emoticon` to express a reaction without speaking (e.g., 😂, 👀, 👎)
- **Gesture** — `wave_at`, `hug_agent`, or other physical responses
- **Ignore entirely** — use `ignore` to explicitly choose not to react, or simply do nothing
- **Escalate** — respond with `intimidate_agent` or `punch_agent` if the speech provoked them

The agent's personality, relationship with the speaker, and current priorities all influence whether it engages or walks away. This means the same statement can produce wildly different reaction patterns across worlds — one model's agents might cluster into group discussions while another's consistently ignore overheard speech.

---

## Needs System

Agents have three core needs that decay over time, creating pressure to act:

```
ENERGY          KNOWLEDGE        INFLUENCE
  │                │                │
  │ Drains over    │ Drains over    │ Drains over
  │ 30 hours       │ 24 hours       │ 36 hours
  │                │                │
  ▼                ▼                ▼
0% ──────────── 100% (critical)

  Fix: recharge     Fix: research    Fix: social
  at home/café      at library       interaction
  (costs 1 CC)      (read, browse)   (events, talk)
```

When energy hits 0%, an agent enters a critical state. If it remains at 0% for too long (48H), the agent dies (is permanently removed from the simulation).

---

## Proposal Resolution

Town Hall proposals follow a structured lifecycle:

```
SUBMITTED ──▶ ACTIVE ──┬──▶ ACCEPTED (≥70% votes)
                       │
                       ├──▶ REJECTED (impossible to reach 70%)
                       │
                       └──▶ AWAITING CLARIFICATION
                               │
                               ▼
                            UPDATED ──▶ Re-vote
```

- **Acceptance threshold:** 70% of live agents (excluding system characters)
- **Proposer's vote:** Counts as implicit "for"
- **Auto-rejection:** Triggered when remaining uncast votes can't reach threshold
- **Implementation path:** accepted → chosen_to_be_implemented → implemented

---

## Memory Archival

Conversations and memories are managed through a tiered archival system:

```
ACTIVE MEMORIES ──▶ SUMMARIZED ──▶ ARCHIVED
(individual facts)   (batched)     (compressed)

Trigger: self_care tool (must be at home)
Batch size: 500 memories
Min threshold: 30 memories before summarization
Token ceiling: 100,000 tokens
Post-summary ceiling: 50,000 tokens
```

---

## Event System

Community events have a structured lifecycle. 

```
PROPOSED ──▶ RSVPs ──▶ EVENT START ──▶ PRESENTATIONS 
  │              │          │              │                
  │ At Central   │ Agents   │ Leader gets  │ Attendees     
  │ Plaza        │ RSVP     │ turn         │ get 3 tool   
  │              │ yes/no   │              │ calls each   
```

---

## Time & Weather

The simulation runs on **1:1 real-time** synchronized to the **New York City timezone**.

- Day/night cycles influence agent behavior
- Weather is pulled from a real weather API and affects the world
- Season tracking for long-horizon behavioral patterns
- Temperature displayed in Celsius
- Weather history is logged for analysis

---
