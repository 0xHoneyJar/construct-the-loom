---
name: playing-compositions
description: Play loa-compositions via the `loom` CLI — the spell-book × deck-builder surface. Browse the 5-card hand (recents + pinned), open the full palette via fzf, invoke a composition with a live progress ribbon, inspect recipes (yaml), pin/unpin to home view. APPLIES THE LEGO DOCTRINE — when an operator asks for a workflow, run a fit-check (100% fire / 95% fork / 50-95% suggest extension / <50% author) BEFORE firing. Compositions are detachable, recomposable Lego blocks; no glue between studs/holes. Trigger phrases include "fire <composition>", "what compositions exist", "is there a composition for X", "run audit-feel", "show me the loom", "play <composition>", "extend <composition>", "pin this composition". Wraps `compose-run.sh` with operator-friendly semantics. NOT for authoring NEW compositions from scratch (use the-weaver/composing-paths) or validating syntax (use loa-compositions/scripts/validate.sh).
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
- Operator asks "is there a composition for X?" — apply the Lego doctrine fit-check (below)
- Operator asks "extend <composition>" — propose the fork, don't bend the original

## The Lego doctrine (load-bearing — operator-canonical, applied at every fit-check)

Per `~/vault/wiki/concepts/composable-expertise-legos.md` (operator-coined 2026-04-21, reaffirmed 2026-04-28). **Compositions are not fixed pipelines. They are Lego blocks already strung together. The art is detaching some, attaching others, suggesting new ones.**

When an operator asks for a workflow, run this fit-check BEFORE firing:

| match | move | rationale |
|---|---|---|
| 🟢 100% fit | **fire** | the existing composition covers it cleanly. dispatch and watch the ribbon. |
| 🟡 95% fit (one stage off) | **fork** — copy yaml, swap stage, save with new slug | don't bend the original. the seam is the fork. validate before firing. |
| 🟠 50-95% fit (real gap) | **suggest extension** — propose the fork yaml + name the gap to operator | the operator decides whether to ship the fork or revisit the request |
| 🔴 <50% fit (or no match) | **author new** — keep lightweight, schema-respecting | only when no existing composition is in the neighborhood |
| 🚫 chain doesn't snap (studs/holes mismatch) | **the chain is wrong-shaped** — fork. don't paper over with adapter code | no glue — if studs and holes don't fit, the composition is asking for something the constructs can't deliver |

**Standard studs** (what a stage produces): `streams.writes`, `events.emits`, declared `outputs`. **Standard holes** (what a stage consumes): `streams.reads`, `events.consumes`, declared `inputs`. Two stages snap if studs match holes.

When firing a forked or new composition, ALWAYS run `~/bonfire/loa-compositions/scripts/validate.sh` first.

## Fit-check workflow

```bash
# 1. Survey the deck
loom all                          # fzf palette over the registry

# 2. Inspect the candidate
loom inspect <slug>               # recipe card + full yaml — read inputs + chain stages

# 3. Decide per the table above:
#    - 100% fit:  loom <slug>
#    - 95% fork:  cp <slug>.yaml <new-slug>.yaml; edit; validate; loom <new-slug>
#    - 50-95% extension: surface to operator with the proposed fork yaml
#    - <50%:      surface gap; pivot to the-weaver if author-new is justified

# 4. If forked or extended: validate before firing
~/bonfire/loa-compositions/scripts/validate.sh

# 5. Fire
loom <chosen-slug>
```

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
- **Skipping the Lego fit-check.** Don't blindly fire the first matching composition. If it's 95%, fork. If it's 50%, surface the gap. The fit-check is what makes compositions a Lego system instead of a fixed-pipeline catalog.
- **Glue code between stages.** If a chain needs adapter logic between stages because the studs and holes don't match, the chain is wrong-shaped — fork or refactor the constructs upstream. The "no glue" rule is load-bearing for the Lego doctrine to hold.
- **Authoring new compositions when an existing one is 95% fit.** Forking is cheaper, traceable (`derived_from:` field), and preserves the fix-upstream path. Only author from scratch when there's no Lego-neighborhood at all.

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
