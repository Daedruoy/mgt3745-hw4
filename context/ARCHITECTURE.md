# Architecture

Status: ACTIVE in Module 4.

## Gate

Name hard constraints and three concrete options. Weights and scores use 1–5; a score of 5 always means most favorable. Define 1/3/5 anchors. Multiply weights by scores and sum. Record estimates and run one sensitivity check.

**Feature:** F-01/F-02, a chair submits a time-sensitive update through a form; the system logs it with a timestamp and displays it in a current-items view, split by submission recency.

**Hard constraints:** zero budget, roughly one week, and limited ability to read code well enough to verify something I didn't build myself.

**Three options:** Hand-built, Existing-service, AI-assisted build.

**Scoring anchors (1–5, 5 always most favorable):** 1 = clearly fails this criterion for my situation, 3 = adequate with some tradeoff, 5 = clearly the best fit for my situation on this criterion.

| Criterion | Weight | Hand-built option | Existing-service option | AI-assisted build |
|---|---:|---:|---:|---:|
| Cost to start | 3 | 3 (9) | 4 (12) | 4 (12) |
| Cost to maintain | 5 | 3 (15) | 2 (10) | 3 (15) |
| Time to working | 4 | 3 (12) | 4 (16) | 5 (20) |
| Inspectability | 4 | 5 (20) | 2 (8) | 2 (8) |
| Switching cost | 2 | 3 (6) | 2 (4) | 3 (6) |
| Fit to spec | 5 | 3 (15) | 2 (10) | 4 (20) |
| **Total** | | **77** | **60** | **81** |


**Sensitivity check:** if I raise Fit to spec's weight from 5 to 4, the totals become Hand-built 74, Existing-service 58, AI-assisted build 77. AI-assisted still wins, and the order of all three options doesn't change. The decision is not sensitive to that one weight moving by a point.

**Result:** AI-assisted wins, narrowly over Hand-built. Existing-service is clearly out, mostly because no generic template is likely to match the specific service that I require to help my chapter run smoother.

## ADR-002: Entries move from localStorage to Cloudflare D1

**Status:** Accepted
**Supersedes:** ADR-001

### Context
Nu Mu chapter updates previously lived only in localStorage, tied to one browser on one device, and did not survive a cleared cache or follow a chair to a second device. That stopped being enough the moment more than one person needed to see the same updates: a chair posting from their phone and the secretary checking from a laptop need to see the same data, which localStorage cannot provide.

What crosses: every field a chair submits- chair name, chapter position, initiative, update title, and event or deadline date- leaves the browser and is stored in Cloudflare D1. To which vendor: Cloudflare, under their free-tier terms, in a region not chosen or controlled by me. Under what terms: no cost at this project’s scale, governed by Cloudflare’s standard terms of service. Who is accountable: I am. I created the database, hold the wrangler login token in this Codespace, and am responsible for what happens to chapter members’ submitted data on Cloudflare’s infrastructure.


### Decision
I will build the backend myself as a Cloudflare Worker backed by D1, rather than use a hosted backend-as-a-service or delegate hosting to an AI builder. This choice is scored in the Gate above: Build won with a real, tested implementation already working (76), well ahead of Buy (45) and Delegate (39), both of which were scored from estimation since I didn't try either this week.

### Alternatives considered
Buy (hosted backend-as-a-service): a service like Supabase or Firebase could plausibly host this data with less setup code. I did not try this, so its Cost to start and Time to working scores are estimates, not experience. It scored lower primarily on Fit to spec; this assignment specifically requires a Worker and D1 built by hand this week, so an off-the-shelf service would have required justifying a deviation from the assignment itself.

Delegate (AI builder hosts it): a tool like bolt.new could generate and host a backend from a prompt. I generally prefer AI-assisted builds, as stated in HW3. Still, I have no experience with a Delegate-hosted backend, and the assignment requires building this week’s Worker by hand, so inspectability scored lowest here (1) for exactly that reason; I would have no way to verify what a Delegate-hosted backend was actually doing.

### Consequences
This makes trust and debugging easier: I read, wrote, and fixed real errors in this Worker myself this week, like the login timeout, the D1 binding check, and the validation logic, so I understand what’s actually running.

Something got harder: deleting an entry is no longer possible from the UI. My HW3 version had a working delete button backed by localStorage; this week’s Worker only implements GET and POST, since I chose not to build one under this week’s time constraint. Chapter updates are now effectively permanent once posted, a real loss of functionality compared to HW3 that a future ADR will address if the chapter actually needs to correct or remove mistaken entries.

Also harder: offline use is now impossible. HW3 worked with no network at all; this week’s version fails to load or save anything without a live connection to Cloudflare, which is a real tradeoff for a chapter tool brothers may want to check between classes with data or bad wifi.

### Revisit trigger
Revisit this decision if the chapter’s usage grows past what the free tier comfortably handles, if a real need for deleting or editing entries emerges, or if I gain enough experience with a Delegate-hosted option to score its Inspectability honestly rather than as an estimate.

## ADR-001

Title and date: ADR-001, 2026-09-17: Build the F-01/F-02 submission-and-log feature assisted by AI rather than buy an existing service or manually build out every feature.
Status: Superseded by ADR-002
Door / concrete acquisition and execution choice: AI-assisted, both this week's prototype and the eventual Notion dashboard, built and maintained with AI directly rather than an off-the-shelf template or an entirely hand-built model that I won't be able to fully flesh out in a reasonable time.
Context: Zero budget, a one-week window, and an acceptance of submission-based splitting, acknowledgment tracking without disclosing individual reasons, that generic templates aren't built for. I'm not yet strong at writing code, which weakens my ability to flesh out my model entirely by hand. Brothers have also reported that added burden from new tools is a real cost, not a hypothetical one, so long-term maintainability matters as much as getting something working this week.
Decision: I will build the F-01/F-02 feature assisted with AI rather than buy an existing service or fully manual, because time to working and Fit to spec outweigh the Inspectability of a Hand-built model.
Consequences and revisit trigger: This makes the output time of this tool much faster and will allow me to update it consistantly. My only trigger would be the handoff of this tool as the next president would require a large onboarding to be able to update the tool.


Keep superseded ADRs. The pedagogical browser build can coexist with a different architecture recommendation; explain the distinction.