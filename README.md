# audit-reporter

A Claude Code skill for running structured smart contract security audits on Solidity and Rust (Solana, Soroban) codebases.

## What it does

The skill runs a four-step workflow against a list of source files:

1. **Description mode** — generates `contract-descriptions.md` summarising each contract in scope: purpose, key operations, privileged functions, and core invariants.
2. **Discovery** — scans every file for `@issue` and `@audit` inline tags, assigns sequential index numbers, writes `discovery.md`, and annotates the source files in-place.
3. **Validation** — each discovered issue is assessed for correctness; false positives are documented and discarded.
4. **Report** — each valid issue gets an `issue-{index}.md` written using the active report template.

All output lands in an `audit-reporter/` directory (or `audit-reporter_N/` if one already exists) relative to the audited project's root. Progress is tracked in `<output-dir>/todo.md`.

## Usage

Install the skill into Claude Code, then trigger it from any project:

```
/audit-reporter
```

Or trigger it naturally:

> "audit these contracts", "run a security review", "report these findings", "describe the contracts"

On first run the skill asks:
- Which files are in scope (or reads `scope.txt` if present)
- Whether to run description mode
- Whether to run the full discovery-validation-report (DVR) workflow
- Which report template to use

After that single setup exchange, it runs to completion without further prompts.

## Inline tagging

Mark issues directly in source files before running the skill:

```solidity
// @issue reentrancy possible — external call before state update
function withdraw(uint256 amount) external {
    token.transfer(msg.sender, amount); // @audit check ordering
    balances[msg.sender] -= amount;
}
```

- `@issue` — flags a highly likely vulnerability
- `@audit` — flags an area of concern needing investigation

Discovery indexes every tag, then validation and reporting work through the list.

## Repository layout

```
SKILL.md                         — skill definition (prompt + full workflow)
report-templates/default.md      — default plaintext report template
examples/contract-description.md — example output for description mode
```

## Report format

Reports use a strict plain-text format so they paste cleanly into Google Docs. No inline backticks, bold, italics, or bullet lists in the body — only bold field labels and code blocks.

Four required fields: **Title**, **Severity**, **Description**, **Recommended Mitigation**.

## Extending

- **New report template**: add a `.md` file to `report-templates/` following the same field structure as `default.md`. The skill lists available templates at setup.
- **Editing the workflow**: all logic lives in `SKILL.md`.
- **Scope filtering**: files in `interfaces/`, `mocks/`, or `test/` directories, and files matching `*.t.sol`, `*Test*.sol`, or `*Mock*.sol` are excluded automatically unless explicitly listed.
