## Example 1 — Research & sensor meeting → evidence separation & backlog items

**Raw transcript (excerpt)**

- “Looking at the bench test logs from yesterday, packet delivery rate on anchor 3 dropped to 41% when the tag was behind the metal baffle. That’s in the sqlite db test_run_1014.”
- “Maybe it’s multipath interference or maybe the Kalman filter covariance matrix is blowing up on rapid RSSI swings. We should probably rewrite the filter to use an unscented Kalman filter.”
- “Or just tune the outlier rejection threshold first? But we need to decide if we want to buy three more UWB anchors for ground truth or stick to BLE RSSI.”
- “Also someone should update the visualizer script to plot raw RSSI alongside estimated distance.”
- “Yeah, and what about that company lunch next Friday?”

**Output**

### 1. Context & Overview
- **Objective**: Improve localization stability and error handling under RF obstruction.
- **Scope**: BLE anchor tracking pipeline, Kalman filtering, diagnostic tooling, and ground-truth validation.
- **Summary**: The discussion reviewed degradation in anchor 3 packet delivery behind a metal baffle. Attendees debated algorithmic vs. physical causes and weighed purchasing UWB ground-truth hardware versus tuning outlier rejection. A tooling update for the visualizer was identified.

### 2. Verified Evidence vs. Hypotheses
- **Verified Evidence**:
  - Anchor 3 packet delivery rate dropped to 41% when the tag was positioned behind the metal baffle during yesterday's bench test (`test_run_1014` in the SQLite database).
- **Meeting Claims & Hypotheses**:
  - Hypothesis: Multipath reflection caused the severe packet delivery drop.
  - Hypothesis: Kalman filter covariance matrix diverges under rapid RSSI fluctuation.
  - Claim: An unscented Kalman filter (UKF) would resolve tracking errors better than tuning outlier rejection.

### 3. Decisions for Supervisor Review
- **Decision Needed**: Ground-truth benchmarking setup: Purchase UWB hardware vs. retain BLE baseline.
- **Context**: Accurate validation of tracking accuracy under obstruction requires dependable ground truth. UWB anchors provide millimeter accuracy but incur hardware expense.
- **Options**:
  - **Option A (Purchase UWB anchors)**: Buy three UWB units for sub-centimeter ground truth logging. Higher fidelity, but requires budget expenditure and new serial ingestion code.
  - **Option B (Retain BLE / physical grid baseline)**: Use fixed laser tape measurements and existing anchors for baseline validation. Zero hardware cost; slightly slower manual fixture repositioning.
- **Recommendation**: Option B for the immediate milestone. Complete the filter diagnostic before committing hardware budget.

### 4. Agreed Decisions
- No final algorithmic architecture decisions reached during the meeting; redesign deferred pending diagnostic findings.

### 5. Proposed Backlog Items
1. **Analyze Kalman filter covariance stability in baffle benchmark logs**
   - **Project**: `ble-localization`
   - **Kind**: `scout`
   - **Suggested Route**: `Secondmate: sensor-research`
   - **Definition of Done**: Produce a diagnostic report in `data/<id>/report.md` evaluating covariance matrix states across `test_run_1014`, identifying whether divergence occurred, and comparing outlier threshold tuning against filter re-architecture.
   - **Dependencies**: Access to `test_run_1014` SQLite dataset.
   - **Evidence Base**: Investigates the covariance blowup hypothesis against verified 41% drop.

2. **Add dual-trace raw RSSI and estimated distance plot to visualizer**
   - **Project**: `ble-localization`
   - **Kind**: `ship`
   - **Suggested Route**: `Secondmate: sensor-research`
   - **Definition of Done**: Update `tools/visualizer.py` to plot raw RSSI traces alongside estimated distance on synced time axes; verified on sample benchmark run.
   - **Dependencies**: None.
   - **Evidence Base**: Established developer tooling requirement.

### 6. Open Questions & Blockers
- Does the 41% packet delivery rate reflect physical RF attenuation or an anchor firmware buffer overflow?
- What is the current outlier rejection threshold setting in the active tracker configuration?

### 7. Parking Lot
- Team lunch scheduling for next Friday.

---

## Example 2 — Backend reliability & incident review → ship/scout classification

**Raw transcript (excerpt)**

- “Grafana showed p99 latency spiked to 4.8s during the 14:00 batch import yesterday. Worker pod CPU stayed under 25%.”
- “I think the database connection pool is getting exhausted because the batch job holds transactions open during outbound partner API calls. We should probably just double the connection pool.”
- “We can’t just increase the pool; Postgres `max_connections` is capped at 100 on the database cluster, so doubling worker connections could starve web pods.”
- “Should we refactor the transaction boundary so the HTTP call is outside the transaction, or move the batch import into an async queue worker?”
- “Also, the staging SSL certificate for the internal metrics endpoint expires in 10 days.”

**Output**

### 1. Context & Overview
- **Objective**: Diagnose and resolve p99 latency spikes during scheduled batch imports.
- **Scope**: Batch import transaction boundaries, database connection limits, and staging certificate maintenance.
- **Summary**: The team examined a 4.8s p99 latency spike during the 14:00 batch run. Low CPU pointed to blocking I/O or pool contention. Attendees considered increasing pool limits vs. refactoring transaction scope or redesigning the worker pipeline. An upcoming certificate expiration was noted.

### 2. Verified Evidence vs. Hypotheses
- **Verified Evidence**:
  - P99 latency reached 4.8s during the 14:00 batch import (recorded in Grafana).
  - Worker pod CPU utilization remained below 25% during the spike.
  - Database cluster `max_connections` is capped at 100.
  - Staging internal metrics SSL certificate expires in 10 days.
