# ✨ So you want to run an audit

This `README.md` contains a set of checklists for our audit collaboration.

Your audit will use two repos: 
- **an _audit_ repo** (this one), which is used for scoping your audit and for providing information to wardens
- **a _findings_ repo**, where issues are submitted (shared with you after the audit) 

Ultimately, when we launch the audit, this repo will be made public and will contain the smart contracts to be reviewed and all the information needed for audit participants. The findings repo will be made public after the audit report is published and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the audit sponsor (⭐️)**.

---

# Audit setup

## 🐺 C4: Set up repos
- [ ] Create a new private repo named `YYYY-MM-sponsorname` using this repo as a template.
- [ ] Rename this repo to reflect audit date (if applicable)
- [ ] Rename audit H1 below
- [ ] Update pot sizes
  - [ ] Remove the "Bot race findings opt out" section if there's no bot race.
- [ ] Fill in start and end times in audit bullets below
- [ ] Add link to submission form in audit details below
- [ ] Add the information from the scoping form to the "Scoping Details" section at the bottom of this readme.
- [ ] Add matching info to the Code4rena site
- [ ] Add sponsor to this private repo with 'maintain' level access.
- [ ] Send the sponsor contact the url for this repo to follow the instructions below and add contracts here. 
- [ ] Delete this checklist.

# Repo setup

## ⭐️ Sponsor: Add code to this repo

- [ ] Create a PR to this repo with the below changes:
- [ ] Confirm that this repo is a self-contained repository with working commands that will build (at least) all in-scope contracts, and commands that will run tests producing gas reports for the relevant contracts.
- [ ] Please have final versions of contracts and documentation added/updated in this repo **no less than 48 business hours prior to audit start time.**
- [ ] Be prepared for a 🚨code freeze🚨 for the duration of the audit — important because it establishes a level playing field. We want to ensure everyone's looking at the same code, no matter when they look during the audit. (Note: this includes your own repo, since a PR can leak alpha to our wardens!)

## ⭐️ Sponsor: Repo checklist

