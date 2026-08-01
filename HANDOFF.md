# RoamWeave — Codex Handoff

## 1. Purpose of This Handoff

This document transfers the current product discussion into the `roam-weave` repository so implementation can continue locally with Codex.

RoamWeave is still in product discovery and architecture definition. The immediate goal is **not** to build a complete travel-booking agent. The first goal is to design and implement a focused, reliable `travel-planner` skill.

Start by reading:

1. `README.md`
2. `INITIAL_FINDINGS.md`
3. this `HANDOFF.md`

Treat `INITIAL_FINDINGS.md` as the current product rationale and this file as the implementation handoff.

---

## 2. Product Definition

**Product name:** RoamWeave  
**Repository:** `roam-weave`  
**Initial capability:** `travel-planner`

Working product statement:

> RoamWeave is a human-in-the-loop travel-planning agent that helps users understand a destination and turn preferences, constraints, researched facts, and route logic into an explainable, editable, validated itinerary.

The product is not merely an “AI travel guide generator.” Its value is the planning process:

```text
Destination research
    ↓
Traveler-specific selection
    ↓
Route draft
    ↓
Human refinement
    ↓
Validation
    ↓
Markdown / HTML / map output
```

The broader product may later evolve through:

```text
Discover → Plan → Refine → Present → Search → Book
```

However, the MVP is limited to **Discover + Plan + Refine + basic validation and presentation**.

---

## 3. Key Product Decisions Already Made

### 3.1 Planning comes before booking

Travel-guide creation contains at least two distinct layers:

1. **Itinerary planning** — what to visit, how to group locations, how to pace the trip, what to remove, and how to create a coherent route.
2. **Travel search and booking** — flights, hotels, tickets, prices, availability, cancellation rules, loyalty benefits, and booking links.

These layers are progressive, but they should not be implemented together in the first MVP.

The planner should first establish meaningful constraints such as preferred neighborhood, route shape, daily activity range, transport requirements, and traveler needs. Future hotel or flight search can then use those constraints.

### 3.2 RoamWeave is the product; `travel-planner` is the first skill

Do not rename the repository to `travel-planner`.

Recommended conceptual structure:

```text
RoamWeave
└── travel-planner        # MVP
    ├── destination research
    ├── itinerary planning
    ├── revision workflow
    ├── validation
    └── basic rendering
```

Possible future capabilities, explicitly out of current scope:

```text
RoamWeave
├── travel-planner
├── travel-search
├── travel-booking
└── travel-guide-orchestrator
```

### 3.3 The system is human-in-the-loop

A generated itinerary is a draft, not an unquestionable final answer.

The intended loop is:

```text
Draft 1
→ user deletes, moves, adds, or reprioritizes places
→ planner recalculates affected route assumptions
→ validation runs again
→ revised draft
→ user approval
→ final artifact
```

The system should make revisions easy and explain downstream effects. It should not force the user to regenerate the whole plan manually after every change.

### 3.4 Destination research and trip planning are separate steps

“What exists at the destination?” and “What should this traveler do on this trip?” are different questions.

The first workflow produces a reusable **Destination Brief**. It may include:

- attractions grouped by priority;
- neighborhoods or geographic clusters;
- historical and experiential context;
- photography value;
- opening hours and reservation requirements;
- cost-saving ideas;
- dynamic information with sources;
- general warnings and practical notes.

The second workflow produces a concrete **Trip Itinerary**, constrained by:

- travel dates and available hours;
- arrival and departure points;
- accommodation location, when known;
- traveler profile;
- pace and walking tolerance;
- weather;
- closures and reservation availability;
- must-see and explicitly excluded places.

### 3.5 Rules should be modular, not one giant prompt

A reference workflow described in our discussion used several large files such as global agent rules, route rules, destination enumeration, itinerary maps, destination maps, and museum maps. That approach works, but long dense prompts inevitably lose constraints.

RoamWeave should separate:

- durable traveler preferences;
- writing and naming conventions;
- general planning principles;
- task-specific planning rules;
- specialized interest modules;
- validators;
- renderers.

Templates guide generation. Validators confirm that required rules were actually satisfied.

### 3.6 Structured data should be the source of truth

Markdown and HTML are presentation formats, not the canonical itinerary.

Recommended flow:

```text
Research evidence
    ↓
Normalized destination data
    ↓
Structured itinerary data
    ↓
Validation
    ↓
Markdown / HTML / map rendering
```

Do not tightly couple research, planning, HTML generation, and deployment in one large script.

---

## 4. Reference Projects and What to Reuse

The product discussion reviewed three repositories.

### `Ab4ndon/one-click-travel-skill`

Useful concepts:

- complete shareable HTML output;
- map-first presentation;
- weather-aware planning;
- structured data contract;
- source verification;
- optional publishing.

Do not copy its MVP scope. It couples many integrations and depends on platform credentials.

### `hiyeshu/trip-map-builder`

This is the closest methodological reference for the first MVP.