- **Meeting Claims & Hypotheses**:
  - Hypothesis: Database connection pool exhaustion caused the latency spike.
  - Hypothesis: The batch job holds active database transactions while awaiting responses from external partner APIs.
  - Claim: Doubling pool size on worker pods would resolve the latency without side effects (disputed due to the 100 connection cap).

### 3. Decisions for Supervisor Review
- **Decision Needed**: Architectural approach for batch import concurrency and database load.
- **Context**: The batch job must not exhaust database connections or impact latency for API web traffic.
- **Options**:
  - **Option 1 (Refactor transaction scope)**: Move external API calls outside the database transaction block so connections are held only during DB writes. Low complexity, minimal risk, fast turnaround.
  - **Option 2 (Migrate to dedicated async queue)**: Decouple the batch import into an external queue worker with independent rate limiting. Higher complexity, requires queue infrastructure additions.
- **Recommendation**: Option 1. Scope transaction boundaries to release connections before network I/O. Evaluate Option 2 only if volume exceeds single-worker capacity.

### 4. Agreed Decisions
- Rejected increasing worker connection pool size without investigating connection hold times, to protect web traffic from connection starvation.

### 5. Proposed Backlog Items
1. **Audit batch import transaction boundaries and connection holding**
   - **Project**: `backend-service`
   - **Kind**: `scout`
   - **Suggested Route**: `Main home`
   - **Definition of Done**: Deliver diagnostic report detailing transaction lifetimes in `services/batch_importer.py`, quantifying connection duration and verifying whether HTTP calls occur inside transactions.
   - **Dependencies**: Access to repository and staging database telemetry.
   - **Evidence Base**: Investigates connection exhaustion hypothesis.

2. **Refactor batch importer to execute outbound HTTP calls outside DB transaction**
   - **Project**: `backend-service`
   - **Kind**: `ship`
   - **Suggested Route**: `Main home`
   - **Definition of Done**: Modify `services/batch_importer.py` so external requests execute before or after DB transactions; verify passing test suite and transaction duration under 50ms in staging.
   - **Dependencies**: Completion of transaction audit scout.
   - **Evidence Base**: Addresses verified risk of long-held transactions.

3. **Renew staging metrics endpoint SSL certificate**
   - **Project**: `infra-config`
   - **Kind**: `ship`
   - **Suggested Route**: `Secondmate: infra-operations`
   - **Definition of Done**: Update and deploy certificate for staging metrics endpoint; verify TLS handshake succeeds and expiration date is extended past 90 days.
   - **Dependencies**: None.
   - **Evidence Base**: Verified 10-day expiration date.

### 6. Open Questions & Blockers
- What is the timeout configured on the outbound partner API clients?
- Are connection pool metrics currently exported to Prometheus/Grafana?

### 7. Parking Lot
- None.

---

## Example 3 — Transcript to LAN board to Firstmate receipt

**Raw transcript (excerpt)**

- “The current zone demo repeats the same polygon data in two YAML files.”
- “We should make `zones.yaml` the single source, but first check whether the engine and UI already read different files.”
- “The simulation showed fast and slow tags behaving differently; we need the data before choosing a sensitivity rule.”
- “Later, a floor-plan receiver-placement simulator could be useful.”

**Read-only reality check before review**

| Key | Action | Status | Evidence |
|---|---|---|---|
| `dec.zone-source` | Choose one configuration owner | `partial` | `event-zone-architecture-review/report.md` describes a single `zones.yaml` design, but the checked repository still has separate runtime/UI references; implementation is not proven. |
| `backlog.1` | Audit update-rate and time-window behavior | `not started` | No completed experiment report or implementation test was found in the relevant checkout; the transcript's fast/slow observation remains meeting evidence. |
| `backlog.2` | Explore receiver-placement planning | `not started` | Current reports classify this as future work; no planning tool or validated fixture was found. |

**Board cards**

- `dec.zone-source` — **Choose the configuration source of truth**. Options compare “one shared `zones.yaml`” with “retain separate files until an adapter is verified”; the recommendation favors the shared file, while the card shows the `partial` evidence and lets the reviewer add a note.
- `backlog.1` — **Measure time/update-rate behavior before setting sensitivity**. Project `event-detection`, kind `scout`, route `Main home`, status `not started`. Dispositions are `approve-and-dispatch-now`, `approve-but-queue`, `park-until-date`, and `drop`.
- `backlog.2` — **Bound a receiver-placement planning scout**. Project `event-detection`, kind `scout`, route `Main home`, status `not started`; the card identifies it as a future idea rather than a current commitment.

The worker reads and trims `config/lavish-axi-host` into `lan_host`, validates that private IP with the standard library, sets `LAVISH_AXI_HOST="$lan_host"` and `LAVISH_AXI_LINK_HOST="$lan_host"`, and reports the printed `http://${lan_host}:<port>/session/...` URL only after `curl` succeeds. In a real run the worker binds the decision source before `bin/fm-procevent-lavish.sh arm <artifact.html> --for <task-id>`; the validation run does not bind or arm.

**Captured answer receipt**

```text
key=backlog.1
choice=approve-but-queue
note=Collect a baseline across both fast and slow tags before tuning thresholds.
status=not started (at board build)
project=event-detection
kind=scout
route=Main home
```

Firstmate then files `backlog.1` as queued with the recorded project, kind, and route. It does not dispatch it. A later `approve-and-dispatch-now` answer would file and dispatch through the normal lifecycle; `park-until-date` would file and apply the exact dated hold; `drop` would record the disposition without filing. The board answer is the authorization boundary, and the worker's recommendation alone never creates work.
