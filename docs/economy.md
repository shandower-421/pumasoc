# PUMA SOC — Economy & Cost Mechanics

> Design reference for all budget, income, spending, and scoring systems.
> All values sourced from `index.html` constants and runtime logic.

---

## Table of Contents

1. [Starting Budget](#1-starting-budget)
2. [Income](#2-income)
3. [Recurring Fees & Auto-Cancellation](#3-recurring-fees--auto-cancellation)
4. [Analyst Workforce](#4-analyst-workforce)
5. [Operational Actions](#5-operational-actions)
6. [Capability Purchases](#6-capability-purchases)
   - Detection Engineering
   - SOAR Platform
   - Vulnerability Management
   - Threat Intel Feeds
7. [Partner Services](#7-partner-services)
   - SAT
   - MSSP
   - Red Team
   - Consultants
8. [Difficulty Modifiers](#8-difficulty-modifiers)
9. [Week Transitions](#9-week-transitions)
10. [Random Events](#10-random-events)
11. [Budget Display Thresholds](#11-budget-display-thresholds)
12. [Scoring — Budget's Role](#12-scoring--budgets-role)
13. [Economy at a Glance](#13-economy-at-a-glance)

---

## 1. Starting Budget

| Difficulty | Starting Budget | Starting Analysts |
|------------|-----------------|-------------------|
| Analyst    | $800,000        | 3                 |
| Engineer   | $500,000        | 2                 |
| Hunter     | $300,000        | 1                 |

Budget carries over between weeks — there is no reset. Unspent money from Week 1 rolls into Week 2, which makes early investment decisions consequential for the whole campaign.

---

## 2. Income

Income is the primary budget replenishment mechanism. It fires on a fixed game-time interval.

| Parameter                  | Value                                    |
|---------------------------|------------------------------------------|
| Base interval             | 360 game-minutes (6 game-hours)          |
| Analyst amount/cycle      | $75,000                                  |
| Engineer amount/cycle     | $50,000                                  |
| Hunter amount/cycle       | $30,000                                  |

**Income decay across weeks:**
The income amount is permanently reduced at each week transition. Difficulty sets the Week 1 baseline; from there:

| Week | Multiplier applied at transition |
|------|----------------------------------|
| → W2 | × 0.85 (−15%)                   |
| → W3 | × 0.80 (−20% of W2 amount)      |
| → W4 | × 0.75 (−25% of W3 amount)      |

A player who starts at $50k/cycle (Engineer) will earn $42.5k/cycle in Week 2, $34k/cycle in Week 3, and $25.5k/cycle in Week 4.

**Implication:** The game is designed around a shrinking income baseline. Capabilities purchased in early weeks must compensate for fewer resources later. Recurring fees become proportionally more expensive over time.

---

## 3. Recurring Fees & Auto-Cancellation

Two services have an ongoing per-cycle cost deducted automatically at each income tick:

| Service | Setup Cost | Recurring Fee / Cycle |
|---------|------------|-----------------------|
| MSSP    | $150,000   | −$15,000              |
| SAT     | $50,000    | −$10,000              |

Fees are deducted **after** the income deposit. If the resulting budget would go negative after deduction, the service is **automatically cancelled** — no refund.

Consultants are also auto-cancelled if budget drops to zero at an income tick.

**Combined recurring drag** (both services active): −$25,000/cycle. At late-game Engineer income (~$25k/cycle), this means running both MSSP and SAT at the same time can zero out an entire income cycle.

---

## 4. Analyst Workforce

| Parameter      | Value    |
|----------------|----------|
| Hire cost      | $75,000  |
| Maximum count  | 8        |

Analysts are the core operational resource. Every active analyst slot handles incident triage and containment. Many actions (Training, Threat Hunt, Tabletop, Patch Sprint, OSINT) consume a free analyst slot for their duration and are blocked when no analyst is idle.

**Hire condition:** Budget ≥ $75k AND analyst count < 8.

Training stacks: up to +30% IR speed bonus from three $30k training sessions (one per free analyst slot).

---

## 5. Operational Actions

These are one-shot actions with an upfront cost and a game-time duration. All require at least one idle analyst.

| Action            | Cost    | Duration      | Effect                                                              |
|-------------------|---------|---------------|---------------------------------------------------------------------|
| Send to Training  | $30,000 | 24 game-hours | +10% IR speed (permanent, max +30% from 3 sessions)               |
| Threat Hunt       | $40,000 | 8 game-hours  | Discovers 2–4 hidden detection gaps; may reveal unknown vulns      |
| Tabletop Exercise | $25,000 | 4 game-hours  | +15% IR speed bonus for 48 game-hours                             |
| Patch Sprint      | $10,000 | 3 game-hours  | Auto-patches all discovered LOW severity vulns at no extra cost   |
| OSINT Research    | $20,000 | 6 game-hours  | Grants 3 guaranteed-detection charges (next 3 threats auto-detected)|

**Patch Sprint economics:** Each LOW vuln would individually cost $1k–$3k; a sprint at $10k can clear multiple at once, but requires a discovered list. Run a scan first.

---

## 6. Capability Purchases

### Detection Engineering

Upgrades detection rules across 12 MITRE ATT&CK technique categories:

```
Initial Access · Execution · Persistence · Privilege Escalation ·
Defense Evasion · Credential Access · Discovery · Lateral Movement ·
Collection · Command & Control · Exfiltration · Impact
```

**Upgrade costs per level (0 → 5 per category):**

| Level | Cost to reach |
|-------|--------------|
| 0 → 1 | $25,000     |
| 1 → 2 | $30,000     |
| 2 → 3 | $35,000     |
| 3 → 4 | $40,000     |
| 4 → 5 | $50,000     |

**Per level bonus:** +5% detection coverage, −8 min MTTD.
**Max-level one category:** $180k total for +25% coverage and −40 min MTTD.
**Full max (all 12 categories):** $2,160,000 — theoretically impossible in a single playthrough.

⚠️ **Week decay:** Rules degrade by 1–2 levels at each week transition. High-level rules represent the largest week-over-week budget sinkhole.

---

### SOAR Platform

| Item              | Cost                                        |
|-------------------|---------------------------------------------|
| Platform (one-time) | $100,000                                  |
| Per playbook      | $20,000 (up to 2 levels, 12 categories)    |

SOAR must be purchased before any playbook can be deployed. Each playbook targets one MITRE ATT&CK category and reduces TRIAGE/CONTAIN time:

- Level 1: −25% triage/contain
- Level 2 (MAX): −50% triage/contain

**Total SOAR investment (platform + all 12 categories at max):**
$100k + (12 × 2 × $20k) = $580,000

⚠️ **Week decay:** Playbooks degrade by 1 level at each week transition. Unlike detection rules (which persist partially), a Level 2 playbook becomes Level 1 — requiring another $20k to restore.

---

### Vulnerability Management

Vulnerability costs are assigned at spawn based on severity:

| Severity | Individual Patch Cost |
|----------|-----------------------|
| HIGH     | $8,000 – $12,000      |
| MEDIUM   | $3,000 – $6,000       |
| LOW      | $1,000 – $3,000       |

**Bulk discount:** Patching Critical (HIGH) or All at once applies a **15% discount** on the aggregate cost.

**Scan cost:** $10,000 — required before any patching is possible.

**Practical patch budget:** A typical week might surface 6–10 vulns. A worst-case set (4 HIGH, 4 MED, 4 LOW) could cost ~$80k–$100k to clear individually, or ~$70k–$85k bulk.

---

### Threat Intel Feeds

Intel feeds are **7-day rolling subscriptions** — each feed expires 7 game-days after purchase, regardless of week boundaries. The SIEM logs a warning when a feed lapses and shows days remaining while active. Higher-tier feeds unlock as the campaign progresses.

**Week 1:**

| Feed                 | Cost    | Detection | MTTD    | Vuln Bonus |
|----------------------|---------|-----------|---------|------------|
| OSINT Aggregator     | $30,000 | +5%       | −15 min | —          |
| IOC Feed Premium     | $55,000 | +8%       | −25 min | —          |
| Nation-State Intel   | $80,000 | +12%      | −40 min | —          |

**Week 2+:**

| Feed              | Cost     | Detection | MTTD    | Vuln Bonus |
|-------------------|----------|-----------|---------|------------|
| Dark Web Monitor  | $100,000 | +10%      | −30 min | —          |
| Zero-Day Exchange | $120,000 | +5%       | —       | −25        |

**Week 3+:**

| Feed                       | Cost     | Detection | MTTD    | Vuln Bonus |
|----------------------------|----------|-----------|---------|------------|
| Threat Graph Analytics     | $140,000 | +12%      | −35 min | —          |
| Adversary Profile Database | $160,000 | +8%       | −20 min | —          |

**Week 4+:**

| Feed                     | Cost     | Detection | MTTD    | Vuln Bonus |
|--------------------------|----------|-----------|---------|------------|
| Predictive Threat Engine | $175,000 | +15%      | −50 min | —          |
| Insider Threat Monitor   | $190,000 | +10%      | −25 min | −15        |

**All W1 feeds:** $165k for +25% detection and −80 min MTTD.
**Subscription timing:** Feeds bought early in a week carry over into the next if they have days remaining — no forced repurchase at week transition. A feed bought on Day 1 expires on Day 8, bridging the week boundary.

---

## 7. Partner Services

### SAT (Security Awareness Training)
- **Setup:** $50,000 | **Recurring:** $10,000/cycle
- **Effect:** Blocks ~35% of Initial Access attempts
- Best purchased early — maximum passive protection per dollar when threat density is still manageable.

### MSSP (Managed Security Service Provider)
- **Setup:** $150,000 | **Recurring:** $15,000/cycle
- **Effect:** Automates analyst assignment — whenever an unassigned incident exists and a free analyst slot is available, the MSSP immediately assigns one. Adds no analyst capacity; removes the manual assignment step.
- High setup cost for a convenience benefit. Most valuable when managing a large incident queue and missing assignments due to inattention.

### Red Team Engagements

One-shot purchases. Multiple can run concurrently. No ongoing fee.

| Engagement                  | Cost     | Duration      |
|-----------------------------|----------|---------------|
| Phishing Simulation         | $30,000  | 12 game-hours |
| External Network Pentest    | $50,000  | 24 game-hours |
| Internal Network Assessment | $75,000  | 48 game-hours |
| Cloud Security Review       | $100,000 | 36 game-hours |
| Full Red Team Engagement    | $150,000 | 72 game-hours |

**Effects on completion:** +2–6% coverage, discovers 1–4 hidden vulns, and suppresses one MITRE technique category from spawning threats for 48 hours.

**Best value:** Phishing Simulation at $30k provides guaranteed coverage gains and vuln discovery at the lowest entry point.

### Consultants

All consultants cost a flat **$75,000** one-time engagement fee. Only one can be active at a time (switching replaces the current, no partial refund implied).

| Consultant       | Role                  | Bonus                                  |
|------------------|-----------------------|----------------------------------------|
| Cassandra Voss   | Detection specialist  | +8% coverage, −20 min MTTD            |
| Marcus Webb      | Offensive security    | +3% coverage, −20 vuln score           |
| Priya Nair       | Risk & resilience     | +5% coverage, −10 min MTTD, −10 vuln  |

Consultants provide daily tactical briefings and recommended actions beyond their passive bonuses. Each engagement runs for **7 game-days from hire** — the days remaining are shown in the left panel and in the modal. Re-engage before expiry to maintain uninterrupted bonuses. Consultants are also auto-cancelled if budget drops to zero at an income tick.

---

## 8. Difficulty Modifiers

| Parameter             | Analyst   | Engineer  | Hunter     |
|-----------------------|-----------|-----------|------------|
| Starting budget       | $800,000  | $500,000  | $300,000   |
| Income/cycle          | $75,000   | $50,000   | $30,000    |
| Starting analysts     | 3         | 2         | 1          |
| Base coverage         | 45%       | 25%       | 10%        |
| Base vuln score       | 45        | 65        | 80         |
| Threat spawn rate     | ×0.75     | ×1.0      | ×1.2       |
| Breach dwell window   | 720 min   | 600 min   | 480 min    |
| Incident expire window | 1440 min | 1200 min  | 960 min    |

Difficulty does **not** change capability costs — the same $100k SOAR costs the same on Hunter as on Analyst. The pressure is entirely on income and starting headroom.

---

## 9. Week Transitions

At each week boundary the following budget-relevant changes occur:

| Item                       | What happens                                                        |
|----------------------------|---------------------------------------------------------------------|
| Budget                     | Carries over — no reset                                             |
| Income amount              | Multiplied down (×0.85 / ×0.80 / ×0.75 cumulatively)              |
| Detection rules            | Decay by 1–2 levels per category                                    |
| Intel feed subscriptions   | Unaffected — expire by time (7 days from purchase)                  |
| Consultant                 | Unaffected — expires by time (7 days from hire)                     |
| SOAR playbooks             | Degrade by 1 level per category                                     |
| SOAR platform              | Persists                                                            |
| Analysts                   | Carry over                                                          |
| Analyst speed bonus (IR)   | Persists                                                            |
| Spawn multiplier           | Escalates by ×1.25                                                  |
| New CVEs                   | 6–9 added depending on week                                         |

**Budget planning implication:** Week transitions create a predictable "reset tax." A player should budget for:
1. Restoring decayed detection rules (potentially $200k+)
2. Restoring SOAR playbooks ($20k × affected categories)
3. Renewing any intel feeds or consultant engagements that lapse ($30k–$190k for feeds; $75k for consultant)

---

## 10. Random Events

Random events fire during gameplay and can affect the budget:

| Event                  | Budget Impact | Other Effect                       |
|------------------------|---------------|------------------------------------|
| DDoS Barrage           | None          | −20% coverage for 8h               |
| Tool Outage            | None          | −15% coverage for 4h               |
| Zero-Day In the Wild   | None          | Forces Skill-5 threat spawn        |
| Insider Threat         | None          | Spawns insider-type incident        |
| **Emergency Budget Audit** | **−$20,000** | Regulatory seizure — no warning  |

The Budget Audit is the only event with a direct cash drain. At low budget thresholds (< $50k) this can cause immediate service cancellations.

---

## 11. Budget Display Thresholds

The budget counter in the header changes color based on current balance:

| Color  | Threshold        | Meaning                                  |
|--------|------------------|------------------------------------------|
| 🟢 Green  | ≥ $150,000    | Healthy — room to invest                 |
| 🟡 Amber  | $50k – $149k  | Caution — recurring fees are at risk     |
| 🔴 Red    | < $50,000     | Critical — service cancellation imminent |

The $150k amber threshold is intentional: it roughly equals one MSSP setup + one income cycle of fees, making it a meaningful early-warning zone.

---

## 12. Scoring — Budget's Role

**Budget does not directly affect the grade.**

The end-of-week score is determined entirely by:
- Breach count (`G.weekBreaches`) — primary grade floor
- Detection coverage %
- Average MTTD (minutes)
- Unpatched discovered vulnerabilities
- Open (unresolved) incidents

The score card does display `totalSpent` (calculated as `startBudget + totalIncome − currentBudget`) for informational purposes, but this figure has no weight in the grade formula.

**Indirect budget effects on score:**
- Buying coverage upgrades → directly improves grade threshold
- Buying intel feeds → lowers MTTD → improves grade threshold
- Buying SOAR/SAT → reduces breach risk → prevents grade floor
- Not buying vulns → unpatched count → blocks S/A grades

Money is the means, not the end. A player who spends efficiently and scores S grades can "win" while another player who hoards budget but takes breaches will fail.

---

## 13. Economy at a Glance

### Cost tiers (one-time spends)

| Tier       | Range          | Examples                                                        |
|------------|----------------|-----------------------------------------------------------------|
| Micro      | $10k – $30k    | Scan, Patch Sprint, Tabletop, Phishing Sim, Training, W1 OSINT |
| Small      | $30k – $75k    | Hunt, SAT, Ext Pentest, Consultant, Analyst, W1 IOC/APT feeds  |
| Medium     | $75k – $150k   | MSSP, Int Assessment, Cloud Review, W2 feeds                   |
| Large      | $150k – $300k  | Full Red Team, W3–W4 intel feeds, SOAR full stack              |
| Commitment | $300k+         | Full detection tree restoration                                 |

### Income vs. fees (Engineer difficulty, W1)

```
Income per cycle:           $50,000
  − MSSP recurring:        −$15,000
  − SAT recurring:         −$10,000
  Net per cycle (both on):  $25,000 free cash
  Net per cycle (neither):  $50,000 free cash
```

### Approximate budget for a "full stack" Week 1

| Item                                   | Cost      |
|----------------------------------------|-----------|
| Hire 1 analyst                         | $75,000   |
| Detection rules (3 categories, L1)     | $75,000   |
| SOAR platform                          | $100,000  |
| SOAR playbooks (4 categories, L1)      | $80,000   |
| Intel: OSINT + IOC feeds               | $85,000   |
| Consultant (Cassandra Voss)            | $75,000   |
| SAT                                    | $50,000   |
| Vulnerability scan + HIGH patches      | ~$60,000  |
| **Total**                              | **~$600,000** |

On Engineer difficulty ($500k start, ~2 income cycles before week ends ≈ +$100k), this plan nearly fits the available budget — but leaves almost no reserve. Any unplanned expense (emergency audit, extra HIGH vulns) pushes into deficit. Prioritisation is still mandatory.
