# Lazycat App Feasibility

An agent skill for evidence-based feasibility assessment and delivery planning for applications targeting Lazycat MicroServer as LPK packages.

## Contents

- `SKILL.md` — the complete English skill guide.
- `agents/openai.yaml` — UI metadata for skill discovery and invocation.

## Use

Invoke the skill with `$lazycat-app-feasibility` when evaluating whether an open-source project, Docker application, Web application, or feature can be delivered on Lazycat MicroServer. The guide covers platform-capability mapping, permissions, storage, identity, multi-instance isolation, databases, routing, POCs, and release gates.

## Validation

```bash
python3 /home/burger/.codex/skills/.system/skill-creator/scripts/quick_validate.py .
```
