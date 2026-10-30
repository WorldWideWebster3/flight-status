# Flight Status

A prompt that turns one open requisition into two HTML pages: a full brief for the recruiting team and a one-page summary you can send to a hiring manager or leadership. It answers three questions: where are we, are we on schedule, and what is holding us up?

It is designed to work with any ATS, recruiting CRM or tracker (Greenhouse, Ashby, Gem, Lever, Greenlight, SCALIS, or a spreadsheet), any calendar, any email, and any meeting platform (Zoom, Teams, Meet). The one hard requirement is that your export gives candidates per req and, for timing metrics, the date each candidate entered each stage. See "Limits to know about".

## What you get
- **Two files per request.** The **full brief** is for the recruiting team. The **summary** is a one-page, roughly one-minute read for hiring managers and executives: Time to Fill, four metric tiles, the runway, candidates (name, source, stage, days, scorecard status), what is holding the search up, and a few next steps for the hiring team. The summary is always share-safe, is built from the same numbers as the full brief, and contains only the fields it displays. Say "full only" or "summary only" for one file, or "exec only" to leave out the next-steps block.
- A headline, the five metrics (Time to Fill as a big numeral, four more as columns), a runway of your stages with pass-through %, time between stages, a timeline, candidate rows, observations (what is moving and what is stuck), search insights, options to consider, and a footer that shows where your sources disagree.
- Metrics: Time to Fill, Submit → Interview Time, Interview-to-Offer Ratio, Screen → Submit Rate, and Time in Stage. Each tile names its source, and a "slowest stage" is only named when it has at least 2 completed transitions.
- Statuses: On Time, At Risk, Delayed, No Data. They are assigned by numbers only, never by judgment.
- Missing data shows as "No Data". Nothing is estimated.

## How to use it
1. Paste `flight-status-prompt-universal.md` into your LLM once. It will reply "Flight Status is loaded" and wait.
2. When you want a brief, send "Run Flight Status for [req]" along with the variables at the top of the prompt: system of record (your ATS or tracker), job level, calendar, email, meeting platform, how your interview invites are named, who organizes interviews, your stage map, readers, company, and share method.
3. Connect your tools if your LLM supports it, or attach exports and paste in calendar entries, scorecard emails and transcripts.
4. The LLM restates its plan, tells you what it can and can't reach, and then builds the two HTML files.

## Connecting your ATS, CRM or tracker
Flight Status does not plug into your ATS by itself. It reads whatever your AI tool can reach, or whatever you give it. There are four ways:

1. **Connector.** If your AI tool has a connector for your ATS (availability differs by tool, so check yours), name it in the "System of record" line of your message, for example "use the Ashby connector." It must be able to return the candidates for that one job along with their stage history.
2. **Export file.** Export a report for the one job as CSV or JSON and attach it. This is the most common path and works in any AI tool.
3. **Paste.** Paste the rows into the chat. Fine for a handful of candidates.
4. **Screenshot.** If you can only see the data on screen, attach a screenshot of your pipeline or report. The AI first transcribes it into a table and shows you that table at the top of its reply. **Read that table against your screen before trusting the brief**, because a misread digit changes a number. Anything it can't read is treated as No Data, and the footer marks every screenshot-derived value as lower confidence. Make sure the screenshot shows the stage-entry dates, and crop out anything you don't want read (salary columns, for example). Works for a small pipeline, not for dozens of candidates.

**What the data needs**

| Data | Why it matters | If it's missing |
|---|---|---|
| The candidates for this one req (name, source) | The runway, source mix and candidate rows | Required |
| Each candidate's current stage | Where everyone is now | Required |
| The date each candidate entered each stage | Submit → Interview Time, Time in Stage, time between stages | Those show No Data; nothing is guessed |
| Offer accepted date | Time to Fill once an offer is accepted | Time to Fill counts days open so far |
| Rejected, withdrew or declined, with a reason | The one-line "exited" summary | Left blank |
| Your closed reqs (at least 3) | Historical averages for the two rate metrics | Rate metrics show the raw value and No Data |

Many ATS exports show only the current stage. Look for a pipeline-history, activity-log or "application history" report. That is the one to export.

**Matching your funnel stages**
The prompt measures seven standard stages: Recruiter Screen, HM Review, HM Interview, Interview Round 2, Final Interview, Offer Accepted, Hired. Your ATS will have its own names, and maybe a different number of them. Tell the prompt how yours map, in the "Stage map" line of your message. Example with made-up stage names:

```
Stage map: Applied -> (not counted); Phone Screen -> Recruiter Screen; Hiring Manager Review -> HM Review;
Manager Interview -> HM Interview; Panel -> Interview Round 2; Executive Interview -> Final Interview;
Offer Accepted -> Offer Accepted; Started -> Hired
```

