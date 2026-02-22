# Fleet Cost Optimizer

**A MyGeotab Add-In that turns raw telematics data into a prescriptive 90-day action plan.**

One click. 30 seconds. Tells your fleet manager exactly who to call, what to fix, and by when — with dollar amounts attached.

![Version](https://img.shields.io/badge/version-1.9-blue) ![License](https://img.shields.io/badge/license-MIT-green) ![Platform](https://img.shields.io/badge/platform-MyGeotab-orange)

---

## The Problem

MyGeotab shows idle hours, fault codes, and exception counts. What it doesn't show is:

> "Demo-19 has 4 recurring fault codes. Schedule a diagnostic this week — a $400 inspection now avoids a $3,500 breakdown. Assign to your maintenance coordinator. Deadline: Day 7."

Every fleet manager I've talked to has the same complaint: plenty of data, no clear next steps. The gap between "seeing the numbers" and "knowing what to do" costs real money. For the demo fleet of 50 vehicles, that gap is about **$10,843/month** in ghost vehicles sitting in the lot, engines idling through lunch breaks, and maintenance getting deferred until something breaks.

## What It Does

**Run Audit** → pulls 365 days of trip, fault, exception, and device data via the Geotab JS API → processes everything in the browser → generates a full report:

- **Executive Summary** with fleet health score, seasonal context, and industry benchmarks (NRCan FleetSmart)
- **4 Finding Cards** — ghost vehicles, idle burn, high-risk driving, breakdown risk — each with dollar amounts and flagged vehicles
- **Device Noise Detection** — separates real mechanical faults from Geotab GO accelerometer issues. Tells you "call IT, not a mechanic" when it's a $0 firmware fix instead of a $400 diagnostic
- **90-Day Action Plan** — URGENT / 30-day / 60-day / 90-day phases, with named owners, deadlines, escalation paths, and measurable KPIs
- **Sustainability Card** — CO₂ tonnes from idle, Clean Fuel Regulations context, ISSB S2 via CSA, provincial carbon pricing ($65-170/tonne)
- **Evidence Report** — per-vehicle drill-down with idle peak hours, fault code details, and trip patterns

Everything is print-ready. Hit Ctrl+P and hand it to your boss.

## Install (2 minutes, zero infrastructure)

1. Log into MyGeotab → **Administration** → **System** → **System Settings** → **Add-Ins**
2. Toggle **Allow unverified Add-Ins** to Yes
3. Click **New Add-In** → **Configuration** tab
4. Paste the contents of `fleet-cost-optimizer-v2.0.json`
5. **Save** → hard refresh (Ctrl+Shift+R)
6. Find **"Fleet Cost Optimizer"** in the sidebar → click **Run Audit**

No server. No API keys. No build step. The entire tool is a single HTML file embedded in a JSON config.

## Technical Architecture

*Required per Competition T&Cs Section IV.2*

```
┌─────────────────────────────────────────────────────────────────┐
│                      MyGeotab Platform                          │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │               Add-In iframe (audit.html)                  │  │
│  │                    ~48KB single file                      │  │
│  │                                                           │  │
│  │  ┌──────────────────┐    ┌────────────────────────────┐  │  │
│  │  │   Geotab JS API  │    │    Analysis Engine         │  │  │
│  │  │                  │───▶│                            │  │  │
│  │  │   api.multiCall  │    │  • Ghost detection (<40%) │  │  │
│  │  │   5 endpoints:   │    │  • Idle peaks (3hr bins)  │  │  │
│  │  │                  │    │  • Risk scoring (2× avg)  │  │  │
│  │  │   1. Device      │    │  • Device noise filter    │  │  │
│  │  │   2. Trip        │    │  • Fault classification   │  │  │
│  │  │   3. Exception   │    │  • Cost modeling (CAD)    │  │  │
│  │  │   4. Rule        │    │  • CO₂ calculation        │  │  │
│  │  │   5. FaultData   │    │                            │  │  │
│  │  └──────────────────┘    └─────────────┬──────────────┘  │  │
│  │                                        │                  │  │
│  │                                        ▼                  │  │
│  │  ┌────────────────────────────────────────────────────┐  │  │
│  │  │              Report Generator                       │  │  │
│  │  │                                                     │  │  │
│  │  │  Executive Summary ──▶ 4 Finding Cards              │  │  │
│  │  │  ──▶ 90-Day Action Plan ──▶ Sustainability Card     │  │  │
│  │  │  ──▶ Evidence Report (per-vehicle drill-down)       │  │  │
│  │  │  ──▶ Print-ready output (@media print CSS)          │  │  │
│  │  └────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                 │
│  Data Flow:                                                     │
│  User clicks "Run Audit"                                        │
│   → JS API multicall (5 endpoints, 365-day window)              │
│   → ~30 seconds processing in browser                           │
│   → Full report rendered to DOM                                 │
│   → Zero external calls after initial API load                  │
└─────────────────────────────────────────────────────────────────┘
```

## Cross-Validation Results

Compared tool output against official Geotab reports:

| Metric | Fleet Cost Optimizer | Official Geotab Report | Match |
|--------|---------------------|----------------------|-------|
| Top idle vehicles | Demo-31 (8.0h), Demo-15 (7.9h), Demo-32 (6.5h) | Same vehicles in top positions | ✅ |
| Fleet idle rate | 9% | 8.8% | ✅ Within rounding |
| Ghost vehicles | 11 below 40% utilization | 11 in lowest activity tier | ✅ |
| Demo-39 faults | Device noise — 111 accelerometer records | 111 accelerometer records in Fault Report | ✅ Exact |
| Demo-15 idle | 7.9 hrs | 7.6 hrs (Geotab server-side) | ⚠️ ~4% rounding across 254 trips |
| Demo-42 | Device noise — 30 accelerometer records | Same pattern as Demo-39/40/41 | ✅ Caught in v2.0 |

The device noise discovery (Demo-39, 40, 41, 42) only happened because of this validation step. Without it, the tool would have told fleet managers to send IT problems to mechanics.

## Competition Fit

| Prize | How This Targets It |
|-------|-------------------|
| Vibe Master ($10K) | End-to-end: strategy → build → validate → polish, all with AI tools |
| Innovator ($5K) | Device noise detection, prescriptive action plans, 3-way fault classification |
| Green Award ($2.5K) | Canadian sustainability story: CO₂, Clean Fuel Regs, ISSB S2, carbon pricing |
| Disruptor ($2.5K) | Tells managers what to DO, not what to look at |
| Intelligence Award ($2.5K) | Built with Geotab Add-In Architect Gem (Google Gemini) |

## The Vibe Coding Stack

| Tool | Role | Where |
|------|------|-------|
| Geotab Add-In Architect Gem | Code generation, feature builds, bug fixes | Google Gemini Gem store |
| Claude | Strategy, data validation, device noise fix, sustainability, debugging | Anthropic |

~50 Gem prompts + ~20 Claude prompts = ~70 total. Full story in [VIBE_CODING_JOURNEY.md](VIBE_CODING_JOURNEY.md).

## Files

| File | What It Is |
|------|-----------|
| `fleet-cost-optimizer-v2.0.json` | The Add-In config — paste into MyGeotab |
| `audit-v2.0.html` | Standalone HTML for local testing |
| `VIBE_CODING_JOURNEY.md` | Full build story with every prompt and bug |
| `PROMPTS.md` | All 70 prompts (Gem + Claude), chronological |
| `Google_Gem_History.txt` | Raw, unedited Gem conversation export |
| `screenshots/` | Tool output screenshots |

## Demo Video

📹 [YouTube link — TODO]

| Time | What's Shown |
|------|-------------|
| 0:00 | The problem: data without action |
| 0:25 | Run audit, $10,843 finding |
| 0:55 | Four finding cards |
| 1:25 | Roadmap: fault codes, named owners, escalation |
| 2:00 | Device noise detection + sustainability |
| 2:30 | How it was built (vibe coding) |

## License

MIT — see [LICENSE](LICENSE).
