# stem-copilot

A Claude Code plugin for rigorous STEM research collaboration. It implements two AI-assisted research methodologies drawn from the paper *"Accelerating Scientific Research with Gemini"* (arXiv: [2602.03837v1](https://arxiv.org/abs/2602.03837v1)):

1. **Adversarial Review** — a 5-pass self-correction protocol that turns Claude into a rigorous, adversarial reviewer of proofs, papers, and derivations.
2. **Neuro-Symbolic Problem Solving** — an iterative loop where Claude proposes mathematical hypotheses, writes code to verify them, and uses execution feedback to self-correct.

The plugin covers all STEM domains: mathematics, computer science, physics, economics, engineering, and more.

## Motivation

AI assistants are prone to two failure modes when doing technical work: **sycophancy** (agreeing with the user rather than finding real errors) and **hallucinated errors** (confidently reporting flaws that don't exist). Both are dangerous in research contexts.

The adversarial review protocol addresses this head-on. Inspired by the methodology in Section 3.2 of the paper — where an AI discovered a genuine flaw in a published cryptographic construction (SNARGs) by systematically re-checking whether definitions matched constructions — the plugin forces Claude through five passes of increasingly rigorous self-correction. Pass 2 explicitly asks *"Am I sure this is wrong, or did I misread the argument?"*, catching false positives before they reach the user. Pass 4 checks for the exact pattern that caught the SNARGs flaw: discrepancies between how an object is *defined* and how it is *used*.

The neuro-symbolic solver addresses a different problem: mathematical reasoning alone can go off the rails without grounding. Inspired by Section 6 of the paper — where an AI working on cosmic string physics used code execution to catch its own numerical instabilities and reformulate its approach — the plugin structures problem-solving as a loop of *propose, code, execute, analyze*. When a hypothesis fails, the plugin uses "negative prompting" to force exploration of fundamentally different approaches rather than minor variations on the same dead end.

Both modes produce structured markdown output with explicit confidence levels, gap tags (`[GAP: ...]`), and clear verdicts — never presenting partial progress as a complete proof.

## Installation

### Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed and configured

### Quick Start (Development)

Load the plugin directly when launching Claude Code:

```bash
claude --plugin-dir /path/to/stem-copilot
```

This is the simplest way to use the plugin during development. Changes to skill files require restarting Claude Code.

### Persistent Installation

To install the plugin permanently so it's available in every session:

1. **Clone** this repository:

   ```bash
   git clone https://github.com/AlvinKuruvilla/stem-copilot.git /path/to/stem-copilot
   ```

2. **Add it as a local marketplace** from within Claude Code:

   ```
   /plugin marketplace add /path/to/stem-copilot
   ```

3. **Install the plugin**:

   ```
   /plugin install stem-copilot@stem-copilot
   ```

### Verify installation

After restarting, the skills `/stem-copilot:review` and `/stem-copilot:solve` should appear in the available skills list.

## Usage

### Adversarial Review

Use `/stem-copilot:review` to review a proof, paper, or derivation. You can provide input by:

- Pasting the content directly into the conversation
- Providing a file path for Claude to read
- Providing a URL for Claude to fetch

The review follows a strict 5-pass protocol:

| Pass | Purpose |
|------|---------|
| 1 | **Initial Review** — Adversarial error detection. No praise, no generic feedback. |
| 2 | **Self-Critique** — Challenge every finding. Retract false positives. |
| 3 | **Revised Review** — Incorporate corrections, add newly discovered issues. |
| 4 | **Second Self-Correction** — Check comprehensive coverage, verify external theorems, scan for subtle issues (definition-construction mismatches, quantifier errors, convergence issues). |
| 5 | **Final Report** — Structured markdown with verdict, findings, confidence levels, gap tags, and self-correction log. |

Output classifies the work as either **Complete Proof** (every step verified) or **Structured Partial Progress** (gaps remain, each explicitly tagged).

### Neuro-Symbolic Problem Solving

Use `/stem-copilot:solve` to tackle a new problem, conjecture, or research question. The solver follows an iterative loop:

1. **Problem Understanding** — Restate the problem, identify the domain, clarify unknowns
2. **Decomposition** — Break into verifiable sub-claims with a dependency graph
3. **Hypothesis-Verify Loop** — For each sub-claim: propose a hypothesis, write verification code (Python/SymPy/NumPy), present to user for approval, execute, and analyze results
4. **Branch Exploration** — When an approach fails, use negative prompting to force fundamentally different methods
5. **Solution Assembly** — Combine verified sub-claims into a coherent solution with explicit caveats

Code execution is always **human-in-the-loop**: Claude presents the code and explains what it tests before asking for permission to run it.

### Auto-Engagement

The plugin's skill definition triggers automatically when Claude detects research-relevant context — reviewing proofs, checking derivations, working with mathematical claims, or exploring conjectures. You don't always need to invoke a command explicitly.

## Output Format

Both modes produce structured markdown with:

- **Verdict**: Complete Proof or Structured Partial Progress
- **Confidence levels**: High, Medium, or Low for each finding
- **Gap tags**: `[GAP: description]` marking every unverified assumption
- **Self-correction log**: A transparency record showing what was added and retracted across passes

## Project Structure

```
├── .claude-plugin/
│   ├── plugin.json          # Plugin metadata
│   └── marketplace.json     # Marketplace registration
├── skills/
│   ├── stem-copilot/
│   │   └── SKILL.md         # Auto-engaging skill (shared principles)
│   ├── review/
│   │   └── SKILL.md         # /stem-copilot:review — 5-pass adversarial review
│   └── solve/
│       └── SKILL.md         # /stem-copilot:solve — neuro-symbolic solver
├── LICENSE
└── README.md
```

## References

- Anil, R., Borgeaud, S., et al. (2025). *Accelerating Scientific Research with Gemini.* arXiv: [2602.03837v1](https://arxiv.org/abs/2602.03837v1)

## License

MIT
