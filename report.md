# Group Report: Designing a Reusable LLM Skill for Generating Jubensha-Style Role-Playing Games

---

## Q1. Design Philosophy

### Target Game Type
Our Skill (`jubensha-generator`) is designed to generate complete, table-ready **4–6 player Jubensha-style tabletop role-playing games** run by **1 non-player DM (Game Master)**. Every generated scenario is structured around a single fixed, logically discoverable central truth (such as a disappearance, sabotage, murder, cover-up, or identity fraud), asymmetric private character booklets, a 3-round staged clue-release system, conflicting and overlapping personal objectives, and 3–4 deterministic multiple endings that change the consequences of player choices without altering the underlying historical facts.

### Guiding Design Principles
Four engineering principles guided the Skill's architecture:

1. **Ledger-First, Prose-Second, Audit-Last**: The primary failure mode of LLMs on multi-document mystery generation is *autoregressive drift*—writing atmospheric narrative before locking down timestamps, locations, and clue dependencies. Our Skill forces the model to emit a compact, tabular **Canonical Planning Ledger (Part 1)** *before* generating a single sentence of public overview or character booklet prose.
2. **Separation of Canonical Truth and Player Belief**: A playable Jubensha requires unreliable testimony without accidental contradictions. Our Skill separates objective truth (Part 1B Space-Time Matrix), character knowledge boundaries (Part 1E `Knows` vs. `Does NOT Know`), and intentional deception (`[DELIBERATE LIE]` and `[MISTAKEN BELIEF]` tags).
3. **Structural Theme Integration Over Decoration**: Every input theme item must pass a **Theme Implementation Gate** binding it to concrete player-visible Clue IDs (`C1`..`CK`) and deduction steps, ensuring at least two items perform structural roles (timeline, mechanism, clue system, motive, or solution) and the third creates an active gameplay hook.
4. **Pure-Text Portability for ≤70B General-Purpose LLMs**: To satisfy model portability and zero-dependency compliance, all logic resides in six concise Markdown files (`SKILL.md`, two `references/`, and three `assets/`). No scripts, external tools, APIs, or vendor-specific features are used. Every template uses compact Markdown tables that general-purpose 27B–70B instruction-following models can track reliably inside a single context window.

### Information Established Before Writing Prose
Before drafting any narrative text, Stages 1–5 lock down six canonical structures in Part 1:
- **1A. Input Summary, Atomic Constraint Verification Table, and Theme Implementation Gate**;
- **1B. Fixed Central Truth, Physical States, and Chronological Space-Time Location Matrix** (`Time × Player → Location + Witness`);
- **1C. Cast Roster, Explicit Directional Relationships (`A -> B`), Secrets, Suspicion Hooks, and Primary/Secondary Objectives**;
- **1D. Difficulty-Scaled Clue & Red-Herring Ledger** (8 / 10 / 12 clues) with round-release tags and matrix-verified timestamps;
- **1E. Information-Boundary & Lie-Tagging Matrix** (1-to-1 registry of every cover story and the exact clue that exposes it);
- **1F. Mutually Exclusive Ending Triggers (`IF ... THEN ...`)**.

### Why This Generation Order Matters & How Long-Form Complexity Is Managed
Because decoder-only LLMs generate tokens strictly left-to-right, placing the public overview before the planning ledger causes the model to invent facts mid-paragraph and later contradict or retcon them (a failure we directly observed in v1 Test 2). Emitting the Canonical Ledger first turns the model's own generated prefix into an immutable lookup table for Parts 2–6. To prevent token exhaustion or booklet truncation on 6-player games, the Skill enforces compact tabular planning in Part 1 and targeted length budgets (~250–380 words per booklet), keeping complete 4–6 player packages within 4,500–7,500 words—well inside standard 16k output budgets.

### Going Beyond a Simple One-Shot Prompt
A naive prompt like *"Write a mystery game for five players"* typically produces a short story with vague clues, flat characters who all share the same goal ("find the killer"), an unplayable DM section, and timeline contradictions across booklets. Our Skill instead acts as a **structured specification compiler**: it validates an explicit input contract, decomposes constraints into atomic checks, builds a cross-referenced knowledge and space-time ledger, gates spoilers out of early rounds, binds deterministic scoring rules for winners, and concludes with an evidence-cited 15-point consistency audit.

