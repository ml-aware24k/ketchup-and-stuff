---
name: gh-issue
description: Turn any unit of work — a change, feature, user request, bug, agent finding, recommendation, architecture/decision, or infra/deploy/cleanup task — into discrete, individually-tracked GitHub issues, with evidence on closure. Use when the user wants to "track this in GitHub", "open an issue for X", "file issues for these items", "log this decision/recommendation", "track action items / findings", or "close out items with evidence" against a doc, a session's output, or an ad-hoc list, in any repo.
---

# GitHub Issue

GitHub issues are this project's tracking ledger for **every** kind of work unit — not just audit
findings. An item can be a change, a new feature / functional requirement, a user request, a bug, an
agent finding, a recommendation, an architecture/tech decision, an open decision from the register, or
an infra / deployment / cleanup task. Turn each into a **discrete, individually-tracked unit**:

```
one item ID  →  one GitHub issue  →  (on closure) one evidence artifact
```

Open items get a fully-specified, ready-to-action issue. Items completed/verified get an evidence
artifact + a closed issue. Nothing is fabricated and nothing is mutated on GitHub without showing the
plan first.

## Inputs
- **Item source** — any of:
  - one or more **doc paths** (a findings/review/PRD/decision-register file, or a folder of them);
  - a set of items **described in the conversation** (e.g. "open issues for these three requests");
  - **agent findings / recommendations produced this session** that the user wants tracked.
  If nothing concrete is given, **scan the repo** for likely source docs (headings/filenames matching
  `audit|review|findings|remediation|retro|assessment|gaps|decision|backlog|action items|requests`),
  present candidates, and let the user pick. **Never file an item without confirmation.**
- **Today's date** — use `currentDate` from session context; **never guess**, never use relative dates.
- **Target repo** — the repo of the working directory. Resolve with
  `gh repo view --json nameWithOwner` and show it in the plan. If items come from outside a repo, ask
  which repo to file against.

## Tracker
Default and primary path is **GitHub issues via the `gh` CLI**. If the repo clearly uses a different
tracker (GitLab, Jira, a plain markdown checklist), map the same model: `issue → ticket/row`,
`label → tag/field`, `gh issue close → that tracker's close action`. Keep the one-item-one-issue
invariant either way.

## Labels

**Discover first, then provision.** Run `gh label list` to see what the repo already has. Map each item
onto existing labels; only create a label when a needed one is genuinely missing. Label creation is a
write — include it in the step-1 plan and create only after confirmation.

**Default GitHub labels** (already present in any repo — reuse, don't recreate; listed here with their
name/description/color so this table doubles as a complete reference when cloning labels into a fresh
repo):

| Label | Description | Color |
|---|---|---|
| `bug` | Something isn't working | `d73a4a` |
| `documentation` | Improvements or additions to documentation | `0075ca` |
| `duplicate` | This issue or pull request already exists | `cfd3d7` |
| `enhancement` | New feature or request | `a2eeef` |
| `good first issue` | Good for newcomers | `7057ff` |
| `help wanted` | Extra attention is needed | `008672` |
| `invalid` | This doesn't seem right | `e4e669` |
| `question` | Further information is requested | `d876e3` |
| `wontfix` | This will not be worked on | `ffffff` |

**Project custom labels** (replicate any that are missing, with these exact name/description/color):

| Label | Description | Color |
|---|---|---|
| `blocker` | Must be resolved before/at sprint 1 | `B60205` |
| `architecture-decision` | Architecture / tech decision (ADR) | `1D76DB` |
| `decision-register` | Open decision from the Decision Register | `5319E7` |
| `quality` | Quality / clarification improvement | `FBCA04` |
| `feature` | Functional requirement / feature request | `0E8A16` |
| `decided` | Decision accepted — proceeding | `0E8A16` |
| `blocked` | Work is blocked by an external dependency | `D93F0B` |
| `infrastructure` | Azure/infra provisioning & environment | `0052CC` |
| `cleanup` | Housekeeping / resource cleanup | `C5DEF5` |
| `deployment` | App deployment to Azure | `1D76DB` |

To create a missing custom label:
`gh label create "<name>" --description "<desc>" --color <hex-no-#>`

**Choosing labels per item — at least one *type* label, plus *lifecycle* labels as they apply:**
- **type:** `bug` · `feature` (functional requirement) or `enhancement` · `documentation` ·
  `architecture-decision` (ADR) · `decision-register` (open decision) · `quality` · `infrastructure` ·
  `deployment` · `cleanup` · `question` (a user request for info).
- **lifecycle / status:** `blocker` (urgent — needed by sprint 1) · `blocked` (external dependency) ·
  `decided` (a decision accepted, proceeding) · `wontfix` / `duplicate` / `invalid` (no-action
  closures) · `help wanted` / `good first issue`.
- **priority:** this project expresses urgency through `blocker` (and milestones), not `sev-*` labels —
  don't invent a high/medium/low scheme it doesn't use.

## Conventions

- **Item IDs** are the atomic unit of tracking. If the source already uses IDs (e.g. `G1`, `S1`,
  `O1`–`O3`, `ADR-04`, `DR-12`), keep them exactly. If none exist, **mint a stable ID per item**: a
  short type prefix + number (e.g. `FEAT3`, `BUG2`, `ADR5`, `INFRA1`) or a short slug. One ID = one
  item, stable across the issue, the artifact, and any doc. **Never merge two items into one issue;
  never split one item across issues.**
