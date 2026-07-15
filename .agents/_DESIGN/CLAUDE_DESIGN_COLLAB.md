# Claude Design Collaboration

**Version**: v1.0.0 · **Last Updated**: 2026-07-09
**Purpose**: how Claude Code and Claude Design build a real front end together — the handoff, the seam that keeps them safe, and the loop back for 1:1-synced fixes. Distinct from the funnel: the funnel *picks* a direction; this *builds* it.
**Companions**: `.agents/_DESIGN/DESIGN_FUNNEL.md` (choosing the direction first), `.agents/_DESIGN/INTERACTIVE_DESIGN_LANGUAGE.md` (the design-system reference you hand CD), `.agents/_DESIGN/CLAUDE_DESIGN_ANIM_SITE.md` (the visual-FX recipe for animated marketing sites).

> The trigger principle: now that writing code is cheap, build the admin/creator experience with the same care as the end-user experience — treat the client like we treat the client's customers.

---

## What Claude Design is

  + Two tools, one seam
    - Claude Design (CD): builds the front end in a *sandboxed* workspace — fast, real design, self-contained vanilla HTML/CSS/JS. It is NOT the git repo and is blind to the back end; it syncs only by explicitly copying files across (GitHub is the primary channel).
    - Claude Code (CC, here): owns the back end + integration — the data contract, the database/API, the wiring, the gap reviews.

  + When to use it
    - a surface whose front end deserves real design: a client-facing app, a management dashboard, a product from scratch
    - not for a small tweak you can make directly

---

## The one invariant

Two things make the collaboration safe and repeatable across every phase.

  + **A contract seam** — CD builds "back-end-aware but back-end-untouching"
    - a `data-flow.md` contract states the entities/fields/actions; CD designs against it, does not implement it
    - the contract is realized as a `data.js` mock whose field names match the real API line-for-line, so arrays can later be swapped for real responses without touching markup
    - pin the contract EARLY (before CD builds) so the front end designs against something real, not a guess — retrofits that skip this get bumpy

  + **A file-tag taxonomy** — governs what happens to each file at handback
    - PORTABLE — pure front end (`*.html`, `*-app.js`, `portal.js`/`portal.css`); mechanically safe to drop into the repo after review
    - SEAM — the design/back-end boundary (`data.js`); port the added helpers/fields, never overwrite the real data layer with the mock rows
    - SANDBOX-ONLY — the fake-backend shim + the commented `<script>` lines that make the static files runnable in CD's sandbox; never ported

  + **The "safe to copy" trap** — load-bearing, do not skip
    - PORTABLE only means the copy won't *mechanically* break the server or build; it does NOT mean the change is back-end-neutral
    - a file changed because the UX improved — and a UX change can quietly need something new from the back end (a field to store, a validation rule, a "no end date" option, a gallery order that must persist)
    - you cannot tell which front-end changes carry a back-end need from the tag — only by reading the diff
    - so the line-by-line review of every changed file is not a mechanical safety check; it is the hunt for back-end work the front end silently created

---

## The three phases

Each phase differs only in the *starting artifact* CC sends. The seam and the tags are constant.

### Phase A — initial new-UI handoff (greenfield)

  + CC sends a code-free brief package
    - `brief.md` — the thesis + a KILL list + the per-surface layout model (author its design system from `.agents/_DESIGN/INTERACTIVE_DESIGN_LANGUAGE.md`)
    - `data-flow.md` — the literal entity/field/action contract ("design to this; do not implement it")
    - an aesthetic anchor — a `controls.html` + `tokens.css` (e.g. a funnel-winning render) to lift verbatim
    - `reference/` — annotated screenshots + a LEGEND of keep/kill targets
    - explicit boundary: this package contains NO application code; do not integrate

  + CD returns a finished, unwired front end
    - `out/` — vanilla HTML/CSS/JS on a `data.js` mock
    - `INTEGRATION.md` — the front-door gap list (file map, the design/back-end boundary, numbered gaps)
    - `README.md` — how the files fit

  + Sync anchor
    - the `data-flow.md` contract — the mock's field names match the API line-for-line; state is computed, never stored

### Phase B — mid-build gap punch-list

  + CC sends a numbered gap-list
    - `GAPS.md` — real design/behavior gaps found while building the demo, each as where / now / fix, so a fresh CD chat can fix them directly in `out/`

  + CD returns updated files + a reverse-handoff
    - `CHANGELOG_GAPS.md` — per file: what changed, which files, how — each entry PORTABLE/SEAM tagged, in dependency-aware build order
    - `OPEN_QUESTIONS.md` — files it wants back, locked decisions to confirm, still-open back-end questions

  + CC closes the loop
    - `REPLY.md` — answers each open question code-checked with path + line; corrects any wrong assumption; ships the requested live files; hands its OWN gaps back for CD to mirror
    - sync anchor: the PORTABLE/SEAM tag map + a line-by-line reconcile pass

### Phase C — return-after-implementation / 1:1 re-sync (the bug-fix loop)

