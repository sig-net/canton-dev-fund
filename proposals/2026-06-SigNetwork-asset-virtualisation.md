# Sig.Network's Asset Virtualisation

| Field | Value |
| :---- | :---- |
| **Author** | Sig.Network |
| **Status** | Submitted |
| **Created** | 2026-07-14 |
| **Label** | `app-building-developer-experience` |
| **Champion** | TBD |

---

## Abstract

Canton grew by deploying on top of the ledgers institutions already used, and letting atomic, private settlement drive volume onto the network. [Sig.Network](https://docs.sig.network/) lets Canton do the same for all of on-chain finance, deploying on top of the popular public chains and giving Canton applications direct access to their assets and markets.

Sig.Network Asset Virtualisation allows Canton contracts to directly control accounts on Ethereum, Solana, and other chains. Assets never leave their home chains and stay usable there natively, with Canton's privacy and settlement guarantees intact.

This solves Canton DeFi's cold-start problem, applications accept deposits on other chains straight from external wallets and exchange accounts, and the ETH a Canton contract holds can still be swapped on Uniswap or deposited on Aave. Onboarding to Canton becomes as easy as depositing to a CEX.

We request $400,000 across five milestones to deliver the Ethereum, Solana, BNB, and Polygon integrations as an open-source SDK any Canton application can adopt. $150,000 is gated on adoption, not delivery. Milestone 1, the Ethereum integration and public SDK, is already delivered: [Bima](https://bima.money/) is building on it today, and [Temple](https://templedigitalgroup.com/), [Ekiden](https://ekiden.fi/), [Yieldy](https://yieldy.io/), and [Minted](https://minted.app) are committed.

---

## Motivation

### The Opportunity

To make Canton the network where every serious on-chain financial transaction originates. The institutional capital is already here. We give every contract access to best execution at full depth across all of on-chain finance, and builders deploy once on Canton to reach every market.

### The Problem

The DeFi markets are not here yet. Public chains have markets that TradFi don't: perpetual futures, prediction markets, credit pools funded by crypto-native stablecoin lenders and a rapidly growing tokenised-asset market. Any of these markets could be rebuilt on Canton, but liquidity tends to stay where it has accumulated. Today Canton participants can only reach it by leaving, taking their capital and volume with them.

Canton's institutional market infrastructure is live, but a Canton application today can only serve users who already hold Canton's tokenised assets, a fraction of the cryptocurrency capital that should be deploying here. That capital will not arrive on its own: users will not bridge, wrap, or re-custody assets to reach a new network when it lacks markets for those assets.

Temple needs order books backed by collateral that lives on Ethereum and Solana. Bima needs external collateral behind its credit lines. Yieldy wants to bring its everyday crypto users, and their capital, onto Canton. Different applications, all missing one thing: native access to cross-chain assets, protocols and liquidity.

### Why Sig.Network

Three architectures exist to solve the DeFi cold-start problem, both on Canton and elsewhere: wrapped-token bridges ([#13](https://github.com/canton-foundation/canton-dev-fund/pull/13), [#147](https://github.com/canton-foundation/canton-dev-fund/pull/147)), execution environment compatibility layers ([#24](https://github.com/canton-foundation/canton-dev-fund/pull/24)), and Asset Virtualisation, the approach proposed here.

**Wrapped-token bridges** are the established route to export price exposure using a redeemable token pegged to a foreign asset. But an asset's best markets live on its native or busiest chain, and wrapping cuts it off from them: wrapped ETH on Solana can't be swapped on Uniswap, lent on Aave, or sent to an Ethereum wallet to pay someone. And because exchanges and custodians only send native assets, there is no direct deposit route into a wrapped token, so retail flow has trouble getting in.

**Execution environment compatibility** lets existing EVM, (or SVM, RISC-V or WASM) protocols redeploy on Canton unchanged, and lets developers build with tools they already know. This works, but only when the whole ecosystem throws its weight behind it and makes it the primary execution environment, as Avalanche's C-Chain and BNB Chain did.

Added as a secondary environment, it has consistently struggled. NEAR[^2], Solana[^3], Cardano[^4], EOS[^5] and Polkadot[^6] all gained working EVMs that are now discontinued or capture a tiny fraction of the chain's activity. A secondary EVM is a second-class citizen on its own chain, lagging on indexers, oracles, keepers, wallets, and new protocols, its liquidity split from the chain it emulates. Most fundamentally, redeploying a protocol's code does not move its users or liquidity; those stay on the original chain, so the flows Canton needs still have no route in.

**Asset Virtualisation**, Sig's approach, lets Canton contracts directly control accounts on external chains. Assets never leave their home chain, so users deposit straight from exchange accounts and standard wallets, the same collateral remains deployable on Uniswap or Aave, and nothing is wrapped. It is the only one of the three architectures that gives external users both a direct deposit route and full native use of their assets.

These architectures work best together. A bridge like CCIP is the most reliable way to move USDC between chains, and an EVM environment lets a team deploy with the expertise and contracts it already has. Asset Virtualisation completes both: bridged USDC can be deployed to other chains' markets and the EVM application can use external assets and liquidity today, rather than waiting for a local DeFi ecosystem to form around it.

Sig.Network is the only company that currently produces this technology and is [live in production](https://docs.sig.network/) on Ethereum and Solana today, built by the team that created [NEAR Chain Signatures](https://docs.near.org/chain-abstraction/chain-signatures). Canton is the next deployment of a working system: this grant accelerates a delivery already underway.

David Millar-Durrant (CEO; ex-Digital Asset, Dfinity, NEAR) leads delivery. Ognjen Marić, co-author of the original Canton Network whitepaper, advises on architecture.

### Deployers

Five Canton teams have committed to build specific products on Asset Virtualisation:

| Project | Committed product |
|---|---|
| [Temple](https://templedigitalgroup.com/) | Asset and Market Virtualization for Canton's first institutional CLOB |
| [Ekiden](https://ekiden.fi/) | Cross-chain spot liquidity backing Canton's first perpetuals venue |
| [Bima](https://bima.money/) | Native Collar loans and a CDP product on Canton |
| [Yieldy](https://yieldy.io/) | Onboarding Solana and EVM users into Canton's RWA yield |
| [Minted](https://minted.app) | MPC threshold signing and multi-chain expansion for mUSD |

Bima is building on it now, and Temple begins next:

> "The team behind Sig.Network have a long history of working with DAML and Canton technology. They are familiar with the network and liquidity fragmentation issues that have plagued interoperability protocols in the past. With their solution, Temple can offer Asset and Market Virtualization to our partners. This unlocks a very large design space for Temple and the industry as a whole."
>
> - **Dan Simerman, Co-Founder, [Temple Digital Group](https://templedigitalgroup.com/)**

---

## Use cases

Asset Virtualisation is a general primitive. Rather than describe it in the abstract, we show four use cases, most of which our partners are already building.

### CEX-like DeFi

An institution's digital-asset operations are split across exchange accounts, and a wallet per chain for DeFi opportunities. A CEX rebundles them into one account, but only by taking custody, and even then the account stops at the exchange's own venues: DeFi credit, long-tail pairs, and on-chain yield stay outside.

**Problem 1: an on-chain venue is capped at its own chain's assets.** A Canton venue today can only list what is already tokenised on Canton, and a user's collateral on Ethereum or Solana cannot back a position on it. This is a large part of why custodial venues still clear 90% of cryptocurrency trading volume: the only venues offering the full asset universe and margining against it are custodial.

**Problem 2: traders choose between CEX order books and DeFi liquidity.** The deep order books live on custodial venues; the credit lines, long-tail pairs, perpetuals, and yield live in DeFi. No venue reaches both, so a desk splits its capital between exchange accounts and per-chain wallets, and pays in time and risk every time the trade is on the other side.

**Resolution.** Asset Virtualisation makes Temple a CEX that can also reach DeFi markets and liquidity. Temple can list an asset the day its chain is supported, and a single Temple account lets a user:

- Deposit USDC from a Coinbase account and ETH from MetaMask into the same account
- Trade Temple's books against that combined balance, cross-margined as one position
- Sweep idle balances into Aave, and recall them the moment an order needs the margin
- Route an order to Uniswap or Raydium when the on-chain pool is deeper than the book

Every step settles as a standard Canton transaction, with Canton's privacy intact and no custodial intermediary. Every external depositor this attracts becomes a Canton participant, and every fill becomes Canton settlement volume.

### Spot-backed yield for perp markets

A perpetual future stays priced correctly through arbitrage. When demand pushes the perp above the spot, someone has to sell the perp and buy spot to pull the two prices back together, and the funding rate exists to incentivise them to do it. How cheaply that trade executes determines how tightly the perp holder can track its underlying.

**Problem 1: the realignment trade spans venues that don't share capital.** The perp trades on Canton, on a venue like [Ekiden](https://ekiden.fi/), the network's first perpetuals CLOB and our reference deployer for this use case; the spot leg trades wherever the asset is deepest, e.g. Ethereum for tokenised gold, Solana for SOL. To execute both legs together, an arbitrageur today must pre-position inventory on every venue and rebalance it continuously. Only desks with balance sheet on every chain can run the trade, they charge for that cost in the funding they demand, and a new venue launches with none of them present.

**Problem 2: expensive arbitrage means the perp fails at its job.** A trader who buys a one-ounce gold perp expects to receive the dollar value of one ounce of gold whenever they sell, even a year later. If realignment capital is scarce, funding runs high and volatile, the position bleeds carry, and the instrument stops being a viable way to hold exposure. High funding also caps how large the long side, and therefore the venue's open interest, can grow.

**Resolution.** Asset Virtualisation lets capital held by a Canton application trade on any venue directly. A yield vault on Canton accepts whatever assets users deposit, from any chain, and when the perp trades rich, sells the perp on Ekiden and buys spot on whichever venue is deepest, as a single flow controlled by Canton contracts. One pool of capital backs the trade everywhere: no inventory stranded per venue, no bridging leg, no unwind risk priced in. Because the trade is cheap to run, capital supplies it at lower funding rates, so funding stays compressed, the perp tracks its underlying, and the gold buyer gets their ounce's worth a year later. Depositors earn market-neutral yield on the assets they already hold, the venue gets a standing realignment counterparty from day one, and every spot leg is flow that Canton's ecosystem would otherwise never touch.

### Collateral compatible with every market

DeFi protocols like Aave Horizon and Morpho run short-term secured financing against a redeemable security, funded by an always-on pool of crypto-native stablecoin lenders. The pool exists to bridge a duration mismatch: the borrower wants to use their collateral now, while the security redeems on its own cycle (daily at NAV, T+1, or interval windows) and its underlying market keeps business hours.

While Canton already settles out-of-hours repo at scale, Sig and Temple want Canton market participants to finance their assets using whatever offers the best terms at that moment, on Canton or off. This also makes Canton the obvious venue for RWA issuance: issue here once, and every chain's capital pools are reachable from one place.

**Problem 1: the issuer functions must be rebuilt on every chain.** To list on one of these venues, the issuer has to reconstruct its back office on the venue's chain: a token reconciled against the register, KYC and whitelisting of qualified wallets, redemption processing, and a permissioned liquidator set with contracts able to take and dispose of the collateral. All of it is rebuilt again for every chain, which is why these venues launch with a handful of assets from a handful of issuers.

**Problem 2: on-chain collateral works one market at a time.** A prime broker posts one pool of collateral and it margins every market the broker reaches. The on-chain equivalent is a supply chain: deploy the token to the financing venue's chain, draw stablecoins there, bridge the proceeds to the chain where the position is, then reverse every leg to unwind. Each leg adds delay and operational risk.

**Resolution.** Combining Sig's settlement with Temple's liquidity, an asset holder can borrow against their collateral in any compatible pool and deploy the proceeds as a position on any chain.

The flow is:
1. Find the best venue to finance your collateral, on Canton or off.
2. Lock the collateral on Temple and, if the venue isn't Canton, create a representation on the target chain.
3. Draw against it.
4. Swap the proceeds to the chain where you want the position.
5. Open the position.

Steps 2 and 3 settle atomically, as do steps 4 and 5.

Asset Virtualisation keeps the issuer functions on Canton and lets the financing counterparties sit on any chain. Redemption, KYC, whitelisting, issuance against the register, and liquidation all live on Canton. If a pool begins liquidating, it sends a message to Canton to initiate redemption, and Canton responds asynchronously, swapping the asset out for its underlying security.

The same collateral is then financed wherever stablecoin liquidity is deepest (e.g. Aave Horizon on Ethereum) and the proceeds deployed wherever the position is (e.g. Hyperliquid). For the desk, that is access to new pools of capital at any hour of the day, including when the security's home market is closed. For the issuer, it is one back office serving every chain and collateral mobility.

> "We’re going to use the product to enable native Collar loans on Canton and for our CDP product."
>
> - **Sid Sridhar, Founder, [Bima](https://bima.money/)**

### Moving existing crypto applications to Canton

Canton's cold-start problem has an application side as well as a liquidity side: established DeFi applications have users and TVL on other chains, and deploying on Canton means starting from zero.

**Problem: migrating an application means asking its users to migrate first.** An application's users hold their assets on its current chains and will not bridge them. So the application either stays where its users are, or deploys on Canton with no users and no TVL on day one.

**Resolution.** With Asset Virtualisation, an application deploys its logic on Canton while its users keep transacting from their existing chains and wallets, so it arrives with its user base and TVL intact and gains Canton's privacy and RWA markets on arrival. Yieldy intends to migrate its Ethereum and Solana native users to Canton this way. Similarly, Bima brings its CDP credit lines to Canton, borrowing against Bitcoin-LST collateral its users keep on Ethereum, settling back to their home-chain addresses. Every application that arrives like this deepens Canton's liquidity and number of market participants.

> "Canton has the institutional rails; Yieldy brings everyday crypto users. Using Sig's network, our app lets Solana and EVM users deposit straight into Canton contracts, with our risk engine routing capital to safe yield. This is a path for crypto-native capital to progressively onboard into Canton's RWA yield."
>
> - **Alex Dimitrijevic, Co-Founder & CEO, [Yieldy](https://yieldy.io/)**


## Specification

### 1. Objective

Deliver shared, open-source ecosystem infrastructure for Canton that:

- **Solves Canton DeFi's cold-start problem** by giving any Canton application native access to the users, assets, and liquidity of Ethereum and Solana, with no bridging, wrapping, or new infrastructure on the user's side.
- **Gives Canton participants best execution** by extending the reach of Canton contracts to external protocols and venues (Uniswap, Aave, Raydium, Hyperliquid), so one Canton account reaches every market.
- **Ships as an open-source SDK** (DAML and TypeScript) that any Canton application can adopt.
- **Is paid on adoption, not just delivery**: two applications in production (Milestone 2) and $1M of organic 30-day volume (Milestone 3) are funded milestones.

### 2. Implementation Mechanics

**How Asset Virtualisation works:**

A Canton application controls accounts on Ethereum and Solana through Sig's decentralised MPC validator network. No single party holds the keys: the network signs a foreign-chain transaction only when the Canton contract instructs it to.

1. **Deposit.** A user sends native assets to a home-chain deposit address derived for them by the MPC network.
2. **Sweep.** Sig's validators observe the deposit and sign a transaction sweeping the funds into the application's pooled home-chain account.
3. **Credit.** The validators submit the matching Canton transaction, and the application credits the user's balance on Canton.
4. **Transact.** The user trades on Canton against that balance like any other Canton asset.
5. **Reach out or withdraw.** When the application needs the home chain, to deploy funds on Uniswap or Aave, or to return them to the user's own wallet, the Canton contract instructs Sig's network to sign the corresponding home-chain transaction. Assets move natively.

The assets stay native on their home chain throughout, and every step lands on Canton as a standard transaction, with privacy, compliance controls, and settlement guarantees intact.

**Atomicity boundary.** Everything on Canton is atomic, including trades between virtualised assets: an order matched on a Canton venue settles both legs atomically, exactly as with native Canton assets. Operations that cross onto another chain (the deposit sweep, a withdrawal, a Uniswap call) are asynchronous instructions with explicit confirmation and failure handling, the same shape as the fiat and corporate-action legs Canton workflows already coordinate. As a future body of work, beyond this grant's scope, we are working with external chains to extend their sequencers so that cross-chain calls become atomic as well, [Sonic](https://www.soniclabs.com/) has already signed on.

**Deliverables per milestone:** a live mainnet integration for each supported chain, and an open-source DAML and TypeScript SDK.

### 3. Architectural Alignment

Asset Virtualisation is designed around Canton's core properties:

| Canton Property | How Asset Virtualisation Preserves It |
|---|---|
| Sub-transaction privacy | Canton smart contracts hold accounts, external chains only see that the application is taking an action, not the Canton part or initiator of the trade |
| DAML transaction model | Sig converts external actions into standard Canton transactions; Canton applications process them identically to native transactions |
| Settlement guarantees | Assets remain on home chains with no custodial intermediary |

No Canton protocol changes are required. Applications call Sig's Canton smart contracts to perform multichain actions.

This proposal is part of the Foundation's priority area of **App Building and Developer Experience**: it delivers interoperability across wallets, assets, and dApps, and removes a significant source of user friction, reaching Canton applications from standard external wallets.

### 4. Backward Compatibility

No backward compatibility impact. All components are additive. Existing Canton participants and Canton protocol behaviour are unchanged.

---

## Milestones and Deliverables

### Milestone 1: Mainnet - Ethereum + Open-Source SDK

| Field | Value |
| :---- | :---- |
| **Estimated Delivery** | July 1, 2026 (delivered) |
| **Focus** | End-to-end Ethereum integration validated on Canton DevNet, then launched to production. SDK publicly released. |

**Deliverables / Value Metrics:**

- Full Ethereum deposit and withdrawal flow demonstrated end-to-end on Canton DevNet
- SDK integration complete and reviewed by Temple
- Live mainnet integration (Ethereum): Ethereum users can deposit and withdraw native assets into Canton applications via standard wallets
- Open-source Asset Virtualisation SDK published with documentation

### Milestone 2: Mainnet - Application Adoption

| Field | Value |
| :---- | :---- |
| **Estimated Delivery** | September 1, 2026 |
| **Focus** | The SDK proves out as ecosystem infrastructure: two Canton application teams running Asset Virtualisation in production. |

**Deliverables / Value Metrics:**

- Two Canton applications live on Canton mainnet processing transactions through the Asset Virtualisation SDK

### Milestone 3: User Adoption

| Field | Value |
| :---- | :---- |
| **Estimated Delivery** | November 1, 2026 |
| **Focus** | Demonstrated production usage of the integrations delivered in the preceding milestones. Does not block Milestones 4 or 5. |

**Deliverables / Value Metrics:**

- Minimum $1M in organic deposit and withdrawal volume within any 30-day window on Canton mainnet

### Milestone 4: Mainnet - Solana

| Field | Value |
| :---- | :---- |
| **Estimated Delivery** | November 1, 2026 |
| **Focus** | Native Solana asset deposits and withdrawals. Unlocks SOL and Solana-native assets and liquidity within Canton's institutional markets. |

**Deliverables / Value Metrics:**

- Live mainnet Solana integration
- Open-source Solana chain adapter with documentation
- SOL and Solana-native assets accessible on Canton

### Milestone 5: Mainnet - BNB + Polygon

| Field | Value |
| :---- | :---- |
| **Estimated Delivery** | December 1, 2026 |
| **Focus** | Expand to BNB and Polygon, each chosen for a distinct market. BNB carries Binance's retail flows and its user base across MENA and Asia. Polygon hosts Polymarket's prediction markets and is a home chain for tokenised funds, including BlackRock's BUIDL and Franklin Templeton's BENJI. |

**Deliverables / Value Metrics:**

- Live mainnet integrations for BNB and Polygon. 
- SDK updated with new chain adapters; network-specific documentation published
- Demonstrated live use of a swap provider (e.g. Uniswap) and a yield provider (e.g. Aave) with virtualised assets on at least one supported chain

---

## Acceptance Criteria

The Tech & Ops Committee will evaluate completion based on:

- End-to-end deposits and withdrawals from each supported chain demonstrated live on Canton mainnet
- Open-source SDK published to a public repository with integration documentation
- Temple live with external-chain users executing transactions through the integration
- Two Canton applications live on mainnet using the integration by M2
- Volume metric met at M3: $1M deposit/withdrawal volume within any 30-day window on Canton mainnet
- At M5: demonstrated live use of a swap provider (e.g. Uniswap) and a yield provider (e.g. Aave) with virtualised assets on at least one supported chain

---

## Funding

**Total Funding Request:** $400,000 USD equivalent in Canton Coin

### Payment Breakdown by Milestone

| Milestone | Focus | Amount | Trigger |
| :---- | :---- | :---: | :---- |
| **M1** | Mainnet - Ethereum + SDK | $150,000 | Committee acceptance |
| **M2** | Application Adoption | $50,000 | Committee acceptance |
| **M3** | User Adoption | $100,000 | Committee acceptance |
| **M4** | Mainnet - Solana | $50,000 | Committee acceptance |
| **M5** | Mainnet - BNB + Polygon | $50,000 | Committee acceptance |

All amounts are paid as the CC equivalent of the USD value. $150,000 of the $400,000 total (M2 + M3) is gated on adoption rather than delivery.

### Volatility Stipulation

The project duration is under 6 months (July–December 2026). CC amounts for each milestone will be calculated at the 30-day moving average of CC/USD at the time of milestone submission. Should the project timeline extend beyond 6 months due to Committee-requested scope changes, remaining milestones will be renegotiated to account for significant USD/CC price movements.

---

## Co-Marketing

Upon each milestone release, Sig.Network will collaborate with the Canton Foundation on:

- Announcement coordination for each mainnet launch
- Joint technical blog post or case study demonstrating Asset Virtualisation in production
- Developer documentation and ecosystem promotion for the open-source SDK

Temple Digital Group, as an early production user of the integration, will provide independent co-marketing support at each milestone.

---

## Maintenance

The Asset Virtualisation SDK and the underlying validator network are part of Sig.Network's core product. Sig.Network operates and maintains both after the grant period, exactly as it does for the live Ethereum and Solana deployments today. The SDK is open source, so the Canton ecosystem retains the integration layer under an open licence regardless.

---

## Rationale

**Why fund this now:**

Asset Virtualisation is the foundational layer for future Canton capabilities, including cross-chain access to tokenised assets and sequencer interoperability with other networks, but those are distinct workstreams. This grant funds the core primitive those capabilities will build on.

> "[Sig.Network] transforms Minted's cross-chain architecture from a validator multisig model to decentralized MPC threshold signing, while enabling multi-chain expansion starting with Solana. Sig's existing Canton POC and production Solana deployment significantly de-risk execution."
>
> - **Luis Cuello, Founder & CEO, [Minted](https://minted.app)**

[^2]: Aurora on NEAR: DeFi TVL is $3.12M with 138 daily active addresses. Source: DefiLlama, July 2026.
[^3]: Neon EVM on Solana: DeFi TVL is $80,566, daily active addresses total 148, and 24-hour DEX volume is $39. Source: DefiLlama, July 2026.
[^4]: Milkomeda (Cardano/Algorand): Milkomeda C1 (Cardano) has a TVL of $0, and Milkomeda A1 (Algorand) has a TVL of $1,146. Both are flagged as deprecated by market indexers. Source: DefiLlama, July 2026.
[^5]: EOS EVM: DeFi TVL is $440 with no tracked ecosystem volume. Source: DefiLlama, July 2026.
[^6]: Moonbeam (Polkadot): Ecosystem TVL is $869,701 with 1,655 active daily addresses; the project has initiated a structural shift to migrate its GLMR token to Ethereum Layer-2 Base. Source: DefiLlama, July 2026.
