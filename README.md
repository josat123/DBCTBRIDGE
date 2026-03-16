# 🌉 Universal Bridge – Cross-Chain Token Bridge

**A secure, EIP-712 based bridge for native cross-chain token transfers between Ethereum L1 and Polygon L2.**

## 🏷️ **Bridge Badges**

| Category | Badges |
|----------|--------|
| **Security & Audit** | [![Audit: Self](https://img.shields.io/badge/Audit-Self--audited-success)](https://github.com/josat123/universal-bridge/security) [![Security: ReentrancyGuard](https://img.shields.io/badge/Security-ReentrancyGuard-blue)](https://github.com/josat123/universal-bridge) [![Security: Ownable2Step](https://img.shields.io/badge/Security-Ownable2Step-blue)](https://github.com/josat123/universal-bridge) [![Security: EIP-712](https://img.shields.io/badge/Security-EIP--712-blue)](https://eips.ethereum.org/EIPS/eip-712) |
| **Verification** | [![Contract Verified](https://img.shields.io/badge/Contract-Verified-success)](https://polygonscan.com/address/0x0Ef6a63a16fB21dD8398183a154596953Ce4E835#code) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) |
| **Chains** | [![Ethereum](https://img.shields.io/badge/Ethereum-3C3C3D?style=flat&logo=ethereum&logoColor=white)](https://ethereum.org) [![Polygon](https://img.shields.io/badge/Polygon-8247E5?style=flat&logo=polygon&logoColor=white)](https://polygon.technology) |
| **Tech Stack** | [![Solidity](https://img.shields.io/badge/Solidity-0.8.20-blue)](https://soliditylang.org/) [![Foundry](https://img.shields.io/badge/Built%20with-Foundry-FFDB1C)](https://book.getfoundry.sh/) [![OpenZeppelin](https://img.shields.io/badge/OpenZeppelin-5.0.0-blue)](https://openzeppelin.com/) |
| **Features** | [![Cross-Chain](https://img.shields.io/badge/Cross--Chain-Ethereum%20↔%20Polygon-purple)](https://github.com/josat123/universal-bridge) [![Tax Aware](https://img.shields.io/badge/Tax%20Aware-Yes-success)](https://github.com/josat123/universal-bridge) [![Fee: 0.05%](https://img.shields.io/badge/Bridge%20Fee-0.05%25-blue)](https://github.com/josat123/universal-bridge) |
| **Status** | [![Tests: Passing](https://img.shields.io/badge/Tests-Passing-brightgreen)](https://github.com/josat123/universal-bridge/actions) [![Deployed](https://img.shields.io/badge/Deployed-L1%20%26%20L2-success)](https://github.com/josat123/universal-bridge) [![GitHub stars](https://img.shields.io/github/stars/josat123/universal-bridge?style=social)](https://github.com/josat123/universal-bridge/stargazers) |

This bridge allows the **same token** to exist natively on multiple chains – not as wrapped versions, but as the original token, with mint/burn mechanics on L2 and lock/release on L1.

---

## ✨ Key Features

- ✅ **Same token on both chains** – No wrapped tokens, no confusion
- ✅ **EIP-712 signatures** – Secure message verification
- ✅ **Replay protection** – Unique nonces prevent double-spending
- ✅ **Tax-aware** – Handles tokens with transfer taxes (like DBTC's 1%)
- ✅ **Configurable fees** – Bridge fees up to 0.3%, currently 0.05%
- ✅ **Ownable2Step** – Transparent ownership management
- ✅ **ReentrancyGuard** – Protection against reentrancy attacks
- ✅ **Already deployed and tested** – Live on Ethereum & Polygon

---

## 🚀 Deployed Contracts

| Chain | Bridge Contract | Messenger Address | BRVBTC Token |
|-------|-----------------|-------------------|--------------|
| **Ethereum L1** | `0xe8681d55585FcDA6a4a39c9a59f39b63fbBa88e8` | `0x25ace71c97b33cC4724cf772e9b8B8F980f9d3B5` | `0x9bc0F4d4B31AdEa0c7Fde6f40a778E4Ce7Bc652d` |
| **Polygon L2** | `0x0Ef6a63a16fB21dD8398183a154596953Ce4E835` | `0x25ace71c97b33cC4724cf772e9b8B8F980f9d3B5` | `0xa5c96d77C280B9F4bA13cd4064C4864Cf69a3BCB` | `0x5Ea83b4c928Aa4055D02592f3AFE0fE87318598D`  |

**Messenger is the same address on both chains**, ensuring consistent cross-chain communication.

---

## 📊 How It Works

### L1 → L2 (Deposit)

User calls depositToChain(token, recipient, amount, dstChainId, gasLimit)

Bridge transfers tokens from user to itself (gross amount including tax)

Bridge sends message via official messenger to L2 bridge

L2 bridge mints tokens to recipient

text

### L2 → L1 (Withdrawal)
User calls withdrawToL1(token, recipient, amount, gasLimit)

Bridge burns tokens from user

Bridge sends message via messenger to L1 bridge

L1 bridge releases tokens to recipient

text

### Tax Handling

For taxed tokens like DBTC (1%):

```solidity
grossAmount = amount * (BPS_DENOMINATOR + taxBps) / BPS_DENOMINATOR;
// User pays tax, recipient receives full amount
🔐 Security Features
Feature	Implementation
Message Signing	EIP-712 typed data signatures
Nonce System	keccak256(token, recipient, amount, nonce, chainId) prevents replay
onlyMessenger	Only official bridge messenger can finalize
onlyRemoteBridge	Verifies sender is registered bridge on other chain
ReentrancyGuard	OpenZeppelin's battle-tested protection
Ownable2Step	2-step ownership transfer prevents mistakes

📦 Installation
Prerequisites
Foundry

Git

Clone & Install
bash
git clone https://github.com/josat123/universal-bridge.git
cd universal-bridge
forge install
Compile
bash
forge build
Test
bash
forge test -vvv
🛠️ Usage
Register a Token
solidity
// For BRVBTC (no tax)
bridge.registerToken(
    address(localToken),
    address(remoteToken),
    false,  // taxed = false
    0       // taxBps = 0
);

// For DBTC (with 1% tax)
bridge.registerToken(
    address(dbtcL1),
    address(dbtcL2),
    true,   // taxed = true
    100     // 1% = 100 bps
);
Set Remote Bridge
solidity
// On L1 (Ethereum)
bridge.setRemoteBridge(137, 0x0Ef6a63a16fB21dD8398183a154596953Ce4E835);

// On L2 (Polygon)
bridge.setRemoteBridge(1, 0xe8681d55585FcDA6a4a39c9a59f39b63fbBa88e8);
Configure Fees (Optional)
solidity
bridge.setFeeCollector(0xYourAddress);
bridge.setBridgeFee(5); // 0.05% (max 30 = 0.3%)
Deposit to L2
solidity
bridge.depositToChain(
    tokenAddress,
    recipientAddress,
    amount,
    137,      // Polygon chainId
    500000    // gas limit
);
Withdraw to L1
solidity
bridge.withdrawToL1(
    tokenAddress,
    recipientAddress,
    amount,
    500000    // gas limit
);
📋 Contract Addresses Summary
Contract	Ethereum L1	Polygon L2
UniversalBridge	0xe8681d55585FcDA6a4a39c9a59f39b63fbBa88e8	0x0Ef6a63a16fB21dD8398183a154596953Ce4E835
Messenger	0x25ace71c97b33cC4724cf772e9b8B8F980f9d3B5	0x25ace71c97b33cC4724cf772e9b8B8F980f9d3B5
BRVBTC	0x9bc0F4d4B31AdEa0c7Fde6f40a778E4Ce7Bc652d	0xa5c96d77C280B9F4bA13cd4064C4864Cf69a3BCB
DBTC	0xe972BbB8bB357418131951CcEaec9d8A5993bea2

📊 Events
Event	Description
TokenRegistered	New token added to bridge
DepositInitiated	L1 → L2 transfer started
DepositFinalized	L2 mint completed
WithdrawalInitiated	L2 → L1 transfer started
WithdrawalFinalized	L1 release completed
FeeCollected	Bridge fee collected
RemoteBridgeSet	Bridge on other chain configured

🧪 Testing
Run the full test suite:

bash
forge test -f mainnet -vvv
Tests include:

✅ Token registration

✅ L1 → L2 deposits

✅ L2 → L1 withdrawals

✅ Tax handling

✅ Fee collection

✅ Security checks (replay protection, onlyMessenger)

🚀 Deployment with CREATE2
To deploy with the same address on both chains:

bash
# Calculate address first
forge script script/Deploy.s.sol --sig "run()" --rpc-url ethereum

# Deploy on L1
forge script script/Deploy.s.sol --rpc-url ethereum --broadcast --verify

# Deploy on L2 (same salt!)
forge script script/Deploy.s.sol --rpc-url polygon --broadcast --verify
See script/Deploy.s.sol for details.

🔒 Security
Audit Status: Self-audited, battle-tested in production with DBTC and BRVBTC

Formal Verification: Not yet, but all invariants tested

Bug Bounty: Coming soon

If you find a vulnerability, please open an issue or contact the team.

📄 License
MIT © 2026 BRVBTC Team

🙏 Acknowledgements
OpenZeppelin for secure contract libraries

Foundry for amazing development tools

The Ethereum and Polygon communities

📬 Contact
GitHub Issues: https://github.com/josat123/universal-bridge/issues

Twitter: @BRVBTC (coming soon)

— The BRVBTC Team
March 2026