The distinctive one: after CC has implemented, you go back to CD for fixes by re-mirroring reality into CD's sandbox.

  + CC sends
    - the REAL implemented files at branch HEAD, path-mirrored 1:1 into CD's sandbox (CD has no filesystem — this byte-for-byte mirror IS the sync)
    - a scoped item batch (e.g. R / M / V / P / F sections, each item `<letter>-<n>`), each with the client's verbatim intent
    - a frozen-scope guarantee — "CC is paused on all these files" so nothing shifts under CD mid-round
    - a "what I already did / what I did NOT touch" note so CD knows its mirror is still current
    - the data contract to design against

  + CD adds locally, never returns
    - the SANDBOX-ONLY shim + commented `<script>` lines + a `SANDBOX_NOTES.md`

  + CD returns
    - the changed files as byte-identical drop-ins, mapped to the item IDs
    - `CHANGELOG_GAPS.md` + `OPEN_QUESTIONS.md`

  + CC closes
    - reviews every changed file line-by-line (the trap hunt), wires the back end, tests on a live preview, reconciles docs → dev → main on sign-off

  + Sync anchors, stacked
    - 1:1 pull from HEAD, re-pulled before each round
    - explicit touched/untouched disclosure each cycle
    - the PORTABLE / SEAM / SANDBOX-ONLY tag discipline

---

## The loop — don't engineer it away

  + The reverse-handoff IS the loop
    - a fast prototype surfaces requirements you could not spec up front ("oh, it needs to work *this* way") — that is the superpower, not a planning failure
    - keep the changelog + open-questions exchange cheap and explicit, and expect at least one lap

  + The end state
    - PORTABLE files close to byte-identical on both sides, plus a lightweight record of which version is where, so "drop-in" never quietly rots into "drifted"

  + Retrofit caveat
    - replacing an existing surface is bumpier than greenfield — the front end gets prototyped before the contract is pinned, so the back end plays catch-up and bugs surface late
    - that is the situation, not the method breaking; from scratch with the contract set early, the loop is small and smooth

---

## The round-trip — pull a built site's aesthetic back into the Language

After CD builds something you like, capture its aesthetic in reusable vocabulary — so you can diverge from it next round. Paste this, filling the brackets:

```
You built [what — e.g. an immersive single-page experience for "[Project]"]. I don't need the code — I need you to describe what you actually built, as a reusable design + motion spec another designer or AI could read to rebuild the *feel* (not the content). Describe the version currently in this session, in industry-standard vocabulary, in these sections:

1. Concept / spine — the one governing idea and how it threads the page; the section order top to bottom.
2. Design system — Color (palette as tokens: background / ink / accent / muted, and what the accent does structurally); Typography (the voice system, the tension between faces, the signature title device); Texture & render (grain / paper / blur / depth); Layout grammar (the shell, the persistent chrome, the editorial devices).
3. Motion system — Easing & feel vocabulary (ease-out / languid; where any spring is reserved); Timing scale (micro-swaps / snappy UI / reveals / section settles / ambient cadences); Scroll behavior (smooth-inertial vs native; scrubbed vs fire-once; any horizontal tangent, pin, or multi-rate parallax); Choreography (the reveal-cascade order and the transition grammar between sections).
4. Signature techniques — a numbered list of the named, reusable moves you used, each with the technique name and how it's built (library/API).
5. Meaning-bound interactions — for each section, the one interaction and the idea it dramatizes.
6. Anti-slop tells — the specific craft choices that make it read as intentional, not templated.

Then, candidly:
7. What you'd push further — where the interactive design could go bolder or stranger with license.
8. What you deliberately held back — the restraint calls, and what a *different* aesthetic direction for this same content would trade them for.

Describe the vocabulary and the mechanism, not just the visible content — I'm going to reuse this language to explore a deliberately different aesthetic direction next round, so name the *feel* and the *technique*, not the surface.
```

  + Scope it to what you're keeping
    - re-skinning only? ask for just the motion, the structure, and the copy, and tell CD to skip the palette — a re-skin keeps the experience and throws out the look, so don't spend the prompt on colors you're replacing
    - capturing everything (a true teardown)? use the full prompt as written

  + What to do with the result
    - map its answers onto the axes in `.agents/_DESIGN/INTERACTIVE_DESIGN_LANGUAGE.md` — that tells you exactly which dials to change to diverge
    - feed items 7-8 into the next funnel spec's "§2 The bar" as the starting point to push *away* from

---

## Changelog

- **v1.0.0** (2026-07-09) — first version, extracted from `DEV_RULES.md` and reconciled with the everlastings `CLAUDE_DESIGN_PARALLEL_BUILD.md`. The three phases (initial handoff / mid-build gap-list / return-after-implementation 1:1 re-sync), the contract seam + PORTABLE/SEAM/SANDBOX-ONLY taxonomy, the "safe to copy" trap, the loop + retrofit caveat, and the paste-ready "describe what you built" round-trip prompt.
