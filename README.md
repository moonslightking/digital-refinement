# Digital Refinement

Digital Refinement is a skill for extracting an investor's cognitive structure from historical materials and turning it into a runnable cognitive persona for LLM agents.

It is designed for investors, traders, fund managers, and investment writers whose public materials contain enough evidence to reconstruct how they reason, change their mind, handle uncertainty, and make decisions.

## What It Produces

A completed refinement project produces a cognitive persona folder with:

- `SKILL.md`: the runnable persona skill.
- `meta.json`: confidence, coverage, corpus, validation, and runtime metadata.
- `cognitive-timeline.md`: the target's cognitive timeline.
- `cognitive-evolution-narrative.md`: the narrative of cognitive change.
- `stage-profiles/`: stage-level cognitive profiles.
- `cognitive-structure/`: inheritable reasoning layer and contextual meta layer.
- `references/`: collected source evidence and context annotations.
- `validation/`: holdout validation, functional tests, baseline comparison, and cross-review notes.

## Core Ideas

- Cognition can be structured into mental models, decision heuristics, value priorities, expression patterns, biases, and boundaries.
- Every judgment must be interpreted in context: date, audience, market environment, and the person's own situation.
- Cognitive personas should preserve flaws and limitations. The goal is not to simulate an ideal investor.
- Social role constraints belong in the contextual meta layer. They should not become hard runtime bans for the agent.

## Workflow

```text
Phase 0 → Phase 0.1 → Phase 0.5 → Phase 1 → Phase 2 → Phase 3 → Phase 4 → Phase 5 → Phase 6
Feasibility  MCB check   Holdout split Data collection Context tagging Evolution model Structure Persona Validation
```

The holdout split must happen before data collection. The MCB check must happen before context tagging.

## Included Files

```text
digital-refinement/
├── SKILL.md
├── README.md
├── README.zh-CN.md
├── references/
│   ├── methodology.md
│   ├── agent-prompts.md
│   ├── context-annotation-guide.md
│   ├── agm-decision-tree.md
│   └── MCB-SCHEMA.md
└── templates/
    ├── persona-skill-template.md
    ├── stage-profile-template.md
    └── meta-json-template.json
```

## External Data

The skill expects a Macro Context Base (MCB) when running investor-focused refinements. The schema is included in `references/MCB-SCHEMA.md`, but the actual MCB data files are intentionally not bundled.

Expected data files:

- `MCB_CN.jsonl`
- `MCB_US.jsonl`
- `MCB_META.json`

Keep source corpora, private notes, and sealed holdout files outside the public skill folder unless they are already cleared for publication.

## Installation

Place the `digital-refinement` folder in your agent's skills directory. The folder root must contain `SKILL.md`.

Example locations:

- Codex: `~/.codex/skills/digital-refinement/`
- Claude Code: `~/.claude/skills/digital-refinement/`

For this repository, edit the source under `skills/skills-staging/digital-refinement/` first, then sync or package it only after review.

## Usage

Trigger this skill when a user asks to refine, distill, or model an investor's cognitive persona. The skill is also appropriate for requests about decision style, mental models, cognitive bias profiles, and thinking evolution.

Typical prompts:

- "Refine Warren Buffett's cognitive persona from shareholder letters."
- "Extract this investor's mental models and decision heuristics."
- "Build a cognitive persona from this KOL's public posts."
- "Analyze how this fund manager's thinking changed over time."

## Quality Bar

A usable refinement must include:

- Source inventory and evidence quality labels.
- Context annotations with MCB references where relevant.
- Stage profiles and cognitive evolution reasoning.
- A separated inheritable layer and meta layer.
- Holdout validation and cross-review notes.
- Clear confidence level and usage boundaries.
