# Fleet Cost Optimizer

**A MyGeotab Add-In that audits your fleet and builds a 90-day action plan.**

One click. 30 seconds. Pulls from 5 API endpoints, cross-references everything, and hands your fleet manager a ready-to-act report — findings, fault codes, cost estimates, and a 90-day plan.

![Version](https://img.shields.io/badge/version-2.0-blue) ![License](https://img.shields.io/badge/license-MIT-green) ![Platform](https://img.shields.io/badge/platform-MyGeotab-orange)

<div align="center">

### 🚀 <a href="https://sanjayaabeysinghe.github.io/fleet-cost-optimizer/fco-intro-v2.html" target="_blank">Watch the Interactive Intro →</a>
*60-second animated walkthrough — what the tool finds, how it classifies, and the 90-day plan it builds*

</div>

---

## The Problem

MyGeotab has over 100 built-in reports. Here's what a fleet manager sees when they open the reports page:

![MyGeotab Reports Dashboard](screenshots/00-mygeotab-reports.png)

Twelve categories. 39 productivity reports. 29 maintenance reports. 14 safety reports. Each one answers a single question — "which vehicles were idling?" or "who had fault codes?" — but none of them connect the dots. To actually act on this data, a fleet manager has to open 5-6 reports, cross-reference them manually, figure out what matters, decide who handles it, and somehow track whether anything got done.

That gap — between seeing the numbers and knowing what to do — costs real money. For this demo fleet of 50 vehicles, it's **$12,443/month.**

> *"Why not just use the built-in Fleet Utilization Report?"* — See [Appendix A](#appendix-a-fleet-utilization-report-comparison) for a side-by-side comparison.

---

## What Fleet Cost Optimizer Does

One button replaces those 5-6 reports with a single audit — what's wrong, what it costs, who owns the fix, and when it's due.

### Executive Summary

![Executive Summary](screenshots/01-executive-summary.png)

The first thing you see after clicking "Run Audit." Version, date, analysis window (23 days across 2 months), and the headline: **$12,443 CAD/month in recoverable costs.** Below that:

- **Canadian Winter:** NRCan recommends 3-5 min warm-up max for modern diesels. Top idler at 8.0 hrs is well beyond cold-weather needs.
- **Benchmarks:** Fleet idle rate 9% — within NRCan's 15% target. Worth noting it's not all bad news.
- **Key Findings:** 13 ghost vehicles, 2 mechanical issues, **4 device issues** (not engine problems — Geotab device reinstall). Mechanical and device are split right in the summary so nobody wastes a mechanic on a firmware fix.

The yellow warning at the bottom flags the data gap: "Tool requested 365 days of history — database contains 23 days."

### Sustainability & Carbon Impact

![Sustainability Card](screenshots/02-sustainability.png)

Mapped to what a Canadian fleet operator actually deals with in 2026. Four regulatory frameworks: Clean Fuel Regulations (SOR/2022-140, still in effect), provincial carbon pricing (BC carbon tax + Quebec cap-and-trade), government contract ESG requirements, and ISSB S2 climate disclosure via CSA.

The carbon footprint table breaks idle hours into 511L fuel/month, 1.4 tonnes CO₂, $628 CAD. Reduction scenarios show what 30% and 65% cuts look like. The "642 trees" and "4.7 passenger cars" equivalencies at the bottom are there for sustainability reporting.

### Monthly Idle Trend

![Monthly Idle Trend](screenshots/03-idle-trend.png)

Bars show idle hours by month, color-coded by season. With only 2 months of data (Jan: 4h, Feb: 142h), the trend is steep — but the pink "Data Maturity" callout explains why. Next audit is scheduled for May 23, 2026. 23 days isn't enough for seasonal comparison and the tool says so.

### Total Opportunity

![Total Opportunity](screenshots/04-total-opportunity.png)

The anchor number. $12,443 CAD/month. The annual estimate ($149,315) comes with a warning: "23d sample — run quarterly for higher confidence."

### The Four Finding Cards

Four categories of recoverable cost, ranked by impact.

#### Ghost Vehicles — $10,400/mo

![Ghost Vehicles](screenshots/05-ghost-vehicles.png)

13 of 50 vehicles below 40% utilization. At $800/mo each (lease + insurance), these are the biggest line item. Demo-10 at 22% utilization and $3/km is the worst offender. Utilization is calculated from each vehicle's actual first trip date — not a fixed 30-day window — so a vehicle that joined the fleet last week doesn't get falsely flagged.

#### Idle Time Burn — $243/mo

![Idle Time Burn](screenshots/06-idle-burn.png)

Demo-31 leads at 8.0 hours. Peak idle happens midday, so this needs an engine-off policy, not a block heater. Fuel cost calculated at $4.30/hour using real trip-level idling durations from the Geotab API.

#### High-Risk Driving — $1,000/mo

![High-Risk Driving](screenshots/07-high-risk.png)

5 vehicles exceed 2× the fleet safety average. "**4 device noise issues found**" is flagged right in the description — so nobody confuses accelerometer mounting problems with dangerous driving. Demo-39 at 6.2× average looks alarming until you realize 15 of its 19 events are device-generated Engine Fault Exceptions, not actual driving behavior.

#### Breakdown Risk — $800/mo

![Breakdown Risk](screenshots/08-breakdown-risk.png)

Demo-19 has 4 recurring fault codes (Low Priority Warning Light × 4, spanning Jan 31 to Feb 20). The recurring pattern — not a one-time glitch — is what triggers the flag. $400 proactive inspection vs $1,200-$3,500 roadside breakdown.

---

## The Roadmap — What Makes This Different

Most fleet tools stop at the finding cards. The fleet manager already knows what to do with a problem vehicle — but getting there means opening the Fault Report, cross-referencing Trip History, checking Exception Events, and piecing together whether it's mechanical, device-related, or driver behavior. That takes time. The Roadmap does that cross-referencing upfront and organizes everything by urgency, so the fleet manager can act on it immediately.

Four phases, each with a dollar target showing how costs should decrease.

### URGENT: Safety & Mechanical (This Week)

![Roadmap URGENT](screenshots/09-roadmap-urgent.png)

Findings are organized by role — maintenance coordinator, IT, fleet manager — with the data already attached. Demo-32: Diagnostic Codes (2): Low Priority Warning Light ×2, pattern analysis ("single recurring code — points to one system"), full event breakdown, and the MyGeotab navigation path: `MyGeotab → Faults → Demo - 32`.

Below the mechanical cards, the DEVICE ISSUE cards (Demo-39, 40, 41, 42) route to IT instead of a mechanic: "NOT an engine fault. Geotab GO device: 111 accelerometer noise records." $0 fix under warranty. Without the 3-way classifier, fleet managers send $400 mechanics to fix firmware problems.

### 30-Day Plan: Quick Wins

![30-Day Plan](screenshots/10-30day-plan.png)

Target: $12,443 → $11,570. Five action cards, each with a different owner. Anti-Idle Policy (Fleet Manager → All Drivers, engines OFF after 3 min, NRCan cold-weather exception). Block Heaters (Maintenance, top 3 idlers, 60-90 day payback). Verify Inspections (confirm URGENT vehicles were actually seen). Device Check (IT, 4 noise vehicles). MyGeotab Monitoring (weekly auto-reports). Most of these cost $0.

### 60-Day Plan: Measure & Adjust

![60-Day Plan](screenshots/11-60day-plan.png)

Target: $6,221 (50% reduction). Compare before and after. The red "If stuck" escalation paths are built in — "If idle NOT down 20%: escalate to GPS idle alerts." "No improvement after 30d → progressive discipline." Ghost Review forces a keep/share/remove decision on all 13 underused vehicles.

### 90-Day Plan: Systematize

![90-Day Plan](screenshots/12-90day-plan.png)

Target: $4,355 (65% reduction) + SOP. Third Audit gives 3 data points for trend analysis. Spring Baseline (April) compares winter vs spring — if ghost vehicles still show low utilization in spring, that confirms year-round underuse, not seasonal variation. Fleet Right-Sizing pulls Finance in for lease decisions. SOP codifies everything: monthly audit 1st Monday, weekly idle review, faults within 48h, quarterly fleet review, annual ESG report. Without SOPs, improvements regress within 6 months.

### Progress Tracker

![Progress Tracker](screenshots/13-progress-tracker.png)

Expected metrics at each phase. Recoverable cost declining from $12,443 to $4,355. Idle hours from 57 to 23. High-risk vehicles from 5 to 0. CO₂ from 1.4 to 0.5 tonnes/month. Annual savings at 90d: **$97,055 CAD + 9.9t CO₂.**

### Printable Checklist

![Checklist](screenshots/14-checklist.png)

Every action from the roadmap as a checkbox, grouped by URGENT, Month 1, Month 2, Month 3. Print it, hand it to your team, track completion with a pen.

---

## Evidence Report

Raw data behind every finding. When someone asks "how do you know Demo-31 is a problem?" — this is what you show them.

![Evidence - Idle Details](screenshots/15-evidence-idle.png)

Fleet Overview: utilization distribution (1 vehicle at 0-25%, 15 at 25-50%, 21 at 50-75%, 13 at 75-100%). Each idle vehicle gets trip-level detail — Demo-31's peak is **21:00-00:00** (73% of idle happens evening, not warm-up). Individual high-idle trips with date, idle duration, and what percentage of that trip was idle.

![Evidence - High Risk Details](screenshots/16-evidence-highrisk.png)

Demo-39: 19 events, wall-to-wall Engine Fault Exception at 2-5 AM, tagged "Device noise." Nobody's speeding at 3 AM — this is automated sensor noise. Seeing this pattern is what convinced me to build the 3-way classifier.

---

## Known Limitations

**1. "Mechanical, NOT driver behavior" is too absolute.** Demo-32's card says "Mechanical, NOT driver behavior" — but the event breakdown shows Speeding:2 and Max Speed:1. Three of seven events ARE driver behavior. The classifier picks the dominant type and stamps the whole card, which breaks down when the split is close to 50/50.

**2. Urgency level doesn't match fault severity.** Demo-32 has "Low Priority Warning Light × 2" — the word "Low Priority" is in the diagnostic name. But the URGENT banner says "Pull from service" and prices it at "$400 vs $3,500 breakdown." That response fits a high-priority fault, not two low-priority warnings over 23 days.

**3. Device issues and mechanical issues share the same URGENT banner.** Demo-39's $0 IT ticket sits next to Demo-32's engine faults under the same red header. Mixing them dilutes the real urgency of mechanical flags.

**4. Cost estimates are hardcoded.** Every mechanical vehicle gets "$400 inspection avoids $1,200-$3,500 breakdown" regardless of severity. Every ghost vehicle gets "$800/mo" regardless of vehicle class. Industry averages, not fleet-specific numbers.

**5. 23-day sample extrapolated to annual.** The tool flags this ("23d sample — run quarterly") but the $149,315 annual projection is still prominent. Seasonal variation could make it 20-30% off.

**6. Demo-15 idle rounding.** Tool shows 7.9 hrs, Geotab shows 7.6 hrs. Fractional-second rounding accumulated across 254 trips. ~4% variance.

---

## Future Roadmap (What could make this a better product) 

## Future Roadmap

**Split classification on action cards.** Replace the binary "Mechanical, NOT driver behavior" with "4 of 7 mechanical, 3 of 7 behavioral. Primary: mechanic. Secondary: coaching."

**Severity-aware urgency tiers.** Parse fault code severity from the diagnostic name. "Low Priority Warning Light" → schedule at next routine service. "Malfunction Indicator Lamp" → pull from service now.

**Separate device issues from mechanical in the URGENT section.** Split into "⚠️ Mechanical — Pull from Service" (red) and "📱 Device Issues — IT Ticket" (purple). Device mounting issues aren't emergencies.

**Configurable cost inputs.** A settings panel where you enter your actual lease rate, hourly shop rate, and fuel price. Three fields and every dollar amount in the report becomes real.

**Camera-correlated coaching optimization.** Geotab integrates with camera providers like Surfsight that capture video clips triggered by safety events — hard braking, collisions, speeding, distracted driving. Right now the tool flags behavioral events based on exception counts alone: "Demo-15 has 12 harsh braking events, 3× fleet average." That tells you there's a pattern but not why. A driver who hard-brakes 12 times avoiding pedestrians in a downtown delivery route is a different problem than one tailgating on a highway.

Camera footage closes that gap. Cross-referencing exception events with timestamped video clips would let the tool classify behavioral flags into camera-verified (footage confirms risky behavior — coaching priority) vs. context-justified (footage shows defensive driving or road conditions — downgrade or dismiss). The same logic that prevents sending a mechanic for a firmware problem now prevents sending a safety coach for a driver who's actually handling a tough route well.

The coaching cards in the action plan would change too. Instead of generic "one-on-one conversation, target -50% events in 30d," each card could reference specific clips: "3 of 12 harsh braking events confirmed as following-distance issues (Feb 12, 15, 19 — clips attached). Focus coaching on highway spacing. Remaining 9 events show intersection stops in the downtown core — no action needed." That turns a vague conversation into a 5-minute review with video evidence both the driver and the safety lead can see.

At fleet scale, camera correlation also surfaces route-level problems. If 4 drivers on the same route all trigger harsh braking at the same intersection, that's not a driver issue — it's a road design or route planning issue. The tool could flag "intersection pattern detected" and route it to operations instead of safety. Same classification logic, one more data source.

**Ace API chat panel.** A conversational interface where a fleet manager asks "which vehicles should I look at first?" and gets a prioritized answer. Attempted 11 times during development — failed every time due to the 50KB Add-In size constraint. The Ace SDK alone would push past the limit. Would need a server-side architecture change to make this work.

**N8N workflow integration.** The audit already organizes findings by role — maintenance coordinator, IT, fleet manager. N8N could route those findings directly to the right person. URGENT mechanical flags go to the maintenance coordinator's inbox immediately. Device noise tickets go to IT. The fleet manager gets a weekly digest with cost trends and roadmap progress. Escalation triggers fire automatically — if idle hours haven't dropped 20% by Day 30, the fleet manager gets a nudge before it stalls. The audit data is already structured for this; it just needs a webhook endpoint and an N8N flow to distribute it.

**Suppress annual estimate under 60 days.** Show it only after 2+ quarterly runs, with a confidence range instead of a point estimate.


## Challenges Faced

**The 50KB wall.** MyGeotab Add-In configs have a ~50KB JSON limit. The final file is 49,393 bytes with 607 bytes of headroom. Every feature addition in the last 5 versions required removing something else. The `isN()` helper function saved ~500 bytes by deduplicating 5 copies of the accelerometer filter pattern. This constraint killed the Ace integration and forced aggressive code compression throughout.

**Device noise discovery.** Three vehicles (Demo-39, 40, 41) had massive exception counts. My tool classified them as mechanical failures needing a mechanic. Cross-validating against the official Geotab Fault Report revealed they were all accelerometer mounting issues — pure device noise. This failure became the tool's best feature: the 3-way classifier. A fourth vehicle (Demo-42) was only caught in v2.0 because the original scan only checked high-event vehicles, not all vehicles.

**"Config not valid" — 15 consecutive failures.** Between v1.3 and v1.5, the Gem kept generating code that broke the MyGeotab config parser. Invisible characters, malformed JSON, size overruns. Each attempt required pasting the entire working version back in and describing only the additions. The lesson: small, incremental changes only.

**Demo database limitations.** The simulator provides 23 days across 2 months. The tool requests 365 days and works with whatever comes back, but seasonal analysis requires data that doesn't exist yet. Projections are flagged accordingly.

**Idle rounding variance.** Geotab's server-side idle calculation doesn't match client-side parsing of trip-level `idlingDuration` strings. The ~4% variance (Demo-15: 7.9 vs 7.6 hrs) comes from fractional seconds accumulating across 254 trips. Not a bug, but a fleet manager comparing against MyGeotab's built-in report would notice the discrepancy.

---

## Install (2 minutes, zero infrastructure)

1. Log into MyGeotab → **Administration** → **System** → **System Settings** → **Add-Ins**
2. Toggle **Allow unverified Add-Ins** to Yes
3. Click **New Add-In** → **Configuration** tab
4. Paste the contents of `fleet-cost-optimizer-v2.0.json`
5. **Save** → hard refresh (Ctrl+Shift+R)
6. Find **"Fleet Cost Optimizer"** in the sidebar → click **Run Audit**

No server. No API keys. No build step. You don't need to upload the HTML separately — the entire UI (HTML, CSS, JavaScript) is embedded inside the JSON config under the `"files"` key. One file does everything.

## Technical Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      MyGeotab Platform                          │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              Fleet Cost Optimizer (Add-In)                │   │
│  │                                                          │   │
│  │   → JS API multicall (5 endpoints, 365-day window)       │   │
│  │   → Client-side analysis (no server, no external calls)  │   │
│  │   → 3-way fault classifier                               │   │
│  │   → 90-day roadmap generator                             │   │
│  │   → Print-ready HTML output                              │   │
│  │                                                          │   │
│  │   Data sources:                                          │   │
│  │   • api.call("Get", "Trip")                              │   │
│  │   • api.call("Get", "FaultData")                         │   │
│  │   • api.call("Get", "ExceptionEvent")                    │   │
│  │   • api.call("Get", "Device")                            │   │
│  │   • api.call("Get", "Diagnostic")                        │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                 │
│  Constraints:                                                   │
│  • Single file, <50KB JSON config                               │
│  • Runs in MyGeotab iframe sandbox                              │
│  • No external dependencies, no build step                      │
│  • Browser-only processing (no server-side)                     │
└─────────────────────────────────────────────────────────────────┘
```

## Cross-Validation Results

Compared tool output against official Geotab reports:

| Metric | Fleet Cost Optimizer | Official Geotab Report | Match |
|--------|---------------------|----------------------|-------|
| Top idle vehicles | Demo-31 (8.0h), Demo-15 (7.9h), Demo-32 (6.5h) | Same vehicles in top positions | ✅ |
| Fleet idle rate | 9% | 8.8% | ✅ Within rounding |
| Ghost vehicles | 13 below 40% utilization | 13 in lowest activity tier | ✅ |
| Demo-39 faults | Device noise — 111 accelerometer records | 111 accelerometer records in Fault Report | ✅ Exact |
| Demo-15 idle | 7.9 hrs | 7.6 hrs (Geotab server-side) | ⚠️ ~4% rounding across 254 trips |
| Demo-42 | Device noise — 30 accelerometer records | Same pattern as Demo-39/40/41 | ✅ Caught in v2.0 |

## Competition Fit

| Category | How Fleet Cost Optimizer Qualifies |
|----------|----------------------------------|
| Vibe Master ($10K) | Full pipeline: data → analysis → action plan → tracking → print |
| Green Award ($2.5K) | CO₂ calculations, Clean Fuel Regs, ISSB S2, provincial carbon pricing |
| Innovator ($5K) | 3-way fault classifier, device noise detection, prescriptive roadmap |
| Best Use of Google Tools ($2.5K) | Built primarily with Geotab Add-In Architect Gem (~50 prompts) |

## The Vibe Coding Stack

| Tool | Prompts | What For |
|---|---|---|
| Geotab Add-In Architect Gem (Google Gemini) | ~50 | Code generation, feature additions, bug fixes, full version upgrades |
| Claude (Anthropic) | ~20 | Strategy, data validation, device noise fix, sustainability, debugging |
| HeyGen | — | AI voiceover and video production for demo video |
| **Total** | **~70** | |

Full prompt history: [`PROMPTS.md`](PROMPTS.md) and [`Google_Gem_History.txt`](Google_Gem_History.txt)

## Files

| File | What It Is |
|---|---|
| `fleet-cost-optimizer-v2.0.json` | The Add-In config. Paste into MyGeotab. |
| `audit-v2.0.html` | Extracted HTML for reading the source code. |
| `fco-intro-v2.html` | Interactive 60-second intro presentation. <a href="https://sanjayaabeysinghe.github.io/fleet-cost-optimizer/fco-intro-v2.html" target="_blank">Live version →</a> |
| `README.md` | You're here. |
| `VIBE_CODING_JOURNEY.md` | Full build story — version by version, every bug, every fix. |
| `SUBMISSION_STORY.md` | Short-form submission description. |
| `VIDEO_SCRIPT.md` | 3-minute demo video script. |
| `PROMPTS.md` | All Claude prompts used during development. |
| `Google_Gem_History.txt` | Complete Gem conversation history. |
| `LICENSE` | MIT. |

## Demo Video

| Time | What's Shown |
|------|-------------|
| 0:00 | The problem: data without action |
| 0:25 | Run audit, $12,443 finding |
| 0:55 | Four finding cards |
| 1:25 | Roadmap: fault codes, named owners, escalation |
| 2:00 | Device noise detection + sustainability |
| 2:30 | How it was built (vibe coding) |

## License

MIT — see [LICENSE](LICENSE).

---

## Appendix A: Fleet Utilization Report Comparison

I ran the Fleet Utilization Report on the same demo fleet to see what it gives you. Five tabs, 50 vehicles, 397-day date range. The core output is total distance per vehicle, bucketed into distance bands — Demo-42 at 253 km on the low end, Demo-43 at 2,551 km at the top. The Vehicle Count tab shows the distribution: 10 vehicles in the 201-500 km range, 21 in 501-1000, 19 over 1000.

That's distance. That's all it has.

Demo-42 shows 253 km and you know it's low — but is that because it's been sitting in a lot for a month, or because it joined the fleet last Tuesday? The report doesn't track when each vehicle started. Demo-31 shows 2,169 km and looks like a top performer — except it also idled 8 hours, which you'd only find in a completely different report. The four vehicles with hundreds of accelerometer exceptions? Not here. The report has columns for 6 exception types but they all came back empty for this fleet.

The data exists inside MyGeotab — idle time, fault codes, exception events — it's just spread across separate reports. A fleet manager would need to open the Fault Report, the Exception Report, Trip History, run them each individually, then mentally piece together which vehicles actually need attention and who should handle it.

Fleet Cost Optimizer pulls Trips, FaultData, ExceptionEvents, Devices, and Diagnostics in one API call and does that cross-referencing for you. Utilization is calculated as a percentage of active days since each vehicle's first recorded trip — not just raw km. Faults get classified into mechanical vs. device noise so nobody sends a mechanic for a firmware problem. And every finding gets a dollar amount, an owner, and a deadline.

---

*Built with the Geotab Add-In Architect Gem (Google Gemini) and Claude (Anthropic), February 2026.*