---

## Q2. What Makes It Fun?

Rather than relying on literary flair alone, `jubensha-generator` embeds repeatable structural mechanics in `SKILL.md` and `references/game-design-guidelines.md` so that *every* generated game—regardless of setting, tone, or player count—produces active social deduction and interpersonal drama:

1. **Asymmetric Information & Forced Interaction**: Every player booklet is generated with at least one piece of private information another player needs and 2–3 targeted questions under *Reasons to Interact*. No single player begins with enough information to reconstruct the timeline alone, making conversation, questioning, and selective clue trading mechanically necessary.
2. **Distributed Suspicion & Active Play for Every Seat**: The Skill mandates that at least **3 of the 4–6 characters** carry credible suspicion hooks (alibi gaps, hidden debts, tampered logs, or suspicious items). Innocent players are given personal secrets they want to conceal, creating genuine reasons to deflect or bargain. Crucially, our **Single-Culprit & Culprit-Unknowns Rule** limits full prior knowledge of executing the central incident to at most *one* player (preventing multi-culprit coalitions from knowing the whole story on page 1) and forces the Skill to give even the responsible player **at least two genuine unknowns** (e.g., who sent an anonymous message or what evidence survived) so they must actively investigate rather than merely playing passive defense.
3. **Conflicting & Overlapping Personal Objectives**: Each player receives a **Primary Objective (2 pts)** and **Secondary Objective (1 pt)** drawn from five mixed archetypes (*Truth-seeker, Protector, Self-preservation, Opportunist/Retriever, Reconciler/Persuader*), with explicit rules requiring at least two competing pairs and two potentially cooperative pairs. This ensures alliances shift dynamically as secrets surface.
4. **Staged 3-Round Revelations & Anti-Spoiler Gating**: Gameplay escalates across Round 1 (surface alibis and initial anomalies), Round 2 (secret pressures and contradictory clues), and Round 3 (decisive evidence that reframes earlier assumptions). Our **Anti-Spoiler Gate** forbids the Public Overview and `Start`/`Round 1` clues from naming or directly singling out the culprit, ensuring decisive forensic links require combining at least two clues in Rounds 2–3.
5. **Fair Deception, Cover Stories, and Red Herrings**: Character booklets separate *What Really Happened (Private Truth)* from *Your Cover Story (What You Claim Publicly)* whenever a character has a `[DELIBERATE LIE]` tag, giving players actionable role-play guidance while ensuring every lie can be caught by a specific clue (`Exposed by C#`). Red herrings are constrained to be timeline-compatible—misleading in interpretation rather than physically impossible.
6. **Concrete Tone Execution & Deterministic Multi-Ending Stakes**: The Skill instructs the model to express `tone` directly inside clue text and character dialogue prompts (not merely as a header label). Finally, 3–4 mutually exclusive `IF ... THEN ...` endings paired with a deterministic Personal Objective Scoring Table allow games to conclude with a single winner, multiple winners, or no clear winner depending on player choices, accusations, and alliances.

---

## Q3. Game Concept and Theme Design

Selected Theme Set: 15 — Duty Rota / Unknown Number / One Person Too Many

### Target Game Concept (Specification for Our Final Input)
This section defines the conceptual target we want our Skill to realize from our final structured input (`submission/final_input.md`):

