# Architecture

Status: ACTIVE in Module 3.

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


## ADR-001

Title and date: ADR-001, 2026-09-17: Build the F-01/F-02 submission-and-log feature assisted by AI rather than buy an existing service or manually build out every feature.
Status: Superseded by ADR-002
Door / concrete acquisition and execution choice: AI-assisted, both this week's prototype and the eventual Notion dashboard, built and maintained with AI directly rather than an off-the-shelf template or an entirely hand-built model that I won't be able to fully flesh out in a reasonable time.
Context: Zero budget, a one-week window, and an acceptance of submission-based splitting, acknowledgment tracking without disclosing individual reasons, that generic templates aren't built for. I'm not yet strong at writing code, which weakens my ability to flesh out my model entirely by hand. Brothers have also reported that added burden from new tools is a real cost, not a hypothetical one, so long-term maintainability matters as much as getting something working this week.
Decision:  I will build the F-01/F-02 feature assisted with AI rather than buy an existing service or fully manual, because time to working and Fit to spec outweigh the Inspectability of a Hand-built model.
Consequences and revisit trigger: This makes the output time of this tool much faster and will allow me to update it consistantly. My only trigger would be the handoff of this tool as the next president would require a large onboarding to be able to update the tool.

Keep superseded ADRs. The pedagogical browser build can coexist with a different architecture recommendation; explain the distinction.