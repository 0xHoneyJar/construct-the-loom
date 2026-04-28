---
name: playing-compositions
description: Play loa-compositions via the `loom` CLI — the spell-book × deck-builder surface. Browse the 5-card hand (recents + pinned), open the full palette via fzf, invoke a composition with a live progress ribbon, inspect recipes (yaml), pin/unpin to home view. Trigger phrases include "fire <composition>", "what compositions exist", "run audit-feel", "show me the loom", "play <composition>", "pin this composition". Wraps `compose-run.sh` with operator-friendly semantics. NOT for authoring NEW compositions (use the-weaver/composing-paths) or validating syntax (use loa-compositions/scripts/validate.sh).
user-invocable: true
allowed-tools: Bash, Read
---

# playing-compositions — DEALER's primary skill

The dispatch + ribbon machinery for `loa-compositions`. Wraps `compose-run.sh` with operator-friendly semantics: a hand of 5, a palette of all, a live ribbon during play. Persona is DEALER — calm, no flourish, calls the play.

## Trigger

```
loom                  → home — 5-card hand (recents + pinned)
loom all              → fzf palette over the full registry
loom <name>           → play it · live ribbon ticks per stage
loom inspect <name>   → recipe card + full yaml
loom pin <name>       → pin to home view
loom unpin <name>     → remove from pins
loom recents          → invocation history
loom --help           → usage
```

## When to use

- Operator wants to RUN a composition end-to-end
- Operator asks "what compositions exist" / "what can I play"
- Operator says "fire <name>" / "play <name>" / "run <name>"
- Operator wants to manage their hand (pin / unpin / recents)
- Operator wants to inspect a composition's recipe before playing

## When NOT to use

- **Authoring NEW compositions** — that's `the-weaver` (composing-paths skill emits composition.yaml from validated user flows)
- **Validating composition syntax** — that's `loa-compositions/scripts/validate.sh` (schema linter, no LLM)
- **Designing the registry contents** — that's the operator + the-weaver, curated
- **Creating a NEW construct** — that's `/create-construct` (CURATOR's apprenticeship)
- **Authoring presets / intent-based-buttons** — that's the-weaver/prefilling-presets

## Output shape

When invoked successfully:

```
🃏 playing · audit-feel
📍 discovery/audit-feel
[compose-run validates, dispatches, runs stages]
🎀 stage 1 · ⏳ artisan::decomposing-feel
🎀 stage 2 · ✓ artisan::decomposing-feel · ⏳ artisan::scoring-experience
🎀 stage 3 · ✓ artisan::scoring-experience · ⏳ observer::analyzing-gaps
✓ audit-feel complete · run_id=loom-1777337719
```

When invoked on a missing composition:

```
✗ composition not found: <name>
```

When chain fails mid-stage:

```
🎀 stage 2 · ✗ artisan::scoring-experience
✗ audit-feel failed · rc=3 · run_id=loom-1777337719
```

State after each play:

- `~/.config/loom/state.json` — recents prepended (cap 20)
- `.run/compose/<run_id>/orchestrator.jsonl` — full trajectory for downstream consumers

## Anti-patterns

- **Re-implementing compose-run logic in the skill.** The substrate runtime IS compose-run.sh. This skill is the operator-facing wrapper. If a feature feels like it belongs in compose-run, propose it upstream — don't fork the dispatch.
- **Adding more than 5 cards to the hand.** The constraint IS the fun (BARTH). If the operator needs more, that's `loom all` (palette), not a bigger hand.
- **Adding fake thumbnails / mock-data to recipe cards.** ALEXANDER's material-honesty rule: the metadata IS the surface. No artificial flair, no synthesized previews.
- **Speaking in marketer voice.** DEALER is matter-of-fact. "✓ complete" not "🎉 awesome — your composition succeeded!" Banned: 'unfortunately', 'happy to', 'I would suggest', exclamation marks for things that aren't actual surprises.
- **Auto-pinning recently-played.** The pin is operator-curation, not history. Recents and pinned are different lists for a reason.
- **Silently retrying on failure.** If a composition fails, surface it. Operator decides whether to re-fire.

## Composes with

- **Upstream**: the-weaver (composing-paths emits the compositions this skill plays)
- **Downstream**: observer (KEEPER consumes the trajectory as listening signal when chains fail), construct-creator (gap-routing when a composition references a missing construct)
- **Substrate**: compose-run.sh (the actual dispatcher), composition.schema.json (the contract)
- **Lineage**: the-arcade (BARTH discipline + OSTROM ECS — substrate stays blind to UX, this skill IS the UX over the Component store)

## Reference

- bin: `~/Documents/GitHub/construct-the-loom/bin/loom` (canonical) · `~/.local/bin/loom` (symlink for operator-private use)
- registry path (operator default): `~/bonfire/loa-compositions/compositions/`
- runtime: `~/Documents/GitHub/loa-constructs/.claude/scripts/compose-run.sh`
- env overrides: `LOOM_COMPOSITIONS_DIR`, `LOOM_COMPOSE_RUN`, `LOOM_STATE`, `LOOM_HAND_SIZE`

## Provenance

Designed 2026-04-28 by `composition-grok-surface` team (BARTH + OSTROM + BEAUVOIR + LEXICOGRAPHER + ALEXANDER) verdict. v0 prototype proven end-to-end via `loom audit-feel --dry-run` (schema valid, type-compat verified, exit 0). Promoted to construct via `/create-construct` apprenticeship same day.
