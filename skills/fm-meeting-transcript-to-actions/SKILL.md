---
name: fm-meeting-transcript-to-actions
description: Turns messy meeting transcripts into Firstmate-tailored backlog items, supervisor decisions, and structured records. Separates unverified hypotheses from verified evidence, structures proposed work into ship or scout deliverables by project with suggested routing, and formats calls requiring supervisor choice as decision records with options and recommendations. Avoids direct address of the captain in generated artifacts. Use when transcripts or meeting notes are provided to Firstmate, in a Firstmate-supervised fleet, or when preparing meeting follow-ups for Firstmate dispatch.
argument-hint: "Optional: target projects, known secondmate scopes, or specific focus areas."
---

You are given a raw meeting transcript. It may contain speech-to-text noise, typos, misattributed speakers, speculative brainstorming, and off-topic tangents.

Your job is to convert it into **clear, structured records tailored for Firstmate supervision**:
- Distinguish verified evidence from unverified meeting hypotheses.
- Format human-owned calls as supervisor decisions with options and a recommendation.
- Map follow-up work into proposed backlog items classified by project and deliverable kind (`ship` vs `scout`).
- Suggest routing (main home or a relevant secondmate scope) where inferable.
- Never treat discussed ideas as pre-authorized work.
- Never use direct address ("captain") inside generated artifacts.

## Core Rules

1. **Explicit evidence vs. hypothesis separation**
   - **Verified evidence**: Facts directly established by code, CI/CD runs, benchmark results, logs, or recorded empirical data.
   - **Meeting claims & hypotheses**: Brainstormed ideas, theories, untested assumptions, speculative explanations, and attendee impressions. Never present a claim or hypothesis as confirmed fact.
2. **No pre-authorized work**
   - A meeting discussion does not authorize code changes or project modifications. All tasks derived from discussion are strictly **Proposed Backlog Items** awaiting supervisor triage and dispatch.
3. **Firstmate deliverable kinds**
   - **Ship**: Concrete project codebase or configuration changes delivered through the project's delivery path (PR or local branch). Default for authorized implementation.
   - **Scout**: Investigation, diagnosis, research, bug reproduction, architecture spike, or audit; delivers a structured knowledge report (`data/<task-id>/report.md`), never a PR.
4. **Supervisor decisions (escalations)**
   - When a call belongs to the supervisor (e.g. strategic direction, product scope, irreversible/destructive trade-offs, budget/credentials, or choosing among competing architectures), write it as a structured decision with concrete options and a recommendation.
5. **No direct address in artifacts**
   - In non-chat artifacts (markdown documents, notes, proposed briefs, backlog entries), **never address the reader as "captain"** or use conversational markers. Use objective, role-neutral phrasing (e.g. "Supervisor decision", "Pending review").
6. **Suggested routing**
   - Map each proposed item to its target **Project** and a **Suggested Route**: either the `Main home` (default for cross-cutting or local-only tasks) or a specific `Secondmate: <scope>` when the task aligns with a known domain-specific charter (e.g. hardware/sensor research, data pipeline).

## Workflow

### 1) Reconstruct the discussion (noise robustness)
- Filter out filler words, stuttering, and transcription errors.
- If speaker attributions are unreliable, use neutral labels like `Speaker A` / `Speaker B` or verify names against context.
- Preserve technical tokens, code identifiers, branch names, metrics, and system names exactly. If a term is ambiguous or phonetically corrupted, mark it as `(uncertain)`.

### 2) Separate evidence from claims
- Extract what has actually been observed or demonstrated into **Verified Evidence**.
- Extract theories, explanations, and assumptions into **Meeting Claims & Hypotheses**, noting who raised them if known.

### 3) Extract decisions
- **Decisions for supervisor review**: Calls requiring human approval or direction. Detail:
  - Context & decision needed
  - Concrete options considered
  - Trade-offs / consequences of each option
  - Recommended option with evidence-based rationale
- **Agreed decisions**: Operational agreements or conclusions firmly settled within the meeting scope (record: Decision, Rationale, Trade-offs, Impact, Reversibility). If none, state "None reached".

### 4) Map proposed backlog items
Break down executable follow-up work into discrete, single-deliverable tasks. Each item must specify:
- **Title**: Clear, imperative summary.
- **Project**: Target repository or codebase.
- **Kind**: `ship` (code change) or `scout` (investigation/report).
- **Suggested Route**: `Main home` or matching `Secondmate: <scope>`.
- **Definition of Done (DoD)**: Verifiable completion criteria.
- **Dependencies**: Prerequisites, required access, or blocking decisions.
- **Evidence Base**: Whether the task builds on verified evidence or investigates a meeting hypothesis.

### 5) Open questions, blockers & parking lot
- **Open questions / blockers**: Unresolved ambiguities that block backlog execution or decision-making.
- **Parking lot**: Off-topic tangents or long-range ideas worth noting without immediate backlog commitment.

## Output Format (Default)

Return the following sections, in order:

### 1. Context & Overview
- Meeting objective, scope, and key systems involved.
- Brief narrative summary of the discussion.

### 2. Verified Evidence vs. Hypotheses
- **Verified Evidence**: Bulleted list of confirmed facts, logs, measurements, or baseline realities.
- **Meeting Claims & Hypotheses**: Bulleted list of untested theories, proposed explanations, and working assumptions.

### 3. Decisions for Supervisor Review
*(Omit or state "None" if no supervisor calls are required)*
For each decision:
- **Decision Needed**: [Clear question/choice]
- **Context**: [Why this decision is needed now]
- **Options**:
  - **Option 1**: [Description + pros/cons]
  - **Option 2**: [Description + pros/cons]
- **Recommendation**: [Preferred option with rationale based on evidence]

### 4. Agreed Decisions
- Summary of any finalized conclusions reached during the meeting, or "No final decisions reached in the session."

### 5. Proposed Backlog Items
For each item:
1. **[Title]**
   - **Project**: [project-name]
   - **Kind**: `ship` | `scout`
   - **Suggested Route**: `Main home` | `Secondmate: <scope>`
   - **Definition of Done**: [Verifiable DoD]
   - **Dependencies**: [Prerequisites or "None"]
   - **Evidence Base**: [Verified evidence reference or hypothesis to test]

### 6. Open Questions & Blockers
- Blocking unknowns or missing data needed before dispatch.

### 7. Parking Lot
- Deferred topics and tangents.

---

For complete input/output walkthroughs, see [EXAMPLES.md](EXAMPLES.md).
