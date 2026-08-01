# RoamWeave — Initial Findings

## 1. Background

RoamWeave is a travel-planning product concept inspired by several agent skills that approach travel from different directions:

- `Ab4ndon/one-click-travel-skill`
- `hiyeshu/trip-map-builder`
- `alibaba-flyai/flyai-skill`

The initial review shows that “creating a travel guide” is not a single capability. It is a progressive decision-making process that begins with understanding the traveler and organizing an itinerary, and may later extend into real-time travel search and booking.

The product name is **RoamWeave** and the repository directory is `roam-weave`.

## 2. Core Product Insight

Travel planning can be divided into two major layers.

### 2.1 Itinerary planning

This layer answers:

> Once the traveler arrives, how should the trip be organized?

Its responsibilities include:

- understanding destination, dates, travelers, interests, budget, pace, and constraints;
- identifying worthwhile attractions and experiences;
- grouping locations geographically;
- assigning one main area or theme to each day;
- reducing unnecessary backtracking;
- accounting for opening hours, reservations, weather, energy, children, seniors, and luggage;
- selecting nearby restaurant candidates without distorting the route around famous restaurants;
- removing unrealistic or low-value activities;
- providing fallback options and explaining trade-offs;
- presenting the result as an itinerary, timeline, map, or shareable guide.

The value of this layer is not the amount of information collected. Its value is turning scattered travel ideas into a coherent, realistic, and adjustable structure.

### 2.2 Travel search and booking

This layer answers:

> What should the traveler actually reserve or purchase to make the trip happen?

Its responsibilities may include:

- flight and train search;
- hotel and room comparison;
- ticket and activity availability;
- baggage rules;
- cancellation conditions;
- taxes, fees, and currencies;
- location suitability relative to the itinerary;
- loyalty points and membership benefits;
- booking links and reservation status;
- total budget and outstanding booking tasks.

Unlike itinerary planning, this layer depends heavily on current prices, schedules, inventory, APIs, and platform data. Results must include their source, query time, dates, currency, tax treatment, and cancellation conditions.

## 3. The Relationship Is Progressive

The two layers should not be treated as unrelated products. They form a progression:

```text
Travel intent
    ↓
Itinerary planning
    ↓
Daily areas, route structure, and accommodation requirements
    ↓
Flight, hotel, ticket, and transport search
    ↓
Adjustment based on real prices and availability
    ↓
Final executable travel plan
```

The preferred order is:

> Establish the travel structure first, then search for products that satisfy that structure.

The opposite approach—starting with available hotels, tickets, or sponsored inventory and then assembling an itinerary around them—risks allowing platform inventory to distort the travel experience.

For example, itinerary planning may first establish that:

- most activities are concentrated in western Tokyo;
- the traveler has a young child and should avoid changing hotels;
- one day requires an early start for Disney;
- a central but quiet hotel with direct rail access is more important than being near a single attraction.

Hotel search can then use these conclusions as meaningful constraints.

## 4. A Three-Stage Mental Model

The complete travel journey can be represented as:

```text
Discover → Plan → Book
```

### Discover

Answers: **What is worth doing?**

- destination inspiration;
- attractions and neighborhoods;
- food and local experiences;
- seasonal suitability;
- family suitability;
- content research from guides, communities, and official sources.

### Plan

Answers: **How should the trip be organized?**

- daily allocation;
- geographic grouping;
- pacing;
- routing;
- prioritization and removal;
- maps and timelines;
- weather and energy alternatives.

### Book

Answers: **What should be reserved or purchased?**

- flights;
- trains;
- hotels;
- tickets;
- transport passes;
- prices and inventory;
- cancellation policies;
- booking links.

For the initial product, Discover and Plan can be combined into one planning workflow. Book should remain a later capability because it introduces real-time dependencies and substantially different reliability requirements.

## 5. Reference Repository Findings

### 5.1 one-click-travel-skill

