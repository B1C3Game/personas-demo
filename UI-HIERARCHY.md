# Persona Card UI Hierarchy

## Layout Structure

```
┌─────────────────────────────────┐
│  Persona Header                 │
│  ├── Name (h2)                  │
│  ├── Role                       │
│  └── Context                    │
├─────────────────────────────────┤
│  Layer 1: Summary (always)      │
│  ├── 🗣️ Soul          ▼/▶      │
│  ├── 🧠 Behaviors     ▼/▶      │
│  ├── ⚡ Key Tension    ▼/▶      │
│  └── 🔥 Friction Point ▼/▶     │
├─────────────────────────────────┤
│  [ Deep dive > ]  or            │
│  [ Minimize    ]                │
├─────────────────────────────────┤
│  Layer 2: Deep Dive (if open)   │
│  ├── 📚 Scenarios       ▼/▶    │
│  ├── 🧠 All Behaviors   ▼/▶    │
│  ├── 🎯 All Goals       ▼/▶    │
│  ├── ⚖️ Decision Criteria ▼/▶  │
│  ├── 🔄 Context Variations ▼/▶ │
│  ├── 📝 Terminology      ▼/▶   │
│  └── 📁 Evidence/Sources  ▼/▶  │
├─────────────────────────────────┤
│  📋 Schema Guide (App.vue)      │
├─────────────────────────────────┤
│  Footer note                    │
└─────────────────────────────────┘
```

## State Machine

```
layer = 1 (default)
  - 4 summary sections visible, expanded on load
  - Button text: "Deep dive >"
  - Deep dive sections: hidden

layer = 2
  - 4 summary sections: stay visible, keep current expand state
  - Button text: "Minimize" (same position, below the 4 sections)
  - Deep dive sections: visible, each collapsed by default

layer = 1 (after minimize)
  - Deep dive sections: hidden
  - Summary sections: re-expanded (via watcher)
```

## Expand/Collapse Behavior

Each section heading is a toggle (click to expand/collapse).

| Section | Default on Load | On Deep Dive | On Minimize |
|---|---|---|---|
| Soul | expanded | unchanged | expanded |
| Behaviors (top 2) | expanded | unchanged | expanded |
| Key Tension | expanded | unchanged | expanded |
| Friction Point | expanded | unchanged | expanded |
| Scenarios | - | collapsed | hidden |
| All Behaviors | - | collapsed | hidden |
| All Goals | - | collapsed | hidden |
| Decision Criteria | - | collapsed | hidden |
| Context Variations | - | collapsed | hidden |
| Terminology | - | collapsed | hidden |
| Evidence/Sources | - | collapsed | hidden |

## Data Source Mapping

| Section | JSON Path |
|---|---|
| Name | `meta.name` |
| Role | `meta.role` |
| Context | `meta.context` |
| Soul | `soul.voice`, `soul.principles[]`, `soul.quotes[]` |
| Behaviors (summary) | `mentalization.behaviors[0:2]` |
| Key Tension | `mentalization.tensions[0]` |
| Friction Point | `mentalization.frictionPoints[0]` |
| Scenarios | `scenarios[]` |
| All Behaviors | `mentalization.behaviors[]` |
| All Goals | `mentalization.goals[]` |
| Decision Criteria | `mentalization.decisionCriteria[]` |
| Context Variations | `mentalization.contextualVariations.*` |
| Terminology | `terminology[]` |
| Evidence | `evidence.sources[]`, `evidence.observations[]`, `evidence.wouldFalsify[]` |

## Component Ownership

- **App.vue**: Persona selector dropdown, schema guide section, footer note
- **PersonaReveal.vue**: Everything inside the persona card (header, sections, toggle button)

## Files

- Persona data: `public/*.persona.json`
- Persona list: `public/personas.json`
- Schema rationale: `public/schema-rationale.json`
- Component: `src/components/PersonaReveal.vue`
- Root: `src/App.vue`