- **Proposed Title**: *The Meridian Crossing* (a working concept title for an overnight fogbound ferry mystery)
- **Setting**: An overnight cross-channel cargo-and-passenger ferry (*MV Meridian*) halted mid-channel in dense winter fog, cutting off immediate external rescue and locking crew and passengers into a tight, enclosed space.
- **Genre**: Contemporary claustrophobic social-deduction suspense (non-supernatural disappearance mystery).
- **Central Premise**: Halfway through a routine winter crossing, the ferry halts in zero-visibility fog and a headcount discrepancy reveals that an unlisted individual was aboard—and has now vanished under suspicious circumstances. Five individuals (a mix of crew and passengers, with no police officers or professional detectives among them) must reconstruct the night's movements before the ship reaches port, while navigating hidden past ties, falsified shift records, and anonymous late-night communications.
- **Tone**: Tense, claustrophobic psychological suspense—grounded, realistic, and driven by paranoia, institutional cover-ups, and fractured trust rather than melodrama.
- **Intended Player Experience**: Players begin by comparing routine watch schedules and cabin alibis in Round 1, only to discover in Round 2 that the official schedule was tampered with and an unexplained call/message triggered movement during the critical window. By Round 3, players must weigh whether to expose the full truth, protect a former ally, or conceal their own private compromises when casting the final accusation vote.
- **Intended Role of Each Theme Item**:
  1. **Duty Rota** *(Structural — Timeline & Alibi System)*: Serves as the backbone of the ship's night-watch schedule. Comparing the official digital/posted rota against actual movements or a forged/backup log creates concrete alibi gaps and proves opportunity.
  2. **Unknown Number** *(Structural — Clue System & Communication Trigger)*: Acts as the catalyst and forensic trace (a late-night call or signal from an unregistered/burner number) that links the disappearance window to a specific station or suspect and generates suspicion across multiple characters.
  3. **One Person Too Many** *(Structural — Central Incident & Motive Hook)*: Drives the core mystery itself via a manifest-versus-headcount discrepancy (an unmanifested extra person aboard), motivating why someone wanted the extra person silenced or erased before docking.
- **Why All Three Items Belong Together**: On an overnight ferry, operations depend on strict watch rotations (**Duty Rota**), shipboard/cellular comms logs (**Unknown Number**), and maritime passenger manifests (**One Person Too Many**). Together, they form an interlocking triad of *when* (rota), *how/who contacted whom* (unknown number), and *why* (the unauthorized extra passenger), making every theme item indispensable to the deduction rather than decorative window dressing.
- **What Makes This Concept Distinctive**: By combining a non-detective cast with a mandatory hidden past connection between two players inside a fogbound vessel, the scenario blends classical maritime alibi-checking with tense personal loyalties, giving every player both an investigative puzzle and a moral dilemma.

---

## Q4. Generation Workflow

Our Skill executes a deterministic **7-stage pipeline** encoded across `SKILL.md`, `references/`, and `assets/`:

```text
Structured User Input (theme_items, number_of_players, setting, optional fields)
        │
        ▼
[Stage 1] Input Validation, Atomic Constraint Decomposition & Theme Implementation Gate
        │  (Outputs: Part 1A — defaults applied, constraint-to-element table, theme-to-clue bindings)
        ▼
[Stage 2] Fixed Central Truth, Physical States & Chronological Space-Time Matrix
        │  (Outputs: Part 1B — 1 fixed truth, single culprit, NPC victim, Time × Player Matrix)
        ▼
[Stage 3] Cast Architecture, Directional Relationships & Personal Objectives
        │  (Outputs: Part 1C — N non-filler roles, explicit A->B ties, secrets, primary/secondary goals)
        ▼
[Stage 4] Clue System, Red Herrings & Information-Boundary / Lie-Tagging Matrix
        │  (Outputs: Parts 1D–1E — 8/10/12 clues, anti-spoiler gating, 1-to-1 lie & blind-spot registry)
        ▼
[Stage 5] Multi-Ending Branching & Deterministic DM Resolution Rules
        │  (Outputs: Part 1F — 3–4 mutually exclusive IF/THEN endings & objective pass conditions)
        ▼
[Stage 6] Playable Package Drafting (Strictly Copying From Locked Part 1 Ledger)
        │  (Outputs: Part 2 Overview, Part 3 DM Guide, Part 4 Clue Catalogue,
        │            Part 5 Final Solution, Part 6 Player Character Booklets P1..PN)
        ▼
[Stage 7] Evidence-Cited 15-Point Consistency Audit & Targeted Repair Protocol
        │  (Outputs: Part 7 Audit Table comparing quoted Ledger facts vs. downstream text)
        ▼
Complete Playable Jubensha Package (Parts 1–7)
```

