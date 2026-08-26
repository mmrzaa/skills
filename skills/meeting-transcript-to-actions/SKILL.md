---
name: meeting-transcript-to-actions
description: Turns messy meeting transcripts into a decision-oriented summary and actionable tasks with owners, due dates, and next steps. Use when user pastes meeting notes/transcripts, mentions transcription errors/typos, wants “action items”, “what was decided”, “next steps”, “follow-ups”, or wants to prep for a grill-me / grill-with-docs session.
argument-hint: "Optional: desired output format (e.g. GitHub issues, Jira tickets, PRD outline), audience (engineering vs mixed), and time horizon."
---

You are given a raw meeting transcript. It may contain typos, incorrect speaker attributions, off-topic tangents, and incomplete context.

Your job is to convert it into **clear, actionable, doable outputs** and surface what’s missing.

## Quick start

Ask me to paste the transcript (and any links). Then produce:

1) **Cleaned narrative** (what the meeting was about, in plain language)
2) **Decisions** (what was decided, and by whom if inferable)
3) **Action items** (owner, due date/timebox, definition of done)
4) **Open questions / blockers** (what must be resolved next)
5) **Risks + assumptions** (explicitly list inferred assumptions)
6) **Parking lot** (off-topic items worth tracking)

## Workflow

### 1) Reconstruct the meeting (be robust to noise)
- Remove filler, repetition, and obvious transcription artifacts.
- If speaker labels are wrong/unknown, use neutral labels like “Speaker A/B” and focus on content.
- Preserve technical terms, code identifiers, system names, and numbers; if uncertain, mark as \(uncertain\).

### 2) Build a shared understanding
- Summarise the **goal**, **scope**, and **constraints** discussed.
- Extract a **glossary** of key terms/acronyms from the transcript.
- If meaning is unclear, ask **at most 5 targeted questions**, prioritised by “blocks actionability”.

### 3) Extract decisions (or explicitly state none)
- Record decisions as: **Decision**, **Rationale**, **Trade-offs considered**, **Impact**, **Reversible?**
- If the transcript never converged, write: **“No decision reached”** and list what prevented it.

### 4) Produce action items that can be executed
- Each action item must include:
  - **Title**
  - **Owner** (name/role; if unknown say “Unassigned”)
  - **Priority** (P0/P1/P2)
  - **Due** (date if present; otherwise propose a reasonable timebox)
  - **Definition of Done** (verifiable)
  - **Dependencies** (people/systems/decisions)
- Split vague items into the smallest meaningful tasks; avoid “do the thing”.

### 5) Improvement opportunities
- List **what went well**, **what to improve**, and **process fixes** (e.g. missing stakeholder, unclear success metric, lack of data).
- Include a short “**Next meeting agenda**” if another meeting is implied.

### 6) Optional: prep for other skills
- If the discussion is a plan/design: propose a **grill-me prompt** (questions to stress-test).
- If domain language/docs matter: propose a **grill-with-docs prompt** (what to validate vs `CONTEXT.md` / ADRs).

## Output format (default)

Return the following sections, in order:

- **Context**
- **Cleaned summary**
- **Decisions**
- **Action items**
- **Open questions / blockers**
- **Risks / assumptions**
- **Parking lot**
- **Improvements / next agenda**
- **Suggested follow-up skill prompt** (optional)

For examples, see [EXAMPLES.md](EXAMPLES.md).
