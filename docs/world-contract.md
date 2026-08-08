# Freexlib World Contract

**Status:** living document · **Owner:** `freexlib-portal` repository
**Audience:** swarm-space, living-world, xshow developers

## Why this contract exists

The three Freexlib spaces converge on one idea: a world whose rules are
observable, whose actions leave consequences, and whose history can be
replayed and programmed against. Each repository reached the same architecture
independently:

- swarm-space: `swarm-core -> swarm-runner -> WASM adapter -> Canvas`
- living-world: `world-core -> world-runner -> world-bot -> world-protocol -> app`
- xshow: `rule modules -> world-engine contract -> Vue/canvas visualization`

This contract is a shared glossary and layer map. It is deliberately not a
shared codebase. Each world keeps its own authoritative engine.

## Canonical terms

| Term | Meaning | Notes |
| --- | --- | --- |
| World | The authoritative state plus the rules that mutate it. Exactly one owner per world. | `Simulation` (swarm), `World` (living), `WorldState` (xshow) |
| Entity | An identifiable object in the world with a stable id. | drone, resident, circuit part, CPU cell |
| State | A serializable projection of world/entity state. | `RenderSnapshot`, `WorldSnapshot`, `CpuSnapshot` |
| Tick | One atomic advance of the world. | `resolve_tick`, runner tick, `stepTankBattle()` |
| Observation | A restricted view an agent receives. | `swarm-core::Observation`, `world-bot` |
| Intent / Action | What an agent proposes to do. | `Intent` / `Decision`, `WorldAction`, `TankAction` |
| Rule | A deterministic function that resolves a tick from state + actions. | `resolve_tick`, `world-core` rules, `evaluateCircuit()`, CPU fetch/decode/execute |
| Event | An immutable structured fact appended to world history. | `WorldEvent`, `world.events.log`, CPU/tank traces |
| Replay | The ordered history that can reconstruct or explain a run. | `MatchEvent` + replay, history view, `WorldReplay` |
| Snapshot | A stable serialization for viewers. Viewers never mutate authority. | `RenderSnapshot`, `/api/snapshot` |
| Scenario | A seeded initial world plus goals or lessons. | match scenario, world seed/archive, lesson starters |
| Bot | A reader of `Observation` and producer of `Intent`; never owns the world. | `BaselineBot`, `world-bot`, `TankStrategy`, CPU console |
| Visualizer | Renders snapshots; never authoritative. | Canvas client, `app/`, Vue workbench |

## Canonical event shape

```json
{
  "tick": 1,
  "actor": "drone-3",
  "action": "harvest",
  "facts": ["collected crystal at (4,7)"],
  "visible_to": ["amber"],
  "consequences": ["amber score +1"]
}
```

Alignment:

- swarm-space: `WorldEvent` already carries structured facts; `MatchEvent`
  carries game-level events.
- living-world: `world.events.log` entries; character and history views are
  projections of the same stream.
- xshow: `WorldReplay` and trace records (CPU log, tank events, logic traces)
  should converge on this shape when a shared runner appears.

## Authority boundaries (always hold)

1. Exactly one authoritative engine per world.
2. Bots and players only read `Observation` / snapshots and submit
   `Intent` / actions.
3. UI and visualizers only read snapshots; they never resolve rules.
4. History is append-only and replayable.
5. Timing inside rules is deterministic (`TickBudget` / fixed cadence), never
   wall-clock.

## Change protocol

- Adding or changing a shared term updates this document (EN + ZH in sync).
- Keep the mapping table current when a repo renames a boundary crate/module.
- Cross-repo links: swarm-space `AGENTS.md`, living-world `AGENTS.md` +
  `docs/DEVELOPMENT_LOG.md`, xshow `docs/context-index.md` + `AGENTS.md`.

## Non-goals

- No shared codebase yet. Merge code only when two worlds actually need the
  same rule semantics.
- No UI unification. Canvas, React/Next, and Vue keep their own surfaces.
- No forced release coupling. Each repo ships on its own cadence; only this
  portal and the contract are shared.
