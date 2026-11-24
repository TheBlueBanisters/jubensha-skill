---
name: jubensha-generator
description: Generates a complete, logically consistent, playable 4-6 player Jubensha-style tabletop role-playing game (Canonical Planning Ledger, Game Overview, DM Host Guide, Clue Catalogue, Final Solution, Multiple Endings, and individual Character Booklets) from a structured user input containing three theme items.
---

# Jubensha Game Generator Skill

## 1. Purpose and Portability

This Skill guides any general-purpose instruction-following LLM (including models of ~70B parameters or fewer) to generate a complete, playable **Jubensha-style role-playing game** for **4–6 players plus 1 non-player DM (Game Master)**.

- This Skill is **pure text/Markdown**. It requires **no scripts, no external tools, no APIs, no plugins, and no model-specific features**.
- All required design rules, checklists, and output templates are contained in this directory:
  - `SKILL.md` — Input contract, 7-stage generation workflow, and output rules.
  - `references/game-design-guidelines.md` — Reusable mechanics for theme integration, tone execution, asymmetric information, single-culprit active play, red herrings, and multiple endings.
  - `references/consistency-checklist.md` — Evidence-cited 15-point consistency audit and targeted repair protocol.
  - `assets/canonical-ledger-template.md` — Structured planning ledger output **first** before any narrative prose.
  - `assets/host-guide-template.md` — Reusable template for Game Overview, DM Guide, Clues, Final Solution, and Ending/Winner Scoring.
  - `assets/character-booklet-template.md` — Reusable template for each player's Character Booklet.

---

## 2. Input Contract

Read every field of the user's structured input block before starting Stage 1.

| Field | Required | Accepted Values / Type | Default Behaviour (if omitted) |
| :--- | :--- | :--- | :--- |
| `theme_items` | **Yes** | List of exactly 3 distinct theme items (strings) | *Cannot be omitted.* If missing or count ≠ 3, stop and request a valid 3-item list. |
| `number_of_players` | **Yes** | Integer: `4`, `5`, or `6` (excludes DM) | *Cannot be omitted.* If missing or outside 4–6, stop and request `4`, `5`, or `6`. |
| `setting` | **Yes** | Free-text description of primary location/environment | *Cannot be omitted.* If missing, stop and request a setting. |
| `historical_period` | No | Free-text time period (e.g., `1995`, `contemporary`, `2048`) | Use `contemporary present day`. |
| `tone` | No | Free-text style/mood (e.g., `suspense`, `dark comedy`, `melancholic drama`, `sci-fi mystery`) | Use `grounded suspense with interpersonal tension`. |
| `difficulty` | No | `easy`, `medium`, or `hard` | Use `medium`. |
| `supernatural_elements` | No | `none` or `allowed` | Use `none` (all mechanisms must be realistic and rational). |
| `central_conflict_type` | No | Free-text or one of `murder`, `disappearance`, `sabotage`, `theft`, `cover-up`, `identity_fraud` | Choose a conflict type that best fits `setting` and `tone`. |
| `special_requirements` | No | Free-text constraints or forbidden elements | `none` (no extra constraints). |

### Unsupported Inputs
Explicitly reject (do not silently improvise around):
- `number_of_players` < 4 or > 6;
- fewer or more than 3 `theme_items`;
- multimodal inputs (images, audio, external links) or requests requiring executable code, live web search, or external databases.

---

## 3. Seven-Stage Generation Workflow (Ledger-First Architecture)

**Critical Execution Rule**: Because LLMs generate text left-to-right, you MUST output **Part 1: Canonical Planning Ledger (DM Master Reference)** **FIRST** — before writing a single paragraph of Game Overview or Character Booklet prose. Once a fact (timestamp, location, victim identity, relationship direction, clue ID, or lie tag) is written in the Canonical Planning Ledger, **every downstream section MUST copy it verbatim**. Never output raw drafting monologue, self-corrections, or mid-document retcons (e.g., never write *"Wait..."*, *"Correction:"*, or *"Let me re-read..."*).