This project aims to create a complete travel-guide artifact in one workflow. It combines maps, weather, attraction research, hotel prices, food recommendations, deep links, HTML generation, and optional deployment.

Useful ideas:

- a complete, shareable output rather than a chat-only response;
- map-first presentation;
- weather-aware itinerary adjustments;
- factual verification using official or public sources;
- structured data contracts before rendering;
- optional deployment of the generated guide.

Risks for an MVP:

- many required integrations;
- authentication and API-key setup;
- dependence on specific Chinese platforms;
- planning logic, data collection, rendering, and deployment are tightly coupled;
- “one click” can hide important decisions and uncertainty.

### 5.2 trip-map-builder

This project is closest to the desired first-stage product. It emphasizes planning from scattered user information, extracting hard constraints, grouping by area, deliberately removing unsuitable locations, researching nearby food, and creating an interactive map.

Useful ideas:

- one main area per day;
- optimize for flow rather than itinerary density;
- treat the itinerary as a reference frame rather than a strict hourly execution script;
- keep restaurants as nearby candidates rather than route anchors by default;
- explain what was removed and why;
- retain durable travel preferences without storing sensitive trip data;
- generate a mobile-friendly, self-contained map page.

This project provides the strongest methodological foundation for the RoamWeave MVP.

### 5.3 flyai-skill

This project provides structured, real-time search for flights, trains, hotels, attractions, and bookable travel products.

Useful ideas for later phases:

- natural-language travel search with structured output;
- distinct commands for different travel inventory types;
- filters for time, price, cabin, hotel category, and location;
- direct booking links;
- separation between broad discovery and specialized structured searches.

This capability belongs primarily to the Search and Book layers, not the initial planning MVP.

## 6. Product Direction

RoamWeave should initially be treated as a planning product, not as a universal travel agent.

### Product promise

> Turn scattered travel ideas and constraints into a researched, realistic, mapped, and shareable itinerary.

### Product principle

> Plan the experience first. Add inventory and booking only after the planning model is stable.

### Product name

**RoamWeave** combines:

- **Roam**: travel, exploration, and movement;
- **Weave**: combining preferences, constraints, locations, research, and routes into one coherent plan.

The name remains suitable as the product grows beyond itinerary planning because it describes the orchestration of travel decisions rather than a single feature.

## 7. Recommended MVP

The initial implementation should expose a single skill:

```text
travel-planner
```

RoamWeave is the product and repository name; `travel-planner` is the first skill or capability inside the product.

### MVP inputs

- destination or candidate destinations;
- travel dates and duration;
- origin when relevant to arrival and departure constraints;
- travelers and accessibility needs;
- children or seniors;
- interests and must-see locations;
- pace preference;
- approximate budget;
- known flight or hotel details;
- food preferences;
- mobility and transportation preferences.

The planner should work even when the user provides fragmented notes, screenshots, or partial information.

### MVP processing

1. Extract hard constraints and preferences.
2. Separate confirmed facts from assumptions.
3. Identify candidate places and required reservations.
4. Group places by geography.
5. Assign one primary area or objective per day.
6. Estimate travel and activity load.
7. Remove or defer unrealistic items.
8. Add nearby meal candidates.
9. Add weather-sensitive and fallback options.
10. Explain major planning decisions and trade-offs.
11. Generate a structured itinerary.

### MVP outputs

The first version may produce:

- a Markdown itinerary;
- a normalized itinerary data file;
- daily route summaries;
- a list of confirmed, optional, and removed locations;
- planning assumptions and unresolved questions;
- optional single-file HTML with an interactive map.

Markdown should remain the baseline output. Interactive HTML can be added after the planning schema and decision logic are stable.

## 8. MVP Planning Principles

The following principles should be explicit and testable:

