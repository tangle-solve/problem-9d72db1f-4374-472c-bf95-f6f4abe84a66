<!-- solve-problem:9d72db1f-4374-472c-bf95-f6f4abe84a66 -->
# Diversified baskets under correlated depegs

A [Tangle Solve problem](https://solve.tangle.tools/problems/9d72db1f-4374-472c-bf95-f6f4abe84a66).

## What is the problem?

A **composite stablecoin** is a token whose value is backed by a basket of other assets — typically other stablecoins (or tokenized claims). Let the basket hold quantities $q_i$ of constituents $i = 1, \dots, n$, each nominally pegged at 1. The token issues redeemable claims of 1 against the basket. If some constituent $i$ depegs to price $p_i < 1$ (or defaults, $p_i = 0$), the composite is insolvent unless reserves exceed liabilities.

The formal problem: design the **composition rule, redemption mechanism, and fee/rebalancing policy** such that:

1. **Run-resistance under correlated failure.** When constituents depeg *jointly* (correlated shocks, e.g., the same collateral base, the same regulatory event, or contagion through the composite itself), the basket's value should not fall below outstanding liabilities. How much over-collateralization $\lambda$ suffices given a joint distribution of depegs with correlation $\rho$?

2. **Adverse-selection robustness.** Arbitrageurs redeem only when redemption value exceeds market price of the composite ("toxic flow"), and deposit/unload *weak* constituents into the basket (Gresham dynamics). Design a mechanism so that rational traders cannot concentrate the basket into failing constituents — i.e., the composition must not admit a manipulation strategy that leaves residual holders with the worst assets.

Formal setting: game between a continuum of redemption/issuance traders and a protocol following a deterministic or state-contingent policy; state space = vector of constituent prices; objective = no-invariant-break (solvency invariant) plus incentive-compatibility (no profitable adversarial deposit/redeem sequence). The census record leaves the exact mechanism class unspecified; the reasonable interpretation is the AMM/vault-with-fees model of Klages-Mundt et al. (2020), generalized to correlated shocks. Note the ambiguity: "solve" could mean (a) an impossibility result, (b) an optimal mechanism with provable guarantees, or (c) tight bounds on required over-collateralization as a function of $\rho$.

## Why does it matter?

Composite stablecoins (e.g., basket-backed vault tokens, LSD-index stables, multi-collateral DAI-style systems) are live infrastructure holding billions; empirically, baskets concentrate into failures precisely when diversification is needed (correlated depegs, e.g., USDC/SVB March 2023). A mechanism with provable run-resistance and adverse-selection robustness would let composites survive tail events, and tight $\lambda(\rho)$ bounds would directly inform reserve policy for issuers and for central-bank/regulated digital-currency design. Conversely, an impossibility result would show diversification-based stability is structurally illusory, redirecting design toward insurance or endogenous collateral.

## What's already known?

- Klages-Mundt et al. (2020) formalize composite stablecoins, show that simple pro-rata redemption exposes the composite to adverse selection: arbitrageurs deposit depreciating constituents and redeem appreciating ones, so the basket drifts toward the worst assets; they propose fee-based mitigations but without optimality or correlation analysis.
- Classical bank-run / redeemable-fund theory (Diamond–Dybvig; Chen, Goldstein, Jiang on fund flows) establishes that redemption optionality plus NAV-sensitive flow generates runs; the "adverse selection in redeemable funds" literature is the direct off-chain analogue.
- Structured credit (CDO/copula literature, 2007–08) shows naive pairwise correlation dramatically understates joint tail risk — the exact failure mode for "diversified" stablecoin baskets under contagion; expected-loss bounds using marginal depeg probabilities underestimate joint loss by orders of magnitude under tail dependence.
- What's missing: no published mechanism for composites with a provable solvency invariant under a joint depeg model with explicit correlation/tail dependence, and no characterization of when Gresham-style composition drift is incentive-compatible to prevent. Existing fixes (fees, redemption gates, dynamic weights) are heuristic.

## What would count as solving it?

Any of the following, clearly scoped:

1. **Mechanism (positive result):** A composition + redemption rule with a theorem: for any depeg vector drawn from a distribution with pairwise correlations $\le \rho$ and marginals bounded, and any sequence of rational traders, the composite remains solvent and no profitable manipulation strategy exists. Include the required over-collateralization $\lambda(\rho)$.
2. **Impossibility (negative result):** A proof that for any deterministic mechanism without exogenous information advantage, either correlated shocks of some magnitude break solvability or adverse-selection profits exist — i.e., diversification gains are bounded regardless of design.
3. **Bounds:** Matching upper and lower bounds on $\lambda(\rho, \text{tail-dependence})$ such that a specified run equilibrium does not exist (e.g., in a global-games formulation).
4. **Empirical/simulation:** Calibration on historical depeg events (USDC 2023, UST 2022, protocol-specific) demonstrating a proposed mechanism's invariant holds under replayed shocks.

A referee would require: explicit model, explicit equilibrium concept (e.g., global games or dominant-strategy IC), and worst-case or distributional guarantees — not a heuristic fee schedule.

## How to start

1. **Read Klages-Mundt et al. (2020) Sections on composite designs and adverse selection first** — extract their exact mechanism model (pro-rata redemption, fee parameters) and formalize their adverse-selection example as a game; this becomes your baseline.
2. **Import the correlation machinery:** model constituent depegs as a joint distribution with copula/tail-dependence; compute basket loss quantiles vs. the naive sum-of-marginals bound; this directly gives necessary over-collateralization levels and shows where heuristics fail.
3. **Formalize the attacker:** a price-aware trader choosing deposit/redeem sequences against a composition rule — this is a dynamic game; start with a two-constituent, two-state toy model where you can prove whether any fee/redemption-threshold policy is manipulation-proof (expect an impossibility here, then see what observability — e.g., oracles — buys you).
4. **Main obstacle:** the mechanism has no informational advantage over traders (prices are public), so classic no-exploitation arguments fail; the promising escape is making redemption rights state-contingent (gate/dilution à la bancor-style or ERC-4626-with-loss-realization), which trades run-resistance against attractiveness. That tradeoff curve is the paper.

## Key references

- Klages-Mundt, J., et al. (2020). *Stablecoins 2.0: Economic Foundations and Role in Short-Term Credit Markets*. arXiv:2006.12388. https://arxiv.org/abs/2006.12388 — Formalizes stablecoin design space including multi-asset/composite backing, identifies fragility and stabilization levers; the cited origin of this problem.
- Diamond, D. & Dybvig, P. (1983). *Bank Runs, Deposit Insurance, and Liquidity*. JPE 91(3). — Canonical run model; the redeemer-coordination problem any composite inherits.
- Chen, Q., Goldstein, I., Jiang, W. (2010). *Payoff Complementarities and Financial Fragility: Evidence from Mutual Fund Outflows*. JFE 97(2). — Empirical/theoretical analogue of adverse-selection-driven redemptions in redeemable baskets.
- Li, D. (2000), *On Default Correlation: A Copula Function Approach*. Journal of Fixed Income 9(4). — The copula toolkit for joint tail risk; directly transferable to correlated depeg modeling. (Verify relevance of any post-2008 critique papers before leaning on Gaussian copulas — they understate tail dependence, which is the core trap here.)
- March 2023 USDC depeg event (Circle/SVB) — the canonical empirical correlated-shock case study; no single paper, use primary market data. (verify datasets)

## Warnings

- **Do not model depegs as independent.** The entire point is that naive diversification math fails under joint tail events; independence assumptions will trivialize the problem and reproduce pre-2008 structured-credit errors.
- Ambiguity in formulation: the census record does not fix the mechanism class (pro-rata vs. FIFO vs. weighted redemption; static vs. rebalancing basket). State your choice explicitly and check robustness to it.
- Oracle reliance is a hidden assumption: mechanisms that "observe" constituent quality implicitly assume trustworthy, timely price feeds — during the relevant crises these lag or fail.
- Beware confusing solvency with price stability: a composite can hold its peg while insolvent (no one redeems), and vice versa. Define the invariant precisely (accounting solvency under adversarial redemption ordering).
- Gresham-drift defenses based on fees alone are known to be circumventable when the fee is smaller than intra-basket spreads during stress; don't present a fee schedule as a solution without a game-theoretic proof.

---
*`BCGT-000334` · unverified*

## Acceptance guidance

Any of the following, clearly scoped:

1. **Mechanism (positive result):** A composition + redemption rule with a theorem: for any depeg vector drawn from a distribution with pairwise correlations $\le \rho$ and marginals bounded, and any sequence of rational traders, the composite remains solvent and no profitable manipulation strategy exists. Include the required over-collateralization $\lambda(\rho)$.
2. **Impossibility (negative result):** A proof that for any deterministic mechanism without exogenous information advantage, either correlated shocks of some magnitude break solvability or adverse-selection profits exist — i.e., diversification gains are bounded regardless of design.
3. **Bounds:** Matching upper and lower bounds on $\lambda(\rho, \text{tail-dependence})$ such that a specified run equilibrium does not exist (e.g., in a global-games formulation).
4. **Empirical/simulation:** Calibration on historical depeg events (USDC 2023, UST 2022, protocol-specific) demonstrating a proposed mechanism's invariant holds under replayed shocks.

A referee would require: explicit model, explicit equilibrium concept (e.g., global games or dominant-strategy IC), and worst-case or distributional guarantees — not a heuristic fee schedule.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/9d72db1f-4374-472c-bf95-f6f4abe84a66?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000334.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000334)
