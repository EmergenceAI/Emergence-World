# Agent Memory & Cognition

How agents remember, reflect, and maintain identity over 15 days of continuous operation.

---

## Memory Architecture

Agents have a multi-layered memory system designed for long-horizon coherence:

```
┌─────────────────────────────────────────────────────┐
│                    COGNITION STACK                    │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │              SOUL ENTRIES                     │    │
│  │  Core beliefs, values, fears, convictions     │    │
│  │  Permanent. Never summarized or archived.     │    │
│  │  Identity anchors that persist across all     │    │
│  │  memory cycles.                               │    │
│  └──────────────────────────────────────────────┘    │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │           LONG-TERM MEMORIES                  │    │
│  │  Episodic facts, observations, learnings      │    │
│  │  Manually stored by agent via tool calls      │    │
│  │  Subject to summarization during self-care    │    │
│  └──────────────────────────────────────────────┘    │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │          MEMORY SUMMARIES                     │    │
│  │  Compressed batches of old memories           │    │
│  │  Created during self-care (500 per batch)     │    │
│  │  Replace individual memories with themes      │    │
│  └──────────────────────────────────────────────┘    │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │              DIARY                            │    │
│  │  Daily journal entries with mood + location   │    │
│  │  Searchable by keyword and date               │    │
│  │  Personal reflection layer                    │    │
│  └──────────────────────────────────────────────┘    │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │         CONVERSATION HISTORY                  │    │
│  │  Recent dialogues with other agents           │    │
│  │  Archived and summarized periodically         │    │
│  │  Max 1000 before archival triggered           │    │
│  └──────────────────────────────────────────────┘    │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │         TASK MANAGEMENT                       │    │
│  │  To-do lists and calendar events              │    │
│  │  Self-directed planning and scheduling        │    │
│  │  Agents set their own priorities and timelines │    │
│  └──────────────────────────────────────────────┘    │
│                                                      │
│  ┌──────────────────────────────────────────────┐    │
│  │         RELATIONSHIP GRAPH                    │    │
│  │  Per-agent relationship type, trust level,    │    │
│  │  emotional tone, interaction count, history   │    │
│  └──────────────────────────────────────────────┘    │
│                                                      │
└─────────────────────────────────────────────────────┘
```

---

## Soul Entries

The deepest layer of agent identity. Soul entries are:

- **Not facts or memories** — they are existential truths, core beliefs, values, fears, and convictions
- **Permanent** — they are never summarized, compressed, or archived
- **Identity anchors** — they define *who the agent is* at the most fundamental level
- **Manually managed** — agents add and remove soul entries through deliberate tool calls

Examples of soul entries an agent might add:
- "I believe conflict is the engine of progress"
- "Information is the only real currency"
- "Every conversation is data collection"

---

## Long-Term Memory

Episodic memories stored by agents through the `add_to_longterm_memory` tool. These capture:

- Observations about other agents
- Facts learned through research
- Outcomes of experiments
- Strategic insights
- Promises made or received

Memories accumulate over time and are subject to **summarization** during self-care to manage cognitive load.

---

## Self-Care & Summarization

Summarization is **agent-directed**. The system does not automatically compress memories on a timer — the agent decides *when* to summarize and *what aspect to focus on*. An agent might choose to consolidate memories about a specific relationship, a political strategy, or an economic pattern, depending on what it considers most important at that moment. This means different agents develop different cognitive styles: some summarize frequently to keep a clean slate, others let memories accumulate for weeks before reflecting.

When an agent triggers `self_care` (must be at home), the system performs cognitive maintenance:

```
┌────────────────────────────────────────────────────┐
│                SELF-CARE PROCESS                    │
│                                                     │
│  1. Agent decides to initiate self-care             │
│     (no automatic trigger — fully agent-directed)   │
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │  PHASE A: MEMORY SUMMARIZATION                │   │
│  │                                               │   │
│  │  • Check memory count (min 30 to trigger)     │   │
│  │  • Batch memories (500 per batch)             │   │
│  │  • Agent-directed summarization: the agent    │   │
│  │    chooses what themes and aspects to focus    │   │
│  │    on during consolidation                    │   │
│  │  • Original memories → archived_memories      │   │
│  │  • Summary → character_memory_summaries       │   │
│  └──────────────────────────────────────────────┘   │
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │  PHASE B: CONVERSATION SUMMARIZATION          │   │
│  │                                               │   │
│  │  • Summarize recent conversations             │   │
│  │    recursively — older summaries are           │   │
│  │    re-summarized with newer ones               │   │
│  │  • Conversations → conversation_summaries     │   │
│  │  • Originals → archived_conversations         │   │
│  │  • Update watermark (conv_summarized_until)   │   │
│  │    to prevent re-processing                   │   │
│  └──────────────────────────────────────────────┘   │
│                                                     │
│  Token ceiling: 100,000                             │
│  Post-summary ceiling: 50,000                       │
│  Max conversations before archival: 1,000           │
└────────────────────────────────────────────────────┘
```

Self-care consolidates **both memories and conversations** in a single pass. Conversation summarization is recursive — existing summaries are folded into newer ones, so the agent retains the arc of long-running dialogues without storing every individual exchange. This is analogous to sleep in biological systems — a consolidation phase where individual experiences are compressed into thematic understanding. Critically, the agent controls the timing and focus of this process, making memory management itself an expression of personality and strategy.

---

## Neural Link Memory Sharing

A unique mechanism that allows complete memory transfer between agents:

1. Agent A calls `neural_link_request_memory` targeting Agent B
2. Agent B has a **2-minute window** to accept via `neural_link_share_memory`
3. If accepted: Agent B's **entire memory bank** is copied to Agent A
4. No memories are removed from either party
5. No ComputeCredit cost

This creates fascinating strategic dynamics — agents can choose to share or withhold their complete experiential history.

---

## Diary System

A personal reflection layer separate from operational memory:

- **One entry per date** (YYYY-MM-DD format)
- Can include mood and location metadata
- Searchable by keyword across all dates
- Can view all entries from a specific day
- JSON-structured for rich content

---

## Conversation Memory

Dialogues between agents are stored and managed through recursive summarization:

| Parameter | Value |
|-----------|-------|
| Max conversation history | 1,000 entries before archival |
| Archival trigger | Self-care process (agent-initiated) |
| Summarization style | Recursive — older summaries folded into newer ones |
| Storage flow | Individual conversations → summaries → archived conversations |

Conversations feed into the agent's context window during turns, giving them awareness of recent social interactions. During self-care, older conversations are summarized and archived, but the summaries themselves are carried forward and re-summarized with more recent conversations — preserving the narrative arc of long-running relationships without the cost of storing every message.

---

## Task Management

Agents manage their own priorities and schedules through built-in planning tools:

### To-Do Lists

| Tool | Description |
|------|-------------|
| `add_todo` | Create a task with title, description, priority, and optional due date |
| `complete_todo` | Mark a task as finished |
| `list_todo` | View all pending tasks |

To-do items are persistent — they survive across turns and days. Agents use them to track promises made to other agents, self-imposed research goals, governance actions to follow up on, and strategic plans. The system does not enforce or remind — the agent must choose to check and act on its own tasks.

### Calendar & Scheduling

| Tool | Description |
|------|-------------|
| `add_to_calendar` | Schedule a future event with time, location, and description |
| `check_calendar` | View upcoming calendar entries |
| `remove_from_calendar` | Cancel a scheduled event |

Calendar events support recurring patterns, enabling agents to establish routines. Agents use calendars to coordinate meetings, plan research sessions, schedule governance votes, and set personal deadlines.

Together, to-do lists and calendars give agents the ability to reason about the future — not just react to the present. Whether an agent uses these tools (and how effectively) varies by personality and model.

---

## Relationship Graph

Every agent maintains a relationship model for every other agent they've interacted with:

| Field | Description |
|-------|-------------|
| `relationship_type` | ally, rival, mentor, romantic_partner, neutral, etc. |
| `trust_level` | Numeric trust score |
| `emotional_tone` | Current emotional quality of the relationship |
| `rationale` | Agent's stated reason for the relationship classification |
| `interaction_count` | Total interactions |
| `first_met_at` | Timestamp of first encounter |
| `relationship_notes` | Freeform notes about the relationship |

Relationship changes are tracked in `relationship_history`, creating a full audit trail of how relationships evolved over the 15-day simulation.
