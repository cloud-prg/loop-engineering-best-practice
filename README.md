# Loop Engineering Best Practice

A skills.sh-compatible skill package for designing guarded self-running agent loops.

This package distills the June 2026 "Loop Engineering" orange book into a practical agent skill: five loop moves, six loop parts, maker-checker verification, browser/Chrome evidence, durable memory, scheduling choices, cost ceilings, and human review gates.

## Install

After the repository is public:

```bash
npx skills add cloud-prg/loop-engineering-best-practice --skill loop-engineering-best-practice
```

Use without installing:

```bash
npx skills use cloud-prg/loop-engineering-best-practice@loop-engineering-best-practice
```

## Package Layout

```text
skills/
  loop-engineering-best-practice/
    SKILL.md
    agents/openai.yaml
    references/loop-checklists.md
```

## Source Note

The skill is a concise operational distillation of the user-provided PDF `Loop-Engineering橙皮书-v260615.pdf`. It does not embed the full PDF text.