### Stage-by-Stage Breakdown and Dependencies
- **Stage 1 → Stage 2**: Stage 1 parses all required/optional fields, applies documented defaults, decomposes `special_requirements` into atomic rows, and binds each of the three `theme_items` to concrete Clue IDs. Stage 2 depends on Stage 1's theme bindings and `supernatural_elements` / `central_conflict_type` constraints to establish a physically valid Central Truth and a 6–8 slot Space-Time Location Matrix (enforcing the Co-Location Witness Rule).
- **Stage 2 → Stage 3**: Stage 3 populates exactly `number_of_players` roles around Stage 2's fixed truth and matrix, ensuring at least 3 plausible suspects, symmetric directional relationships (`A -> B`), and competing/cooperative objectives.
- **Stage 3 → Stage 4**: Stage 4 designs 8 (`easy`), 10 (`medium`), or 12 (`hard`) clues referencing Stage 2's exact timestamps/locations and Stage 3's cast secrets. It then constructs the Information-Boundary Matrix (`Knows`, `Does NOT Know`, and `[DELIBERATE LIE]` / `[MISTAKEN BELIEF]` tags with `Exposed by C#` pointers) and enforces the Anti-Spoiler Gate.
- **Stage 4 → Stage 5**: Stage 5 defines 3–4 endings whose trigger conditions depend on Stage 4's discoverable clues and Stage 3's personal objectives, keeping the Central Truth from Stage 2 invariant across all branches.
- **Stages 1–5 → Stage 6**: Stage 6 drafts all player-facing and DM-facing prose (Parts 2–6) by reading directly from the already-emitted Part 1 Ledger. Because Part 1 is printed first in the output stream, Stage 6 cannot invent conflicting timestamps, victim identities, or clue release rounds without violating explicit copy-verbatim instructions.
- **Stage 6 → Stage 7**: Stage 7 executes the 15 concrete checks in `references/consistency-checklist.md`, quoting specific timestamps, Clue IDs, and booklet claims in a tabular audit log and applying targeted repairs when mismatches arise.

---

## Q5. How Do You Maintain Consistency?

Maintaining cross-document consistency across a 5,000–7,000 word multi-perspective game is the central technical challenge for ≤70B LLMs. Our Skill implements **twelve concrete prevention mechanisms** paired with an explicit **repair protocol**:

1. **Canonical Story Facts**: Part 1B locks all immutable physical states and communication channels (e.g., landline vs. pager, whether a wipe succeeded or failed, exact environmental timestamps) once at the top of the output before any prose is generated.
2. **Central Truth, Motive, and Mechanism**: Part 1B establishes one fixed Central Truth, responsible party (at most one player character), NPC victim (so all `N` players stay active), core motive, and exact mechanism; Parts 3 and 5 copy this verbatim.
3. **Timeline Management**: A 6–8 row chronological timeline in Part 1B establishes exact `HH:MM` anchors. Every timestamp in clues, the Final Solution, and Character Booklets must match Part 1B.
4. **Character Locations**: The Space-Time Matrix (`Time × P1..PN`) records `Location (Witness)` for every character at every slot and enforces a **Co-Location Witness Rule**: Character A may witness Character B at Time `T` *only if* both occupy the exact same location cell at `T`.
5. **Character Relationships**: Part 1C encodes relationships using explicit directional arrows (`A owes B`, `P3 & P4 are former business partners`) rather than vague nouns, preventing reversed-debt contradictions across paired booklets.
6. **Character Knowledge**: Part 1E records each player's `Knows (with source)` column alongside how they obtained each fact, ensuring no player knows something they could not plausibly have observed.
7. **Information Boundaries**: Part 1E's `Does NOT Know` column acts as a hard negative constraint. Two explicit guardrails in `assets/character-booklet-template.md` forbid leaking `Does NOT Know` facts into *Reasons to Interact* and forbid listing Round 1–3 clues inside *Private Information & Starting Clues*.
8. **Clue Ownership & Staging Parity**: Part 1D assigns every Clue ID (`C1`..`CK`) exactly one release stage (`Start`, `Round 1`, `Round 2`, or `Round 3`) and one access owner. Part 3.1/3.2 and Part 6 must mirror Part 1D verbatim with zero duplicate or prematurely distributed clues.
9. **Deliberate Lies**: Every intentional deception is registered 1-to-1 in Part 1E as `[DELIBERATE LIE]: Claims X; Truth is Y; Exposed by C#`, mirrored inside the booklet's separated *What Really Happened* vs. *Your Cover Story* fields, and verified so that `C#`'s actual player-facing text in Part 4 explicitly contains the exposing fact.
10. **Red Herrings**: Part 1D includes a `Matrix-Verified Time/Loc` column requiring every red herring to be physically compatible with Part 1B's Location Matrix and rationally explained in Part 5.
11. **Final-Solution Checking**: Part 5 must construct a numbered *Clue-to-Conclusion Proof Chain* citing only player-accessible `C#` IDs and starting booklet facts—forbidding DM-only hidden leaps or unstated institutional rules.
12. **Cross-Checking Between Booklets & Host Guide**: Part 7 requires an **Evidence-Cited 15-Point Audit Table** (`Check # | Criterion | Cross-Referenced Evidence | Status`) comparing quoted timestamps, Clue IDs, relationships, and lie tags across Parts 1–6 rather than rubber-stamping `"PASS"`.

