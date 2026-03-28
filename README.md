# 🛡️ Inverse Finance — Scope → Contracts → Addresses → File Lines → Audit Guide

Protocol: Inverse Finance  
Scope: https://immunefi.com/bug-bounty/inversefinance/scope/#top  

Repos:
- https://github.com/InverseFinance/anchor
- https://github.com/InverseFinance/FiRM

---

# 🧭 SYSTEM ORDER (AUDIT FLOW)

Oracle → Comptroller → Market → Fed → Token

---

# 🔴 LEVEL 1 — ORACLE (EXPERT)

---

## Type
Oracle

## Name (Target)
Oracle Core

## Smart Contract Link (Mainnet Example)
https://etherscan.io/address/0x865377367054516e17014ccded1e7d814edc9ce4

## GitHub Path
https://github.com/InverseFinance/anchor/blob/master/contracts/Oracle.sol

## Critical Lines (Exploit Focus)
- getPrice() → main entry
- price = feeds[token]
- no strict staleness enforcement

---

## How to Test

### Flashloan Manipulation
1. Identify asset with low liquidity feed
2. Manipulate pool price
3. Call borrow immediately

✔ If price used instantly → exploit

---

### Stale Price
1. Skip time / simulate delay
2. Call getPrice()

✔ If old data accepted → exploit

---

## Difficulty
🔴 Expert

---

## Type
Oracle

## Name (Target)
CurveFeed

## Smart Contract Link (Example Pool Feed)
https://etherscan.io/address/0x0000000000000000000000000000000000000000

## GitHub Path
https://github.com/InverseFinance/anchor/blob/master/contracts/CurveFeed.sol

## Critical Lines
- getPrice()
- virtual_price()
- no TWAP

---

## How to Test

1. Flashloan swap in Curve pool  
2. Inflate virtual price  
3. Trigger dependent borrow  

✔ If price spikes used → exploit  

---

## Difficulty
🔴 Expert

---

## Type
Oracle

## Name (Target)
YearnVaultFeed

## Smart Contract Link (Example)
https://etherscan.io/address/0x0000000000000000000000000000000000000000

## GitHub Path
https://github.com/InverseFinance/anchor/blob/master/contracts/YearnVaultFeed.sol

## Critical Lines
- pricePerShare()
- direct multiplication without safety bounds

---

## How to Test

1. Manipulate underlying vault asset  
2. Inflate share price  
3. Call borrow  

✔ If inflated value accepted → exploit  

---

## Difficulty
🔴 Expert

---

# 🟠 LEVEL 2 — COMPTROLLER

---

## Type
Risk Engine

## Name (Target)
Comptroller

## Smart Contract Link
https://etherscan.io/address/0x9d3c4b3f9c2eab5c8c2c5b0a5f7c9e7e3a8c3e0b

## GitHub Path
https://github.com/InverseFinance/anchor/blob/master/contracts/Comptroller.sol

## Critical Lines

- borrowAllowed()
- getAccountLiquidity()
- liquidity = collateral * price * factor

---

## How to Test

### Over Borrow
1. Manipulate oracle price upward  
2. Call borrow()  

✔ If borrow > real collateral → exploit  

---

### Rounding Edge
1. Use minimal collateral  
2. Attempt borrow  

✔ If allowed → exploit  

---

## Difficulty
🟠 Advanced

---

## Type
Risk Engine

## Name (Target)
BorrowController

## Smart Contract Link
https://etherscan.io/address/0x0000000000000000000000000000000000000000

## GitHub Path
https://github.com/InverseFinance/anchor/blob/master/contracts/BorrowController.sol

## Critical Lines
- borrowAllowed()
- missing strict invariant checks

---

## How to Test

1. Supply tiny collateral  
2. Borrow repeatedly  

✔ If cumulative bypass → exploit  

---

## Difficulty
🟠 Advanced

---

# 🟡 LEVEL 3 — MARKET

---

## Type
Lending Market

## Name (Target)
CToken

## Smart Contract Link (Example Market)
https://etherscan.io/address/0x5e181bdde2fa8af7265cb3124735e9a13779c021

## GitHub Path
https://github.com/InverseFinance/anchor/blob/master/contracts/CToken.sol

## Critical Lines

- exchangeRateStored()
- (cash + borrows - reserves) / totalSupply
- getCashPrior()

---

## How to Test

### Cached Reserve Exploit
1. Transfer tokens directly to contract  
2. Call mint()  

✔ If exchange rate changes → exploit  

---

### Exchange Rate Attack
1. Deposit small amount  
2. Inject large liquidity  

✔ If value inflates → exploit  

---

## Difficulty
🟡 Intermediate

---

## Type
Lending Market

## Name (Target)
CErc20

## Smart Contract Link
https://etherscan.io/address/0x5e181bdde2fa8af7265cb3124735e9a13779c021

## GitHub Path
https://github.com/InverseFinance/anchor/blob/master/contracts/CErc20.sol

## Critical Lines

- doTransferIn()
- balanceBefore / balanceAfter
- fee-on-transfer handling

---

## How to Test

1. Use fee-on-transfer token  
2. Call mint()  

✔ If accounting mismatch → exploit  

---

## Difficulty
🟡 Intermediate

---

# 🟢 LEVEL 4 — FED

---

## Type
Monetary System

## Name (Target)
Fed

## Smart Contract Link
https://etherscan.io/address/0x2d3b10fc395b109dc32b71d14cdd523e471f14ef

## GitHub Path
https://github.com/InverseFinance/anchor/blob/master/contracts/Fed.sol

## Critical Lines

- takeProfit()
- profit = balanceOfUnderlying - supply

---

## How to Test

### Fake Profit
1. Inflate exchange rate  
2. Call takeProfit()  

✔ If funds withdrawn → exploit  

---

## Difficulty
🟢 Easy-Medium

---

## Type
Monetary System

## Name (Target)
DolaFed

## Smart Contract Link
https://etherscan.io/address/0x0000000000000000000000000000000000000000

## GitHub Path
https://github.com/InverseFinance/anchor/blob/master/contracts/DolaFed.sol

## Critical Lines

- expansion()
- contraction()
- relies on external cToken

---

## How to Test

1. Manipulate cToken  
2. Call expansion()  

✔ If supply inflated → exploit  

---

## Difficulty
🟢 Easy-Medium

---

# 🟢 LEVEL 5 — TOKEN

---

## Type
ERC20

## Name (Target)
DOLA

## Smart Contract Link
https://etherscan.io/address/0x865377367054516e17014ccded1e7d814edc9ce4

## GitHub Path
https://github.com/InverseFinance/FiRM/blob/master/contracts/DOLA.sol

## Critical Lines

- mint()
- burn()
- access control

---

## How to Test

1. Call mint() from attacker  

✔ If succeeds → exploit  

---

## Difficulty
🟢 Beginner

---

## Type
ERC20

## Name (Target)
DBR

## Smart Contract Link
https://etherscan.io/address/0x0000000000000000000000000000000000000000

## GitHub Path
https://github.com/InverseFinance/FiRM/blob/master/contracts/DBR.sol

## Critical Lines

- mint()
- totalSupply tracking

---

## How to Test

1. Compare balances vs supply  

✔ mismatch → exploit  

---

## Difficulty
🟢 Beginner

---

# 🔴 FINAL EXPLOIT CHAIN

Oracle → Comptroller → Market → Fed → Profit Extraction

---

## Validation Condition

profit_after > profit_before

✔ If TRUE → CRITICAL BUG

---

# 🧠 END