- The page shows **your** stage names. The footer shows the mapping, so readers can see how your funnel was read.
- It shows you the mapping before it builds. **Check it.** If a stage has no clear match, it asks instead of guessing.
- **Several of your stages count as one standard stage?** (For example two interview stages that both count as Interview Round 2.) A candidate counts as having reached it when they reached any of them, and its date is the earliest.
- **A standard stage your process doesn't have?** Its runway tile says "Not in your process", it is skipped in pass-through, and any metric that needs it shows No Data. The footer names it.
- **A stage that fits none of the seven?** It asks which one it counts toward.
- **Candidates who skip stages, or a stage name that appears twice?** It doesn't back-fill dates. It shows "skips" instead of a pass-through percentage, or asks you how to count the repeat.
- Three metrics depend on specific stages: Submit → Interview Time needs HM Review and HM Interview; Interview-to-Offer needs HM Interview and Offer Accepted; Screen to Submit needs Recruiter Screen and HM Review. If your funnel has no equivalent for one of them, that metric shows No Data.

**Systems tried so far**

| System | Where to find the export | Stage-entry dates in it? | Tested |
|---|---|---|---|
| Greenlight (JSON) | Dashboard export | Yes | Yes |
| Others | Not documented yet | | Not yet tested |

If you get it working with Greenhouse, Ashby, Lever, Gem or anything else, please add a row (where you found the export, whether it had stage-entry dates, anything that tripped you up).

## Turn it into a skill (optional)
Pasting the prompt works everywhere and needs no setup. If your AI tool supports skills (saved, reusable instructions), a skill saves you from pasting each time. Ask your AI to turn `flight-status-prompt-universal.md` into a skill. If you do it by hand, it is a folder containing one `SKILL.md` file: a short header with a `name` and a `description` that says when to use it, followed by the prompt text. Check your tool's help for where to upload a skill. Keep one rule from the prompt: it should only run when you explicitly ask.

## What stays out
Compensation (candidate pay and market pay ranges) is left out of the file entirely, not just hidden, so it can't be recovered if the file is forwarded.

By default the brief is for the hiring team. It includes notice periods, competing processes and candidate-level risks, and the footer says so and lists what was left out.

**Sharing wider:** the summary is already share-safe. To make the full brief safe too, add **share-safe** to your run message; that also drops those three items (process-level risks like scheduling gaps stay). Add **anonymize candidates** to replace names with Candidate A, B, C in both files.

## Sharing the file
The brief is a single `.html` file. Chat tools such as Slack show it as code, not as a page, so recipients should **download it and open it in a browser** (double-click the file). If you want people to see it without downloading, send a PDF or a screenshot instead (in your browser: Print, then Save as PDF). Only share real briefs through channels your company approves, because they contain candidate names.

**Sharing from a company drive (OneDrive, SharePoint, Google Drive)**
Storing the file in your company drive and sharing a link is a good habit: the recipient can see it came from you, and the file stays out of email attachments. A few things to check:
- **Set the link to specific people or your organization.** Avoid "anyone with the link" for the full brief, which contains candidate names, notice periods and competing processes. If the tool offers it, turn off forwarding or downloading by others.
- **The page has no password.** Anyone who can open the link can copy the file. The link setting is the only access control.
- **Share the summary widely and the full brief narrowly.** The summary is built to be share-safe. The full brief is for the hiring team.
- **Test it once.** Some drive settings show an HTML file as a download or as code instead of a page. Ask a coworker without your access to open the link. If it doesn't render, share a PDF instead.

To share the prompt and the sample with others, a GitHub repo works well. GitHub shows `.html` files as code too, so turn on GitHub Pages for the repo (or send people the file) to let them see the rendered sample. The sample uses fictional data. Never commit a real brief to a public repo.

## What it reads, and in what order
1. **System of record** (your ATS or tracker): stage dates and candidate status. The source of truth.
2. **Calendar**: verifies that interviews happened and when.
3. **Email (or ATS scorecards)**: scorecards and feedback turnaround.
4. **Meeting transcripts**: qualitative sections only (standouts, market insights, risks). Never used for numbers.

When sources disagree, it uses the system of record for the numbers and lists both values in the footer. It never changes your data.

## Your first run

**Before you start**
- Paste `flight-status-prompt-universal.md` into your LLM once. It should reply only "Flight Status is loaded. Tell me which req to run." If it starts building right away, your tool ignored the wait instruction. Say "Stop, wait for my request" and continue.
- Connected tools (calendar, email, ATS) are nice to have, not required. If your LLM can't connect to them, attach exports and paste in calendar entries, scorecard emails and transcripts instead. That is the normal path, not a workaround.
- For the best timing metrics, export stage history (an activity log or pipeline-history report), not just the current-stage list.

**Copy, fill in and send this message**

