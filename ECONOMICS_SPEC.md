# Megabytes (MGB) – Economic Specification

This document defines the **complete economic model** of the Megabytes blockchain.  
These parameters are enforced by consensus and cannot change unless the community performs a deliberate, coordinated hard fork. 
In other words, the economic model is permanently fixed for all existing and future nodes running the **official Megabytes protocol**.

This specification is intended to be **cryptographically anchored in block #1** via an `OP_RETURN` commitment, ensuring long-term public verifiability of its authenticity.

---

## 1. Monetary Unit

- **Base unit:** `MGB`
- **Atomic unit (minimum indivisible):** `1 MGB = 100,000,000 units (COIN)`
- **No smaller subdivision beyond `1 unit`**

---

## 2. Block Reward (Subsidy)

### Initial block subsidy

```text
6.25 MGB
```

### Consensus rule (as implemented)

```cpp
consensus.nInitialSubsidy = 25 * COIN / 4;   // 6.25 MGB
```

The block subsidy decreases by half at each halving interval using:

```cpp
nSubsidy = nInitialSubsidy >> halvings;
```

### Genesis block subsidy

```text
0 MGB   // No premine
```

The genesis block (height 0) does not create any spendable coins.

---

## 3. Halving Schedule

- **Halving interval:**

```cpp
consensus.nSubsidyHalvingInterval = 2'102'400; // blocks
```

- **Target block time:**

```text
60 seconds per block
```

This results in a halving approximately every **4 years**.

### Emission schedule (high-level)

| Period    | Block Height Range         | Approx. Years | Block Reward (MGB) |
|-----------|----------------------------|---------------|--------------------|
| Era 1     | 1 → 2,102,399              | 0–4           | 6.25               |
| Era 2     | 2,102,400 → 4,204,799      | 4–8           | 3.125              |
| Era 3     | 4,204,800 → 6,307,199      | 8–12          | 1.5625             |
| …         | …                          | …             | /2 each era        |
| Final era | after ~64 halvings         | far future    | 0                  |

The reward is halved every `2,102,400` blocks until it effectively reaches zero.

---

## 4. Maximum Supply (Hard Cap)

### Total maximum supply (hard cap)

```text
26,280,000 MGB
```

### Enforced by consensus through:

```cpp
static constexpr CAmount MAX_MONEY = 26280000 * COIN;
consensus.nMaxMoneyOut = 26280000 * COIN;
```

The function `MoneyRange()` guarantees that no transaction or block can create coins beyond this hard limit:

```cpp
inline bool MoneyRange(const CAmount& nValue)
{
    return nValue >= 0 && nValue <= MAX_MONEY;
}
```

---

## 5. Inflation Model

Megabytes uses a **Bitcoin-style geometric halving model**.

The total issued supply over infinite time is:

TotalSupply ≈ 2 × nInitialSubsidy × nSubsidyHalvingInterval

With:

- `nInitialSubsidy = 6.25 MGB`
- `nSubsidyHalvingInterval = 2,102,400 blocks`

we obtain:

TotalSupply ≈ 2 × 6.25 × 2,102,400 = 26,280,000 MGB

### Long-term inflation

After several halving periods, the block subsidy becomes negligible and converges to zero.  
In the long term, **new coin issuance stops**, and miners are compensated purely through transaction fees.

---

## 6. Fees

Megabytes uses **market-based transaction fees**, similar to Bitcoin.

### Consensus rules regarding fees

- The fee for a transaction is:

```text
fee = sum(vin) – sum(vout)
```

- The fee must satisfy:

```cpp
MoneyRange(fee) == true;
```

- No minimum fee is enforced at the consensus layer.

### Relay policy (non-consensus)

Nodes use a default minimum relay transaction fee:

```cpp
static constexpr unsigned int DEFAULT_MIN_RELAY_TX_FEE{100000}; // 0.00100000 MGB per kB
```

Notes:

- This is a **node policy**, not a consensus rule.
- Node operators may override this via the `-minrelaytxfee` command-line parameter.
- Transaction inclusion in a block is determined by miner policy and mempool fee levels.

### Fee market

Fees are determined by:

- Transaction size (in bytes)
- Network congestion
- Miner preferences

There is **no protocol-level cap or floor** on fees beyond `MoneyRange()`.

---

## 7. Additional Economic Guarantees

### No premine

- `GetBlockSubsidy(0) = 0`
- No coins are created at genesis for founders, team, or investors.

### No developer tax or treasury

- 0% of block rewards are reserved for a developer fund or treasury.
- All block rewards go to miners according to consensus rules.

### No staking

- Megabytes is a **pure Proof-of-Work (PoW)** economic model.
- There is **no on-chain staking mechanism** and no inflation based on stake.

### No hidden inflation

- The hard cap of `26,280,000 MGB` is enforced by consensus.
- There is no mechanism to increase the supply beyond this limit without an explicit hard fork.

### Multi-algorithm mining (separate from economics)

Megabytes may use multiple PoW algorithms as part of its consensus security design.  
However, **mining algorithms do not affect the monetary policy**:

- They do not change `nInitialSubsidy`
- They do not change `nSubsidyHalvingInterval`
- They do not change `MAX_MONEY`

---

## 8. Economic Commitment (On-Chain Proof)

This version of the economic specification is intended to be **committed on-chain** in:

- **Block:** `#1`
- **Commitment type:** `OP_RETURN` output in the coinbase transaction
- **Commitment value:** SHA-256 hash of this file

### Verification instructions

1. Download this file as `ECONOMICS_SPEC.md`.
2. Compute its SHA-256 hash:

```bash
sha256sum ECONOMICS_SPEC.md
```

3. Extract the coinbase transaction of block `#1` and inspect the `OP_RETURN` output.
4. Verify that the `OP_RETURN` data exactly matches the computed SHA-256 hash.

Any modification to this document would change its SHA-256 hash and therefore break the on-chain commitment, making this version **cryptographically immutable**.

---

## 9. Versioning

- **This document:** `ECONOMICS_SPEC.md`
- **Status:** Frozen
- **Allowed changes:** None  

---

## 10. Summary (TL;DR)

- **Hard cap:** `26,280,000 MGB`
- **Initial reward:** `6.25 MGB`
- **Halving interval:** `2,102,400 blocks` (~4 years at 60 s/block)
- **Block time target:** `60 seconds`
- **Reward curve:** Bitcoin-style geometric halving
- **Premine:** `0 MGB` (genesis subsidy = 0)
- **Fees:** Market-based, with a default min relay fee of `0.00100000 MGB/kB` (policy, not consensus)
- **Long-term inflation:** Converges to `0` as subsidy goes to zero
- **On-chain commitment:** SHA-256 hash of this file stored via `OP_RETURN` in block `#1`

---

**End of `ECONOMICS_SPEC.md`.**