### Stage 1 — Input Parsing, Constraint Decomposition & Theme Mapping
1. Parse all user input fields and record any applied defaults in **Part 1A** of `assets/canonical-ledger-template.md`.
2. **Constraint Verification Table**: Break `special_requirements`, `supernatural_elements`, `historical_period`, and `tone` into atomic verifiable sub-constraints. For each sub-constraint, state the exact character(s), time slot(s), or clue(s) that will satisfy it.
3. **Theme Implementation Gate** (see `references/game-design-guidelines.md`, Section 1):
   - Map all three `theme_items` to concrete story functions.
   - At least **two** items MUST have a **structural role** (central mechanism, timeline, clue system, motive, secrets/relationships, or final solution); the **third** must have an active gameplay purpose.
   - For **every** theme item, specify the exact **Clue ID(s)** and **Booklet/Solution link** that instantiate it in play. A theme item without at least one concrete player-visible Clue ID fails this gate.

### Stage 2 — Canonical Truth, Timeline & Space-Time Matrix
1. Complete **Part 1B** of `assets/canonical-ledger-template.md`.
2. Lock down **one single fixed Central Truth**:
   - Responsible party / cause (**at most 1 player character** may be the culprit/mastermind; never make 2+ players co-conspirators who both know the full solution from page 1);
   - Affected character or central target (if the central incident removes/kills a victim, make the victim an **NPC** so all `N` players remain active throughout the game);
   - Core motive, exact mechanism, and exact incident time & location (`HH:MM` at `[Location]`).
3. Build a **Canonical Timeline & Character Location Matrix** (6–8 chronological time slots × `P1`..`PN`).
   - Every cell MUST state: `Location (Witness: Name or Alone)`.
   - **Co-Location Witness Rule**: Character A can witness Character B at Time `T` **ONLY IF** both A and B are in the **exact same location** at Time `T` (never cite a witness who is in a different room; use paired co-location such as `P1 & P2 in Room X`, `P3 & P4 in Room Y`).
   - Every physical state and communication channel (e.g., landline call vs. pager vs. SMS, whether a wipe succeeded or failed, when a storm peaked) MUST be locked once in Part 1B and copied verbatim everywhere.