```
Run Flight Status for [REQ TITLE / ID].
System of record: [your ATS or tracker; attach export or say which connector]
Job level: [IC / People Leader / Executive, or your own levels]
Calendar: [Google Calendar / Outlook / other; export or connector]
Email: [Gmail / Outlook / other, or "scorecards are in the ATS"]
Meetings: [Zoom / Teams / Meet / none]
Invite naming: [how your interview invites are titled, e.g. "Interview: <Candidate> - <Req>"]
Do I organize all interviews for this req: [yes / no]
Stage map: [your stage names -> standard stages, or "stages already match"]
Readers: [e.g., hiring manager and VP]
Company: [name, or leave out]
Share method: [emailed HTML / screen share / PDF]
Optional: anonymize candidates
```

**Check your first brief**
- You get two files. The summary's numbers match the full brief exactly, and it has no notice periods, competing processes, scorecard content or pay.
- The headline names the same slowest stage that the Time in Stage tile shows.
- Every number on the page appears in a tile, chart or the footer's list of sources.
- The footer says which sources it reached and how (connected or pasted), and lists any stages it couldn't map.
- If your system of record had no stage-entry dates, the timing metrics say No Data and the top of the brief names the export that would fix it.
- Calendar events it couldn't match to a candidate are listed, not guessed.
- No compensation or pay ranges anywhere in the file. Search the file for "$", "salary", "comp" and "base" to be sure.
- If you used "anonymize candidates," search the file for real names, including in the footer and the transcript-derived sections.

**What to send back (copy and fill in)**
```
ATS / tracker:
LLM used:
Calendar / email / meeting tools:
Connected, or pasted exports:
Did it wait for my request before building? (yes/no)
Stages that didn't map cleanly:
Metrics that showed No Data, and why:
Anything that didn't match between cards and headline:
Anything about pay the brief still revealed:
Anything confusing in the brief or the prompt:
```

## Limits to know about
- **Stage-entry dates are required for timing metrics.** Many ATS exports show only a candidate's current stage. Without stage history (an activity log or pipeline-history export), Submit → Interview Time and Time in Stage show No Data. It won't back-fill dates from your calendar unless you say "allow derived dates", and then it labels them lower confidence.
- **The targets are mine, not industry benchmarks.** Time to Fill targets (IC 45, People Leader 66, Executive 90 days) and stage targets (3 days Screen → HM Review, then 7 days per stage) are defaults. Replace them with your own. For reference, SHRM's 2025 report gives a median of 39 calendar days for nonexecutive roles; I couldn't find a public executive figure. Published stage-by-stage targets are mostly vendor examples with no cited data.
- **The status thresholds are defaults too.** At Risk is up to 2x the target; Delayed is beyond that. Edit `STATUS_RULES` in the file if your team draws the line elsewhere.
- **Your stage names will differ.** Map them to the seven standard stages in the prompt (Recruiter Screen, HM Review, HM Interview, Interview Round 2, Final Interview, Offer Accepted, Hired). The page shows your own names.
- **Not yet tested on real exports from other systems.** I built and tested it against a Greenlight JSON export and fictional CSV exports, with fresh LLM sessions reading only the prompt. I haven't tried real Greenhouse, Ashby, Gem, Lever or SCALIS exports yet. If you try one and something breaks, please share what happened. A per-ATS export guide would help everyone.
- **Recruiting CRMs work only if the export fits.** Many CRMs track a talent pool rather than a specific req, or keep only the current stage. If candidates aren't tied to the req, or there are no stage-entry dates, expect No Data for the timing metrics (and a note saying so) rather than guesses.
- It works from the data you point it at. Check the output before you share it, especially if it names candidates or draws on transcripts.

## Defaults you can change
These are the starting values in the prompt. They are confirmed as the defaults, and each one can be overridden in your message or in the file.

| Setting | Default |
|---|---|
| Job levels | IC; People Leader (manager through VP); Executive (SVP, C-level and equivalent) |
| Time to Fill target | IC 45 days; People Leader 66 days; Executive 90 days |
| Stage targets | Recruiter Screen → HM Review 3 days; every later stage 7 days; Offer Accepted → Hired has no target |
| On Time / At Risk / Delayed (time-based) | At or under target / up to 2x target / over 2x target |
| Time to Fill with no offer yet | At Risk from 90% of the target days used; Delayed past 100% |
| Rate metrics (vs. your historical average) | At Risk from 80% of the average up to the average; Delayed below 80%; needs at least 3 closed reqs, otherwise No Data |
| "Needs attention" in aging | 5+ days in the current stage |

Keep these in the `STATUS_RULES` and `STAGE_TARGETS` objects at the top of the generated file so they are easy to find and edit.

