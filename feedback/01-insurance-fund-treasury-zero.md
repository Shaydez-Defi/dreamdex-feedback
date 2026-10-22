# InsuranceFund treasury() points to the zero address with no TREASURER_ROLE holders

Severity: normal
Area: contracts

## Steps to reproduce
1. On Somnia Tokyo testnet (chain 50383), call `treasury()` on the InsuranceFund proxy at `0x50826894D47C6D4f3BeA38BFDC7Dc5c5be171b80`.
2. Call `getRoleMemberCount(bytes32)` for `TREASURER_ROLE` (`0x4bdd44f3663e20a67aaaedfa9c7a68a72e80eac6090dc42ccd5bd346813f124d`).

## Expected
treasury() should be a controllable address able to receive fund sweeps/allocations.

## Actual
`treasury()` returns `0x0000000000000000000000000000000000000000` and `getRoleMemberCount(TREASURER_ROLE)` returns `0`. Nobody currently holds the role needed to set the treasury. If any sweep or allocation routine routes to `treasury()`, funds are sent to the zero address and are unrecoverable.

## Evidence
eth_call results:
- treasury() -> 0x0000...0000
- getRoleMemberCount(TREASURER_ROLE) -> 0
- getRoleMemberCount(ALLOCATOR_ROLE) -> 1 (0x95f23a563ecee5c6f841d762943d2f2fec742253)
- getRoleMemberCount(FUNDER_ROLE) -> 1 (same address)
- getRoleMemberCount(DEFAULT_ADMIN_ROLE) -> 1 (0xd58596620ee6951b2958a931949e4ff7ce6222be)

## Environment
Network: Somnia Tokyo testnet (chain 50383). RPC: https://api.hideki2.infra.testnet.somnia.network. Read-only eth_call.