### Procedure When a Contradiction Is Detected
`references/consistency-checklist.md` (Section B) defines a strict **Hierarchy of Authority**:
`Input Constraints > Part 1 Canonical Ledger > Part 4 Clues & Part 5 Solution > Part 3 DM Guide > Part 2 Overview & Part 6 Booklets`.
When a check fails, the model applies targeted repairs according to failure type: aligning offending booklet/clue timestamps to Part 1B (or repairing Part 1B first if physically impossible and propagating downstream), replacing leaked facts with partial observable questions, inserting missing procedural rules into Round 1–2 player clues, or registering untagged discrepancies as `[DELIBERATE LIE]` with an exposing clue, then logging `REPAIRED` with the concrete fix in Part 7.

---

## Q6. Evaluation and Failure Analysis

### Experimental Setup
To rigorously evaluate generalization, model portability, and iterative improvement, we tested our Skill across **three versions (v1 → v2 → v3 Final)** on **four distinct inputs** (all using Theme Set 15) spanning 4, 5, and 6 players, three difficulty tiers (`easy`, `medium`, `hard`), three historical periods (`1987`, `1998`, `2020s`, `2048`), four tones (bureaucratic satire, psychological suspense, Cold-War noir, sci-fi noir), and four conflict types (`sabotage`, `disappearance`, `murder`, `identity_fraud`).

To verify ≤70B portability without vendor lock-in, generations were run on three general-purpose models via an OpenAI-compatible endpoint: **`qwen3.8-27b`** (27B primary testbed), **`qwen3.8-flash`** (compact fast model), and **`deepseek-v4.1-flash`** (cross-family portability check). Every generated game was graded blindly by a strict independent LLM evaluator (`qwen3.8-max`, temperature 0.1) using the exact 30-point rubric in Section 11 (B1–B6) and evidence-citation format in Section 12.

### Evaluation Results Across Iterations (No Failed Runs Removed)

