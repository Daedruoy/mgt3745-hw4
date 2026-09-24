# HW4 Additions for Your Context Files

You copied your HW3 context files over the template's. Good. The template's
versions carried a few HW4 skeletons; here they are, to paste into yours.

## ARCHITECTURE.md: paste above ADR-001

```markdown
## The Gate: HW4 rerun

Where should entries live now that they must survive a cleared cache?

| Criterion | Weight | Build (Worker + D1) | Buy (hosted BaaS) | Delegate (AI builder hosts it) |
|---|---|---|---|---|
| Cost to start | 2 | 4, real cost was time, not money; free tier, already done | 4, estimated, most BaaS have fast free-tier setup | 3, estimated, still requires prompting, review, and a second migration off what's already built |
| Cost to maintain | 3 | 3, free tier covers this project's scale; I now understand the code enough to fix it | 2, estimated, another vendor relationship and dashboard to track | 2, estimated, maintenance depends entirely on a tool I don't control and haven't tested |
| Time to working | 5 | 5, it's done and verified, working right now | 3, estimated, plausible but unverified | 2, estimated, would require abandoning the current working build and starting over on a new platform |
| Inspectability | 2 | 4, I read and debugged this Worker myself this week, real earned inspectability, not full mastery | 2, estimated, depends entirely on the vendor's abstraction | 1, honest low score, no experience reading what an AI-hosted builder actually generates or deploys |
| Switching cost | 2 | 2, *scored from Session B experience*: moving off localStorage took real hours, new commands, new debugging, a genuine cost | 2, estimated, likely comparable since another migration | 3, estimated, lower switching cost only because nothing has been built there yet, easier to walk away from a decision never made |
| Fit to spec | 5 | 5, the assignment requires this exact thing, a Worker + D1 built by hand this week, so fit is essentially guaranteed by requirement | 2, estimated, a hosted BaaS wasn't what the assignment specified, would likely require justifying a deviation | 2, estimated, the assignment explicitly frames this week's hand-build as separate from and prerequisite to any Delegate decision |
| **Weighted total** | | **76** | **45** | **39** |

## ADR-002: Entries move from localStorage to Cloudflare D1

**Status:** Accepted
**Supersedes:** ADR-001

### Context
Nu Mu chapter updates previously lived only in localStorage, tied to one browser on one device, and did not survive a cleared cache or follow a chair to a second device. That stopped being enough the moment more than one person needed to see the same updates: a chair posting from their phone and the secretary checking from a laptop need to see the same data, which localStorage cannot provide.

What crosses: every field a chair submits- chair name, chapter position, initiative, update title, and event or deadline date- leaves the browser and is stored in Cloudflare D1. To which vendor: Cloudflare, under their free-tier terms, in a region not chosen or controlled by me. Under what terms: no cost at this project’s scale, governed by Cloudflare’s standard terms of service. Who is accountable: I am. I created the database, hold the wrangler login token in this Codespace, and am responsible for what happens to chapter members’ submitted data on Cloudflare’s infrastructure.
### Decision
I will build the backend myself as a Cloudflare Worker backed by D1, rather than use a hosted backend-as-a-service or delegate hosting to an AI builder. This choice is scored in the Gate above: Build won with a real, tested implementation already working (76), well ahead of Buy (45) and Delegate (39), both of which were scored from estimation since I have not tried either this week.
### Alternatives considered
Buy: a service like Supabase or Firebase could plausibly host this data with less setup code. I did not try this, so its Cost to start and Time to working scores are estimates, not experience. It scored lower primarily on Fit to spec; this assignment specifically requires a Worker and D1 built by hand this week, so an off-the-shelf service would have required justifying a deviation from the assignment itself.

Delegate: a tool like bolt.new could generate and host a backend from a prompt. I generally prefer AI-assisted builds, as stated in HW3. Still, I have no experience with a Delegate-hosted backend, and the assignment requires building this week’s Worker by hand, so inspectability scored lowest here (1) for exactly that reason; I would have no way to verify what a Delegate-hosted backend was actually doing.
### Consequences
Something got harder: deleting an entry is no longer possible from the UI. My HW3 version had a working delete button backed by localStorage; this week’s Worker only implements GET and POST, since I chose not to build one under this week’s time constraint. Chapter updates are now effectively permanent once posted, a real loss of functionality compared to HW3 that a future ADR will address if the chapter actually needs to correct or remove mistaken entries.

Also harder: offline use is now impossible. HW3 worked with no network at all; this week’s version fails to load or save anything without a live connection to Cloudflare, which is a real tradeoff for a chapter tool brothers may want to check between classes with data or bad wifi.
### Revisit trigger
Revisit this decision if the chapter’s usage grows past what the free tier comfortably handles, if a real need for deleting or editing entries emerges, or if I gain enough experience with a Delegate-hosted option to score its Inspectability honestly rather than as an estimate.
```

Then change ADR-001's status line to `**Status:** Superseded by ADR-002` and
edit nothing else in it.

## FEATURES.md: add to the Verification table

| Statement | HW3 verdict | HW4 verdict | Reason |
|---|---|---|---|
| Survive cleared cache | CANNOT TEST YET | | now testable |
| Server unreachable | | | how would you simulate an outage? |
| Server returns 500 | | | |
| Server returns 400 | | | |
| Second client writes to the same table | | | DEFERRED if ADR-002 says so |

And one EARS unwanted-behavior statement for the validation rule you add to
`worker.js`: `IF ..., THEN THE SYSTEM SHALL reject it and say why.`

## STANDARDS.md: three new rules

- User values reach SQL through `bind()`, never string concatenation.
- No credential in the repository. Database ids are addresses and may appear in `wrangler.toml`.
- A failed request is shown to the user on the page and is never thrown in the console.

## CLAUDE.md: restate them for the agent

- Never build SQL by concatenating strings. Use `prepare(...).bind(...)`.
- Never write a credential, token, or key into any file in this repository.
- Never add a dependency without adding a row to TOOLS.md.
- Handle failed responses on the page. Never throw to the console.
