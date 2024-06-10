# Size audit details

- Total Prize Pool: $200,000 in USDC
  - HM awards: $168,000 in USDC
  - QA awards: $6,500 in USDC
  - Judge awards: $15,000 in USDC
  - Validator awards: $10,000 in USDC
  - Scout awards: $500 in USDC
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-06-size/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts June 10, 2024 20:00 UTC
- Ends July 2, 2024 20:00 UTC

## Publicly Known Issues

_Note for C4 wardens: Anything included in this `Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._

- The protocol currently supports only a single market (USDC/ETH for borrow/collateral tokens)
- The protocol does not support rebasing/fee-on-transfer tokens
- The protocol does not support tokens with different decimals than the current market
- The protocol only supports tokens compliant with the IERC20Metadata interface
- The protocol only supports pre-vetted tokens
- The protocol owner, KEEPER_ROLE, PAUSER_ROLE, and BORROW_RATE_UPDATER_ROLE are trusted
- The protocol does not have any fallback oracles.
- Price feeds must be redeployed and updated in case any Chainlink configuration changes (stale price timeouts, decimals, etc)
- In case Chainlink reports a wrong price, the protocol state cannot be guaranteed. This may cause incorrect liquidations, among other issues
- In case the protocol is paused, the price of the collateral may change during the unpause event. This may cause unforeseen liquidations, among other issues
- It is not possible to pause individual functions. Nevertheless, BORROW_RATE_UPDATER_ROLE and admin functions are enabled even if the protocol is paused
- Users blacklisted by underlying tokens (e.g. USDC) may be unable to withdraw
- If the Variable Pool (Aave v3) fails to `supply` or `withdraw` for any reason, such as supply caps, Size's `deposit` and `withdraw` may be prevented
- Centralization risk related to integrations (USDC, Aave v3, Chainlink) are out of scope
- The Variable Pool Borrow Rate feed is trusted and users of rate hook adopt oracle risk of buying/selling credit at unsatisfactory prices
- The insurance fund (out of scope for this project) may not be able to make all lenders whole, may be unfair, and may be manipulated
- LiquidateWithReplacement might not be available for big enough debt positions
- All issues acknowledged on previous audits and automated findings

# Overview

Size is a credit marketplace with unified liquidity across maturities.

Supported pair:

- (W)ETH/USDC: Collateral/Borrow token

Target networks:

- Ethereum mainnet
- Base

## Documentation

### Overview, Accounting, and Protocol Design

- [Whitepaper](https://docs.size.cash/)

### Technical overview

#### Architecture

The architecture of Size v2 was inspired by [dYdX v2](https://github.com/dydxprotocol/solo), with the following design goals:

- Upgradeability
- Modularity
- Overcome [EIP-170](https://eips.ethereum.org/EIPS/eip-170)'s contract code size limit of 24kb
- Maintaining the protocol invariants after each user interaction (["FREI-PI" pattern](https://www.nascent.xyz/idea/youre-writing-require-statements-wrong))

For that purpose, the contract is deployed behind an UUPS-Upgradeable proxy, and contains a single entrypoint, `Size.sol`. External libraries are used, and a single `State storage` variable is passed to them via `delegatecall`s. All user-facing functions have the same pattern:

```solidity
state.validateFunction(params);
state.executeFunction(params);
state.validateInvariant(params);
```

The `Multicall` pattern is also available to allow users to perform a sequence of multiple actions, such as depositing borrow tokens, liquidating an underwater borrower, and withdrawing all liquidated collateral. **Note:** in order to accept ether deposits through multicalls, all user-facing functions have the [`payable`](https://github.com/sherlock-audit/2023-06-tokemak-judging/issues/215) modifier, and `deposit` always uses `address(this).balance` to wrap ether. This means leftover amounts, if [sent forcibly](https://consensys.github.io/smart-contract-best-practices/development-recommendations/general/force-feeding/), are always credited to the depositor.

Additional safety features were employed, such as different levels of Access Control (ADMIN, PAUSER_ROLE, KEEPER_ROLE, BORROW_RATE_UPDATER_ROLE), and Pause.

#### Tokens

In order to address donation and reentrancy attacks, the following measures were adopted:

- No withdraws of native ether, only wrapped ether (WETH)
- Underlying borrow and collateral tokens, such as USDC and WETH, are converted 1:1 into deposit tokens via `deposit`, which mints `szaUSDC` and `szWETH`, and received back via `withdraw`, which burns deposit tokens 1:1 in exchange for the underlying tokens.

#### Maths

All mathematical operations are implemented with explicit rounding (`mulDivUp` or `mulDivDown`) using Solady's [FixedPointMathLib](https://github.com/Vectorized/solady/blob/main/src/utils/FixedPointMathLib.sol). Whenever a taker-maker operation occurs, all rounding tries to favor the maker, who is the passive party. In some generic situations, such as in yield curve calculations, the rounding is always in one direction.

Decimal amounts are preserved until a conversion is necessary:

- USDC/szaUSDC: 6 decimals
- WETH/szETH: 18 decimals
- szDebt: same as borrow token
- Price feeds: 18 decimals

All percentages are expressed in 18 decimals. For example, a 150% liquidation collateral ratio is represented as 1500000000000000000.

#### Oracles

##### Price Feed

A contract that provides the price of ETH in terms of USDC in 18 decimals. For example, a price of 3327.39 ETH/USDC is represented as 3327390000000000000000.

##### Variable Pool Borrow Rate Feed

In order to set the current market average value of USDC variable borrow rates, we perform an off-chain calculation on Aave's rate, convert it to 18 decimals, and store it in the Size contract. For example, a rate of 2.49% on Aave v3 is represented as 24900000000000000. The admin can disable this feature by setting the stale interval to zero. If the oracle information is stale, orders relying on the variable rate feed cannot be matched.

## Links

- **Previous audits:**
  - <https://github.com/code-423n4/2024-06-size/blob/main/audits/2024-03-19-LightChaserV3.md>
  - <https://github.com/code-423n4/2024-06-size/blob/main/audits/2024-03-26-Solidified.pdf>
  - <https://github.com/code-423n4/2024-06-size/blob/main/audits/2024-05-30-Spearbit-draft.pdf>
- **Documentation:** <https://docs.size.cash/>
- **Website:** <https://size.credit/>
- **X/Twitter:** <https://x.com/SizeCredit>

# Scope

_See [scope.txt](https://github.com/code-423n4/2024-06-size/blob/main/scope.txt)_

### Files in scope

|                                                                              File                                                                              | Logic Contracts | Interfaces | SLOC |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         Libraries used                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------:|:----------:|:----:|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
| [/src/Size.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/Size.sol)                                                                             | 1               | ****       | 202  | @openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol @openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol @openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol @openzeppelin/contracts-upgradeable/utils/PausableUpgradeable.sol @src/libraries/LoanLibrary.sol @src/libraries/actions/Initialize.sol @src/libraries/actions/UpdateConfig.sol @src/libraries/actions/SellCreditLimit.sol @src/libraries/actions/SellCreditMarket.sol @src/libraries/actions/Claim.sol @src/libraries/actions/Deposit.sol @src/libraries/actions/BuyCreditMarket.sol @src/libraries/actions/SetUserConfiguration.sol @src/libraries/actions/BuyCreditLimit.sol @src/libraries/actions/Liquidate.sol @src/libraries/Multicall.sol @src/libraries/actions/Compensate.sol @src/libraries/actions/LiquidateWithReplacement.sol @src/libraries/actions/Repay.sol @src/libraries/actions/SelfLiquidate.sol @src/libraries/actions/Withdraw.sol @src/SizeStorage.sol @src/libraries/CapsLibrary.sol @src/libraries/RiskLibrary.sol @src/SizeView.sol @src/libraries/Events.sol @src/interfaces/IMulticall.sol @src/interfaces/ISize.sol @src/interfaces/ISizeAdmin.sol |
| [/src/SizeStorage.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/SizeStorage.sol)                                                               | 1               | ****       | 61   | @aave/interfaces/IPool.sol @openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol @src/interfaces/IWETH.sol @src/libraries/LoanLibrary.sol @src/libraries/OfferLibrary.sol @src/oracle/IPriceFeed.sol @src/token/NonTransferrableScaledToken.sol @src/token/NonTransferrableToken.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| [/src/SizeView.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/SizeView.sol)                                                                     | 1               | ****       | 136  | @src/SizeStorage.sol @src/libraries/YieldCurveLibrary.sol @src/libraries/LoanLibrary.sol @src/libraries/actions/UpdateConfig.sol @src/libraries/AccountingLibrary.sol @src/libraries/RiskLibrary.sol @src/SizeViewData.sol @src/interfaces/ISizeView.sol @src/libraries/Errors.sol @src/libraries/OfferLibrary.sol @src/libraries/actions/Initialize.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| [/src/SizeViewData.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/SizeViewData.sol)                                                             | ****            | ****       | 23   | @aave/interfaces/IPool.sol @openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol @src/SizeStorage.sol @src/token/NonTransferrableScaledToken.sol @src/token/NonTransferrableToken.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| [/src/libraries/AccountingLibrary.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/AccountingLibrary.sol)                               | 1               | ****       | 187  | @src/SizeStorage.sol @src/libraries/Errors.sol @src/libraries/Events.sol @src/libraries/Math.sol @src/libraries/LoanLibrary.sol @src/libraries/RiskLibrary.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| [/src/libraries/CapsLibrary.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/CapsLibrary.sol)                                           | 1               | ****       | 38   | @src/SizeStorage.sol @src/libraries/Errors.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| [/src/libraries/DepositTokenLibrary.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/DepositTokenLibrary.sol)                           | 1               | ****       | 42   | @aave/interfaces/IAToken.sol @openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol @openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol @src/SizeStorage.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| [/src/libraries/Errors.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/Errors.sol)                                                     | 1               | ****       | 67   | @src/libraries/LoanLibrary.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| [/src/libraries/Events.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/Events.sol)                                                     | 1               | ****       | 79   | @src/libraries/LoanLibrary.sol @src/libraries/actions/Initialize.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| [/src/libraries/LoanLibrary.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/LoanLibrary.sol)                                           | 1               | ****       | 103  | @src/SizeStorage.sol @src/libraries/AccountingLibrary.sol @src/libraries/Errors.sol @src/libraries/Math.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| [/src/libraries/Math.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/Math.sol)                                                         | 1               | ****       | 42   | @solady/utils/FixedPointMathLib.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| [/src/libraries/Multicall.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/Multicall.sol)                                               | 1               | ****       | 24   | @openzeppelin/contracts/utils/Address.sol @src/SizeStorage.sol @src/libraries/CapsLibrary.sol @src/libraries/RiskLibrary.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| [/src/libraries/OfferLibrary.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/OfferLibrary.sol)                                         | 1               | ****       | 52   | @src/libraries/Errors.sol @src/libraries/Math.sol @src/libraries/YieldCurveLibrary.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| [/src/libraries/RiskLibrary.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/RiskLibrary.sol)                                           | 1               | ****       | 81   | @src/SizeStorage.sol @src/libraries/Errors.sol @src/libraries/LoanLibrary.sol @src/libraries/Math.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| [/src/libraries/YieldCurveLibrary.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/YieldCurveLibrary.sol)                               | 1               | ****       | 88   | @openzeppelin/contracts/utils/math/SafeCast.sol @src/libraries/Errors.sol @src/libraries/Math.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| [/src/libraries/actions/BuyCreditLimit.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/BuyCreditLimit.sol)                     | 1               | ****       | 38   | @src/libraries/OfferLibrary.sol @src/libraries/YieldCurveLibrary.sol @src/SizeStorage.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| [/src/libraries/actions/BuyCreditMarket.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/BuyCreditMarket.sol)                   | 1               | ****       | 143  | @src/SizeStorage.sol @src/libraries/AccountingLibrary.sol @src/libraries/Errors.sol @src/libraries/Events.sol @src/libraries/LoanLibrary.sol @src/libraries/Math.sol @src/libraries/OfferLibrary.sol @src/libraries/RiskLibrary.sol @src/libraries/YieldCurveLibrary.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| [/src/libraries/actions/Claim.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Claim.sol)                                       | 1               | ****       | 34   | @src/libraries/LoanLibrary.sol @src/libraries/Math.sol @src/SizeStorage.sol @src/libraries/AccountingLibrary.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| [/src/libraries/actions/Compensate.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Compensate.sol)                             | 1               | ****       | 111  | @src/SizeStorage.sol @src/libraries/Math.sol @src/libraries/AccountingLibrary.sol @src/libraries/Errors.sol @src/libraries/Events.sol @src/libraries/LoanLibrary.sol @src/libraries/RiskLibrary.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| [/src/libraries/actions/Deposit.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Deposit.sol)                                   | 1               | ****       | 56   | @openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol @openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol @src/interfaces/IWETH.sol @src/libraries/CapsLibrary.sol @src/SizeStorage.sol @src/libraries/DepositTokenLibrary.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| [/src/libraries/actions/Initialize.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Initialize.sol)                             | 1               | ****       | 175  | @aave/interfaces/IPool.sol @openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol @src/interfaces/IWETH.sol @src/libraries/Math.sol @src/libraries/LoanLibrary.sol @src/oracle/IPriceFeed.sol @src/token/NonTransferrableScaledToken.sol @src/token/NonTransferrableToken.sol @src/SizeStorage.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| [/src/libraries/actions/Liquidate.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Liquidate.sol)                               | 1               | ****       | 76   | @src/libraries/Math.sol @src/libraries/LoanLibrary.sol @src/libraries/AccountingLibrary.sol @src/libraries/RiskLibrary.sol @src/SizeStorage.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| [/src/libraries/actions/LiquidateWithReplacement.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/LiquidateWithReplacement.sol) | 1               | ****       | 113  | @src/libraries/Math.sol @src/libraries/LoanLibrary.sol @src/libraries/OfferLibrary.sol @src/libraries/YieldCurveLibrary.sol @src/SizeStorage.sol @src/libraries/actions/Liquidate.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| [/src/libraries/actions/Repay.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Repay.sol)                                       | 1               | ****       | 28   | @src/SizeStorage.sol @src/libraries/AccountingLibrary.sol @src/libraries/RiskLibrary.sol @src/libraries/LoanLibrary.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| [/src/libraries/actions/SelfLiquidate.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/SelfLiquidate.sol)                       | 1               | ****       | 43   | @src/libraries/AccountingLibrary.sol @src/libraries/LoanLibrary.sol @src/libraries/Math.sol @src/libraries/RiskLibrary.sol @src/SizeStorage.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| [/src/libraries/actions/SellCreditLimit.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/SellCreditLimit.sol)                   | 1               | ****       | 27   | @src/SizeStorage.sol @src/libraries/OfferLibrary.sol @src/libraries/YieldCurveLibrary.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| [/src/libraries/actions/SellCreditMarket.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/SellCreditMarket.sol)                 | 1               | ****       | 143  | @src/libraries/LoanLibrary.sol @src/libraries/Math.sol @src/libraries/OfferLibrary.sol @src/libraries/YieldCurveLibrary.sol @src/SizeStorage.sol @src/libraries/AccountingLibrary.sol @src/libraries/RiskLibrary.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| [/src/libraries/actions/SetUserConfiguration.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/SetUserConfiguration.sol)         | 1               | ****       | 46   | @src/SizeStorage.sol @src/libraries/LoanLibrary.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| [/src/libraries/actions/UpdateConfig.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/UpdateConfig.sol)                         | 1               | ****       | 110  | @openzeppelin/contracts/utils/Strings.sol @src/SizeStorage.sol @src/libraries/Errors.sol @src/libraries/Events.sol @src/libraries/Math.sol @src/libraries/actions/Initialize.sol @src/oracle/IPriceFeed.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| [/src/libraries/actions/Withdraw.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/libraries/actions/Withdraw.sol)                                 | 1               | ****       | 43   | @src/SizeStorage.sol @src/libraries/DepositTokenLibrary.sol @src/libraries/Math.sol @src/libraries/Errors.sol @src/libraries/Events.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| [/src/oracle/IPriceFeed.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/oracle/IPriceFeed.sol)                                                   | ****            | 1          | 5    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| [/src/oracle/PriceFeed.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/oracle/PriceFeed.sol)                                                     | 1               | ****       | 59   | @chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol @openzeppelin/contracts/utils/math/SafeCast.sol @src/libraries/Math.sol @src/libraries/Errors.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| [/src/token/NonTransferrableScaledToken.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/token/NonTransferrableScaledToken.sol)                   | 1               | ****       | 65   | @aave/interfaces/IPool.sol @aave/protocol/libraries/math/WadRayMath.sol @openzeppelin/contracts/interfaces/IERC20Metadata.sol @src/libraries/Math.sol @src/token/NonTransferrableToken.sol @src/libraries/Errors.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| [/src/token/NonTransferrableToken.sol](https://github.com/code-423n4/2024-06-size/blob/main/src/token/NonTransferrableToken.sol)                               | 1               | ****       | 38   | @openzeppelin/contracts/access/Ownable.sol @openzeppelin/contracts/token/ERC20/ERC20.sol @src/libraries/Errors.sol                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| **Totals** | **32** | **1** | **2578** | | |

### Files out of scope

_See [out_of_scope.txt](https://github.com/code-423n4/2024-06-size/blob/main/out_of_scope.txt)_

| File         |
| ------------ |
| ./script/Addresses.sol |
| ./script/BaseScript.sol |
| ./script/BuyCreditLimit.s.sol |
| ./script/BuyCreditMarket.s.sol |
| ./script/Claim.s.sol |
| ./script/Compensate.s.sol |
| ./script/Deploy.s.sol |
| ./script/Deploy.sol |
| ./script/DepositUSDC.s.sol |
| ./script/DepositWETH.s.sol |
| ./script/GetUserView.s.sol |
| ./script/GrantRole.s.sol |
| ./script/Liquidate.s.sol |
| ./script/LiquidateWithReplacement.s.sol |
| ./script/Ownable.s.sol |
| ./script/Repay.s.sol |
| ./script/SellCreditLimit.s.sol |
| ./script/UpdateConfig.s.sol |
| ./src/interfaces/IMulticall.sol |
| ./src/interfaces/ISize.sol |
| ./src/interfaces/ISizeAdmin.sol |
| ./src/interfaces/ISizeView.sol |
| ./src/interfaces/IWETH.sol |
| ./test/BaseTest.sol |
| ./test/BaseTestVariablePool.sol |
| ./test/Logger.sol |
| ./test/fork/Deploy.t.sol |
| ./test/fork/ForkTest.sol |
| ./test/helpers/AssertsHelper.sol |
| ./test/helpers/libraries/YieldCurveHelper.sol |
| ./test/invariants/Bounds.sol |
| ./test/invariants/CryticTester.sol |
| ./test/invariants/CryticToFoundry.t.sol |
| ./test/invariants/ExpectedErrors.sol |
| ./test/invariants/FoundryTester.sol |
| ./test/invariants/Ghosts.sol |
| ./test/invariants/Helper.sol |
| ./test/invariants/Properties.sol |
| ./test/invariants/PropertiesSpecifications.sol |
| ./test/invariants/TargetFunctions.sol |
| ./test/invariants/interfaces/ITargetFunctions.sol |
| ./test/libraries/Math.t.sol |
| ./test/libraries/OfferLibrary.t.sol |
| ./test/libraries/WadRayMath.t.sol |
| ./test/libraries/YieldCurveLibrary.t.sol |
| ./test/local/actions/BuyCreditLimit.t.sol |
| ./test/local/actions/BuyCreditLimitValidation.t.sol |
| ./test/local/actions/BuyCreditMarket.t.sol |
| ./test/local/actions/BuyCreditMarketValidation.t.sol |
| ./test/local/actions/Claim.t.sol |
| ./test/local/actions/ClaimValidation.t.sol |
| ./test/local/actions/Compensate.t.sol |
| ./test/local/actions/CompensateValidation.t.sol |
| ./test/local/actions/Deposit.t.sol |
| ./test/local/actions/DepositValidation.t.sol |
| ./test/local/actions/Initialize.t.sol |
| ./test/local/actions/InitializeValidation.t.sol |
| ./test/local/actions/Liquidate.t.sol |
| ./test/local/actions/LiquidateValidation.t.sol |
| ./test/local/actions/LiquidateWithReplacement.t.sol |
| ./test/local/actions/LiquidateWithReplacementValidation.t.sol |
| ./test/local/actions/Multicall.t.sol |
| ./test/local/actions/Pause.t.sol |
| ./test/local/actions/Repay.t.sol |
| ./test/local/actions/RepayValidation.t.sol |
| ./test/local/actions/SelfLiquidate.t.sol |
| ./test/local/actions/SelfLiquidateValidation.t.sol |
| ./test/local/actions/SellCreditLimit.t.sol |
| ./test/local/actions/SellCreditLimitValidation.t.sol |
| ./test/local/actions/SellCreditMarket.t.sol |
| ./test/local/actions/SellCreditMarketValidation.t.sol |
| ./test/local/actions/SetUserConfiguration.t.sol |
| ./test/local/actions/SetUserConfigurationValidation.t.sol |
| ./test/local/actions/SizeView.t.sol |
| ./test/local/actions/UpdateConfig.t.sol |
| ./test/local/actions/UpdateConfigValidation.t.sol |
| ./test/local/actions/Upgrade.t.sol |
| ./test/local/actions/Withdraw.t.sol |
| ./test/local/actions/WithdrawValidation.t.sol |
| ./test/local/oracle/PriceFeed.t.sol |
| ./test/local/token/NonTransferrableScaledToken.t.sol |
| ./test/local/token/NonTransferrableToken.t.sol |
| ./test/mocks/DAI.sol |
| ./test/mocks/MockAavePool.sol |
| ./test/mocks/PoolMock.sol |
| ./test/mocks/PriceFeedMock.sol |
| ./test/mocks/SizeMock.sol |
| ./test/mocks/USDC.sol |
| ./test/mocks/WETH.sol |
| ./test/mocks/YAMv2.sol |
| Totals: 90 |

## Scoping Q &amp; A

### General questions

| Question                                | Answer                       |
| --------------------------------------- | ---------------------------- |
| ERC20 used by the protocol              |       USDC, WETH             |
| Test coverage                           | 100%                      |
| ERC721 used  by the protocol            |           None              |
| ERC777 used by the protocol             |           None               |
| ERC1155 used by the protocol            |              None            |
| Chains the protocol will be deployed on | Ethereum, Base |

### External integrations (e.g., Uniswap) behavior in scope

| Question                                                  | Answer |
| --------------------------------------------------------- | ------ |
| Enabling/disabling fees (e.g. Blur disables/enables fees) | Yes   |
| Pausability (e.g. Uniswap pool gets paused)               |  Yes   |
| Upgradeability (e.g. Uniswap gets upgraded)               |   Yes  |

### EIP compliance checklist

N/A

# Additional context

## Main invariants

- DEPOSIT_01: Deposit credits the sender
- DEPOSIT_02: Deposit transfers tokens to the protocol

- WITHDRAW_01: Withdraw deducts from the sender
- WITHDRAW_02: Withdraw removes tokens from the protocol

- BORROW_01: Borrow increases the borrower's cash
- BORROW_02: Borrow increases the number of loans

- CLAIM_01: Claim does not decrease the sender's cash
- CLAIM_02: Claim is only valid for CreditPositions

- LIQUIDATE_01: Liquidate increases the sender collateral

- LIQUIDATE_02: Liquidate decreases the sender's cash if the loan is not overdue
- LIQUIDATE_03: Liquidate only succeeds if the borrower is liquidatable
- LIQUIDATE_04: Liquidate decreases the borrower's debt
- LIQUIDATE_05: Liquidate clears the loan's debt

- SELF_LIQUIDATE_01: Self-Liquidate increases the sender collateral
- SELF_LIQUIDATE_02: Self-Liquidate decreases the borrower's debt

- SELF_LIQUIDATE_03: Self-Liquidate does not change the borrower's CR (up to a precision)

- REPAY_01: Repay transfers cash from the sender to the protocol
- REPAY_02: Repay decreases the borrower's debt
- REPAY_02: Repay clears the loan's debt

- LOAN_01: loan.credit >= minimumCreditBorrowAToken
- LOAN_02: minTenor <= loan.tenor <= maxTenor
- LOAN_03: COUNT(credit positions) >= COUNT(debt positions)
- LOAN_04: loan.liquidityIndexAtRepayment > 0 => loan.loanStatus == REPAID
- LOAN_05: A CreditPosition's debtPositionId is never updated

- TOKENS_01: The sum of collateral deposit tokens is equal to the underlying collateral
- TOKENS_02: The sum of borrow deposit tokens is equal to the sum of borrow deposit tokens for each user

- UNDERWATER_01: A user cannot make an operation that leaves any user underwater
- UNDERWATER_02: Underwater users cannot borrow

- COMPENSATE_01: Compensate does not change the borrower's debt if minting new credit

- COMPENSATE_02: Compensate reduces the borrower's debt if using an existing credit

- SOLVENCY_01: SUM(outstanding credit) == SUM(outstanding debt)
- SOLVENCY_02: SUM(credit) <= SUM(debt)
- SOLVENCY_03: SUM(positions debt) == user total debt, for each user
- SOLVENCY_04: SUM(positions debt) == SUM(debt)

- FEES_01: Fragmentation fees are applied whenever there is a credit fractionalization
- FEES_02: Cash swap operations increase the fee recipient balance

- DOS: Functions should not revert if preconditions are met (Denial of Service)

- REVERTS: Actions behave as expected under dependency reverts

## Attack ideas (where to focus for bugs)

After our last security audit, we implemented numerous fixes and added new features to the protocol. However, these changes were not reviewed by the auditors. We believe these are the most significant untested and potentially vulnerable areas of the protocol.

1. Swap fee: During Spearbit's security review, a critical-severity vulnerability (5.1.1) was discovered that allowed any borrower to avoid paying fees. Consequently, we redesigned our fee mechanism, transitioning from a repayment fee to a swap fee. Rather than charging fees upon loan repayment, we introduced a fee for every cash-for-credit operation.

2. Fragmentation fee: While refactoring our fee structure, we noticed that our previous "early exit fee" was being charged incorrectly in all exits, since the purpose of this fee was to enable us to subsidize `claim`  operations after loan repayments. Because of that, we renamed it to `fragmentationFee` so that it better reflects the intended behavior of this fee, which is to be charged only when there is a credit fractionalization/split.

3. Liquidations: In Spearbit's security review, several High and Medium-severity vulnerabilities (5.2.1, 5.3.1, 5.3.3) were identified concerning liquidation and self-liquidation incentives. Consequently, we overhauled our incentives mechanism, moving away from a fixed overdue liquidation reward and a variable liquidation reward based on collateral ratio to a variable liquidation reward based on the loan's future value.

4. Create Debt/Credit pair: Following our last audit, we conducted a major refactor to consolidate similar functions into a single function. Additionally, we added optional input parameters to existing functions to enhance their functionality. Specifically:
   - `BorrowAsMarketOrder` was renamed to `SellCreditMarket`. Instead of receiving an array of receivable credit positions, it now accepts a `creditPositionId` parameter. If set to `RESERVED_ID`, it can create a DebtPosition/CreditPosition pair for a "simple borrow". Passing an existing credit position ID allows for a "lender exit".
   - `LendAsMarketOrder` was merged with `BuyMarketCredit` into a single `BuyCreditMarket` function. Like `SellCreditMarket`, it accepts a `creditPositionId` parameter. Setting it to `RESERVED_ID` enables the creation of a DebtPosition/CreditPosition pair for a "simple lend". Passing an existing credit position ID enables a credit buy operation.
   - `BorrowerExit` was eliminated in favor of a `BuyCreditMarket` + `Compensate` flow.
   - `Compensate` now accepts a `creditPositionToCompensateId` that can be set to `RESERVED_ID`, which can create a DebtPosition/CreditPosition pair, allowing for debt reduction on an existing loan while creating a new debt. This facilitates partial repayments.

5. Tenor vs Due Date: as part of a refector, market orders now accept a `tenor` parameter instead of a `dueDate`, and the due date is calculated on-chain based on the `block.timestamp`

## All trusted roles in the protocol

| Role                                | Description                       |
| --------------------------------------- | ---------------------------- |
| KEEPER_ROLE                          |  Liquidation bots   |
| PAUSER_ROLE                             |  Protocol admin and monitoring services capable of pausing the protocol |
| BORROW_RATE_UPDATER_ROLE                             |  Backend bot that performs an off-chain calculation of the Variable Pool (Aave v3) borrow rate over a certain period of time and updates the contract with the result  |
| DEFAULT_ADMIN_ROLE                             |  Protocol admin (multisig)  |

## Describe any novel or unique curve logic or mathematical models implemented in the contracts

N/A

## Running tests

# Setup

```bash
git clone --recurse https://github.com/code-423n4/2024-06-size.git
cd 2024-06-size
```

# Build

```bash
forge build
```

# Tests

```bash
forge test
```

# Invariant tests

```bash
yarn echidna-property
yarn echidna-assertion
```

# Property-based tests

```bash
for i in {0..5}; do halmos --loop $i; done
```

*Note*: We're not sure why Halmos outputs the following warning: `Skipped SizeViewData.json due to parsing failure: KeyError: 'metadata'.`

## Miscellaneous

Employees of Size and employees' family members are ineligible to participate in this audit.
