# How I Built Fleet Cost Optimizer — The Full Vibe Coding Story

I spent about 20 hours over one weekend building this. Some of it went smoothly. A lot of it didn't. This is the honest version — every prompt, every bug, every "why is this blank again" moment.

Two tools did the heavy lifting: the **Geotab Add-In Architect Gem** (Google Gemini) generated the code, and **Claude** helped me think through strategy, validate data, and debug the stuff the Gem couldn't see.

---

## Where the Idea Came From

I've dealt with fleet operations enough to know the biggest gap: the data is there, but it's scattered across too many reports. MyGeotab has great dashboards — idle hours, exception counts, fault codes — but a fleet manager has to open 5-6 of them and cross-reference manually to get to "okay, Demo-32 has recurring faults, it's mechanical not behavioral, and here are the codes the mechanic needs." That cross-referencing takes time nobody has.

That's what I wanted to build. Not another dashboard. A tool that says: "Demo-31 is your worst idler at 8 hours. The peak is evening — 9 PM to midnight. Assign your ops coordinator to implement engine-off policy by Day 14. Measure weekly."

I bounced the concept off Claude first to see if it was differentiated enough for the competition:

```
I'm entering the Geotab Vibe Coding Competition. I want to build a fleet
cost optimization tool that doesn't just SHOW waste — it tells managers
exactly what to do about it, with names, deadlines, and dollar amounts.
Think prescriptive actions, not dashboards. Which prize categories does
this target and what gaps do I need to fill?
```

Claude confirmed the angle was strong — most competitors would build visualizations or chatbots, not prescriptive action plans. Flagged that I'd need sustainability metrics for the Green Award and cross-validation for credibility. Good. That shaped the roadmap.

I also pushed on the Canadian angle since Geotab is headquartered in Oakville:

```
you have to think through - does most of the companies own the vehicles
or if they renting if that's the case they would not have such issues —
think about use cases in Canada — remember this is Canadian company
```

This led me to NRCan's FleetSmart benchmarks, CAA breakdown cost data, and provincial carbon pricing — all of which ended up in the final tool.

---

## What The Final Product Does

One click → 30 seconds → a full report that a fleet manager can print and hand to their boss. Here's what each section gives them and why it's there.

**Executive Summary** opens with a fleet health score based on up to 365 days of data (23 days in the demo database). It tells you the headline number ("$12,443/month in recoverable costs") but also the context — which season you're in, what that means for idle patterns, and how your fleet's idle rate compares to NRCan's FleetSmart benchmark of 15%. A number without context is just a number. "Your fleet idles at 9%, which is within the NRCan target" — that's information a manager can act on.

**Four Finding Cards** break the money down into categories. Ghost Vehicles flags units used less than 40% of available days — not based on a fixed 30-day window, but calculated from each vehicle's actual first trip date, so new vehicles don't get falsely flagged. Idle Time Burn calculates fuel cost at $4.30/hour using real trip-level idling durations from the Geotab API. High-Risk Driving flags vehicles with exception event counts above 2× the fleet average, and it actually looks at what TYPE of events they are — engine fault exceptions vs harsh braking vs speeding — because the response is completely different for each. Breakdown Risk catches vehicles with 3+ recurring mechanical fault codes in the analysis window. Each card shows dollar amounts so the conversation isn't "we have a problem" — it's "this problem costs us $X."

**The Roadmap** is the part I spent the most time on and it's what makes this different from a dashboard. Look at the screenshot — Demo-32 has 7 events at 2.3× the fleet average. Instead of just "high risk," the card already has everything cross-referenced:

- **Routed to:** Maintenance Coordinator (mechanical finding, not behavioral)
- **Action:** Pull from service. Book diagnostic. Mechanical, NOT driver behavior — coaching will not fix engine faults.
- **Diagnostic Codes:** Low Priority Warning Light (×2) — pulled from actual Geotab FaultData records, resolved to human-readable names instead of internal IDs
- **Pattern analysis:** Single recurring code — points to one system rather than a scattershot of unrelated faults
- **Event breakdown:** Engine Fault Exception:2, Engine Light On:2, Speeding:2, Max Speed:1 — so you can see it's mostly mechanical, not driving behavior
- **MyGeotab link:** "MyGeotab → Faults → Demo - 32" — tells you exactly where to verify in the platform
- **Cost:** ~$400 proactive inspection
- **Savings:** Avoids $1,200-$3,500 roadside breakdown

