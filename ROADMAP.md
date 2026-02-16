# Roadmap

Potential improvements to stem-copilot, roughly ordered by impact. None of these are in progress — this is a backlog for future work.

## New Skills

### `/stem-copilot:threat-model`
Structured threat modeling skill for security architecture review. Takes a system description and produces a STRIDE-based or attack-tree-based analysis with the same rigor as the review mode — explicit gap tags, confidence levels, and structured output. Natural complement to the review skill for cybersecurity work.

### `/stem-copilot:verify-fix`
Follow-up mode that takes the original review report + a revised version of the work and checks whether each flagged finding was actually addressed. Re-reviews only the areas that were flagged rather than running all 5 passes from scratch. Outputs a delta report: what's fixed, what's still open, what's regressed.

### `/stem-copilot:compare`
Diff two versions of a proof, design document, or security analysis. Identifies what changed, whether changes address known issues, and whether new issues were introduced. Useful for iterating on work that's already been reviewed.

## Enhancements to Existing Skills

### Auto-save review reports
`/stem-copilot:review` should write its final Pass 5 report to a file (e.g., `review-report-<timestamp>.md`) in addition to printing it. Long reviews scroll past the terminal buffer and the output is lost.

### Argument hints
Add `argument-hint` to the skill frontmatter so the autocomplete shows usage:
- `/stem-copilot:review [file, URL, or paste]`
- `/stem-copilot:solve [problem statement]`

### Domain-specific reference checklists
The `skills/review/references/` directory can hold domain-specific checklists that get loaded as context during Pass 4 (comprehensive coverage). Candidates:
- `references/crypto-checklist.md` — common flaws in provable security reductions (tightness gaps, hybrid argument errors, game-hopping mistakes)
- `references/k8s-security-checklist.md` — known multi-tenant attack surfaces (IMDS, DNS, kernel, CNI enforcement, RBAC pitfalls)
- `references/protocol-checklist.md` — common protocol analysis mistakes (reflection, interleaving, type confusion, nonce reuse)
- `references/complexity-checklist.md` — common reduction errors (gadget construction, direction of reduction, promise problems)

These checklists give the reviewer domain knowledge it might otherwise miss. The K8s review caught the cloud metadata endpoint and CNI assumption without a checklist — with one, coverage would be even more reliable.

### Smarter auto-engage routing
The parent `stem-copilot` skill currently just describes when to engage. It could include routing logic that examines the user's context and suggests the most appropriate mode:
- Existing work being reviewed → suggest `/stem-copilot:review`
- New problem being tackled → suggest `/stem-copilot:solve`
- Security architecture document → suggest `/stem-copilot:threat-model` (once built)

### Test the solve mode
The neuro-symbolic solve mode (`/stem-copilot:solve`) has not been exercised yet. It should be tested with a problem that requires the full loop: decomposition, code generation, execution, failure handling, branch exploration, and solution assembly.

## Structural

### Publish to a marketplace
Currently installed via `--plugin-dir` or local marketplace. Once stable, publish to a public Claude Code plugin marketplace for easier distribution.
