# Development Fund Proposal: Asset Virtualisation for Canton - Sig.Network

**Author:** Sig.Network
**Status:** Submitted
**Created:** 2026-06-09

---

## Abstract

Sig.Network requests $400,000 to give every Canton contract native access to the liquidity on Ethereum, Solana: no bridges, no custodians, no wrapped tokens. We call this Asset Virtualisation.

Canton is built for institutions, private, configurable, and with best-in-class access to RWAs and their markets. But Canton participants also want what only public chains provide today: 24-hour swap liquidity, always-on overcollateralised credit lines, liquid perpetuals markets for hedging, and whatever yield or structuring product DeFi ships next quarter.

Today this ecosystem lives on Ethereum, Solana, and Hyperliquid, and it is hard to move. Sig.Network gives Canton applications the reach of a prime brokerage: one venue, every market, settled on Canton.

Broadly, Asset Virtualisation is a general-purpose technology which can:
- Accept deposits from any chain, directly from that chain's wallets and exchange accounts
- Withdraw funds to native or Canton accounts
- Access foreign-chain DeFi liquidity (e.g. Uniswap, Aave, Raydium, Hyperliquid)

This proposal funds the core primitive and its Ethereum, Solana, and further EVM deployments. The same foundation then carries every chain that follows: Hyperliquid and other networks will be scoped in later proposals.

Asset Virtualisation solves Canton DeFi's cold-start problem. Applications get immediate access to external liquidity and the DeFi products users already rely on: looping, leverage, yield. Canton's privacy, regulated-market access, and fast finality become strictly additive.

Temple, Canton's first institutional CLOB, and Bima, a Bitcoin-derived stablecoin, are the reference deployers for this work. The open-source SDK delivered alongside makes Asset Virtualisation available to every Canton application. It is ecosystem infrastructure, not a single-application integration.

The team executing this integration built NEAR Chain Signatures, the foundational technology underpinning this work. David Millar-Durrant (CEO; ex-Digital Asset, Dfinity, NEAR) leads delivery. Ognjen Marić, author of the original Canton Network whitepaper, advises on architectural design.

Sig.Network is already live in production on Ethereum and Solana. This grant brings that system to Canton.

---

## Attestations