- **Issue title:** `[ID] <concise description>` — e.g. `[FEAT3] Self-service onboarding form`.
- **Artifacts** (evidence of completion) live in `evidence/<ID>-<slug>.md`. An evidence artifact is for
  an item you are **closing as done/verified** — it is *not* required to merely file an open item.
  Write/commit the artifact **directly to the working branch**. This skill does **not** route artifacts
  through a pull request and does **not** gate on branch-protection rules — branch protection is out of
  scope; if a direct commit is blocked, surface that to the user rather than working around it.
- **Status glyphs** when you render a status table: 🔴 OPEN, 🟡 IN PROGRESS / BLOCKED, ✅ CLOSED.

## Workflow

1. **Inventory → plan.** Build an item table:
   `ID | source (doc §/conversation) | type | description | state (open / done) | labels | existing issue (#/none)`.
   Decide "done" from real evidence, not optimism. Assign IDs (reuse existing, else mint). Cross-check
   existing issues with `gh issue list --repo <owner/repo> --limit 100 --state all`. Note any **custom
   labels that need creating**.
   **Present this plan and STOP.** List exactly what will be created (issues + labels), closed,
   labelled, and which artifact files will be written. Do not run any `gh` write command or write any
   artifact until the user confirms. (Filing/closing issues and creating labels are outward-facing and
   hard to reverse.)

2. **After confirmation — provision labels.** Create any missing custom labels (table above) with
   `gh label create`.

3. **After confirmation — OPEN items**, for each: create or update the issue with its type + lifecycle
   labels. Body uses [template.md](template.md) **open** block: **Context**, **Impact of changes**
   (blast radius — what it touches, downstream items affected, reversibility), **Instructions /
   acceptance criteria** (numbered, copy-pasteable), **Recommendation**, **Notes & risk**, and a
   **Verification (pending)** checklist. Do not close it; do not invent a result.

4. **After confirmation — DONE items**, for each:
   - Ensure the issue exists (create if missing, same label rules).
   - Write the evidence artifact `evidence/<ID>-<slug>.md` from [template.md](template.md) **closed**
     block: descriptive title, a real **Verification** (command + output, a PR/commit link + diff, a
     test/CI result, a config/doc change, or a dated decision), then **Status**, **Impact/Value**,
     **Impact of changes** (the realized blast radius — what the change touched, downstream items
     affected, how to roll back), **Change applied**, **Rationale**, **Notes & residual risk**.
     Mirror the block inline into the source doc's per-item section when there is one.
   - Close with a comment containing the full evidence artifact content (not just a link — paste the
     entire `evidence/<ID>-<slug>.md` body into the comment so the closure is self-contained on GitHub):
     `gh issue close <#> --repo <owner/repo> --comment-file evidence/<ID>-<slug>.md`.
     Add `decided` for an accepted decision, or `wontfix`/`duplicate`/`invalid` for a no-action close.

5. **Sync the source (if there is a doc).** Append or refresh one self-contained **Traceability**
   section: an `ID | item | issue | artifact | status` table with the glyphs, plus
   `_Last reconciled <today's date>._`.

6. **Prepend to the changelog (never overwrite existing entries).** Maintain a running changelog at
   the repo root named `changelog-<repo>.md`, where `<repo>` is the short repository name (e.g.
   `changelog-recruiting.md` for `owner/recruiting`). **Prepend** a new dated entry summarizing *this
   run* under a `## <today's date>` heading, inserted directly under the file's `# Changelog — <repo>`
   title so entries read **newest first (descending)**: issues opened / closed / labelled / edited
   (by #), labels created, and artifacts written — a short table or bullet list. **Never rewrite,
   reorder, or delete earlier entries** — only ever insert a new entry above them; earlier runs stay a
   frozen paper trail (same append-only-in-content, newest-first-in-order discipline as doc version
   history tables). If the file doesn't exist, create it with an `# Changelog — <repo>` title and the
   first entry. Commit it to the working branch alongside any artifacts; if a direct commit is blocked,
   surface that rather than working around it.

7. **Report.** Summarize: labels created, issues opened, issues closed, artifacts written, the
   changelog entry appended, items left open (with their issue #s), and anything you could not
   reconcile (e.g. claimed done but no evidence).

## Rules
- **Plan before mutate.** Never create/close an issue, create a label, or write an artifact before the
  user confirms the step-1 plan.
- **Never fabricate evidence.** A closure's Verification must be a real proof that exists in the source
  or was actually produced this session. Outstanding check → `[ ] pending`; don't invent output and
  don't close on it.
- **One ID, one issue, one artifact.** Don't bundle items; don't split one item across issues.
- **Don't downgrade silently.** If something is partially done / blocked, say so in Status and keep the
  residual work or risk visible (use `blocked` when an external dependency holds it up).
- **Discover, don't dictate.** Map items onto the repo's existing labels; create new labels only from
  the project custom set above when an equivalent is genuinely missing.
- Absolute dates only (today's `currentDate`). Match the existing tone and structure of any source doc.
