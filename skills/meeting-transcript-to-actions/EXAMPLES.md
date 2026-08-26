## Example 1 — messy technical transcript → tasks

**Raw transcript (excerpt)**

- “uh so the deploy thing, it failed again… I think… something with redis? or *radius*? anyway”
- “we should add retries… but careful not to double charge”
- “let’s do it this sprint”
- “also the dashboard is wrong sometimes”
- (side chat about lunch)

**Output**

### Context
- Goal: stabilise deployments and prevent duplicate charges.
- Scope: deploy pipeline + payment retry logic + dashboard correctness.
- Constraints: avoid double-charging; minimise customer impact.

### Decisions
- **No final decision reached** on retry strategy.
  - Blocker: unclear where failures originate (deploy vs runtime vs external dependency).

### Action items
1) **Investigate deploy failures around Redis**
   - Owner: Unassigned (suggest SRE/on-call)
   - Priority: P0
   - Due: 2 business days (timebox)
   - DoD: root cause identified; mitigation documented; next steps agreed
   - Dependencies: access to CI logs + deploy logs

2) **Propose safe retry strategy for payments**
   - Owner: Unassigned (suggest payments engineer)
   - Priority: P0
   - Due: 3 business days
   - DoD: design note covering idempotency keying, backoff, and failure modes
   - Dependencies: decision on idempotency approach; current gateway capabilities

3) **Define “dashboard wrong” bug with repro**
   - Owner: Unassigned (suggest frontend/data owner)
   - Priority: P1
   - Due: 5 business days
   - DoD: clear repro steps + screenshots/queries + expected vs actual
   - Dependencies: identify affected metric(s)

### Open questions / blockers
- Was “redis” actually Redis (or another component mis-heard as “radius”)?
- Where did the deploy fail: build, rollout, readiness, runtime crash, timeout?
- Do we have idempotency keys end-to-end today?

### Risks / assumptions
- Assumption: failures correlate with cache/session dependency.
- Risk: naïve retries can double-charge.

### Parking lot
- Lunch discussion.

### Improvements / next agenda
- Next meeting: bring deploy logs, error rates, and payment failure samples; end with explicit decision on retry semantics.

### Suggested follow-up skill prompt
- Use `grill-me` on: “payment retry strategy under idempotency constraints”.

---

## Example 2 — non-technical transcript → decision clarity

**Raw transcript (excerpt)**

- “we should maybe train folks… the process is confusing”
- “yeah onboarding takes forever”
- “do we have time this quarter?”
- “I can ask HR”

**Output**

### Decisions
- **No decision reached** on timing or ownership.

### Action items
1) **Measure current onboarding time**
   - Owner: Unassigned
   - Priority: P1
   - Due: 1 week
   - DoD: baseline onboarding duration + pain points summary

2) **Draft training proposal**
   - Owner: Unassigned (suggest team lead)
   - Priority: P2
   - Due: 2 weeks
   - DoD: outline session topics, audience, format, and required time investment

3) **Confirm HR support**
   - Owner: “I can ask HR” (if that’s a named person, assign)
   - Priority: P2
   - Due: 1 week
   - DoD: yes/no + what HR can provide

### Open questions
- What “success” looks like (time-to-productivity? reduced mistakes?).
- Budget/time allocation this quarter.