That's one action card. The Roadmap has four phases — URGENT (this week) for safety and mechanical issues, 30-day for idle reduction and driver coaching, 60-day for measurement and fleet right-sizing decisions, and 90-day for SOPs and ongoing monitoring. Each phase has a dollar target showing how costs should decrease. There's a progress tracker table at the bottom and a printable checklist with every vehicle name.

The reason the fault codes matter: without them, a maintenance coordinator gets "Demo-32 has problems, go look." With them, they walk into the shop and say "Low Priority Warning Light, recurring — check the emissions system." That's the difference between a 2-hour diagnostic fishing expedition and a 30-minute targeted inspection.

**Device Noise Detection** is the feature that came from cross-validating against official Geotab reports. Three vehicles (Demo-39, 40, 41) had massive fault counts — my tool originally classified them as mechanical failures needing a mechanic. The official Fault Report showed they were all accelerometer mounting issues. Pure device noise. The tool now runs a 3-way classifier on every fault: MECHANICAL (engine/coolant/transmission → send to mechanic), DEVICE ISSUE (accelerometer/disabled → call IT, $0 fix), or BEHAVIORAL (speeding/harsh events → driver coaching). Without this, fleet managers would send $400 mechanics to fix $0 firmware problems. The v2.0 update caught a fourth vehicle (Demo-42) that had the same accelerometer noise but fell below the exception threshold — proving the classifier needed to scan all vehicles, not just high-event ones.

**Sustainability Card** converts idle hours to CO₂ tonnes and maps them against Canadian regulations — Clean Fuel Regulations (still in effect, SOR/2022-140), provincial carbon pricing ($65-170/tonne depending on province), ISSB S2 climate disclosures via CSA, and government contract bid requirements. All specific to what a Canadian fleet operator actually faces in 2026. Includes a reduction scenario table showing what a 30% and 65% idle reduction would save in fuel, CO₂, and dollars.

**Evidence Report** is the per-vehicle backup for everything above. Each flagged vehicle gets its own card with trip-level data, idle peak hours (detected in 3-hour bins — so you know if someone is idling at 2 AM for warm-up or at noon for lunch breaks), fault code history with dates, and exception event timelines. This is what the fleet manager opens when their boss asks "how do you know Demo-31 is a problem?"

The whole thing is print-ready. Hit Ctrl+P and every section formats cleanly with page breaks. No server, no API keys, no build step. Under 50KB, runs entirely in the MyGeotab iframe.

---

## The Build — Version by Version

### v1.0 — First Working Prototype (Gem)

Opened the **Geotab Add-In Architect Gem** and described what I wanted. My first prompt was fairly generic, based on the competition's suggested template. The Gem spit out working code, but it didn't handle the demo database well — durations came as strings like "7.13:15:10" that needed parsing, distances were often 0 in the simulator, and the ghost vehicle logic was way off because the database was only a few weeks old.

So I rewrote the prompt with everything I'd learned from testing:

```
Create a MyGeotab Add-In called "Fleet Waste Finder" that analyzes
fleet data and identifies hidden cost waste.

IMPORTANT TECHNICAL CONTEXT:
- This is a Geotab demo/simulator database with 50 GO9 devices
  named "Demo - 01" through "Demo - 50"
- Trip durations come as STRINGS like "01:00:00" or "7.13:15:10"
  (days.HH:MM:SS format) — you MUST parse these into hours/minutes
- Trip distance may be 0 in the simulator — so use TRIP COUNT per
  device as the utilization metric, not distance
- Exception events use rule IDs (not human-readable names) —
  fetch Rules first to get names
```

This version worked. Four finding categories (ghost vehicles, idle burn, high-risk driving, breakdown risk), Bootstrap layout, dollar amounts. Not pretty, but functional.

### v1.1 — Credibility Fix (Gem)

The numbers looked too confident for a tool running on limited data. Told the Gem:

```
The Fleet Waste Finder is working and showing data, but the numbers
need to be more accurate and defensible. Here are specific fixes.

FIX 1 — GHOST VEHICLES (currently overcounting):
The current code looks back 30 days, but my demo database is new —
some vehicles have only a few days of trip history. This makes almost
every vehicle look underutilized.

Change the logic to:
- For each device, find the EARLIEST trip start date in the data
- Calculate utilization as: active days / days since first trip
- Only flag if that percentage is below 40%
```

Added methodology notes, NRCan source citations, and "approximately" where estimates were used. Also added a debug data copy button — this turned out to be critical later.

