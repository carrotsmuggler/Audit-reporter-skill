# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is a **Claude Code skill** — a single `SKILL.md` file that teaches Claude how to run a structured smart contract security audit workflow. It is not a compiled or executed codebase. There are no build steps, tests, or dependencies to install.

The skill is invoked as `audit-reporter` from within Claude Code when a user asks to audit smart contracts, generate vulnerability reports, or run any part of the audit workflow.

## Repository layout

```
SKILL.md                         — the skill definition (prompt + workflow)
report-templates/default.md      — the default issue report template
examples/contract-description.md — example output for description mode
```

## How the skill works

The skill runs a four-step workflow against a user-provided list of Solidity or Rust source files:

1. **Description mode** — generates `contract-descriptions.md` summarising each contract in scope.
2. **Discovery** — scans every file for `@issue` and `@audit` inline tags, assigns sequential index numbers, writes `discovery.md`, and annotates the source files in-place.
3. **Validation** — each discovered issue is assessed for correctness; false positives are documented and discarded.
4. **Report** — each valid issue gets an `issue-{index}.md` written using the active report template.

All output lands in an `audit-reporter/` directory (or `audit-reporter_N/` if one already exists) relative to the audited project's root.

Progress is tracked in `<output-dir>/todo.md`, which is created at the start and updated throughout.

## Report template format

Reports in `report-templates/default.md` follow a strict plain-text format: only bold field labels and code blocks are allowed — no inline backticks, bold, italics, or bullet lists in the body. This is intentional so reports paste cleanly into Google Docs.

The four required fields are: **Title**, **Severity**, **Description**, **Recommended Mitigation**.

## Contract description format

Descriptions in `examples/contract-description.md` use a fixed structure per contract:
- **Summary** — flowing prose only, no "Key operations:" label
- **Appendix** — optional, prose-first, max 4 lines per function
- **Privileged functions** — bullet list
- **Core invariants** — max 6, specific not generic

Start every contract description with `"The [ContractName] contract ..."`.

## Extending the skill

- **New report template**: add a `.md` file to `report-templates/` following the same field structure as `default.md`. The skill will list available templates during setup.
- **Editing the workflow**: all logic lives in `SKILL.md`. The operational flow, subagent count formula, and per-step rules are all defined there.
- **Scope filtering**: files inside `interfaces/`, `mocks/`, or `test/` directories, and files matching `*.t.sol`, `*Test*.sol`, or `*Mock*.sol` are excluded automatically unless explicitly listed by the user.
