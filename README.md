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

**Win:** Survive 7 game days with fewer than 3 breaches and at least $200k budget remaining.
**Lose:** 3 successful breaches.

### Core Loop

1. Threats probe your estate. Your **Detection Coverage %** determines whether they're caught.
2. Detected threats create **incidents** in the center panel — assign an analyst to start IR.
3. Undetected threats silently dwell and breach after ~2 real minutes.
4. Unhandled incidents breach after ~4 real minutes.
5. A **Lessons Learned** prompt appears after each resolved incident — click it for a bonus. It expires after ~1 game hour.

### Capabilities

| Action | Effect |
|---|---|
| **Detection Eng** | Upgrade SIEM rules per ATT&CK category — raises Coverage % |
| **Vuln Mgmt** | Scan and patch assets — lowers Vulnerability Score |
| **Red Team** | Commission pentests to reveal hidden detection gaps |
| **Threat Intel** | Subscribe to feeds for passive Coverage + MTTD boosts |
| **Hire Analyst** | Add responders (max 8) — each handles one incident at a time |
| **Send to Training** | Takes an analyst offline temporarily; permanently boosts all analyst IR speed by 20% |
| **MSSP** | Auto-assigns to incidents when all your analysts are busy |
| **Consultant** | Retains an expert for a daily prioritised recommendation |

### Random Events

Periodically the game throws a crisis: DDoS barrages, tool outages, zero-days, insider threats, and emergency budget audits. Active crises appear in the left panel and may temporarily penalise Coverage %.

### Controls

| Key | Action |
|---|---|
| `Space` | Pause / Resume |
| `1` `2` `3` `4` | Game speed (1×–4×) |
| `?` | How to play |

**NEW GAME** button in the header resets to the difficulty screen (requires a confirmation click).

### Persistence

The game auto-saves every ~10 real seconds and whenever you navigate away or close the tab. On reload, it picks up exactly where you left off — no manual save/load needed.

## Threat Actors

APT28 (Fancy Bear), APT29 (Cozy Bear), Lazarus Group, APT41, Sandworm, Volt Typhoon, FIN7, REvil, BlackCat, LockBit, TA505, and others — each with distinct skill ratings affecting detection difficulty.

## Tech

Single HTML file (~3500 lines). Vanilla JS game loop at 100ms ticks. No frameworks, no build tooling. State persisted to `localStorage` under key `pumasoc_save`.
