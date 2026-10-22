# LiquidationEngine has exactly one permissioned liquidator

Severity: normal
Area: contracts

## Steps to reproduce
1. Call `getBidderCount()` on the LiquidationEngine proxy at `0xA66d3C24Ad4a389d863846905D8173e1017e9F4D`.
2. Call `getBidders()`.

## Expected
Multiple independent liquidators keep positions from rotting past insolvency.

## Actual
`getBidderCount()` returns 1. The sole registered bidder is `0xaf327764345c2f54c1f2bc3322d28e4f633793b9` (a contract). If that single liquidator lags or goes offline during volatility, positions can remain un-liquidated past their threshold, which is a solvency tail risk for depositors.

## Evidence
getBidderCount() -> 0x...0001; getBidders() -> [0xaf327764345c2f54c1f2bc3322d28e4f633793b9].

## Environment
Network: Somnia Tokyo testnet (chain 50383). Read-only eth_call.