> "The team behind Sig.Network have a long history of working with DAML and Canton technology. They are familiar with the network and liquidity fragmentation issues that have plagued interoperability protocols in the past. With their solution, Temple can offer Asset and Market Virtualization to our partners. This unlocks a very large design space for Temple and the industry as a whole."
>
> - **Dan Simerman, Co-Founder, [Temple Digital Group](https://templedigitalgroup.com/)**

> "We’re going to use the product to enable native Collar loans on Canton and for our CDP product."
>
> - **Sid Sridhar, Founder, [Bima](https://bima.money/)**

> "[Sig.Network] transforms Minted's cross-chain architecture from a validator multisig model to decentralized MPC threshold signing, while enabling multi-chain expansion starting with Solana. Sig's existing Canton POC and production Solana deployment significantly de-risk execution."
>
> - **Luis Cuello, Founder & CEO, [Minted](https://minted.app)**

> "Canton has the institutional rails; Yieldy brings everyday crypto users. Using Sig's network, our app lets Solana and EVM users deposit straight into Canton contracts, with our risk engine routing capital to safe yield. This is a path for crypto-native capital to progressively onboard into Canton's RWA yield."
>
> - **Alex Dimitrijevic, Co-Founder & CEO, [Yieldy](https://yieldy.io/)**

---

## Motivation

At its core, Canton is a technology for connecting separate networks: participants transact across sub-networks natively, privately, and atomically. Sig.Network extends that reach beyond Canton's borders, to the assets and liquidity of public blockchains, starting with Ethereum and Solana. Those chains have the liquidity, Canton has what they lack: privacy, synchronised settlement, and regulated RWA markets. We bring the two together for all Canton participants.

Canton's institutional market infrastructure is live. But a Canton application today can only serve users who already hold Canton's tokenised assets, a fraction of the cryptocurrency capital that should be deploying here. That capital will not arrive on its own, institutions will not bridge, wrap, or re-custody assets to reach a new network when it lacks markets for those assets.

We are building Asset Virtualisation for Canton because Canton's applications are already asking for it. Temple needs order books backed by collateral that lives on Ethereum and Solana. Bima needs external collateral behind its credit lines. Yieldy wants to bring its everyday crypto users, and their capital, onto Canton. Different applications, all missing one thing: native access to cross-chain assets, protocols and liquidity.

The alternatives on file are wrapped token bridges ([#13](https://github.com/canton-foundation/canton-dev-fund/pull/13), [#147](https://github.com/canton-foundation/canton-dev-fund/pull/147)). Wrapped tokens cannot be deposited from CEXs or custody accounts, strand assets outside their home ecosystem's markets, and are a user experience nightmare.

---

## Use cases

What we're building is a highly general technology that extends Canton network's reach to other networks. Rather than technology in general, we've selected a few use cases, many of which our partners are already building.

### CEX like DeFi

An institution's digital-asset operations are split across exchange accounts, and a wallet per chain for DeFi opportunities. A CEX rebundles them into one account, but only by taking custody, and even then the account stops at the exchange's own venues: DeFi credit, long-tail pairs, and on-chain yield stay outside.

**Problem 1: an on-chain venue is capped at its own chain's assets.** A Canton venue today can only list what is already tokenised on Canton, and a user's collateral on Ethereum or Solana cannot back a position on it. This is a large part of why custodial venues still clear 90% of cryptocurrency trading volume: the only venues offering the full asset universe and margining against it are custodial. No one wants to be constrained to participating in a subset of markets.

**Problem 2: traders choose between CEX order books and DeFi liquidity.** The deep order books live on custodial venues; the credit lines, long-tail pairs, perpetuals, and yield live in DeFi. No venue reaches both, so a desk splits its capital between exchange accounts and per-chain wallets, and pays in time and risk every time the trade is on the other side.

**Resolution.** Asset Virtualisation makes Temple a CEX that can also reach DeFi markets and liquidity. Temple can list an asset the day its chain is supported, and a single Temple account lets a user:

- Deposit USDC from a Coinbase account and ETH from MetaMask into the same account
- Trade Temple's books against that combined balance, cross-margined as one position
- Sweep idle balances into Aave, and recall them the moment an order needs the margin
- Route an order to Uniswap or Raydium when the on-chain pool is deeper than the book

Every step settles as a standard Canton transaction, with Canton's privacy intact and no custodial intermediary. A Temple account is therefore both things at once: the place where assets from every chain are held, and the place where they participate in every market, exchange books and DeFi alike, without anyone taking custody. Every external depositor this attracts becomes a Canton participant, and every fill becomes Canton settlement volume.

### Efficient off-chain matching

Off-chain matching with on-chain settlement is now a dominant market structure:
- Polymarket runs an off-chain matching engine ($10B per month[^1])
- NEAR Intents runs an RFQ engine against locked assets ($2B per month[^1])
- CoW Swap lets searchers match orders, then offloads settlement to on-chain liquidity ($2B per month[^1])

These venues share two structural problems.

**Problem 1: Capital is trapped in one venue at a time.** A market maker's capital can only back orders on one contract at a time. If their ETH is committed on CoW Swap and a profitable order lands on NEAR Intents, they can't redeploy in time: auction windows plus chain finality run to minutes, and the auction is gone. The alternative, committing capital and settling later, slows execution. Either way, fragmentation raises the cost of capital to market-make.

**Problem 2: Trades can't be chained atomically.** Suppose one engine has deep Solana liquidity and another deep Ethereum liquidity. To execute an ETH→SOL trade, a solver breaks it into an ETH→USDC leg on the first book and a USDC→SOL leg on the second. Because the legs settle independently, the maker has to price the risk that one leg fills while the other fails, and carry the cost of unwinding it.

**Resolution.** Bringing the order books onto Canton solves both. Capital backs every book from a single synchronised position, so there is no redeployment lag, and multi-leg trades settle atomically as one Canton transaction, accessible from the whole ecosystem. Sub-transaction privacy means none of this leaks order-book contents to other market participants. Finally, since the cross-chain assets are controlled directly by Canton smart contracts, settlement is guaranteed. 

### Moving existing crypto applications to Canton

Canton's cold-start problem has an application side as well as a liquidity side: established DeFi applications have users and TVL on other chains, and deploying on Canton means starting from zero.

**Problem: migrating an application means asking its users to migrate first.** An application's users hold their assets on its current chains and will not bridge them. So the application either stays where its users are, or deploys on Canton with no users and no TVL on day one.

**Resolution.** With Asset Virtualisation, an application deploys its logic on Canton while its users keep transacting from their existing chains and wallets, so it arrives with its user base and TVL intact and gains Canton's privacy and RWA markets on arrival. Yieldy intends to migrate their Ethereum and Solana native users to Canton this way. Similarly Bima brings it's CDP product credit lines to Canton, borrowing against Bitcoin-LST collateral its users keep on Ethereum, settling back to their home-chain addresses. Every application that arrives like this deepens Canton's liquidity and number of market participants.

### Collateral compatible with every market

DeFi protocols like Aave Horizon and Morpho run short-term secured financing against a redeemable security, funded by an always-on pool of crypto-native stablecoin lenders. The pool exists to bridge a duration mismatch: the borrower wants to use their collateral now, while the security redeems on its own cycle (daily at NAV, T+1, or interval windows) and its underlying market keeps business hours.

While Canton already settles out-of-hours repo at scale, Sig and Temple want Canton market participants to finance their assets using whatever offers the best terms at that moment, on Canton or off. This also makes Canton the obvious venue for RWA issuance, issue here once, and every chain's capital pools are reachable from one place.

**Problem 1: the issuer functions must be rebuilt on every chain.** To list on one of these venues, the issuer has to reconstruct its back office on the venue's chain: a token reconciled against the register, KYC and whitelisting of qualified wallets, redemption processing, and a permissioned liquidator set with contracts able to take and dispose of the collateral. All of it is rebuilt again for every chain, which is why these venues launch with a handful of assets from a handful of issuers.

**Problem 2: on-chain collateral works one market at a time.** A prime broker posts one pool of collateral and it margins every market the broker reaches. The on-chain equivalent is a supply chain, deploy the token to the financing venue's chain, draw stablecoins there, bridge the proceeds to the chain where the position is, then reverse every leg to unwind. Each leg adds delay and operational risk.

**Resolution.** Combining Sig's settlement with Temple's liquidity, an asset holder can borrow against their collateral in any compatible pool and deploy the proceeds as a position on any chain.

The flow is:
1. Find the best venue to finance your collateral, on Canton or off.
2. Lock the collateral on Temple and, if the venue isn't Canton, mint a representation on the target chain.
3. Draw against it.
4. Swap the proceeds to the chain where you want the position.
5. Open the position.

Steps 2 and 3 settle atomically, as do steps 4 and 5.

Asset Virtualisation keeps the issuer functions on Canton and lets the financing counterparties sit on any chain. Redemption, KYC, whitelisting, issuance against the register, and liquidation all live on Canton. If a pool begins liquidating, it sends a message to Canton to initiate redemption, and Canton responds asynchronously, swapping the asset out for its underlying security.

The same collateral is then financed wherever stablecoin liquidity is deepest (e.g. Aave Horizon on Ethereum) and the proceeds deployed wherever the position is (e.g. Hyperliquid). For the desk, that is access to new pools of capital at any hour of the day, including when the security's home market is closed. For the issuer, it is one back office serving every chain and collateral mobility.

<!-- Proof read to here -->

## Specification

### 1. Objective

<!-- Enable users on Ethereum, Solana, and additional networks to deposit and withdraw native assets into Canton applications using standard wallets, with no bridging, wrapping, or new infrastructure on the user's side. Deliver an open-source Asset Virtualisation SDK that any Canton application can adopt. -->

To extend the reach of Canton contracts to external assets, liquidity and protocols.

### 2. Implementation Mechanics

**How Asset Virtualisation works:**

1. A user initiates a transaction on their home chain (e.g., ETH deposit from MetaMask)
2. Sig's decentralised MPC validator network detects the action and constructs the canonical Canton transaction
3. Sig signs and submits the transaction to the Canton application using chain-signature cryptography
4. The Canton application executes it as a standard Canton transaction: privacy, compliance controls, and settlement guarantees intact
5. Settlement flows back to the user's home-chain address via the same MPC path

No assets are locked. No wrapped tokens are minted. The home-chain asset remains real and unencumbered throughout. External liquidity is accessible to Canton applications in real time, without creating synthetic representations of those assets on Canton.

**Deliverables per milestone:** a live mainnet integration for each supported chain, and an open-source, DAML and TypeScript SDK with Temple as the reference implementation. We also can deploy on an EVM on Canton chain if we see adoption.

### 3. Architectural Alignment

Asset Virtualisation is designed around Canton's core properties:

| Canton Property | How Asset Virtualisation Preserves It |
|---|---|
| Sub-transaction privacy | External chain actions resolve to Canton identities; external observers see standard home-chain transactions, not Canton order flow |
| DAML transaction model | Sig converts external actions into standard Canton transactions; Canton applications process them identically to native transactions |
| Settlement guarantees | Assets remain on home chains; settlement paths are cryptographically bound; no custodial intermediary |
| No custody assumption | Sig's MPC holds policy-bound signing authority, not funds |

No Canton protocol changes are required. Applications opt in by calling Sig's Canton smart contracts to perform multichain actions; beyond adding those calls, existing application contracts are unchanged.

### 4. Backward Compatibility

No backward compatibility impact. All components are additive. Existing Canton participants and Canton protocol behaviour are unchanged.

### 5. Alignment with Canton Priorities

This proposal sits squarely in the Q2 2026 priority area of **App Building and Developer Experience**: it delivers interoperability across wallets, assets, and dApps, and removes the largest source of user friction, reaching Canton applications from standard external wallets. The open-source SDK is a shared ecosystem asset rather than a single-team integration, and adoption is the funded measure of success: Milestone 2 requires two applications live in production and Milestone 3 requires $1M of organic volume.

---

## Milestones and Deliverables

### Milestone 1: Mainnet - Ethereum + Open-Source SDK
- **Estimated Delivery:** July 1, 2026
- **Focus:** End-to-end Ethereum integration validated on Canton DevNet, then launched to production. Ethereum users can deposit and withdraw native assets into Canton applications via standard wallets. SDK publicly released.
- **Deliverables / Value Metrics:** Full Ethereum deposit and withdrawal flow demonstrated end-to-end on Canton DevNet, SDK integration complete and reviewed by Temple, live mainnet integration (Ethereum), open-source Asset Virtualisation SDK published with documentation.

### Milestone 2: Mainnet - Application Adoption
- **Estimated Delivery:** August 1, 2026
- **Focus:** The SDK proves out as ecosystem infrastructure: two Canton application development teams running Asset Virtualisation in production.
- **Deliverables / Value Metrics:** Two Canton applications live on Canton mainnet processing transactions through the Asset Virtualisation SDK

### Milestone 3: User Adoption
- **Estimated Delivery:** November 1, 2026
- **Focus:** Demonstrated production usage of the integrations delivered in the preceding milestones.
- **Deliverables / Value Metrics:** Minimum $1M in organic deposit and withdrawal volume within any 30-day window on Canton mainnet. Doesn't block Milestones 4 or 5.

### Milestone 4: Mainnet - Solana
- **Estimated Delivery:** November 1, 2026
- **Focus:** Native Solana asset deposits and withdrawals. Unlocks SOL and Solana-native assets and liquidity within Canton's institutional markets.
- **Deliverables / Value Metrics:** Live mainnet Solana integration; open-source Solana chain adapter; documentation; SOL and Solana-native assets accessible on Canton

### Milestone 5: Mainnet - Additional EVM Networks
- **Estimated Delivery:** December 1, 2026
- **Focus:** Expand to BNB and Polygon, broadening the native liquidity and asset classes accessible on Canton.
- **Deliverables / Value Metrics:** Live mainnet integrations for two additional EVM networks; SDK updated with new chain adapters; network-specific documentation published; demonstrated live use of a swap provider (e.g. Uniswap) and a yield provider (e.g. Aave) with virtualised assets on at least one supported chain

---

## Acceptance Criteria

The Tech & Ops Committee will evaluate completion based on:

- End-to-end deposits and withdrawals from each supported chain demonstrated live on Canton mainnet
- Open-source SDK published to a public repository with integration documentation
- Temple live with external-chain users executing transactions through the integration
- Two Canton applications live on mainnet using the integration by M2
- From M5 (Additional EVM Networks) onwards: demonstrated live use of a swap provider (e.g. Uniswap) and a yield provider (e.g. Aave) with virtualised assets on each supported chain
- Volume metric met at M3: $1M deposit/withdrawal volume within any 30-day window on Canton mainnet

---

## Funding

**Total Funding Request:** $400,000 USD equivalent in Canton Coin

### Payment Breakdown by Milestone

- Milestone 1 (Mainnet - Ethereum + SDK): $150,000 CC equivalent upon committee acceptance
- Milestone 2 (Application Adoption): $50,000 CC equivalent upon committee acceptance
- Milestone 3 (User Adoption): $100,000 CC equivalent upon committee acceptance
- Milestone 4 (Mainnet - Solana): $50,000 CC equivalent upon committee acceptance
- Milestone 5 (Mainnet - 2 Additional EVMs): $50,000 CC equivalent upon committee acceptance

### Volatility Stipulation

The project duration is under 6 months (July–December 2026). CC amounts for each milestone will be calculated at the 30-day moving average of CC/USD at the time of milestone submission. Should the project timeline extend beyond 6 months due to Committee-requested scope changes, remaining milestones will be renegotiated to account for significant USD/CC price movements.

---

## Co-Marketing

Upon each milestone release, Sig.Network will collaborate with the Canton Foundation on:

- Announcement coordination for each mainnet launch
- Joint technical blog post or case study demonstrating Asset Virtualisation in production
- Developer documentation and ecosystem promotion for the open-source SDK

Temple Digital Group, as the first production user of the integration, will provide independent co-marketing support at each milestone.

---

## Maintenance and Sustainability

The Asset Virtualisation SDK and the underlying validator network are part of Sig.Network's core product. Sig.Network operates and maintains both after the grant period, exactly as it does for the live Ethereum and Solana deployments today. The SDK is open source, so the Canton ecosystem retains the integration layer under an open licence regardless.

---

## Rationale

**Why Sig.Network:**

This integration needs a team that knows both worlds, and ours does: familiar with Canton and its clients, with a strong background in DeFi and cross-chain infrastructure. Sig.Network on Canton is the next deployment of a proven system, not greenfield research.

This grant de-risks and accelerates a delivery already underway. Asset Virtualisation is also the foundational layer for future Canton capabilities, including cross-chain access to tokenised assets and sequencer interoperability with other networks, but those are distinct workstreams. This grant funds the core primitive those capabilities will build on.

**Why this approach over a bridge:**

The wrapped-token bridge architecture introduces custody risk, a bridge attack surface, and synthetic representations of assets that institutional counterparties will not accept in place of the real thing. Asset Virtualisation introduces none of these. Assets remain real and on their home chains. Canton's synchronised settlement extends to external participants without Canton ever holding or wrapping their assets.

This is not a design preference. It is an institutional requirement, and why every institutional Canton application that follows will need the same primitive.

[^1]: Figures as of June 2026
