# RoamWeave

> Turn scattered travel ideas into a researched, realistic, mapped, and shareable itinerary.

RoamWeave is an evolving travel-planning project. Its first capability is a focused `travel-planner` skill that organizes destinations, constraints, preferences, research, and route logic into a coherent itinerary.

Chinese README: [README.zh-CN.md](./README.zh-CN.md)

## Product progression

```text
Discover → Plan → Refine → Present → Search → Book
```

The initial MVP focuses on **Discover + Plan + Refine + basic validation and presentation**. Real-time flight, hotel, ticket, and booking integrations will be considered only after the planning model is stable.

## Initial scope

The first version will focus on:

- extracting travel constraints and preferences;
- separating confirmed facts, assumptions, and open questions;
- grouping places geographically;
- planning realistic daily routes;
- controlling pace and unnecessary backtracking;
- explaining trade-offs and removed items;
- providing food and weather alternatives;
- producing structured itinerary data and Markdown output;
- optionally rendering an interactive itinerary map later.

## Current non-goals

The MVP should not book travel, compare live prices, automate browser sessions, depend on marketplace APIs, or imply that reservations have been made.

## Documents

- [Documentation index](./docs/README.md)
- [Initial Findings](./docs/INITIAL_FINDINGS.md)
- [Codex Handoff](./docs/HANDOFF.md)

## Repository direction

```text
roam-weave/
├── README.md
├── README.zh-CN.md
├── docs/
│   ├── README.md
│   ├── INITIAL_FINDINGS.md
│   └── HANDOFF.md
├── schemas/
│   └── itinerary.schema.json
└── skills/
    └── travel-planner/
```

The next milestone is a design-and-contract pass for `travel-planner`: skill instructions, planning references, itinerary data model, JSON Schema, Markdown templates, realistic fixtures, and a validation checklist.

## Status

Early MVP definition, focused on the `travel-planner` capability.
