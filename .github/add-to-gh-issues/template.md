# Templates

Two blocks. Use the **open** block as the issue body for any item still to be done (feature, request,
bug, recommendation, decision, task). Use the **closed** block for an item you are closing as
done/verified — it becomes both the `evidence/<ID>-<slug>.md` file and, when a source doc exists, the
inline per-item section.

---

## Open block — issue body for an item still to be done

```markdown
**Item ID:** <ID>  ·  **Type:** <bug|feature|enhancement|architecture-decision|decision-register|infrastructure|deployment|cleanup|quality|documentation|question>  ·  **Source:** <doc §x / this session / request>

## Context
What this is and why it matters — the value if done, or the risk/cost if not.

## Impact of changes
The blast radius of doing this work — what it touches and who/what is affected. Components, files,
schemas, or systems changed; downstream items/issues impacted (link by #); user-facing or outward
effects; reversibility (easy revert vs. one-way door); and any migration/coordination it forces.
State "Low / localized" explicitly when the change is self-contained.

## Instructions / acceptance criteria
1. <discrete, copy-pasteable step, or a testable acceptance criterion>
2. ...

## Recommendation
The preferred option (and any alternative, with the trade-off). For a decision item, the proposed
resolution.

## Notes & risk
Dependencies, anything that could block it (use the `blocked` label if an external dependency holds it
up), risk introduced by the change itself.

## Verification (pending)
- [ ] <command, test, or check that will confirm completion>
```

---

## Closed block — `evidence/<ID>-<slug>.md` + inline doc section

```markdown
# <ID> — <Descriptive Label>

> **Issue:** <repo>#<n>  ·  **Type:** <type>  ·  **Source:** <doc §x / this session>
> **Status:** ✅ CLOSED <YYYY-MM-DD>   (or)   🟡 PARTIAL / DECIDED <YYYY-MM-DD>

## Verification
<!-- A REAL, citable proof of completion — produced this session or quoted from the source.
     One or more of: a command + its actual output, a PR/commit link + diff, a test/CI result,
     a config/doc change, or a dated decision. Never fabricate. Outstanding check → "[ ] pending". -->
```

```
$ <command>
<actual output>
```

```markdown
## Status
<CLOSED / PARTIAL / DECIDED> — one-line statement of the end state.

## Impact / Value
What this delivered, or the risk/cost it removed.

## Impact of changes
The realized blast radius — what the applied change actually touched and who/what it affected:
components/files/schemas/systems changed, downstream items/issues impacted (link by #), user-facing
or outward effects, and reversibility (how to roll back). State "Low / localized" when self-contained.

## Change applied
The exact change(s) made — commits/PR, code/config edits, provisioning, the accepted decision.

## Rationale
Why this approach (and why any deliberate exception or alternative was rejected).

## Notes & residual risk
What remains after the change. For PARTIAL/blocked items, state what's left and what it's waiting on.
```