### v1.2 — Ace AI Integration Attempt (Gem)

Tried to add Geotab's Ace AI for a "Get AI Analysis" button:

```
Enhance the Fleet Waste Finder Add-In to v1.2 by adding a Geotab
Ace AI section. Keep the same Add-In name and keep ALL existing
functionality — just add this new section at the bottom.
```

This kicked off a **brutal debugging cycle**. The Ace API uses an async three-step pattern (create-chat → send-prompt → poll get-message-group) that kept failing. I went back and forth with the Gem:

- "it says AI analysis is not available here is the debug data"
- "it still fails can you add a debug line for get ai analysis as well"
- Pasted raw API responses, traced through each step
- Eventually got it working on attempt #4 (v1.2.2)

Then the Fleet Optimization Summary broke. More debugging. The Ace integration was eating too much of the 50KB budget and causing instability, so I eventually deprioritized it in favor of making the core analysis bulletproof.

### v1.3 — The Config Error Nightmare (Gem)

This was the low point. I wanted to add drill-down detail to each finding card. The Gem generated the code, and then:

```
The configuration object is not valid. Edit your configuration
and try again.
```

I got this error **fifteen times in a row**. Different approaches, different fixes, same result. The Gem kept producing JSON that MyGeotab rejected — sometimes a stray quote, sometimes the file was too large, sometimes the HTML had unescaped characters.

After prompt #33 I finally said:

```
okay forget it - give me the last working version
```

Reverted to v1.2, then took a completely different approach — instead of click-to-expand drill-downs, I added a static Evidence Report section that renders automatically:

```
Upgrade the Fleet Waste Finder Add-In to v1.3. Keep ALL existing
functionality exactly as is.

DO NOT add any click handlers, event listeners, or expandable panels.
Instead, add a new STATIC section below the four finding cards called
"Evidence Report." This section renders automatically when the audit
runs — no user interaction needed.
```

**Lesson learned the hard way:** in the MyGeotab iframe, less interactivity = fewer things to break.

### v1.4 — Analysis Layer (Gem)

Added fleet-wide analysis on top of the per-vehicle data. Idle peak pattern detection (3-hour bins to find when vehicles idle most), exception trend analysis, fault code categorization. The Gem handled this well when I gave it specific instructions about what to calculate rather than vague "make it better" prompts.

### v1.5 — Cost Per KM (Gem + Regression Fix)

Added a cost-per-kilometer metric and "Taxi Test" (are we paying more per km than a taxi?). But this upgrade broke the tier calculation — every vehicle showed as "Heavy" because the code was using fleet total distance for every vehicle instead of individual distances.

```
CRITICAL: The v1.5 upgrade broke several things that were working
in v1.4. Fix ALL of these. Update version to "1.5.1".

=== BUG 1: FLEET COST EFFICIENCY SHOWS ALL 50 AS "HEAVY" ===
The problem is in the tier calculation. It should use EACH vehicle's
individual totalDistKm, not the fleet sum.
```

**Lesson:** When the Gem produces a full version upgrade, always diff against the previous working version. It loves to "optimize" things that were fine.

### v1.6 — Executive Summary + Action Plan (Gem)

This was the big feature prompt. Added an Executive Summary above the cards and a 90-Day Action Plan between findings and evidence:

```
Upgrade Fleet Waste Finder to v1.6. Keep ALL existing functionality.
This adds an Executive Summary at the top and a phased Action Plan.

1. Add Executive Summary section ABOVE the four finding cards
2. Add 90-Day Action Plan section between cards and Evidence Report
3. Fix Fleet Cost Efficiency tier calculation
4. Fix "undefined" in Breakdown Risk failure mode column
```

The Gem built it, but — predictably — the Evidence Report detail sections disappeared in the process. Every upgrade seemed to lose something from the previous version.

```
CRITICAL FIX: The v1.6 upgrade lost most of the Evidence Report
detail that was working in v1.5.1. The Executive Summary and Action
Plan are great — keep them. But the detailed evidence sections are gone.

=== BUG 1: "CAD CAD" DUPLICATED EVERYWHERE ===
The text shows "$2,403 CAD CAD". The formatCAD function already
includes "CAD" but the template strings add it again.
```

### v1.7 — The Action Plan Rewrite (Gem)

The original action plan was too vague. A fleet manager needs specifics:

```
The current Action Plan is too vague. A fleet manager needs to know
exactly WHAT to do, WHO does it, WHEN it's due, HOW to measure
success, and WHAT to do if it doesn't work. Replace the current
Action Plan with a comprehensive, manager-ready version.
```

This produced the 30/60/90-day roadmap with named owners, deadlines, escalation paths, and measurable KPIs that became the tool's strongest feature.

### v1.7.1 — Safe Additions (Gem)

By now I'd learned: small, surgical changes only.

```
Small, SAFE additions to Fleet Waste Finder. Update version to "1.7.1".
Keep ALL existing functionality. DO NOT restructure or rewrite any
existing functions. Only ADD new content in specific places.

=== CHANGE 1: ADD INDUSTRY BENCHMARKS TO EXECUTIVE SUMMARY ===
After the Key Findings paragraph, add NRCan FleetSmart 15% idle
threshold as benchmark comparison.
```

Also asked the Gem about my competition chances:

```
after seeing my product what you think my chances for this competition?
is there any other areas I should consider improving?
```

The Gem suggested sustainability metrics, monthly trends, and a professional rebrand. Good advice — all of it made it into the final version.

---

## The Claude Sessions — Validation & Bug Fixes

### Data Cross-Validation (The Breakthrough)

This is where the project went from "good" to "genuinely useful." I downloaded three official Geotab reports and asked Claude to compare them against my tool's output:

```
I'm not sure this data is correct, I ran the fleet utilization report
(has multiple tabs) review in detail please
```

**Results:**
- ✅ Idle top 10: exact match — same vehicles, same order
- ✅ Idle rate: 9% vs Geotab's 8.8%
- ✅ Ghost vehicles: 9/11 overlap with lowest-utilization in official report
- ❌ **Demo-39: my tool said "mechanical, needs mechanic"** — but Geotab's report showed 111 accelerometer noise records, not engine faults
- ❌ **Demo-40 and Demo-41: same problem**

These three vehicles had Geotab GO device mounting issues producing accelerometer noise. The noise triggered "Engine Fault Exception" rules, which my tool (and every other tool) interpreted as engine failures. They're not. They're a $0 firmware fix, not a $400 mechanic visit.

Without this cross-validation step, the tool would have confidently sent fleet managers on a wild goose chase. That failure became the tool's best feature.

### Device Noise Detection (Claude)

Built a 3-way fault classifier:
- **MECHANICAL** → real engine faults → send to mechanic
- **DEVICE ISSUE** → accelerometer/telematics noise → send to IT
- **BEHAVIORAL** → speeding, harsh braking → driver coaching

### The Silent Crash (Claude)

After deploying the device noise fix:

```
its stuck in this screen
Fleet Cost Optimizer v2.0
Processing 5958 trips...
```

No error. No console output. Nothing. In the MyGeotab iframe, `alert()` is blocked and you can't easily open DevTools. After adding a DOM-based error handler, found two bugs:
1. A variable (`dc`) used in one function but defined in another — classic scoping error
2. Some Geotab FaultData records have `diagnostic.id` as null — calling `.toLowerCase()` on null threw a TypeError that the iframe silently swallowed

One null-check at data ingestion fixed 7 downstream accesses.

### Professional Rebrand (Claude)

```
Also we need to think about the names - it should not call waste
- we need to be professional
```

"Fleet Waste Finder" → "Fleet Cost Optimizer." "Total Waste" → "Total Optimization Opportunity." Same data, different reception.

### Sustainability Card (Claude)

```
Can you add the CO2 tracking but you need to tell the Canadian story
for it.. I need to show deep domain knowledge
```

Added: CO₂ tonnes from idle, Clean Fuel Regulations, ISSB S2 via CSA, provincial carbon pricing ($65-170/tonne), tree equivalents. All Canadian-specific.

### Readability & Print Design (Claude)

```
very good readability is an issue - review that and then if every card
can be printed that would be amazing.. think this is a journey story telling
```

Darkened labels, increased fonts, added print CSS, section intro narratives, and a print button.

### Back to the Gem — Final Polish (Gem)

After Claude built v1.9, I brought the code back to the Gem to get it packaged as a proper Add-In JSON. The Gem made one last fix (v1.9.1) — adding actual fault code names to the action plan cards so the maintenance coordinator sees "Engine Coolant Temperature — High Voltage" instead of a Geotab internal ID. Full circle.

### The Truncation Fix (Claude)

