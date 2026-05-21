# Example: Contract Description

This shows the expected output format for description mode.

---

#### LendingBroker

The LendingBroker contract is the main contract in the protocol that handles loans. There are 2 types of loans: dynamic loans with variable interest rates, and fixed-rate loans with fixed rates and durations.

The contract interacts with an exclusive Moolah vault where only the broker can borrow and repay. This vault has 0 interest rates; actual interest calculations are handled by the LendingBroker instead.

When a user borrows, the contract borrows from the vault on the user's behalf, requiring the user to whitelist the broker on Moolah. During repayments, interest is deducted and retained by the broker; the principal is repaid to the vault under the user's account. Liquidations are triggered via the liquidate function.

##### Appendix: Dynamic loans

Dynamic loans apply a variable rate to the position. Debt is denominated in shares at the current getRate value, which increases over time — requiring higher repayments to cover the same borrowed shares.

Rate accrual is handled in RateCalculator. The Bot sets a rate per second via setRatePerSecond; getRate grows based on this. During repayments, interest is always cleared before the principal.

##### Privileged functions

- onMoolahLiquidate
- refinanceMaturedFixedPositions
- setMarketId

##### Core invariants

INV-1: Sum of loan principals must reflect the total amount borrowed from Moolah.
INV-2: During repayments, interest is always repaid before the principal.
INV-3: Each loan position must be at least minLoan value.

---
