# Apocalypter Technical Wiki — Coding Agent Guide

## Purpose

Use this technical wiki as the first reference when writing, reviewing, or debugging Apocalypter mods.

The documentation distinguishes direct vanilla evidence from derivation. Do not silently promote an inferred value into a verified game fact.

## Stable lookup convention

Use:

`<GameObject path> :: <FSM name>`

Examples:

- `__GameManager__ :: Hunger`
- `__GameManager__ :: Thirst`
- `__GameManager__ :: Fatigue`
- `__GameManager__ :: Sanity`
- `Player :: Sleep`
- `Player :: Health`
- `Player :: InCar`
- `PlayerCamera :: UseBed`
- `SaveLoadGame :: SaveLoadGame`

## Evidence labels

- **verified** — directly represented in supplied vanilla runtime or serialized data.
- **derived** — arithmetic or direct interpretation of verified values.
- **needs-verification** — runtime semantics, missing detailed action parameters, or incomplete serialized decoding prevent a definitive claim.

Runtime variable values are snapshots and are not automatically default values.

## Important semantic convention

The engine/global variable is named `Sanity`, but high values are harmful and the base FSM reduces the value toward zero. In design prose, this wiki therefore calls the mechanic **Insanity** while preserving `Sanity` as the implementation identifier.

## Resolution order for modding questions

1. Read `technical-wiki/index.html`.
2. Search `technical-wiki/facts.jsonl` for normalized facts.
3. Identify the exact GameObject/FSM pair.
4. Check the supplied runtime `fsm_detail.txt` before patching action values or action order.
5. Use the broad `fsm_dump.txt` to find related objects, states and action types.
6. Use the offline serialized scan to find FSMs that were not instantiated in the runtime snapshot.
7. Use managed assembly inspection for behavior implemented in ordinary C# rather than PlayMaker.

## High-confidence survival hooks

| Goal | Target |
|---|---|
| Hunger rate / thresholds | `__GameManager__ :: Hunger` |
| Thirst rate / thresholds | `__GameManager__ :: Thirst` |
| Fatigue / forced sleep | `__GameManager__ :: Fatigue` |
| Insanity recovery / thresholds | `__GameManager__ :: Sanity` |
| Insanity mutant spawn loop | `__GameManager__ :: SanityMutantSpawn` |
| Sleep transaction | `Player :: Sleep` |
| Bed interaction | `PlayerCamera :: UseBed` |
| Death / health state | `Player :: Health` |
| Player vehicle transition | `Player :: InCar` |
| Global save/load | `SaveLoadGame :: SaveLoadGame` |

## Key verified survival facts

- Hunger: +0.01/s; warning >80; dying >99.9; dying applies Health -0.1/s.
- Thirst: +0.02/s; warning >80; dying >99.9; dying applies Health -0.1/s.
- Fatigue: +0.02/s; warning >80; >99.9 initiates sleep and requests SleepTime=6.
- Sanity/Insanity: -0.05/s toward zero; display >20; warning >80; sanity mutant spawn gate >90; dying >95; dying applies Health -0.35/s.
- Sleep activation: Fatigue -100, Sanity -30, Hunger +10, Thirst +15, Health +40, Drunkenness -100, weed/alcohol effects disabled.

## Modding practice

Prefer the narrowest responsible FSM. Before changing a PlayMaker action, inspect the full state and its action order; several actions in one state may depend on values written earlier in the same state.

For items and loot, do not assume an item's restoration value or a spawner's effective probability unless detailed action parameters or serialized object data support the number.