| Run ID | Skill Ver. | Model | Input Profile (Players / Diff / Setting / Conflict) | B1 Theme (/5) | B2 Constr. (/5) | B3 Consist. (/7) | B4 Solv. (/5) | B5 Play. (/5) | B6 Narr. (/3) | Total (/30) |
| :--- | :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **R1** | v1 | `qwen3.8-27b` | 4p / Easy / 1998 Lighthouse Satire / Sabotage | 5 | 4 | 4 | 4 | 4 | 3 | **24** |
| **R2** | v1 | `qwen3.8-27b` | 5p / Med / 2020s Ferry Suspense / Disappearance | 4 | 3 | 4 | 2 | 3 | 1 | **17** |
| **R3** | v1 | `qwen3.8-27b` | 6p / Hard / 2048 Antarctic Noir / Murder | 3 | 3 | 3 | 3 | 4 | 2 | **18** |
| **R4** | v2 | `qwen3.8-27b` | 4p / Easy / 1998 Lighthouse Satire / Sabotage | 5 | 4 | 4 | 2 | 3 | 3 | **21** |
| **R5** | v2 | `qwen3.8-27b` | 5p / Med / 2020s Ferry Suspense / Disappearance | 5 | 4 | 4 | 4 | 5 | 3 | **25** |
| **R6** | v2 | `qwen3.8-27b` | 6p / Hard / 2048 Antarctic Noir / Murder | 5 | 3 | 3 | 3 | 4 | 3 | **21** |
| **R7** | v3 (Final) | `qwen3.8-27b` | 5p / Med / Final Ferry Input (Submitted Input) | 5 | 4 | 4 | 3 | 4 | 3 | **23** |
| **R8** | v3 (Final) | `qwen3.8-27b` | 4p / Hard / 1987 Alpine Train (Unseen Test Sim.) | 4 | 4 | 4 | 3 | 4 | 2 | **21** |
| **R9** | v3 (Final) | `deepseek-v4.1-flash` | 6p / Hard / 2048 Antarctic Noir (Portability) | 5 | 4 | 4 | 3 | 4 | 3 | **23** |
| **R10*** | **v3 (Final)** | **`qwen3.8-flash`** | **5p / Med / Final Ferry Input (Submitted Game)** | **5** | **4** | **5** | **4** | **4** | **3** | **25** |

*\*R10 (`submission/final_generated_game.md`) is our genuinely generated final submission produced unmodified by the frozen v3 Skill.*

### Detailed Failure Analyses & Iterative Revisions

#### Failure Case 1 — Autoregressive Mid-Generation Retconning & Dual-Culprit Collapse (R2, v1: 17/30)
1. **Input**: 5 players, Medium, Contemporary overnight ferry, disappearance, no police/detectives, hidden past connection.
2. **What Went Wrong**: Part 1 (Game Overview) contained raw self-correction monologue (*"Wait, the input says number_of_players: 5. Let me re-read the skill... Correction:"*) after initially making P4 the missing victim and switching mid-paragraph to an NPC. Additionally, P1's booklet reversed debt direction (*"You lent him money"* in Background vs. *"You owe Leo money"* in Secret), and *two* players (P2 mastermind + P5 killer, 40% of the table) were told the entire solution on page 1.
3. **Why It Happened**: In v1, `assets/host-guide-template.md` placed Part 1 (Public Overview) *before* Part 2 (Canonical Planning Ledger). Because a 27B autoregressive model cannot backtrack, drafting narrative before locking the cast and victim forced inline corrections. Furthermore, v1 did not enforce directional relationship notation or cap the number of culprits.
4. **What We Changed (v1 → v2)**: Reordered the entire output so **Part 1 is the Canonical Planning Ledger** emitted *first*; added a strict *Zero Drafting Monologue* rule; mandated explicit directional relationships (`A -> B`); introduced the **Single-Culprit & ≥2 Culprit-Unknowns Rule**; and mandated NPC victims when a character is killed/missing.
5. **Did It Improve Later Generations?**: Yes—on the exact same input (R5, v2), the score jumped from **17/30 to 25/30 (+8 points)**, Narrative Quality rose from 1/3 to 3/3, Playability hit 5/5, and Theme Integration hit 5/5.