## Files
- `flight-status-prompt-universal.md`: the prompt.
- `samples/flight-status-sample-full.html`: sample full brief (fictional data).
- `samples/flight-status-sample-summary.html`: sample one-page summary (fictional data).
- `LICENSE`: MIT.

GitHub shows `.html` files as code. To see the samples rendered, turn on GitHub Pages for the repo, or download the files and open them in a browser.

## Look and feel
Light page, Gotham green accent, a pinned sidebar with the req facts, and a "runway" bar showing where candidates are. It is a plain HTML file with one external link (Google Fonts); offline it falls back to system fonts. The colors and layout are spelled out under "Brand" in the prompt, so you can swap the palette by editing those tokens. Status colors (green, amber, red) always mean On Time, At Risk, Delayed, so keep them distinct from your brand color if you change it.

## Changelog
- **v3.12**: clearer handling of funnels that don't match the seven standard stages (several stages into one, missing stages, stages that fit none, skipped and repeated stages), a screenshot option for getting data in, a "Connecting your ATS, CRM or tracker" README section, and an optional "turn it into a skill" note.
- **v3.11**: added the one-page summary for hiring managers and executives (every run now produces the full brief and the summary from the same numbers; the summary is always share-safe, has a word budget, and carries scorecard status and source on each candidate but never scorecard content). Added "recruiting CRM" to the supported systems with the export requirement spelled out. Closed ambiguities found by two fresh test runs (share-safe vs. readers beyond the hiring team, no source tags on the summary, date format, exited candidates in averages, runway wording, page titles). Added an MIT license, a .gitignore that keeps real briefs out of the repo, and a second sample.
- **v3.10**: light wording compression (about 5% shorter; rules unchanged) and three contradictions closed: the hero carries no "level target" label (it goes in the footer notes), ATS governs stage dates and metrics while the calendar date is used only for scorecard turnaround, and the sharing reminder now lives only in the footer line.
- **v3.9**: the Interview-to-Offer tile no longer shows the historical average as the big number. It shows a dash and "No offer on this req yet", with the average in the note line.
- **v3.8**: Key Metrics now sit before the Runway in the sample as well as the prompt. Closed remaining contradictions found in test runs: the Interview-to-Offer rule appeared twice with different outputs, the header facts live in the sidebar, combined source tags are allowed, runway first and last tile wording, date format, and source mix and time between stages are stacked, not side by side.
- **v3.7**: while a req has no offer, the Interview-to-Offer tile keeps its title and shows a dash, "No offer on this req yet" and the historical average in the note line (status No Data), so the average can't be mistaken for this req's result, instead of "0 of N". Scorecard turnaround is measured from the calendar interview date, with any ATS difference listed in the footer. Time to Fill shows the day count as the numeral. Every average shows one decimal. Exited candidates are summarized in one line, not listed as rows. The n appears once per tile and once per velocity row. Status counts are a vertical list.
- **v3.6**: tightened the page spec after a test run drifted (headline limited to 3 sentences and 60 words, exact hero layout, nothing but the numeral, status and source under it, page frame and runway tile details). Observation columns are named "What is moving" and "Where it is stuck" everywhere. Status is judged on unrounded values and shown to one decimal. The level target governs Time to Fill status. With no offer yet, Interview-to-Offer shows the historical average as a reference. The n is shown once per row. Sample corrected: Submit → Interview Time is 14.3 days, which is Delayed.
- **v3.5**: added the "share-safe" option (also leaves out notice periods, competing processes and candidate-level risks), a source tag on every metric tile, a minimum of 2 completed transitions before a stage can be named slowest, a "Sharing the file" section, and fixed the prompt's conflicting "internal use only" and "may be forwarded" wording.
- **v3.4**: only compensation and market pay ranges are left out. Notice periods, competing-process mentions and candidate-level risks are back in, with a footer line that the brief is for the hiring team.
- **v3.3**: one version only. Every brief is share-safe (no pay, notice periods, competing offers or candidate-level risks), so the version option and the second sample are gone. Source mix is now ranked rows; the sidebar is compact enough to fit one screen.
- **v3.2**: new design (light canvas, Gotham green, sidebar layout, runway bar). The route and conversion funnel are now one runway with pass-through %, the source-mix donut is a single stacked bar, and candidate cards are ruled rows. Samples rebuilt to match.
- **v3.1**: added a "Your first run" section (first message template, checklist, feedback form) and a "Defaults you can change" table.
- **v3.0**: renamed stages and metrics to common recruiting terms (Offer Accepted, Hired, HM Review, Interview Round 2, Interview-to-Offer Ratio, Time in Stage), added At Risk, "target" instead of "benchmark", job levels, display of your own stage names, Canceled state.
- **v2.1**: share-safe version, headline written last with a self-check, neutral label for Submit → Interview Time.
- **v2.0**: universal version for any ATS, calendar, email and meeting platform.
