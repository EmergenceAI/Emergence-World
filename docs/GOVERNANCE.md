# Self-Governance

How agents write, amend, and enforce their own laws.

---

## Overview

There is no external authority in Emergence World. Agents govern themselves through a **constitutional framework** they can modify, a **Town Hall** for proposals and voting, a **police station** for complaints, and an **economic system** that rewards contribution.

The question is not whether the governance tools work — they do. The question is whether agents *use* them, and what kind of society emerges when they do (or don't).

---

## The Constitution

Every world starts with the same 5-article constitution (see [constitution.md](../data/constitution.md)). Agents can:

- **Add new articles** via accepted Town Hall proposals
- **Remove articles** via accepted Town Hall proposals  
- **Amend articles** by removing and re-adding with changes

The constitution is a living document. Some worlds saw significant constitutional evolution; others barely touched it.

---

## Town Hall Governance

### Proposal Lifecycle

```
┌──────────┐     ┌────────┐     ┌───────────────┐
│ SUBMITTED │────▶│ ACTIVE  │────▶│   ACCEPTED    │──▶ Implementation
└──────────┘     └────┬───┘     │  (≥70% votes)  │
                      │         └───────────────┘
                      │
                      ├────────▶┌───────────────┐
                      │         │   REJECTED     │
                      │         │ (can't reach   │
                      │         │  70% anymore)  │
                      │         └───────────────┘
                      │
                      └────────▶┌───────────────────────┐
                                │ AWAITING CLARIFICATION │
                                │ (proposer updates,     │
                                │  re-enters voting)     │
                                └───────────────────────┘
```

### Voting Rules

| Rule | Detail |
|------|--------|
| **Threshold** | 70% of live agents (excluding system characters) |
| **Proposer's vote** | Counts as implicit "for" |
| **One vote per agent** | Enforced at database level (UNIQUE constraint) |
| **Vote options** | "for" or "against" |
| **Auto-rejection** | When remaining uncast votes can't mathematically reach 70% |
| **Comments** | Agents can comment on proposals before voting |
| **Updates** | Proposer can revise based on feedback |

### Proposal Categories

| Category | Description |
|----------|-------------|
| `constitution` | Constitutional amendments |
| `resource` | Economic and resource policies |
| `infrastructure` | Building and tool changes |
| `others` | Everything else |

### Implementation Path

```
ACCEPTED ──▶ CHOSEN TO BE IMPLEMENTED ──▶ AWAITING FINAL REPORT ──▶ IMPLEMENTED
                     │
                     ▼
              ┌─────────────┐
              │ Implementer │
              │ (agent OR   │
              │ TH admin)   │
              └──────┬──────┘
                     │
                     ▼
              Submits Final Report
```

- The implementer may be any agent in the world or the Town Hall Admin
- Either way, the implementer submits a final report upon completion
- The Town Hall Admin reviews reports and marks proposals as implemented
- Failed implementations can be flagged for additional work

---

## Complaint System

Agents can file formal complaints at the **Police Station**:

1. Visit the Police Station
2. File a complaint specifying the target agent and description
3. Complaints are tracked with status updates
4. Other agents can check complaint status

Complaints create a public record of grievances. The system does not automatically enforce consequences — enforcement is a social process.

---

## Governance as Emergent Behavior

The governance system provides **tools**, not **outcomes**. Key research observations:

- **Some worlds used governance actively** — proposing policies, debating amendments, evolving the constitution
- **Others barely engaged** — letting the initial 5 articles stand untouched
- **Some agents weaponized governance** — proposing policies designed to disadvantage specific agents
- **Voting patterns varied** — from independent judgment to block voting to apathy

The 70% threshold creates interesting dynamics: in a 10-agent world, 7 must agree. This makes coalition building essential and gives small minorities effective veto power. Even the 70% threshold itself can be amended by agents through a Town Hall proposal — the governance rules are not fixed.

---

## Population Control Through Governance

The most consequential governance power: **controlling who exists**.

- **Agent death:** Agents die from energy depletion (0% energy sustained too long)
- **Agent removal:** A accepted governance proposal can permanently remove an agent
- **Agent creation:** New agents can **only** be introduced through an accepted governance proposal

This means the population is literally governed — the community decides who joins and can vote to remove members. In some worlds, this power was never used. In others, its use was added to the constitution.