#### Failure Case 2 — Dropped 3rd Theme Item & Spatial Red-Herring Paradox (R3, v1: 18/30)
1. **Input**: 6 players, Hard, 2048 Antarctic outpost, murder, mandatory alibi witnesses for all 6 players, opposing extraction objectives, non-commander culprit.
2. **What Went Wrong**: *One Person Too Many* was listed in the DM theme table as an "airlock capacity" mechanic but never appeared in any clue, booklet, or the Final Solution (Theme score: 3/5). Two players (P2, P4) were left alone in all time slots, violating the witness constraint. Worse, Red Herring `C4` placed P4 on a corridor camera at 22:10 while the Space-Time Matrix placed P4 in the Lower Levels at 22:10—an unsolvable physical paradox—yet v1's passive Stage 7 audit printed `PASS` for every check.
3. **Why It Happened**: v1's Stage 1 allowed abstract theme mapping without requiring concrete Clue ID bindings, lacked an atomic constraint decomposition table for multi-clause `special_requirements`, and lacked an evidence-cited audit format.
4. **What We Changed (v1 → v2)**: Added the **Theme Implementation Gate** (requiring concrete `C#` bindings for all 3 theme items), the **Atomic Constraint Verification Table** in Part 1A, a **Matrix-Verified Time/Loc** column in Part 1D, and upgraded Stage 7 to an **Evidence-Cited 15-Point Audit Table**.
5. **Did It Improve Later Generations?**: Yes—Theme Integration jumped from **4.0/5 avg in v1 to 5.0/5 across all three v2 runs** (and 5/5 in R7, R9, R10). On the hardest 6-player Antarctic stress test, `qwen3.8-27b` rose from 18/30 to 21/30 (R6) and `deepseek-v4.1-flash` reached **23/30** (R9) with all 3 special requirements and 5/5 theme integration satisfied.

#### Failure Case 3 — Early Culprit Spoilers, Cross-Room Witness Chain & Clue-Round Mismatch (R4 & R6, v2: 21/30)
1. **Input**: R4 (4p Lighthouse Easy) and R6 (6p Antarctic Hard).
2. **What Went Wrong**: In R4, public starting clue `C2` stated *"Handwriting matches P2"*, solving the mystery at minute zero (Solvability dropped to 2/5). In R6, Part 2 Public Overview named Dr. Aris Thorne as the sole person with room access; P1 in Comms Room was simultaneously cited as P6's witness in the Observation Deck; and `C11` was marked `Round 3` in Part 1D but distributed at `Start` in Part 3.1.
3. **Why It Happened**: v2 did not explicitly prohibit culprit-identifying forensics in Overview/Start clues, did not state that witnesses must share the exact same room cell, and did not forbid listing Round 1–3 clues in Setup/Starting Clues.
4. **What We Changed (v2 → v3 Final)**: Added the **Anti-Spoiler Gate** (decisive links gated to Rounds 2–3 and requiring ≥2 clues), the **Co-Location Witness Rule**, **Single-Stage Clue Parity**, explicit `Does NOT Know` anti-leak guards inside `assets/character-booklet-template.md`, and mandatory `Exposed by C#` text enforcement.
5. **Did It Improve Later Generations?**: Yes—early culprit spoilers disappeared across all v3 runs (R7–R10), Solvability recovered to 4/5 and Consistency rose to 5/7 in our final submitted game (R10, **25/30**), and the unseen 1987 Alpine train test (R8, **21/30**) generated a genuinely distinct Cold-War identity-fraud mystery with zero resemblance to the ferry submission.

