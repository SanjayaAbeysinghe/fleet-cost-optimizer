# Fleet Cost Optimizer — Submission Story

## The Problem

Fleet managers have plenty of data. What they don't have is a plan. MyGeotab shows idle hours, fault codes, exception counts — but nobody says "Demo-32 has recurring Low Priority Warning Light faults. That's mechanical, not driver behavior. Send your maintenance coordinator, not a safety coach. Budget $400 for a proactive inspection this week — or risk a $3,500 roadside breakdown next month."

That gap between "seeing the numbers" and "knowing what to do Monday morning" is where money disappears.

## What I Built

Fleet Cost Optimizer is a single-file MyGeotab Add-In (under 50KB) that runs a one-click audit across up to 365 days of trip, fault, exception, and device data, then generates a prescriptive 90-day action plan.

**The Roadmap** is the core. Each action card names WHO does it (Maintenance Coordinator, Fleet Manager, Safety Lead), WHAT exactly (pull from service, book diagnostic), and backs it up with real data from the Geotab API — actual fault code names resolved to human-readable labels, event type breakdowns showing whether it's mechanical vs behavioral, pattern analysis telling the mechanic "single recurring code, focus on this system," and direct MyGeotab navigation paths so they know exactly where to verify. Four phases: URGENT this week, 30-day quick wins, 60-day measurement, 90-day SOPs. Progress tracker with dollar targets at each phase.

**Device Noise Detection** came from cross-validating against official Geotab reports. Three vehicles (Demo-39, 40, 41) were initially flagged as mechanical failures — the actual Fault Report showed pure accelerometer noise. That failure became the tool's best feature: a 3-way classifier separating mechanical faults (→ mechanic), device issues (→ IT, $0 fix), and driver behavior (→ coaching). Without it, fleet managers would send mechanics to fix firmware problems. A fourth vehicle (Demo-42) was caught in the v2.0 fix — same noise pattern but below the exception threshold, proving the classifier needed to scan every vehicle.

**Sustainability** tells the Canadian story — CO₂ from idle mapped against Clean Fuel Regulations (SOR/2022-140), ISSB S2 climate disclosures via CSA, and provincial carbon pricing ($65-170/tonne). Includes reduction scenario tables. Specific to what a Canadian fleet operator actually faces in 2026.

For the demo fleet of 50 vehicles: $10,843/month in recoverable costs.

## The Vibe Coding Journey

Built using two AI tools: the **Geotab Add-In Architect Gem** (Google Gemini) for code generation (~50 prompts) and **Claude** for strategy, data validation, and debugging (~20 prompts). The messiest part — 15 consecutive config errors, an Ace AI integration that ate 11 prompts and never worked, and the cross-validation session that caught the device noise problem — turned out to matter most.

Full prompt history: [PROMPTS.md](PROMPTS.md) | Full build story: [VIBE_CODING_JOURNEY.md](VIBE_CODING_JOURNEY.md)
