# Single EOA wallet controls every perps contract and all 21 pool upgrades

Severity: normal
Area: contracts

## Steps to reproduce
1. Call `owner()` on the proxies: MarginBank `0x478769E1177C0773c43A593DCCc4Ad15F15F1394`, PerpPoolFactory `0xE631fAEE547baeE07353E290f0562ea67d856560`, LiquidationEngine `0xA66d3C24Ad4a389d863846905D8173e1017e9F4D`, InsuranceFund `0x50826894D47C6D4f3BeA38BFDC7Dc5c5be171b80`, VoucherDistributor `0x2ACD9EFdA721450906bfE1bef3edE269636E42C4`, OperatorPermissionsRegistry `0x6B2FbfeD328FF5F20C0BE7D3C3437613aFFE8D3a`.
2. Call `owner()` on the UpgradeableBeacon at `0xb787f7d2f87d32c1505c110c4457ed36a3f6b002` (used by all 21 perp pool proxies).
3. Check `eth_getCode` on the owner.

## Expected
Privileged control over a perps stack would normally sit behind a multisig or timelock.

## Actual
All seven return the same address, `0xd58596620ee6951b2958a931949e4ff7ce6222be`, which is an EOA (no code). It can unilaterally upgrade the implementation of every contract and all 21 pools via `UpgradeableBeacon.upgradeTo()`, and holds DEFAULT_ADMIN_ROLE on InsuranceFund. One leaked key = total protocol takeover with no delay or co-signer.

## Evidence
owner() eth_call results as above; eth_getCode(0xd585...) returns 0x.

## Environment
Network: Somnia Tokyo testnet (chain 50383). Read-only eth_call / eth_getCode.