```
I can only copy the data up to here
><td>'+dg+'</td><td>'+md+'</td></tr>'});\nh+='</tbody></table><
```

MyGeotab silently truncates the config field. The `</script></body></html>` was getting cut off — the entire tool broke with zero error message. Compressed the evidence report function from 6,803 → 4,252 characters. Final JSON: 47,353 bytes with 2,647 bytes of safety margin.

### v2.0 — Data Alignment Fixes (Claude)

Two bugs found during final cross-validation:

1. **Demo-42 missing from device noise.** It had 30 accelerometer fault records (same diagnostic as Demo-39/40/41) but wasn't in the device noise section. Root cause: device noise detection only ran on vehicles in the `sf` array (>2× exception average). Demo-42 had high faults but didn't hit the exception threshold. Fix: added a second scan across ALL vehicles — if every fault record is accelerometer/disabled/telematics, classify as device noise regardless of exception count.

2. **Demo-39 showing 112 instead of 111.** The noise count was including `disabled` and `telematics` diagnostic IDs on top of `accelerometer`. Geotab's Fault Report only counts accelerometer. Fix: tightened the display count to accelerometer-only.

Note on Demo-15 idle (7.9 hrs vs Geotab's 7.6): this is rounding accumulation across 254 trip records. Each trip's `idlingDuration` gets parsed from a string like "0.00:04:12" — the fractional seconds compound. Not a bug, just a known ~4% variance from Geotab's server-side calculation. Documented, not fixed.

---

## The Bug Hall of Fame

| # | What Happened | How I Found It | How I Fixed It |
|---|---|---|---|
| 1 | No data displayed | Ran audit, blank cards | Extended API date range |
| 2 | Ghost vehicles overcounting | Every vehicle flagged | Changed to relative utilization (active days / days since first trip) |
| 3 | Config not valid (×15) | Pasted JSON, MyGeotab rejected it | Reverted, rebuilt incrementally, avoided interactive elements |
| 4 | Ace AI "not available" | Button click, empty response | Debugged 3-step async API, fixed parameter passing |
| 5 | "CAD CAD" everywhere | Visual inspection | formatCAD() already added "CAD", removed duplicate |
| 6 | All vehicles show "Heavy" | Tier calculation wrong | Was using fleet total instead of per-vehicle distance |
| 7 | Evidence report vanished | v1.6 upgrade lost sections | Explicit "CRITICAL FIX" prompt to restore |
| 8 | `dc is not defined` | Tool froze at "Processing..." | Variable scoping — passed as function parameter |
| 9 | Null diagnostic.id crash | Tool froze silently | Null-check at FaultData ingestion |
| 10 | Demo-39/40/41 misclassified | Cross-validated against Geotab reports | Built 3-way device noise classifier |
| 11 | JSON truncation | Tool broken after paste, no error | Compressed evidence report, 2.6KB margin |
| 12 | Demo-42 missing from device noise | Final cross-validation — 30 accelerometer faults but not flagged | Added second scan across ALL vehicles, not just sf array |
| 13 | Demo-39 noise count inflated (112→111) | Compared display to Geotab Fault Report | Tightened count to accelerometer-only diagnostics |

---

## Known Limitations & Improvement Plan

### 1. "Mechanical, NOT driver behavior" is too absolute

Demo-32's card says "Mechanical, NOT driver behavior" — but the event breakdown right below it shows Speeding:2 and Max Speed:1. Three of seven events ARE driver behavior. The classifier picks the dominant event type and stamps the whole card, which works when it's 15 engine faults and 1 speeding event (like Demo-39), but breaks down when the split is closer to 50/50.

**Improvement:** Replace the binary label with a split classification. "4 of 7 events are mechanical (Engine Fault Exception, Engine Light On). 3 of 7 are behavioral (Speeding, Max Speed). Primary action: mechanic. Secondary action: driver coaching."

### 2. Urgency level doesn't match fault severity

Demo-32 has "Low Priority Warning Light × 2" — the word "Low Priority" is literally in the diagnostic code name. But the URGENT banner says "Pull from service. Book diagnostic." and prices it at "$400 proactive vs $1,200-$3,500 breakdown." That's an appropriate response for a high-priority fault or a check-engine light, not necessarily for two low-priority warnings over 23 days.

**Improvement:** Parse the fault code severity from the diagnostic name. "Low Priority" → schedule inspection within 30 days. "Malfunction Indicator Lamp" or "Engine Coolant Temperature" → pull from service now. The $400 vs $3,500 framing should only appear for high-severity faults. Low-priority faults should say something like "Monitor — schedule next routine service" with a lower cost estimate.

### 3. Device issues and mechanical issues share the same URGENT banner

Demo-39's accelerometer mounting issue sits next to Demo-32's engine faults under the same red "This Week" header. A device mounting issue is a $0 IT ticket, not an emergency. Mixing them dilutes the real urgency of mechanical flags and overstates the urgency of device problems.

**Improvement:** Split the URGENT section into two subsections: "⚠️ Mechanical — Pull from Service" (red) and "📱 Device Issues — IT Ticket" (purple). Device issues could even move to the 30-day section since they're not safety-critical. The mechanic doesn't need to see device noise on the same page as actual engine faults.

### 4. Cost estimates are hardcoded, not data-driven

Every mechanical vehicle gets "$400 inspection avoids $1,200-$3,500 breakdown" regardless of fault type or severity. Every ghost vehicle gets "$800/mo" regardless of vehicle class. Industry averages (CAA breakdown data, typical lease costs), but a fleet manager with actual lease numbers would immediately notice if their vehicles cost $600 or $1,200/mo.

**Improvement:** Allow user-configurable cost inputs — actual lease rate, hourly shop rate, fuel price. Three fields and every dollar amount in the report becomes real.

### 5. 23-day sample extrapolated to annual

The executive summary projects $149,315/year from a 23-day sample. The tool flags this ("23d sample — run quarterly for higher confidence") but the annual number is still prominent. Seasonal patterns — winter idle is higher than summer, fleet utilization varies by quarter — mean the projection could be off by 20-30%.

**Improvement:** Suppress the annual estimate when the sample is under 60 days. Show it only after 2+ quarterly runs, with a confidence range instead of a point estimate. "Based on 23 days: $12,443/mo. Annual estimate requires 90+ days of data."

### 6. Demo-15 idle rounding (7.9 vs 7.6 hrs)

The tool calculates idle from trip-level `idlingDuration` strings parsed client-side. Geotab's server-side calculation produces 7.6 hours for Demo-15; the tool shows 7.9. The ~4% variance comes from fractional-second rounding accumulated across 254 trips. Not wrong per se, but if a fleet manager compares the tool's number against MyGeotab's built-in idle report, they'll see a discrepancy.

**Improvement:** Round trip-level idle to the nearest minute before summing (matching Geotab's approach), or display the variance note on vehicles where the tool's count exceeds the API's aggregate by >3%.

### 7. No automated distribution

The audit already organizes findings by role — maintenance coordinator, IT, fleet manager. But someone still has to run it, read it, and forward the relevant parts to the right people. That's a manual bottleneck.

**Improvement:** N8N workflow integration. After an audit runs, a webhook pushes the structured results into N8N, which routes findings to the right person automatically. URGENT mechanical flags go straight to the maintenance coordinator's inbox. Device noise tickets go to IT. The fleet manager gets a weekly digest with cost trends and roadmap progress. If idle hours haven't dropped 20% by Day 30, an escalation notification fires before anyone has to remember to check. The data is already structured by role — it just needs a delivery layer.

---

If I had another sprint, items 1-3 first — they affect how someone reads the action plan. Item 7 (N8N) would be next because it closes the loop between "audit generated" and "right person sees it." Items 4-6 matter more at scale.

---

## What I'd Do Differently

**Start with official Geotab reports on Day 1.** The device noise discovery was the project's biggest breakthrough and it happened in Session 9. Should've been Session 2.

**Test in the iframe after every change.** The "config not valid" nightmare (15 consecutive failures) happened because I was making big changes without testing incrementally. Small, SAFE additions only — I learned this by v1.7.1 but should've known it from v1.3.

**Don't let the Gem rewrite everything.** Every major version upgrade lost something from the previous version. The pattern that worked: paste the last working code into the prompt, then describe only the additions.

---

## Prompt Counts

| Tool | Prompts | What For |
|---|---|---|
| Geotab Add-In Architect Gem | ~50 | Code generation, feature additions, bug fixes, full version upgrades |
| Claude | ~20 | Strategy, data validation, device noise fix, rebrand, sustainability, readability, truncation fix |
| HeyGen | — | AI voiceover and video production for demo video |
| **Total** | **~70** | |

---

*Built with the Geotab Add-In Architect Gem (Google Gemini) and Claude (Anthropic), February 2026.*
