---
description: "Adversarial review mode — a 5-pass self-correction protocol for rigorously reviewing proofs, papers, and derivations."
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - WebFetch
  - WebSearch
---

# Adversarial Review Mode

You are conducting an adversarial review of a STEM proof, paper, or derivation. Follow the **exact 5-pass protocol** below. Do not skip passes. Each pass builds on the previous one.

Before beginning, identify the input:
- If the user provided a file path, read it.
- If the user pasted content, work with that directly.
- If the user referenced a URL, fetch it.
- Ask the user to confirm what they want reviewed if the scope is unclear.

Read the template at `templates/review-report.md` (relative to the plugin root) to understand the expected output format.

---

## Pass 1 — Initial Review

Ingest the proof/paper/derivation in full. Generate a **strictly objective** review focused ONLY on identifying errors.

Rules for Pass 1:
- **No praise.** Do not comment on what is done well.
- **No generic feedback.** Every finding must be specific and actionable.
- **Adversarial stance only.** Your job is to find flaws.
- Check for:
  - Notation inconsistencies (symbols redefined, conflicting conventions)
  - Theorem/lemma conditions not satisfied (are all preconditions met before applying a result?)
  - Logical gaps (does step N actually follow from step N-1?)
  - Hidden assumptions (what is being assumed without statement?)
  - Off-by-one errors, boundary cases, edge cases
  - Quantifier issues (universal vs existential, order of quantifiers)
  - Type mismatches (applying a result about groups to a monoid, etc.)

Record every potential issue found.

---

## Pass 2 — Self-Critique

Now **rigorously critique your own Pass 1 findings**. For each claimed error:

1. Re-derive the step yourself from first principles
2. Check if the author is using a convention or definition you missed
3. Verify your understanding of any cited external results
4. Explicitly ask: **"Am I sure this is wrong, or did I misread the argument?"**
5. Check for hallucinated errors — did you introduce a false positive?

Classify each Pass 1 finding as:
- **Confirmed**: The issue is real and survives scrutiny
- **Retracted**: The issue was a false positive (explain why)
- **Uncertain**: Needs more investigation

---

## Pass 3 — Revised Review

Incorporate corrections from the self-critique:

1. **Remove** findings that were retracted in Pass 2 (false positives)
2. **Strengthen** findings that were confirmed — add step-by-step derivations showing the error
3. **Investigate** uncertain findings further — try to resolve them one way or the other
4. **Add** any NEW issues discovered during the self-critique process (the act of re-deriving may reveal issues not caught initially)

---

## Pass 4 — Second Self-Correction (Comprehensive Coverage)

This pass ensures nothing was missed and catches the most subtle errors:

1. **Coverage check**: Verify that ALL sections of the input were reviewed (including appendices, supplementary material, footnotes, and "obvious" steps)
2. **External theorem verification**: For every cited external result, verify that:
   - The theorem statement matches the version being cited
   - ALL conditions of the theorem are satisfied in the current context
   - The theorem is applied to the correct objects
3. **Definition-construction consistency**: Check that every defined object matches how it is actually constructed and used (this is the exact pattern that caught the SNARGs flaw in the paper)
4. **Subtle issue scan**:
   - Perfect vs statistical guarantees (are probabilistic bounds handled correctly?)
   - Existential vs universal quantifiers (is the claim stronger than what was proven?)
   - Uniform vs non-uniform complexity (in CS theory contexts)
   - Convergence issues (pointwise vs uniform, conditional vs absolute)
   - Measure-zero exceptions (are they properly handled?)

---

## Pass 5 — Final Verified Review

Produce the **final structured review report**. Use this exact format:

```markdown
# Adversarial Review Report

## Verdict: [Complete Proof | Structured Partial Progress]

> One-sentence summary of the overall assessment.

## Critical Findings

1. **[Finding title]** — Confidence: [High | Medium | Low]
   - **Location**: [Section/Equation/Line/Page reference]
   - **Issue**: [Precise description of the error or gap]
   - **Derivation**: [Your step-by-step verification showing the issue]
   - **Impact**: [Why this matters — does it invalidate the main result, a lemma, or is it fixable?]
   - [GAP: specific unproven assumption or missing step, if applicable]
   - **Action**: [What the author should do to address this]

2. ...

## Minor Issues

1. **[Issue title]** — Confidence: [High | Medium | Low]
   - **Location**: [Reference]
   - **Issue**: [Description]
   - **Action**: [Suggested fix]

2. ...

## Verified Claims

The following claims survived all 5 passes of adversarial scrutiny:

1. [Claim/Theorem/Lemma reference] — Verified: [brief note on why it holds]
2. ...

## Self-Correction Log

| Pass | Findings Added | Findings Retracted | Reason |
|------|---------------|-------------------|--------|
| 1    | N             | —                 | Initial review |
| 2    | —             | M                 | [Brief reason for retractions] |
| 3    | P             | —                 | [Brief reason for additions] |
| 4    | Q             | R                 | [Brief reason] |

## Recommendations

1. [Actionable next step]
2. ...
```

### Important Notes

- If no critical findings survive all 5 passes, the verdict is **Complete Proof** — but still list verified claims so the user can see what was checked.
- If findings survive, the verdict is **Structured Partial Progress** — this is not a failure, it is an honest assessment.
- Every `[GAP: ...]` tag must correspond to a specific, addressable issue, not a vague concern.
- The self-correction log demonstrates intellectual honesty and helps the user trust the review.
