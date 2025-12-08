# Megabytes (MGB) – Economic Overview

Megabytes uses a **fully transparent and immutable economic model**, cryptographically committed on-chain in **block #1**.  
The block subsidy, halving schedule, maximum supply, and all economic rules are permanently fixed unless the community performs a deliberate hard fork.


<p align="left">
  <img src="img/megabytes_emission_curve.png" width="850"/>
</p>

## Economic Model Summary (TL;DR)

- **Hard cap:** 26,280,000 MGB  
- **Initial block reward:** 6.25 MGB  
- **Halving interval:** 2,102,400 blocks (~4 years)  
- **Block time:** 60 seconds  
- **Reward curve:** Bitcoin-style geometric halving  
- **Premine:** 0 MGB  
- **Long-term inflation:** Converges to 0  
- **Mining:** Pure Proof-of-Work (no staking, no developer tax)  

## Economic Commitment (Block #1)

Megabytes anchors its entire economic model on-chain:

| Field | Value |
|-------|--------|
| **Document** | [ECONOMICS_SPEC.md](ECONOMICS_SPEC.md) |
| **Commitment Type** | OP_RETURN in block #1 |
| **Hash (SHA-256)** | `2AA75B318A97BB706A9325530406633405A96539FA58F6EE93A520EA3244813B` |
| **Purpose** | Guarantees immutability of the economic rules |

### How to verify:
```bash
sha256sum ECONOMICS_SPEC.md  
# Compare with OP_RETURN hash stored in block #1
```
---