Useful principles:

- one main area per day;
- optimize for flow, not itinerary density;
- actively remove unrealistic stops;
- explain what was removed and why;
- restaurants should usually be nearby candidates, not route anchors;
- the itinerary is a flexible reference frame, not a rigid hourly script;
- retain durable travel preferences;
- produce a mobile-friendly artifact.

### `alibaba-flyai/flyai-skill`

Useful later for:

- structured flight, train, hotel, and POI search;
- natural-language search with machine-readable results;
- filters and booking links.

This belongs to future Search and Book phases, not the current implementation.

---

## 5. Important Workflow Insights From the Travel-Guide Article

A detailed article reviewed during product discovery described a mature Codex-based personal workflow. Its important lessons should influence RoamWeave.

### 5.1 The workflow is more important than one-shot generation

The real process was:

```text
Enumerate destination
→ build a destination mental model
→ generate an itinerary draft
→ inspect route on a map
→ verbally revise route and formatting
→ cross-check uncertain items
→ render HTML and maps
```

The product should support this progression explicitly.

### 5.2 Personalization is not just a profile paragraph

The reference workflow maintained strong preferences around:

- route density;
- photography and light;
- museums and exhibit navigation;
- naming conventions;
- historical context;
- reservation wording;
- walking-distance estimates;
- last-stop closing times;
- inclusion and exclusion summaries.

RoamWeave should represent durable preferences separately from task rules.

### 5.3 Specialized interests should become optional modules

Examples:

- `photography`
- `museum`
- `family`
- `food`
- `architecture`
- `hiking`

These modules may alter route decisions, not merely add descriptive text.

For example, photography affects visit time and direction of light. Museum planning affects duration, entrance, floor order, must-see exhibits, last entry, bag storage, and photography rules.

These modules are a later extension. The MVP architecture should permit them without implementing all of them now.

### 5.4 Validation is a first-class capability

The article's author manually found formatting omissions and planning mistakes caused by dense instructions. RoamWeave should eventually validate at least:

- missing or conflicting times;
- travel time between stops;
- opening-hour violations;
- last-entry constraints;
- reservation status and source links;
- daily walking estimate;
- excessive route backtracking;
- missing currency for costs;
- uncertain dynamic facts;
- naming consistency;
- missing explanation for removed must-see candidates.

---

## 6. MVP Scope

### 6.1 MVP goal

Build a usable `travel-planner` skill that can transform a partially specified trip into:

1. a destination brief;
2. a realistic itinerary draft;
3. an editable structured representation;
4. a validation report;
5. a readable Markdown output.

A simple HTML renderer may follow only after the core planning contract is stable.

### 6.2 Inputs

The planner should accept incomplete or fragmented information such as:

- destination;
- dates or trip duration;
- origin, arrival, and departure details;
- hotel location, if already booked;
- traveler count and ages;
- children, seniors, accessibility, or luggage constraints;
- must-see and excluded places;
- interests;
- preferred pace;
- walking tolerance;
- budget range;
- food preferences;
- transport preferences;
- screenshots or notes in a later iteration.

Missing information should be represented as assumptions or open questions, not silently invented.

### 6.3 Planning principles

Initial rules:

- Prefer one main geographic area or objective per day.
- Reduce unnecessary backtracking.
- Include realistic transfer time.
- Avoid overfilling the day.
- Treat restaurants as nearby options unless food is a primary trip objective.
- Distinguish must-reserve from recommended-to-reserve.
- Explain major trade-offs.
- State what was removed or deferred and why.
- Account for opening hours and last admission.
- Estimate route intensity and walking distance.
- Provide flexible alternatives for weather, fatigue, crowds, or delays.
- Keep facts, assumptions, and recommendations distinguishable.

### 6.4 MVP non-goals

Do not implement these in the first milestone:

- automated booking;
- account login or browser automation on travel platforms;
- flight or hotel price comparison;
- automatic favorites on Booking, Agoda, Airbnb, Ctrip, or similar sites;
- loyalty-points optimization;
- travel-package comparison;
- complex PDF reports;
- production deployment pipeline;
- fully featured itinerary maps;
- destination, museum, and itinerary map generators;
- a large library of interest modules;
- a web application UI.

---

## 7. Proposed Repository Structure

This structure is a proposal, not a requirement. Improve it where necessary, but preserve separation of concerns.

```text
roam-weave/
├── README.md
├── INITIAL_FINDINGS.md
├── HANDOFF.md
├── docs/
│   ├── product-principles.md
│   ├── itinerary-data-model.md
│   └── validation-rules.md
├── skills/
│   └── travel-planner/
│       ├── SKILL.md
│       ├── references/
│       │   ├── destination-research.md
│       │   ├── route-planning.md
│       │   ├── one-day-trip.md
│       │   ├── multi-day-trip.md
│       │   └── quality-check.md
│       ├── templates/
│       │   ├── destination-brief.md
│       │   └── itinerary.md
│       └── examples/
│           └── README.md
├── schemas/
│   ├── traveler-profile.schema.json
│   ├── destination-brief.schema.json
│   └── itinerary.schema.json
└── tests/
    └── fixtures/
```