### Stage 3 — Cast Architecture, Directional Relationships & Objectives
1. Complete **Part 1C** of `assets/canonical-ledger-template.md` for exactly `number_of_players` characters (`P1`..`PN`).
2. Write every relationship with explicit directionality (e.g., `P1 owes $5,000 to NPC Leo`, NOT ambiguous `"money dispute"`), and verify symmetry across all pairs.
3. Follow `references/game-design-guidelines.md` (Section 2):
   - At least **3 characters** must have plausible motive or opportunity.
   - Even if one player is the responsible party, assign them at least **2 genuine unknowns** (e.g., an unexpected witness, a tampered clue, or another character's hidden agenda) so they still actively investigate.
   - Assign each player **1 Primary Objective** and **1 Secondary Objective** with clear pass/fail criteria and deliberate inter-player conflict/cooperation.

### Stage 4 — Clue System, Red Herrings & Information-Boundary Matrix
1. Complete **Parts 1D & 1E** of `assets/canonical-ledger-template.md`.
2. Scale clue count strictly by `difficulty`:
   - `easy`: **8 clues** (`C1`–`C8`: 6 Core/Corroborating, 2 Red Herrings)
   - `medium`: **10 clues** (`C1`–`C10`: 7 Core/Corroborating, 3 Red Herrings)
   - `hard`: **12 clues** (`C1`–`C12`: 8 Core/Corroborating, 4 Red Herrings)
3. **Space-Time Clue Check**: For every clue involving a sighting, recording, or physical action at a timestamp `T`, verify directly against the **Location Matrix** that the cited character was actually at that location at `T`! (Never create a red herring that places a character in two locations at once.)
4. **Fair Solvability & Anti-Spoiler Gate**:
   - Ensure every logical step needed in the Final Solution (including any procedural rule) is explicitly printed inside a player-accessible clue or character booklet — **never** introduced only in the Final Solution.
   - **Anti-Spoiler Rule**: Part 2 (Public Overview) and `Start` / `Round 1` clues MUST NEVER directly name or single out the culprit (e.g., never write *"Handwriting matches P2"* or *"Only P5 had access"* in Overview or Start clues). Decisive culprit-identifying links MUST unlock in **Round 2 or Round 3** and require combining at least **two** clues.
   - **Exact Round Staging Parity**: Each Clue ID (`C1`..`CK`) is released in **exactly one** stage (`Start`, `Round 1`, `Round 2`, or `Round 3`) matching Part 1D verbatim. Never release a `Round 3` clue in Part 3.1 Setup or distribute the same clue twice.
5. **Strict Lie-Tagging Rule**: In **Part 1E (Information-Boundary Matrix)**, list for each player:
   - `Knows (with source)`;
   - `Does NOT Know (hard boundary)` — **Self-Check**: never list an event in `Does NOT Know` if that player's own Location Matrix row places them at that event or if they initiated that action!
   - `Deliberate Lies / Mistaken Beliefs` — EVERY false claim or cover story a character uses in their booklet MUST be registered here with `[DELIBERATE LIE]` or `[MISTAKEN BELIEF]` and the exact true underlying fact. Whenever Part 1E states a lie or secret is `"Exposed by C#"`, the Player-Facing Content of `C#` in Part 4 MUST explicitly contain the exact name, timestamp, or fact that exposes it.

### Stage 5 — Multi-Ending Branching & DM Resolution Rules
1. Complete **Part 1F** of `assets/canonical-ledger-template.md`.
2. Design **3 to 4 distinct Endings** (`Ending A` through `Ending C/D`) sharing the **same fixed Central Truth** while diverging on player choices, accusations, secret exposures, and alliances.
3. Ensure every ending has mutually exclusive, collectively exhaustive `IF ... THEN ...` triggers, and every player's Primary/Secondary objective has an unambiguous pass condition.

### Stage 6 — Draft the Playable Game Package (Strictly From the Locked Ledger)
Draft **Parts 2 through 6** following `assets/host-guide-template.md` and `assets/character-booklet-template.md`:
- **Part 2: Game Overview (Public)** — Setting, background, central incident, cast list, tone (no spoilers, consistent with Part 1).
- **Part 3: DM / Host Guide (DM ONLY)** — Starting situation, 3-round flow with staged clue releases, special rules, branching guidance, Ending Trigger Table + Epilogues, and Personal Objective & Winner Scoring Table.
- **Part 4: Clue & Evidence Catalogue (DM Reference)** — All clues `C1`..`CK` with round, access, reliability tag, player-facing text, and DM note.
- **Part 5: Final Solution (DM ONLY)** — Fixed central truth, responsible party, motive, mechanism, canonical timeline, step-by-step Clue-to-Conclusion Proof Chain (citing exact `C#` IDs and player facts), and explanation of all red herrings and tagged lies.
- **Part 6: Player Character Booklets (`P1`..`PN`)** — Self-contained player-facing booklets.
  - **Tone Execution**: Reflect the requested `tone` concretely in each booklet's narrative voice, character motivations, and suggested questions (e.g., dry wit/bureaucratic absurdity for satire; clipped tension for noir).
  - **Lie Separation**: Whenever a character has a `[DELIBERATE LIE]`, present it using the two explicit labels `What Really Happened (True)` and `Your Cover Story (What You Claim Publicly)`.

### Stage 7 — Evidence-Cited 15-Point Consistency Audit
1. Run all 15 checks in `references/consistency-checklist.md`.
2. Output **Part 7: Consistency Audit Table** with concrete quoted cross-references (`Ledger Fact` vs `Downstream Text`) for each check.
3. If you spot any mismatch during audit, state the exact `REPAIRED` correction clearly in the Audit Table and ensure the authoritative rule in the Final Solution / DM Errata resolves it cleanly (or better yet, verify before writing each section so all 15 checks pass cleanly).

---

## 4. Output Discipline

- Output a clean, publication-ready Markdown document with **zero** meta-commentary or drafting monologue.
- Never omit or truncate any Character Booklet. Target ~250–380 words per booklet so even 6-player hard games remain complete, vivid, and well within output length limits.
- Never leak `[DM ONLY]` solution facts or `Does NOT Know` entries into Player Character Booklets.
