# 15-Point Evidence-Cited Consistency Audit & Repair Protocol

Run all 15 checks systematically during **Stage 7** after drafting Parts 1–6.
**Do NOT rubber-stamp checks as `PASS` without evidence.** For every check, cite the exact timestamps, locations, clue IDs, or booklet claims you compared.

---

## A. The 15 Concrete Checks

1. **Single Fixed Central Truth**: Verify that Part 1B (Ledger), Part 3 (DM Guide), and Part 5 (Final Solution) state the exact same responsible party/cause, target, motive, and mechanism.
2. **Incident & Environmental Timing Consistency**: Compare all key timestamps (central incident, environmental events like storms/glitches/lockdowns) across Part 1B (Location Matrix), Part 4 (Clues), Part 5 (Solution), and Part 6 (Booklets). Zero drift allowed.
3. **Event Order & Physical State Consistency**: Verify chronological feasibility and consistent physical states (e.g., if a terminal wipe *failed* in Clue C1, the Overview must NOT call it "wiped"; if a door was locked at 22:00, no one walks through it unlocked at 22:05).
4. **Spatial-Temporal Location Compatibility**: Cross-check EVERY clue sighting/recording and EVERY Character Booklet timeline against the Part 1B Location Matrix. Verify no character (including in red herrings!) is placed in two locations at the same time.
5. **Directional Relationship Symmetry**: Check every pair of linked characters (e.g., debt direction `A owes B`, shared past, rivalry) across Part 1C and both Character Booklets. Zero reversed facts (e.g., "lent money" vs "owes money").
6. **Information-Boundary Integrity**: Verify no Character Booklet contains facts from its `Does NOT Know` column, and at most 1 player has prior knowledge of executing the central incident (with ≥2 genuine unknowns).
7. **Deliberate Lie Tagging Parity**: Verify 1-to-1 parity between Part 1E (`[DELIBERATE LIE]` / `[MISTAKEN BELIEF]` tags) and all Character Booklets (`Your Cover Story`). Remove any untagged factual discrepancies.
8. **Essential Clue Availability**: Verify every single fact, procedural rule, and motive required by Part 5 (Final Solution) appears explicitly in player-accessible Clues (`C1`..`CK`) or starting Character Booklets.
9. **Zero Solution-Only Leaps**: Confirm Part 5 (Final Solution) introduces ZERO crucial facts or institutional rules that never appear in Parts 2, 4, or 6.
10. **Clue Accounting**: Confirm Part 5 explains all major clues (Core, Corroborating, and Red Herrings) with no orphaned Clue IDs.
11. **Red Herring Timeline Compatibility**: Confirm every Red Herring is physically compatible with the Part 1B Location Matrix and true mechanism.
12. **Theme Item Concrete Integration**: Confirm all 3 `theme_items` appear in both Part 1A AND in specific player-visible Clues (`C#`), Booklets, and the Final Solution, with at least 2 serving structural roles.
13. **Input Contract & Special Requirements Compliance**: Verify every row of Part 1A's Constraint Verification Table (exact player count, setting, period, tone executed in booklet voice, exact difficulty clue count, supernatural rule, conflict type, and every clause of `special_requirements`).
14. **Cross-Document Identifier & Cleanliness Parity**: Confirm character names, Clue IDs, round numbers, and personal objectives match identically across Parts 1–6, with zero drafting monologue (`"Wait..."`, `"Correction:"`).
15. **Deterministic Endings & Winner Scoring**: Confirm Ending Trigger rules (`IF ... THEN ...`) are mutually exclusive and cover all vote/decision outcomes, and every player's Primary/Secondary objectives have unambiguous pass conditions.

---

## B. Targeted Repair Protocol (When Any Check Fails)

1. **Hierarchy of Authority**:
   - `Input Constraints` > `Part 1 Canonical Ledger` > `Part 4 Clue Catalogue & Part 5 Final Solution` > `Part 3 DM Guide` > `Part 2 Overview & Part 6 Booklets`.
2. **Repair Actions**:
   - *Timeline / Location / State Conflict*: Align the offending clue/booklet/overview to the Canonical Ledger (or fix the Ledger first if physically impossible, then propagate).
   - *Untagged Lie or Fact Reversal*: Either register the cover story as `[DELIBERATE LIE]` in Part 1E with a contradicting clue, or fix the contradictory sentence to match Part 1C.
   - *Missing Procedural Rule / Solution Leap*: Insert the missing rule directly into a Round 1 or Round 2 player-facing Clue or Booklet.
   - *Missing Theme Item or Special Requirement*: Add/upgrade a Clue or Location Matrix witness entry so the requirement is concretely satisfied in player-facing text.
3. **Audit Output Format (Part 7)**:
   Present a compact Markdown table:
   `| Check # | Criterion | Cross-Referenced Evidence (Quoted Facts/IDs) | Status (PASS / REPAIRED) |`