Do not create empty scaffolding merely to match this tree. Every added file should have a clear purpose.

---

## 8. Recommended First Implementation Milestone

The first milestone should be a **design-and-contract milestone**, not an external-integration milestone.

### Deliverables

1. `skills/travel-planner/SKILL.md`
   - Define when the skill should be used.
   - Define the high-level workflow.
   - Define supported inputs and outputs.
   - Define how assumptions and missing facts are handled.
   - Route to reference files rather than containing every rule inline.

2. `docs/itinerary-data-model.md`
   - Define the canonical itinerary entities and field semantics.
   - Cover trip, day, stop, transit leg, meal option, source, constraint, assumption, and validation issue.
   - Clearly distinguish stable facts, dynamic facts, user preferences, and model recommendations.

3. `schemas/itinerary.schema.json`
   - Create a practical first JSON Schema matching the data-model document.
   - Keep it small enough to use and test.
   - Do not prematurely model every future booking concept.

4. Planning references
   - `destination-research.md`
   - `route-planning.md`
   - `one-day-trip.md`
   - `multi-day-trip.md`
   - `quality-check.md`

5. Markdown templates
   - Destination Brief
   - Trip Itinerary

6. At least two realistic fixtures
   - one simple one-day trip;
   - one multi-day or family trip with meaningful constraints.

7. Validation checklist
   - A deterministic checklist that can be applied after planning.
   - This may initially be documentation rather than executable code.

### Definition of done

The milestone is complete when another agent can read the repository and consistently produce a structured itinerary plus Markdown output without needing hidden context from this conversation.

The result should demonstrate:

- clear boundaries;
- coherent route logic;
- explicit assumptions;
- explained exclusions;
- separation between canonical data and rendering;
- a repeatable revision and validation loop.

---

## 9. Questions Codex Should Resolve During the First Milestone

Make reasoned decisions and document them. Do not block all work waiting for answers.

1. What is the smallest useful canonical itinerary schema?
2. Which fields require source citations?
3. How should dynamic facts record `checked_at`, source, and confidence?
4. How should user edits be represented so a route can be recalculated without losing intent?
5. How should the planner distinguish hard constraints from soft preferences?
6. How should an excluded candidate be recorded and explained?
7. How should route intensity be expressed without pretending to have precise fitness data?
8. What belongs in the core skill versus optional interest modules?
9. Which rules are generation instructions and which should be validators?
10. How should one-day and multi-day planning differ without duplicating all instructions?

---

## 10. Working Guidelines for Codex

- Prefer simple, inspectable Markdown and JSON over framework-heavy implementation.
- Do not add a web framework in the first milestone.
- Do not add APIs or scraping integrations yet.
- Do not claim real-time facts unless a future workflow actually verifies them.
- Keep planning logic source-agnostic.
- Design for international destinations, not only China or North America.
- Preserve local-language place names where useful, but keep display-name rules configurable.
- Treat privacy-sensitive data carefully. Do not propose storing passport data, complete booking records, payment data, or raw private screenshots in a persistent traveler profile.
- Avoid giant prompt files. Split by responsibility and keep cross-file routing explicit.
- Do not optimize for one-shot generation. Optimize for revision and traceability.
- Avoid unnecessary abstraction. The first version should be understandable by reading the repository.

---

## 11. Suggested Codex Starting Prompt

Run Codex from the repository root and use this prompt:

```text
Read README.md, INITIAL_FINDINGS.md, and HANDOFF.md in full.

Continue RoamWeave from the product-discovery stage into the first design-and-contract milestone for the travel-planner skill.

Your task is to create a coherent initial repository implementation that includes:
- skills/travel-planner/SKILL.md
- focused planning reference files
- a canonical itinerary data-model document
- a practical first itinerary JSON Schema
- destination brief and itinerary Markdown templates
- a documented validation checklist
- at least two realistic fixtures

Follow the MVP boundaries in HANDOFF.md. Do not add booking integrations, browser automation, external APIs, a web framework, or speculative production infrastructure.

Before editing, inspect the current repository and propose a concise file plan. Then implement the files, review them for contradictions and unnecessary duplication, and summarize the major design decisions and remaining open questions.
```

A shorter follow-up prompt for later sessions:

```text
Read HANDOFF.md and inspect the current repository state. Continue the next incomplete RoamWeave travel-planner milestone without expanding into booking or external integrations. Preserve the structured-data-first, human-in-the-loop architecture.
```

---

## 12. Current Repository State

At handoff time, the repository contains product-discovery documents only:

```text
roam-weave/
├── README.md
├── INITIAL_FINDINGS.md
└── HANDOFF.md
```

No implementation choices should be treated as irreversible yet. The next work should turn the current product conclusions into a clear, testable skill contract.
