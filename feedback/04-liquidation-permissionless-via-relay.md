# Liquidation entry point is permissionless via open relay, bypassing the single-bidder record

Severity: normal
Area: contracts

## Steps to reproduce
1. On Somnia Tokyo testnet (chain 50383), inspect the relay at `0x03ca1f61c5a475951f69f51bd16523075fdfb1cc`.
2. Its 177-byte runtime reads the EIP-1967 implementation slot (`0x360894...382bbc`) and DELEGATECALLs the raw calldata. There is no owner check, allowlist or msg.sender filter anywhere in the relay bytecode.
3. Observe on-chain liquidation transactions:
   - tx 0xf3d1aab70b07df94d107a213fc7aca3ab4278dcee9cf4229c6129b7799467121 at block 30493426, sender EOA 0x92d237dea7eb9d37e1b303da586f4b83956df6ad
   - tx 0xe7074f0ee454471c7dfd69c1a9b4de8e3e72cf48c49b9ea467035d2fb38267b5 at block 30497141, sender EOA 0xc400f086fdb3aa5b0a565dfc8988fb6fe2511850
   Both call selector 0x03159a8f on the relay with pool argument 0xdec00272b831af8cbe05300dadc81df018db999f and executed real liquidations.
4. Compare with LiquidationEngine 0xA66d3C24Ad4a389d863846905D8173e1017e9F4D: getBidders() returns exactly one registered bidder, 0xaf327764345c2f54c1f2bc3322d28e4f633793b9.

## Expected
If liquidation is permissioned to a single registered bidder, the execution path should enforce that permission.

## Actual
Real liquidations were executed by two EOAs outside the bidder set, via a relay with zero access control. The documented single-bidder permission does not hold on the actual execution path; the entry is permissionless in practice.

## Impact
A permission model that exists on records but not on the execution path is inconsistent. Either the bidder restriction is bypassable by anyone (defeats the intended permissioning) or the real gating lives elsewhere and is undocumented. In both cases the team's threat model for who can liquidate positions does not match observed reality.

## Evidence
- eth_getCode(0x03ca1f61...) full disassembly: no access control, pure EIP-1967 delegatecall forwarder.
- eth_getLogs at blocks 30493426 and 30497141 with the two transaction hashes above.
- getBidders() eth_call result.

## Environment
Network: Somnia Tokyo testnet (chain 50383). RPC: https://api.hideki2.infra.testnet.somnia.network. Read-only analysis (eth_getCode, eth_call, eth_getLogs, eth_getTransactionByHash).