1. **One primary area per day.** Avoid crossing the city repeatedly.
2. **Flow over density.** A shorter coherent itinerary is better than a large checklist.
3. **Constraints before recommendations.** Confirmed dates, hotels, bookings, and mobility needs take priority.
4. **Remove items deliberately.** State what was removed, deferred, or made optional and why.
5. **Avoid false precision.** Plans should use reasonable windows rather than pretending every trip follows an exact minute-by-minute schedule.
6. **Restaurants are usually candidates.** Do not distort a day around a famous restaurant unless the user explicitly prioritizes it.
7. **Weather and energy can override the plan.** Offer indoor, low-energy, or nearby alternatives.
8. **Separate facts from recommendations.** Opening hours, addresses, and reservation rules require verification; taste and route quality are judgments.
9. **Preserve user agency.** The system should explain choices and make the itinerary easy to revise.
10. **Do not imply booking.** The MVP may identify reservation requirements but should not claim to reserve anything.

## 9. Initial Non-Goals

The first MVP should not attempt to:

- book flights, hotels, restaurants, or tickets;
- guarantee current prices or availability;
- optimize airline points or credit-card rewards;
- maintain live inventory;
- act as a travel agency;
- depend on a specific travel marketplace;
- require multiple external API keys before producing a useful plan;
- generate a perfectly timed hour-by-hour execution script;
- cover every possible destination-specific edge case;
- automatically deploy every result to a public website.

These exclusions keep the first version focused on the most stable and reusable capability: planning quality.

## 10. Suggested Evolution

### Phase 1 — Travel Planner

```text
Research + Prioritize + Route + Present
```

Focus:

- planning method;
- itinerary data model;
- geographic grouping;
- pacing and trade-offs;
- Markdown output;
- optional map rendering.

### Phase 2 — Travel Search

```text
Planner constraints → Structured inventory search
```

Add:

- flight search;
- hotel search;
- train and transport search;
- ticket and activity search;
- normalized comparisons;
- timestamps and source attribution.

### Phase 3 — Booking Assistance

```text
Selected options → Booking checklist and handoff
```

Add:

- booking links;
- cancellation and baggage summaries;
- reservation status;
- outstanding action tracking;
- price-change rechecks;
- optional platform integrations.

### Phase 4 — RoamWeave Orchestrator

A higher-level workflow can coordinate the specialized capabilities:

```text
RoamWeave
├── travel-planner
├── travel-search
└── travel-booking
```

The orchestrator should remain lightweight. Its purpose is to decide which capability is needed and pass structured results between them, not duplicate their internal logic.

## 11. Initial Architecture Direction

A future-friendly repository may evolve toward:

```text
roam-weave/
├── README.md
├── INITIAL_FINDINGS.md
├── skills/
│   └── travel-planner/
│       ├── SKILL.md
│       ├── references/
│       ├── assets/
│       └── scripts/
├── schemas/
│   ├── traveler-profile.schema.json
│   └── itinerary.schema.json
├── examples/
└── docs/
```

For the initial repository, only the findings and overview are required. The implementation structure should be introduced after the planner’s input/output contract is defined.

## 12. Key Open Questions

The next design stage should resolve:

- What is the minimum information required before the planner can produce a useful first draft?
- How should the skill behave when dates, accommodation, or arrival times are unknown?
- Which user preferences are safe and useful to retain across trips?
- What normalized itinerary schema supports both Markdown and future map rendering?
- How should locations be scored for priority, distance, effort, and weather sensitivity?
- When should the system ask a question versus make and label an assumption?
- How should source freshness and factual verification be represented?
- What is the right balance between automatic removal and user-controlled alternatives?
- Should the first map implementation require an API key, or use an open map provider?
- What acceptance criteria distinguish a good itinerary from a merely complete itinerary?

## 13. Recommended Next Step

The next artifact should be a focused MVP specification for the `travel-planner` skill. It should define:

- user problem and target scenarios;
- input contract;
- itinerary schema;
- planning algorithm and decision rules;
- output formats;
- source and verification policy;
- privacy and memory boundaries;
- acceptance criteria;
- example prompts and expected outputs;
- explicit non-goals.

The product should not add live booking integrations until this planning workflow produces consistently useful itineraries across several distinct trip types.
