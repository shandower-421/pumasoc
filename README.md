# PumaSOC

A browser-based retro cyberpunk Security Operations Center management game. Defend your client estate against nation-state and criminal threat actors across 7 game days.

## Play

Open `soc-game/index.html` in any modern browser. No build step, no dependencies (except Google Fonts for typography).

A local server gives the best experience:

```bash
python3 -m http.server 8742
# then open http://localhost:8742/soc-game/
```

## How to Play

You run a global SOC. Threats spawn continuously — your job is to detect, respond, and harden before they breach.

**Win:** Survive 7 game days with fewer than 3 breaches.
**Lose:** 3 successful breaches.

### Core Loop

1. Threats probe your estate. Your **Detection Coverage %** determines whether they're caught immediately on spawn.
2. Detected threats create **incidents** in the center panel — assign an analyst to start IR.
3. Undetected threats silently dwell and breach after a difficulty-dependent window.
4. Unhandled incidents also breach if left unassigned too long.
5. A **Lessons Learned** prompt appears after each resolved incident — click it for a permanent IR speed bonus. It expires after ~1 game hour.

### Difficulty

| | Analyst | Engineer | Hunter |
|---|---|---|---|
| Starting budget | $800k | $500k | $300k |
| Income / cycle | $75k | $50k | $30k |
| Starting analysts | 3 | 2 | 1 |
| Detection base | 45% | 25% | 10% |
| Threat spawn rate | −25% | baseline | +20% |
| Undetected breach window | ~2.4 min | ~2 min | ~1.6 min |
| Unhandled breach window | ~4.8 min | ~4 min | ~3.2 min |

Times are at 1× speed. On Hunter, 90% of threats spawn undetected — invest in Detection Engineering immediately and hire a second analyst before the backlog builds.

### Capabilities

| Action | Effect |
|---|---|
| **Detection Eng** | Upgrade SIEM rules per ATT&CK category — raises Coverage % |
| **Vuln Mgmt** | Scan and patch assets — lowers Vulnerability Score and reduces threat spawn rate |
| **Red Team** | Commission pentests to reveal hidden detection gaps and suppress attack categories |
| **Threat Intel** | Subscribe to feeds for passive Coverage + MTTD boosts |
| **Hire Analyst** | Add responders (max 8, $75k each) — each handles one incident at a time |
| **Send to Training** | Takes an analyst offline temporarily; permanently boosts all analyst IR speed by 10% (max +30%) |
| **MSSP** | Auto-assigns to incidents when all your analysts are busy ($15k/cycle retainer) |
| **Consultant** | Retains an expert for a daily prioritised recommendation and passive defensive bonus |

### Random Events

Periodically the game throws a crisis: DDoS barrages, tool outages, zero-days, insider threats, and emergency budget audits. Active crises appear in the left panel and may temporarily penalise Coverage % or drain budget.

### Controls

| Key | Action |
|---|---|
| `Space` | Pause / Resume |
| `1` `2` `3` `4` | Game speed (1×–4×) |
| `?` | How to play |
| `M` | Mute / unmute |

**NEW GAME** button in the header resets to the difficulty screen.

### Persistence

The game auto-saves every ~10 real seconds and whenever you navigate away or close the tab. On reload, it picks up exactly where you left off. State is stored in `localStorage` under key `pumasoc_save`.

## Threat Actors

APT28 (Fancy Bear), APT29 (Cozy Bear), Lazarus Group, APT41, Sandworm, Volt Typhoon, FIN7, REvil, BlackCat, LockBit, TA505, and others — each with distinct skill ratings (1–5) affecting detection difficulty and attack speed.

## Tech

Single HTML file (~4200 lines). Vanilla JS game loop at 100ms ticks. No frameworks, no build tooling.