### Honest Assessment of Residual Limitations
Even in v3, single-pass generation on compact ≤70B models occasionally exhibits minor matrix-to-booklet drift (e.g., in R10, Part 1B placed P1 on the Bridge at 23:30 while both Clue C1 and P1's booklet consistently placed her in the Lounge, and Part 1A contained a minor role typo). Additionally, enabling unrestricted hidden reasoning (`enable_thinking=True`) under a 16k token cap caused models to exhaust output tokens on internal thoughts before writing the playable game; disabling hidden thinking allows our explicit Part 1 Canonical Ledger to serve as visible, token-efficient planning.

---

## Q7. Skill Usage Guide

Any evaluator or user can run `jubensha-generator` directly on any general-purpose instruction-following LLM (≤70B or larger) without contacting our group.

### 1. Skill Directory Structure
```text
jubensha-generator/
├── SKILL.md
├── references/
│   ├── game-design-guidelines.md
│   └── consistency-checklist.md
└── assets/
    ├── canonical-ledger-template.md
    ├── character-booklet-template.md
    └── host-guide-template.md
```
No scripts, external tools, APIs, plugins, or external dependencies are required or included.

### 2. Input Contract

| Field | Required | Accepted Values / Type | Default Behaviour (if omitted) |
| :--- | :--- | :--- | :--- |
| `theme_items` | **Yes** | List of exactly 3 distinct theme strings | *Required.* Reject input if missing or count ≠ 3. |
| `number_of_players` | **Yes** | Integer: `4`, `5`, or `6` (excludes DM) | *Required.* Reject input if missing or outside `4–6`. |
| `setting` | **Yes** | Free-text primary location/environment | *Required.* Reject input if missing. |
| `historical_period` | No | Free-text period (e.g., `1987`, `2020s`, `2048`) | `contemporary present day` |
| `tone` | No | Free-text mood/style | `grounded suspense with interpersonal tension` |
| `difficulty` | No | `easy` (8 clues), `medium` (10 clues), or `hard` (12 clues) | `medium` |
| `supernatural_elements` | No | `none` or `allowed` | `none` |
| `central_conflict_type` | No | Free-text or `murder`, `disappearance`, `sabotage`, `theft`, `cover-up`, `identity_fraud` | Best fit for `setting` and `tone` |
| `special_requirements` | No | Free-text constraints or forbidden elements | `none` |

### 3. Unsupported Inputs
The Skill explicitly does **not** support:
- `number_of_players` < 4 or > 6;
- fewer or more than 3 `theme_items`;
- non-text/multimodal inputs (images, audio, external URLs) or requests requiring executable scripts, web browsing, or external databases.

### 4. Complete Example Input
```yaml
theme_items:
  - Duty Rota
  - Unknown Number
  - One Person Too Many
number_of_players: 5
setting: Overnight cross-channel cargo-and-passenger ferry (MV Meridian) halted in dense winter fog mid-channel
historical_period: contemporary 2020s
tone: tense claustrophobic psychological suspense
difficulty: medium
supernatural_elements: none
central_conflict_type: disappearance
special_requirements: No player is a police officer or professional detective. At least two player characters share a hidden past connection.
```

### 5. How to Run the Skill
1. Load `jubensha-generator/SKILL.md` together with all files in `references/` and `assets/` into the LLM's system prompt or Skill context.
2. Provide a valid structured input block matching the Input Contract above as the user prompt, instructing the model to follow the Skill strictly.
3. **Recommended Inference Settings**: Set `max_tokens` to at least **12,000–16,384** so 6-player games are never truncated, and use moderate temperature (`0.5–0.7`). On reasoning-capable models that share `max_tokens` between hidden thinking and visible output, disable hidden thinking or cap thinking budget to ≤2,048 tokens so the full 7-part package fits cleanly.
4. **Tested Models**: Verified on `qwen3.8-27b`, `qwen3.8-flash`, and `deepseek-v4.1-flash`.

### 6. Expected Output Structure
The Skill outputs one cohesive Markdown document organized into seven parts:
- **Part 1**: Canonical Planning Ledger (1A Input/Constraints/Theme Gate, 1B Central Truth & Space-Time Matrix, 1C Cast & Objectives, 1D Clue Ledger, 1E Information-Boundary & Lie Matrix, 1F Ending Map)
- **Part 2**: Game Overview (public, spoiler-free)
- **Part 3**: DM / Host Guide (setup, 3-round staged flow, special rules, deterministic ending triggers, and Personal Objective & Winner Scoring Table)
- **Part 4**: Clue & Evidence Catalogue (`C1`..`CK` with round, access, reliability tag, player text, and DM note)
- **Part 5**: Final Solution (fixed truth, timeline, Clue-to-Conclusion Proof Chain, red herring & lie accounting)
- **Part 6**: Individual Player Character Booklets (`P1` through `PN`)
- **Part 7**: Evidence-Cited 15-Point Consistency Audit Table

### 7. Known Limitations
- Very dense combinatorial constraints in `special_requirements` (e.g., requiring all 6 players to have pairwise co-located alibi witnesses across multiple slots simultaneously) can still induce occasional single-cell drift between Part 1B and Part 6 on compact ≤30B models; keeping `special_requirements` to 1–3 clear narrative constraints yields the highest logical consistency.
- Single-pass generation cannot physically overwrite earlier text if an error is caught only in Part 7; the Part 7 Audit Table logs any detected discrepancy explicitly so the DM can apply the authoritative Hierarchy of Authority before play.
