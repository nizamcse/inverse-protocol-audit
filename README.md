# 🧠 Inverse Finance — Full Audit Playbook (Beginner → Expert)

---

## 🔗 Repositories

- FiRM: https://github.com/InverseFinance/FiRM  
- Anchor: https://github.com/InverseFinance/anchor  

---

# 🚨 VULNERABILITY SEVERITY (IMMUNEFI STANDARD)

## 🔴 Critical

- Direct theft of user funds  
- Direct theft of NFTs  
- Permanent freezing of funds  
- Permanent freezing of NFTs  
- Unauthorized minting of NFTs  
- Manipulable RNG abuse  
- NFT metadata manipulation (tokenURI)  
- Protocol insolvency  
- Theft of unclaimed yield / royalties  
- Permanent freezing of unclaimed yield / royalties  

---

## 🟠 High

- Temporary freezing of funds  
- Temporary freezing of NFTs  

---

## 🟡 Medium

- Contract unusable (no funds)  
- Block stuffing  
- Griefing (no profit attacker)  
- Gas theft  
- Unbounded gas usage  

---

## 🟢 Low

- Failure to deliver promised returns  

---

# 🟢 LEVEL 1 — CORE TOKENS (EASY)

---

## DOLA Token

- **Type:** Core Stablecoin  
- **Contract:** https://etherscan.io/address/0x865377367054516e17014ccded1e7d814edc9ce4  
- **Repo Path:** `anchor/contracts/Dola.sol`

### 🔍 Audit Focus (Line-Level)

- `mint()` → access control  
- `burn()` → supply correctness  
- `transfer()` → edge cases  
- `totalSupply` updates  

### 🧪 How to Test

1. Unauthorized mint  
2. Mint → burn mismatch  
3. Small amount rounding (1 wei)  

---

## xINV

- **Type:** Staking Token  
- **Contract:** https://etherscan.io/address/0x1637e4e9941d55703a7a5e7807d6ada3f7dcd61b  
- **Repo Path:** `anchor/contracts/XINV.sol`

### 🔍 Focus

- `exchangeRateStored()`  
- mint/redeem  

### 🧪 Tests

- Deposit small → withdraw large  
- Check rounding bias  

---

# 🟡 LEVEL 2 — LENDING CORE (IMPORTANT)

---

## CErc20 Markets

- **Type:** Lending  
- **Example:** https://etherscan.io/address/0x63df5e23db45a2066508318f172ba45b9cd37035  
- **Repo Path:**  
  - `anchor/contracts/CErc20.sol`  
  - `anchor/contracts/CToken.sol`

---

### 🔍 Critical Lines

- `getCashPrior()`  
- `doTransferIn()`  
- `mintInternal()`  
- `redeemUnderlyingInternal()`  

---

### 🧪 Exploit Tests

#### 1. Cached Reserve + Direct Transfer

- Transfer tokens directly to contract  
- Call `mint()`  
- Check incorrect reserve usage  

---

#### 2. Fee-on-transfer Tokens

- Deposit X  
- Contract receives X - fee  
- Check if accounting uses X  

---

#### 3. Rounding Loop

- mint small  
- redeem  
- repeat → profit  

---

# 🟠 LEVEL 3 — ORACLE + PRICING

---

## FiRM Oracle

- **Type:** Price Oracle  
- **Contract:** https://etherscan.io/address/0xabe146cf570fd27ddd985895ce9b138a7110cce8  
- **Repo Path:** `FiRM/src/oracle/Oracle.sol`

---

### 🔍 Focus

- `getPrice()`  
- decimals normalization  
- stale price  

---

### 🧪 Exploits

#### Oracle Lag

- manipulate DEX  
- oracle not updated  
- borrow  

---

#### Decimal Mismatch

- 8 vs 18 decimals  
- over-borrow  

---

#### No TWAP

- flashloan → price spike  

---

# 🔴 LEVEL 4 — FED + PSM (CRITICAL)

---

## Fed

- **Contract:** https://etherscan.io/address/0x2b34548b865ad66a2b046cb82e59ee43f75b90fd  
- **Repo Path:** `anchor/contracts/Fed.sol`

---

### 🔍 Focus

- `expansion()`  
- `contraction()`  
- `takeProfit()`  

---

### 🧪 Exploits

#### Accounting Drift

- manipulate cToken exchangeRate  
- inflate supply  

---

#### Profit Theft
