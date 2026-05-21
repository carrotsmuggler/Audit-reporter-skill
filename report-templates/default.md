# Default Report Template

The body of every field must be written in plain text. The only markdown allowed are the bold field label lines (**Title:**, **Severity:**, **Description:**, **Recommended Mitigation:**) and code blocks when they directly ground the explanation in specific lines of code. Everything else — inline function names, contract names, variable names, numbers — must be plain text with no backticks, no inline bold, no italics, and no bullet lists. This format is designed to be pasted directly into Google Docs.

Use the exact field labels below. Write the Description as a single flowing section — do not split it into sub-sections.

---

**Title:** A concise, descriptive title that captures the vulnerability.

**Severity:** One of: High / Medium / Low / Informational / Governance

**Description:**
Write a single cohesive section in paragraph form covering all four of the following points — do not create separate headers or sub-sections for them:

1. What the vulnerability is and where it occurs (specific contract and function name).
2. What an attacker can achieve or what can go wrong — be specific about consequences, not vague.
3. A proof of concept walkthrough that demonstrates the exploit path step by step (include whenever feasible).
4. Clear reasoning for the assigned severity level, based on the actual impact and realistic likelihood.

Write in paragraphs. Do not use bullet points or numbered lists in the description unless the content is genuinely list-like and paragraphs would obscure it.

**Recommended Mitigation:**
Describe a design-level or logic-level fix. Examples: "Add a reentrancy guard", "Implement an access control check before X", "Use a pull-over-push pattern for withdrawals". Do NOT provide exact code patches or line-by-line diffs — keep it at the approach level.

---

## Example

**Title:** Share inflation possible during large losses

**Severity:** Medium

**Description:**
In case of a large loss where users exit the vault before losses get reported, the protocol can enter a state where very few wei of assets back a large number of shares.

Consider the vault has 1000e18 DAI of backing, and there is a loss of 200e18 DAI, with 1:1 share ratio. Users can quickly withdraw all except 1 wei of assets by burning up ~800e18 shares, leaving the pool with 1 wei DAI backing and 200e18 shares. Now if users enter the vault at this state, they will be minted 200e18 shares for each wei of their deposit, meaning a simple 1e18 DAI deposit can net 200e36 shares. Such inflated share numbers can lead to overflows in vault operations, leading to stuck funds.

The contract implements safeguards for the scenario where the totalAssets hit 0, but the share inflation can happen if the totalAssets hit any low number, not just 0.

**Recommended Mitigation:**
A clean fix for this is difficult. In case of very large losses putting the vault in this state, consider shutting them down and creating a new vault with the share ratios reset to avoid large share inflation.
