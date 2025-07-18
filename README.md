
# VestraNFT - `stx-vesting-nft`

A programmable, vesting NFT smart contract on the **Stacks blockchain** (Clarity), with built-in **staking, metadata, token fusion**, and **vesting-based upgrades**. This contract enables NFT issuance with dynamic utilities based on ownership duration and user interactions, backed by STX payments and rewards.

---

## 📦 Features

### ✅ Core Features

* **NFT Minting with Vesting:** Mint NFTs with programmable vesting periods.
* **Leveling System:** NFT levels increase based on how long it’s held.
* **STX-Based Utility:** Actions (minting, leveling) require STX payments.

### 🎁 Extended Features

* **Token Metadata:** Set custom name, description, and image URI per token.
* **Staking System:**

  * Stake NFTs to earn STX rewards over time.
  * Reward multiplier based on token level.
* **Reward Claiming:** Claim rewards anytime based on staked duration.
* **Fusion Mechanism:** Combine two NFTs into a more powerful one.

### 🔒 Enhanced Safety & Validation

* Robust error handling for invalid actions.
* Overflow/underflow protection.
* Ownership & balance checks before any state changes.

---

## 📚 Contract Structure

### 🧾 Constants

* `mint-price`: Initial minting cost (default 200 STX).
* `level-up-price`: Cost to upgrade token level (default 50 STX).
* `reward-rate`: Rewards per block when staked (default 0.01 STX).

### 📊 Data Storage

* `tokens`: Stores NFT ownership and vesting metadata.
* `token-levels`: Stores level-based utility data.
* `token-metadata`: Human-readable info (name, image, description).
* `staking-info`: Tracks staking timestamps and last reward claims.

---

## ⚙️ Public Functions

### 🛠 Minting

```clojure
(mint (vesting-period uint)) → (ok token-id) | (err ...)
```

* Mints a new NFT with the specified `vesting-period` (in blocks).
* Requires STX payment of `mint-price`.

---

### 📈 Token Progression

```clojure
(update-token-level (token-id uint)) → (ok new-level) | (err ...)
```

* Updates the token level based on how many blocks have passed since creation.
* Requires `level-up-price` in STX.

---

### 🎨 Metadata

```clojure
(set-token-metadata (token-id, name, description, image-uri)) → (ok true)
(get-token-metadata (token-id)) → (ok {name, description, image-uri}) | (err ...)
```

* Allows token owners to attach rich metadata to their NFTs.

---

### 🎁 Staking

```clojure
(stake-token (token-id)) → (ok true)
(unstake-token (token-id)) → (ok true)
(get-pending-rewards (token-id)) → (ok uint)
(claim-rewards (token-id)) → (ok reward-amount) | (err ...)
```

* Stake your NFT to earn block-based STX rewards.
* Rewards scale with token level.
* Must claim before unstaking.

---

### 🧬 Token Fusion

```clojure
(fuse-tokens (token-id-1, token-id-2)) → (ok new-token-id) | (err ...)
(preview-fusion (token-id-1, token-id-2)) → (ok {new-level, new-vesting-period})
```

* Burn two owned NFTs to create a new one with average vesting and increased level.
* Fusion preview lets users see potential outcomes.

---

### 💸 Administration (Owner Only)

```clojure
(set-mint-price (new-price)) → (ok true)
(set-level-up-price (new-price)) → (ok true)
(set-reward-rate (new-rate)) → (ok true)
(set-level-utility (token-id, level, utility)) → (ok true)
(withdraw-stx (amount)) → (ok amount) | (err ...)
```

* Adjust contract economics and metadata as the contract owner.

---

### 🔍 Read-Only Views

```clojure
(get-last-token-id) → (ok uint)
(get-owner (token-id)) → (ok (some principal)) | (err ...)
(get-token-uri (token-id)) → (ok none | some uri) | (err ...)
```

* Basic information retrieval about NFTs.

---

## 🚧 Errors

| Code       | Meaning                                |
| ---------- | -------------------------------------- |
| `err u100` | Owner-only access                      |
| `err u101` | Not the token owner                    |
| `err u102` | Invalid token                          |
| `err u103` | Insufficient STX balance               |
| `err u104` | Invalid parameters                     |
| `err u105` | Zero amount error                      |
| `err u106` | Unauthorized                           |
| `err u107` | Already staked                         |
| `err u108` | Not staked                             |
| `err u109` | Staking error (e.g., transfer failure) |
| `err u110` | Fusion failed                          |
| `err u111` | Fusion on same token is invalid        |

---

## 🚀 Deployment Notes

* Deploy as a Clarity contract on the Stacks blockchain.
* Ensure `contract-owner` is set correctly on deployment. It's defaulted to `tx-sender`.

---

## 🧪 Example Usage

```clojure
;; Mint a token with a vesting period of 100 blocks
(mint u100)

;; Level up the token (after enough blocks have passed)
(update-token-level u1)

;; Stake the token
(stake-token u1)

;; After a while, claim rewards
(claim-rewards u1)

;; Unstake and fuse with another token
(unstake-token u1)
(fuse-tokens u1 u2)
```

---
