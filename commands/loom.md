# /loom — composition deck-builder (DEALER)

Operator handle for invoking compositions via `bin/loom` — the spell-book / palette / live-ribbon surface for `loa-compositions`. Persona: DEALER (matter-of-fact, no flourish, calls the play).

## Usage

```
/loom                  → show 5-card hand (recents + pinned)
/loom all              → fuzzy palette over ALL compositions
/loom <name>           → play a composition with live ribbon
/loom inspect <name>   → show the recipe yaml
/loom pin <name>       → pin to home view
/loom unpin <name>     → remove from pins
/loom recents          → invocation history
/loom --help           → full usage
```

## What this does

Shells out to `loom` (canonical `bin/loom` in this construct, symlinked to `~/.local/bin/loom` on install). The bin reads the composition registry (`LOOM_COMPOSITIONS_DIR`, defaults to `~/bonfire/loa-compositions/compositions`), dispatches via `compose-run.sh`, tails `.run/compose/<run_id>/orchestrator.jsonl` for the live ribbon.

State persists at `~/.config/loom/state.json` (recents + pinned).

## When to invoke

- Operator wants to play a composition end-to-end
- Operator asks "what compositions exist" / "what can I run"
- Operator asks to pin/promote a composition
- Operator says "fire <composition-name>" / "play <name>" / "run <name>"

## When NOT to invoke

- Authoring a NEW composition → use `the-weaver` (composing-paths skill)
- Validating composition syntax → `loa-compositions/scripts/validate.sh`
- Composing constructs into a new pack → `/create-construct`

## IP boundary

- **Public substrate**: `loa-constructs` schema + `compose-run.sh` runtime
- **This construct (the-loom)**: PUBLIC — anyone can install, bring their own compositions
- **Private moat**: `loa-compositions` registry (the curated workflows are the IP)

## Reference

- bin: `~/Documents/GitHub/construct-the-loom/bin/loom` (canonical)
- registry: `LOOM_COMPOSITIONS_DIR` (operator default `~/bonfire/loa-compositions/compositions/`)
- runtime: `compose-run.sh` from `loa-constructs`
- skill: `skills/playing-compositions/SKILL.md`
- persona: `identity/DEALER.md`

## Provenance

Designed 2026-04-28 by `composition-grok-surface` team verdict (BARTH + OSTROM + BEAUVOIR + LEXICOGRAPHER + ALEXANDER). Sibling-paired with `the-weaver` (BEAUVOIR weaves, DEALER deals). v0 prototype proven end-to-end via `loom audit-feel --dry-run` before construct apprenticeship via `/create-construct`.
