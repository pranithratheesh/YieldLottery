🎟️ NoLossLottery

A no-loss lottery smart contract built on Ethereum that uses Aave for yield generation and Chainlink VRF v2 Plus for provably fair winner selection.

Users deposit ETH, funds are supplied to Aave to earn yield, and only the generated yield is periodically awarded to a randomly selected participant. Depositors can withdraw their principal at any time.

✨ Key Features

💰 No-loss model – Users never lose their deposited ETH

📈 Yield generation via Aave – Deposits earn interest automatically

🎲 Provably fair randomness – Winner selection powered by Chainlink VRF

🔐 Reentrancy-safe – Uses OpenZeppelin’s ReentrancyGuard

⚡ Non-custodial – Users can withdraw their principal anytime

🧪 Designed for testnets (Sepolia / Aave v3)

🏗️ Architecture Overview
User ETH
   │
   ▼
NoLossLottery
   │ depositETH
   ▼
Aave Wrapped Token Gateway (WETH)
   │
   ▼
aWETH (interest-bearing)
   │
   ├─ principal → withdrawable by users
   └─ yield → distributed via lottery


Randomness for winner selection is provided by Chainlink VRF v2 Plus, ensuring fairness and tamper resistance.

📦 Contracts & Dependencies
External Protocols

Aave v3

IWrappedTokenGatewayV3

IAToken

IPoolDataProvider

Chainlink VRF v2 Plus

VRFConsumerBaseV2Plus

VRFV2PlusClient

OpenZeppelin

ReentrancyGuard

🔁 Core Flow
1. Deposit
deposit() payable


User deposits ETH

ETH is supplied to Aave via the Wrapped Token Gateway

User is added to the players list if new

2. Withdraw
withdraw(uint256 amount)


User withdraws part or all of their deposited ETH

Funds are withdrawn from Aave and sent back to the user

Principal is always preserved (no-loss guarantee)

3. Pick a Winner
pickWinner()


Owner-only

Calculates total deposits

Reads Aave liquidity index & scaled balance

Computes generated yield

Requests randomness from Chainlink VRF

Locks the lottery until randomness is fulfilled

4. Fulfill Randomness
fulfillRandomWords()


Selects a random player

Withdraws only the yield from Aave

Sends yield to the winner

Resets lottery state

🧮 Yield Calculation
value_now = scaledBalance × liquidityIndex / 1e27
yield     = value_now − totalDeposits


This ensures:

Principal is untouched

Only earned interest is distributed

🔐 Access Control
Function	Access
deposit	Anyone
withdraw	Anyone
pickWinner	Owner only
emergencyWithdraw	Owner only
🚨 Emergency Withdraw
emergencyWithdraw()


Allows the contract owner to withdraw all funds from Aave in case of emergencies (paused markets, protocol issues, etc.).

📡 Events

Deposited(user, amount)

Withdrawn(user, amount)

RandomnessRequested(requestId)

LotteryWinner(winner, prize)

GetValueNowAndTotalDeposits(...)

These events make the contract easy to index and monitor via subgraphs or off-chain services.

⚠️ Important Notes & Caveats

❗ Players array is not pruned when users fully withdraw
(acceptable for MVP, but should be optimized for production)

❗ Assumes Aave market liquidity is healthy

❗ Designed primarily for testnet / experimental use

❗ Owner has emergency powers — governance/multisig recommended for production

🧪 Recommended Improvements

Use weighted randomness based on deposit size

Remove inactive players efficiently

Add deposit caps or cooldowns

Automate winner selection via Chainlink Automation

Add pausability & upgradeability

📜 License

MIT License
SPDX-License-Identifier: MIT

🤝 Acknowledgements

Aave Protocol

Chainlink VRF

OpenZeppelin
