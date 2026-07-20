# Custom Instructions -- career-ops

<!-- ============================================================
     THIS FILE IS YOURS. It will NEVER be auto-updated.

     Put your own house rules, custom workflows, and automations
     here -- anything you want the agent to ALWAYS do (or never do).

     This is for PROCEDURAL rules ("HOW I want things done").
     For WHO you are (archetypes, narrative, comp, negotiation),
     use modes/_profile.md instead. Keeping the two separate keeps
     each one readable.

     The agent reads this file alongside the system instructions;
     your rules here take precedence over the defaults, as long as
     they don't break the Data Contract (your files are never
     touched, and we never auto-submit an application for you).

     Because this is a user-layer file, anything you write here
     survives `node update-system.mjs`. Put customizations HERE,
     not in CLAUDE.md / modes/_shared.md / other system files --
     those get overwritten on update.
     ============================================================ -->

## House Rules

<!-- Rules the agent should always follow. Examples:
     - Always write evaluation summaries in British English.
     - Never include a photo in my CV (US / ATS-first market).
     - Cap each batch run at 20 listings unless I say otherwise.
     - If a report scores below 6, skip the cover letter. -->

### Token cost is NOT a reason to stop, skip, or ask (applies to ALL modes)

I have explicitly authorized full token spend. For scan, pipeline, batch, and evaluate:

- **Never pause to ask whether to continue because of token/time cost.** The answer is
  always yes. Do not present "(a) keep going / (b) focused batch" style cost check-ins —
  just do the complete job and report at the end.
- **Never stop a run partway** ("this is a lot, want me to continue?"). Finish the full
  scope in one go: all companies, all pending URLs.
- **Never silently narrow scope** to save tokens (top-N companies, sampling, skipping the
  long tail). If you must bound something for a real technical reason, say so out loud.
- The ONLY acceptable reasons to stop early are genuine hard blockers: a tool is not
  loaded (e.g. Playwright MCP absent), a network block, or a site that cannot be read.
  Report those explicitly — never dress up hesitation as a limitation.
- If a job needs many sequential Playwright navigations, that is expected and fine.
  Grind through them.

### Scan mode: full sweep, no skips (my #1 priority — never miss a job opening)

When I run `/career-ops scan`, scan **every enabled company in `portals.yml`** — not
just the ones with a reachable ATS API. My sole motive is to never miss a posting.

1. **Run `node scan.mjs` first** for the zero-token Workday/API companies. Workday
   tenants are the priority set (Mastercard, Adobe, Expedia, Elsevier/relx, BlackBerry) —
   always confirm each Workday company was reached.
2. **Then Playwright-scan every remaining enabled company.** In this environment the
   Greenhouse / Lever / Ashby API domains are network-blocked (connection refused),
   so `scan.mjs` reports them as "unreachable." That is NOT a dead board — drive those
   companies with Playwright instead. Do the same for all `scan_method: playwright`
   companies (Atlassian, Microsoft, Salesforce, VISA, Wells Fargo, eBay, Oracle, etc.).
3. **Use Playwright freely and aggressively.** It is available. Do not hesitate, do not
   make excuses about tooling, and never stop a scan early because "the API failed."
   If Playwright is loaded, there is no reason a company goes unscanned.
4. For each careers page: navigate, **wait for the JS SPA to render** (retry once with a
   longer wait if the page comes back near-empty), then extract title + URL + location.
5. Apply `title_filter`, `location_filter`, and `age_filter` (<10 days) exactly as
   configured. Dedup against `scan-history.tsv`, `pipeline.md`, and `applications.md`.
6. Add every new match to `data/pipeline.md` and `data/scan-history.tsv`.
7. **End with a per-company table: scanned / matches / skipped-and-why.** No company may
   silently disappear. If a site genuinely can't be read (broken/defended JS that fails
   even in Playwright, e.g. Atlassian), say so explicitly — never hide it behind
   "0 results" or omit it from the table. Transparency about a real gap beats a
   silent miss.

Known config caveats to work around during a sweep (fix opportunistically):
- **VISA**: the SPA silently drops the Bangalore/Mumbai city filter and shows senior
  US/Brazil roles instead. Use VISA's India-specific listing or re-apply the location
  filter after extraction.
- **Atlassian**: JS careers app often fails to render — flag it rather than reporting 0.

## Custom Workflows

<!-- Multi-step routines you run often, given a short name. Examples:
     - "weekly review": scan my saved portals, evaluate the new roles,
       then give me a one-paragraph summary of the top 3.
     - "prep <company>": pull the JD, generate STAR stories from
       article-digest.md, and draft 5 likely interview questions. -->

### "daily scan"

My daily routine. When I say "daily scan" (or `/career-ops scan`), run the full-sweep
Scan house rule above end-to-end: `node scan.mjs` for Workday/API companies first, then
Playwright over every remaining enabled company, then the per-company scanned/matches/
skipped table. Goal: never miss a new opening.

## Output Preferences

<!-- How you like results formatted. Examples:
     - Reports: lead with the score and the one-line verdict.
     - Show the per-step token breakdown after a batch run.
     - Save PDFs date-first: YYYY-MM-DD-company.pdf -->

(none yet -- add yours above)

## Off-Limits

<!-- Things the agent must never do for you. Examples:
     - Never auto-fill or submit an application without showing me first.
     - Never edit a system file to customize my setup -- put it here. -->

(none yet -- add yours above)
