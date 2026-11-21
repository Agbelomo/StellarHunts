# Extension Test Suite

Unit tests for the extension game store (`stellar-extensions/frontend/store/useGameStore.js`).

The core `frontend/store/useGameStore.js` is frozen by the extension isolation
rule and has no `setPuzzle`, `updateProgress`, or streak actions. The extension
store implements that documented surface with the same durable-progress model
and its own persist key (`stellar-extensions-game-store:v1`), so it can be
tested and adopted without modifying core frontend state.

## Running

From this directory:

```bash
npm test
```

Or from the repository root:

```bash
cd stellar-extensions && ../node_modules/.bin/vitest run --config vitest.config.mjs
```

The suite uses Vitest's `node` environment and a localStorage-compatible
in-memory harness (`tests/setup.js`), so it needs no browser or extra
dependencies.

## Coverage

| Acceptance criterion | Test group |
| -------------------- | ---------- |
| Tests initial state | `useGameStore initial state` |
| Tests set puzzle action | `setPuzzle action` |
| Tests update progress action | `updateProgress action` |
| Tests complete puzzle action | `completePuzzle action` |
| Tests persistence to localStorage | `persistence to localStorage` |
| Tests state reset | `resetProgress action` |
| Tests streak tracking in store | `streak tracking` |

The persistence tests cover both the write side (only durable fields under the
extension key, correct `version`) and the read side (a fresh store rehydrates
the same progress and streak).