- [ ] Modify the [Overview](#overview) section of this `README.md` file. Describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the auditors should keep in mind when reviewing. (Here are two well-constructed examples: [Ajna Protocol](https://github.com/code-423n4/2023-05-ajna) and [Maia DAO Ecosystem](https://github.com/code-423n4/2023-05-maia))
- [ ] Review the Gas award pool amount, if applicable. This can be adjusted up or down, based on your preference - just flag it for Code4rena staff so we can update the pool totals across all comms channels.
- [ ] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [ ] [This checklist in Notion](https://code4rena.notion.site/Key-info-for-Code4rena-sponsors-f60764c4c4574bbf8e7a6dbd72cc49b4#0cafa01e6201462e9f78677a39e09746) provides some best practices for Code4rena audit repos.

## ⭐️ Sponsor: Final touches
- [ ] Review and confirm the pull request created by the Scout (technical reviewer) who was assigned to your contest. *Note: any files not listed as "in scope" will be considered out of scope for the purposes of judging, even if the file will be part of the deployed contracts.*
- [ ] Check that images and other files used in this README have been uploaded to the repo as a file and then linked in the README using absolute path (e.g. `https://github.com/code-423n4/yourrepo-url/filepath.png`)
- [ ] Ensure that *all* links and image/file paths in this README use absolute paths, not relative paths
- [ ] Check that all README information is in markdown format (HTML does not render on Code4rena.com)
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# Size audit details
- Total Prize Pool: $200000 in USDC
  - HM awards: $168000 in USDC
  - (remove this line if there is no Analysis pool) Analysis awards: XXX XXX USDC (Notion: Analysis pool)
  - QA awards: $6500 in USDC
  - (remove this line if there is no Bot race) Bot Race awards: XXX XXX USDC (Notion: Bot Race pool)
 
  - Judge awards: $15000 in USDC
  - Lookout awards: XXX XXX USDC (Notion: Sum of Pre-sort fee + Pre-sort early bonus)
  - Scout awards: $500 in USDC
  - (this line can be removed if there is no mitigation) Mitigation Review: XXX XXX USDC (*Opportunity goes to top 3 backstage wardens based on placement in this audit who RSVP.*)
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2024-06-size/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts June 10, 2024 20:00 UTC
- Ends July 2, 2024 20:00 UTC

## Automated Findings / Publicly Known Issues

The 4naly3er report can be found [here](https://github.com/code-423n4/2024-06-size/blob/main/4naly3er-report.md).



_Note for C4 wardens: Anything included in this `Automated Findings / Publicly Known Issues` section is considered a publicly known issue and is ineligible for awards._
## 🐺 C4: Begin Gist paste here (and delete this line)





# Scope

*See [scope.txt](https://github.com/code-423n4/2024-06-size/blob/main/scope.txt)*

### Files in scope


| File   | Logic Contracts | Interfaces | SLOC  | Purpose | Libraries used |
| ------ | --------------- | ---------- | ----- | -----   | ------------ |
| /src/Size.sol | 1| **** | 202 | |@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol<br>@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol<br>@openzeppelin/contracts-upgradeable/utils/PausableUpgradeable.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/actions/Initialize.sol<br>@src/libraries/actions/UpdateConfig.sol<br>@src/libraries/actions/SellCreditLimit.sol<br>@src/libraries/actions/SellCreditMarket.sol<br>@src/libraries/actions/Claim.sol<br>@src/libraries/actions/Deposit.sol<br>@src/libraries/actions/BuyCreditMarket.sol<br>@src/libraries/actions/SetUserConfiguration.sol<br>@src/libraries/actions/BuyCreditLimit.sol<br>@src/libraries/actions/Liquidate.sol<br>@src/libraries/Multicall.sol<br>@src/libraries/actions/Compensate.sol<br>@src/libraries/actions/LiquidateWithReplacement.sol<br>@src/libraries/actions/Repay.sol<br>@src/libraries/actions/SelfLiquidate.sol<br>@src/libraries/actions/Withdraw.sol<br>@src/SizeStorage.sol<br>@src/libraries/CapsLibrary.sol<br>@src/libraries/RiskLibrary.sol<br>@src/SizeView.sol<br>@src/libraries/Events.sol<br>@src/interfaces/IMulticall.sol<br>@src/interfaces/ISize.sol<br>@src/interfaces/ISizeAdmin.sol|
| /src/SizeStorage.sol | 1| **** | 61 | |@aave/interfaces/IPool.sol<br>@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol<br>@src/interfaces/IWETH.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/OfferLibrary.sol<br>@src/oracle/IPriceFeed.sol<br>@src/token/NonTransferrableScaledToken.sol<br>@src/token/NonTransferrableToken.sol|
| /src/SizeView.sol | 1| **** | 136 | |@src/SizeStorage.sol<br>@src/libraries/YieldCurveLibrary.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/actions/UpdateConfig.sol<br>@src/libraries/AccountingLibrary.sol<br>@src/libraries/RiskLibrary.sol<br>@src/SizeViewData.sol<br>@src/interfaces/ISizeView.sol<br>@src/libraries/Errors.sol<br>@src/libraries/OfferLibrary.sol<br>@src/libraries/actions/Initialize.sol|
| /src/SizeViewData.sol | ****| **** | 23 | |@aave/interfaces/IPool.sol<br>@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol<br>@src/SizeStorage.sol<br>@src/token/NonTransferrableScaledToken.sol<br>@src/token/NonTransferrableToken.sol|
| /src/libraries/AccountingLibrary.sol | 1| **** | 187 | |@src/SizeStorage.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol<br>@src/libraries/Math.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/RiskLibrary.sol|
| /src/libraries/CapsLibrary.sol | 1| **** | 38 | |@src/SizeStorage.sol<br>@src/libraries/Errors.sol|
| /src/libraries/DepositTokenLibrary.sol | 1| **** | 42 | |@aave/interfaces/IAToken.sol<br>@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@src/SizeStorage.sol|
| /src/libraries/Errors.sol | 1| **** | 67 | |@src/libraries/LoanLibrary.sol|
| /src/libraries/Events.sol | 1| **** | 79 | |@src/libraries/LoanLibrary.sol<br>@src/libraries/actions/Initialize.sol|
| /src/libraries/LoanLibrary.sol | 1| **** | 103 | |@src/SizeStorage.sol<br>@src/libraries/AccountingLibrary.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Math.sol|
| /src/libraries/Math.sol | 1| **** | 42 | |@solady/utils/FixedPointMathLib.sol|
| /src/libraries/Multicall.sol | 1| **** | 24 | |@openzeppelin/contracts/utils/Address.sol<br>@src/SizeStorage.sol<br>@src/libraries/CapsLibrary.sol<br>@src/libraries/RiskLibrary.sol|
| /src/libraries/OfferLibrary.sol | 1| **** | 52 | |@src/libraries/Errors.sol<br>@src/libraries/Math.sol<br>@src/libraries/YieldCurveLibrary.sol|
| /src/libraries/RiskLibrary.sol | 1| **** | 81 | |@src/SizeStorage.sol<br>@src/libraries/Errors.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/Math.sol|
| /src/libraries/YieldCurveLibrary.sol | 1| **** | 88 | |@openzeppelin/contracts/utils/math/SafeCast.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Math.sol|
| /src/libraries/actions/BuyCreditLimit.sol | 1| **** | 38 | |@src/libraries/OfferLibrary.sol<br>@src/libraries/YieldCurveLibrary.sol<br>@src/SizeStorage.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/BuyCreditMarket.sol | 1| **** | 143 | |@src/SizeStorage.sol<br>@src/libraries/AccountingLibrary.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/Math.sol<br>@src/libraries/OfferLibrary.sol<br>@src/libraries/RiskLibrary.sol<br>@src/libraries/YieldCurveLibrary.sol|
| /src/libraries/actions/Claim.sol | 1| **** | 34 | |@src/libraries/LoanLibrary.sol<br>@src/libraries/Math.sol<br>@src/SizeStorage.sol<br>@src/libraries/AccountingLibrary.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/Compensate.sol | 1| **** | 111 | |@src/SizeStorage.sol<br>@src/libraries/Math.sol<br>@src/libraries/AccountingLibrary.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/RiskLibrary.sol|
| /src/libraries/actions/Deposit.sol | 1| **** | 56 | |@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol<br>@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol<br>@src/interfaces/IWETH.sol<br>@src/libraries/CapsLibrary.sol<br>@src/SizeStorage.sol<br>@src/libraries/DepositTokenLibrary.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/Initialize.sol | 1| **** | 175 | |@aave/interfaces/IPool.sol<br>@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol<br>@src/interfaces/IWETH.sol<br>@src/libraries/Math.sol<br>@src/libraries/LoanLibrary.sol<br>@src/oracle/IPriceFeed.sol<br>@src/token/NonTransferrableScaledToken.sol<br>@src/token/NonTransferrableToken.sol<br>@src/SizeStorage.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/Liquidate.sol | 1| **** | 76 | |@src/libraries/Math.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/AccountingLibrary.sol<br>@src/libraries/RiskLibrary.sol<br>@src/SizeStorage.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/LiquidateWithReplacement.sol | 1| **** | 113 | |@src/libraries/Math.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/OfferLibrary.sol<br>@src/libraries/YieldCurveLibrary.sol<br>@src/SizeStorage.sol<br>@src/libraries/actions/Liquidate.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/Repay.sol | 1| **** | 28 | |@src/SizeStorage.sol<br>@src/libraries/AccountingLibrary.sol<br>@src/libraries/RiskLibrary.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/SelfLiquidate.sol | 1| **** | 43 | |@src/libraries/AccountingLibrary.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/Math.sol<br>@src/libraries/RiskLibrary.sol<br>@src/SizeStorage.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/SellCreditLimit.sol | 1| **** | 27 | |@src/SizeStorage.sol<br>@src/libraries/OfferLibrary.sol<br>@src/libraries/YieldCurveLibrary.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/SellCreditMarket.sol | 1| **** | 143 | |@src/libraries/LoanLibrary.sol<br>@src/libraries/Math.sol<br>@src/libraries/OfferLibrary.sol<br>@src/libraries/YieldCurveLibrary.sol<br>@src/SizeStorage.sol<br>@src/libraries/AccountingLibrary.sol<br>@src/libraries/RiskLibrary.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/SetUserConfiguration.sol | 1| **** | 46 | |@src/SizeStorage.sol<br>@src/libraries/LoanLibrary.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/libraries/actions/UpdateConfig.sol | 1| **** | 110 | |@openzeppelin/contracts/utils/Strings.sol<br>@src/SizeStorage.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol<br>@src/libraries/Math.sol<br>@src/libraries/actions/Initialize.sol<br>@src/oracle/IPriceFeed.sol|
| /src/libraries/actions/Withdraw.sol | 1| **** | 43 | |@src/SizeStorage.sol<br>@src/libraries/DepositTokenLibrary.sol<br>@src/libraries/Math.sol<br>@src/libraries/Errors.sol<br>@src/libraries/Events.sol|
| /src/oracle/IPriceFeed.sol | ****| 1 | 5 | ||
| /src/oracle/PriceFeed.sol | 1| **** | 59 | |@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol<br>@openzeppelin/contracts/utils/math/SafeCast.sol<br>@src/libraries/Math.sol<br>@src/libraries/Errors.sol|
| /src/token/NonTransferrableScaledToken.sol | 1| **** | 65 | |@aave/interfaces/IPool.sol<br>@aave/protocol/libraries/math/WadRayMath.sol<br>@openzeppelin/contracts/interfaces/IERC20Metadata.sol<br>@src/libraries/Math.sol<br>@src/token/NonTransferrableToken.sol<br>@src/libraries/Errors.sol|
| /src/token/NonTransferrableToken.sol | 1| **** | 38 | |@openzeppelin/contracts/access/Ownable.sol<br>@openzeppelin/contracts/token/ERC20/ERC20.sol<br>@src/libraries/Errors.sol|
| **Totals** | **32** | **1** | **2578** | | |

### Files out of scope

*See [out_of_scope.txt](https://github.com/code-423n4/2024-06-size/blob/main/out_of_scope.txt)*

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

