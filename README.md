# ✨ So you want to sponsor a contest

This `README.md` contains a set of checklists for our contest collaboration.

Your contest will use two repos:
- **a _contest_ repo** (this one), which is used for scoping your contest and for providing information to contestants (wardens)
- **a _findings_ repo**, where issues are submitted.

Ultimately, when we launch the contest, this contest repo will be made public and will contain the smart contracts to be reviewed and all the information needed for contest participants. The findings repo will be made public after the contest is over and your team has mitigated the identified issues.

Some of the checklists in this doc are for **C4 (🐺)** and some of them are for **you as the contest sponsor (⭐️)**.

---

# Contest setup

## ⭐️ Sponsor: Provide contest details

Under "SPONSORS ADD INFO HERE" heading below, include the following:

- [ ] Name of each contract and:
  - [ ] source lines of code (excluding blank lines and comments) in each
  - [ ] external contracts called in each
  - [ ] libraries used in each
- [ ] Describe any novel or unique curve logic or mathematical models implemented in the contracts
- [ ] Does the token conform to the ERC-20 standard? In what specific ways does it differ?
- [ ] Describe anything else that adds any special logic that makes your approach unique
- [ ] Identify any areas of specific concern in reviewing the code
- [ ] Add all of the code to this repo that you want reviewed
- [ ] Create a PR to this repo with the above changes.

---

# Contest prep

## ⭐️ Sponsor: Contest prep
- [ ] Make sure your code is thoroughly commented using the [NatSpec format](https://docs.soliditylang.org/en/v0.5.10/natspec-format.html#natspec-format).
- [ ] Modify the bottom of this `README.md` file to describe how your code is supposed to work with links to any relevent documentation and any other criteria/details that the C4 Wardens should keep in mind when reviewing. ([Here's a well-constructed example.](https://github.com/code-423n4/2021-06-gro/blob/main/README.md))
- [ ] Please have final versions of contracts and documentation added/updated in this repo **no less than 8 hours prior to contest start time.**
- [ ] Ensure that you have access to the _findings_ repo where issues will be submitted.
- [ ] Promote the contest on Twitter (optional: tag in relevant protocols, etc.)
- [ ] Share it with your own communities (blog, Discord, Telegram, email newsletters, etc.)
- [ ] Optional: pre-record a high-level overview of your protocol (not just specific smart contract functions). This saves wardens a lot of time wading through documentation.
- [ ] Delete this checklist and all text above the line below when you're ready.

---

# bunker.finance contest details
- $28,500 USDC main award pot
- $1,500 USDC gas optimization award pot
- Join [C4 Discord](https://discord.gg/code4rena) to register
- Submit findings [using the C4 form](https://code4rena.com/contests/2022-05-bunker-contest/submit)
- [Read our guidelines for more details](https://docs.code4rena.com/roles/wardens)
- Starts May 3, 2022 00:00 UTC
- Ends May 5, 2022 23:59 UTC

This repo will be made public before the start of the contest. (C4 delete this line when made public)

# Resources

- [Code on Github](https://github.com/bunkerfinance/bunker-protocol/tree/752126094691e7457d08fc62a6a5006df59bd2fe)

## Non-Concerns

We do not plan to add any ERC777-backed cTokens to Bunker, so any vulnerabilities (e.g. any known vulnerabilities in Compound) that require the underlying to be an ERC777 (or similar) are not in the scope of this contest.

# Contest Scope

This protocol is a fork of Compound 2.9 that allows users to collateralize ERC721s, ERC1155s, and CryptoPunks.

To learn more about Compound, you can read the documentation here:
- [Compound Documentation](https://compound.finance/docs)

The following contracts/functions are part of the audit scope:

## CNft.sol (191 SLoC)

This contract implements an ennumerable version of ERC1155 token standard. It can wrap an ERC721, ERC1155, or CryptoPunk. cNFT represents a collateralized NFT, similar to how cTokens represent collateralized Ether or ERC20s.

This contract uses the following external libraries:
- `@openzeppelin/contracts/token/ERC721/IERC721Receiver.sol`
- `@openzeppelin/contracts/token/ERC1155/IERC1155Receiver.sol`
- `@openzeppelin/contracts/interfaces/IERC1155.sol`
- `@openzeppelin/contracts/interfaces/IERC721.sol`
- `@openzeppelin/contracts/utils/introspection/ERC165.sol`
- `@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol`
- `@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol`
- `@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol`

This contract also uses the `ERC1155Enumerable.sol`, which is also in scope for this contest. Details in the next section.

## ERC1155Enumerable.sol (41 SLoC)

This contract implements an enumerable version of ERC1155 that allows for enumerating the NFTs an address owns.

This contract uses the following libraries:
- `@openzeppelin/contracts/interfaces/IERC1155.sol`
- `@openzeppelin/contracts-upgradeable/token/ERC1155/ERC1155Upgradeable.sol`
- `./EnumerableUintSet.sol` (a subset of OpenZeppelin's `EnumerableSet.sol`)

## PriceOracleImplementation.sol (18 SLoC)

This contract is Bunker's implementation of Compound's `PriceOracle` interface. It makes an external call to the USDC/ETH Chainlink feed.

## CErc20.sol and CEther.sol (~4 SLoC added each), CToken.sol (~95 SLoC added)

The `liquidateBorrowNft` function in `CErc20.sol`/`CEther.sol` and `liquidateBorrowNftInternal`/`liquidateBorrowNftFresh` functions in `CToken.sol` are in scope for this contest. They are the code paths used for liquidating cNFTs. No additional libraries are used.

## Comptroller.sol (See [here](https://github.com/bunkerfinance/bunker-protocol/commit/752126094691e7457d08fc62a6a5006df59bd2fe) for diff from Compound 2.9, about ~140 SLoC changed)

This contract contains logic for accounting of cNFTs and dictating when certain actions (e.g. supplying/borrowing/liquidating) are allowed. No additional libraries are used.

## UniswapV2PriceOracle.sol (111 SLoC)

This contract implements a 30 minute UniswapV2 TWAP oracle. It makes three external calls to a `UniswapV2Pair` contract (`price0CumulativeLast`/`price1CumulativeLast`/`getReserves`), one external call to `UniswapV2Factory` (`getPair`) and one external call to an `ERC20` contract (`decimals`).

One library is used (`Oracles/libraries/FullMath.sol`) which is a copy of Uniswap's `mulDiv` function in the [FullMath](https://github.com/Uniswap/v3-core/blob/main/contracts/libraries/FullMath.sol) library, but with very small modifications to make it compatible with Solidity 0.8.

## CNftPriceOracle.sol (56 SLoC)

This contract implements a price oracle for cNFT. It prices the cNFT at the price of the underlying's NFTX token (computed by `UniswapV2PriceOracle`), minus the mint fee. It makes one external call to a `NFTXVault` contract, `mintFee`.

Like in `UniswapV2PriceOracle`, the `FullMath` library is used.

# Areas of Concern

- Reentrancy attacks, especially those caused by the ERC721/ERC1155/CryptoPunks contracts.
- Oracle manipulation via flash loan (we are aware that with low liquidity in UniswapV2, the TWAP can be manipulated with enough capital).
- Any bug that causes loss of user funds.
