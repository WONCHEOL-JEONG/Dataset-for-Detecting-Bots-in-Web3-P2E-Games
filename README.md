# Dataset-for-Detecting-Bots-in-Web3-P2E-Games

Dataset accompanying the paper **"Who Plays to Earn? Detecting Bots in Web3 Games via On-Chain Transactions Analysis"**.

This document describes the labeling dataset computed for **all 57,879 accounts** across three Play-to-Earn games — Alien Worlds (WAX), DeFi Kingdoms (DFK Chain), and Splinterlands (Hive). The methodology is described in **§3.1 (Detection Feasibility)** and **§4.4 (Ground Truth Creation)** of the paper.

> **Important.** The heuristic scores/labels are *reference material for the annotators* only. They never select, filter, or pre-label accounts, and the final ground truth is 3,551 bots / 54,328 humans. See [Evidence of non-circularity](#direct-evidence-that-the-heuristic-did-not-determine-the-final-labels) below.

## How the labels were produced

Ground truth was created in two stages. Stage 1: Heuristic, Stage 2: Annotators.

### Stage 1 — Heuristic reference scoring

1. **Population.** Every account with at least 10 on-chain actions during the 31-day collection window (March 1 – April 1, 2026) across the three games: 57,879 accounts in total.
2. **Four independent rules.** Each account is tested against four threshold rules, each drawn from a *different* behavioral dimension so that no single dimension can dominate the verdict:

   | Rule | Dimension | Condition |
   |---|---|---|
   | H1 | Activity volume | transaction count > 100 in the window |
   | H2 | Behavioral diversity | action-type entropy < 0.5 bit |
   | H3 | Temporal regularity | 24/7 activity ratio > 0.5 |
   | H4 | Volume regularity | CV of daily volume < 0.2 |

3. **Scoring.** `heuristic_score` = number of satisfied rules (0–4). `candidate_label` is derived deterministically from the score: `0` (score 0), `-1` (score 1–2, ambiguous zone), `1` (score ≥ 3, strong candidate).
4. **Role.** The scores and labels are passed to the Stage-2 annotators **purely as reference material**, and every account proceeds to annotation regardless of its score or label.

### Stage 2 — Independent annotator validation
### (Annotator 1 labeling, Annotator 2 labeling → Discussion → Final ground truth)

Two annotators independently labeled each account by examining its **raw transaction history and full 32-feature behavioral profile**, with the Stage-1 scores and labels visible only as supporting reference; disagreements were resolved by discussion until consensus. A human may legitimately be extreme on one dimension (e.g., a hardcore player's near-24/7 activity) but not on several at once, whereas a bot cannot normalize all dimensions without forfeiting the throughput that makes automation profitable (paper §3.1) — a bot verdict therefore requires **mechanically extreme behavior on multiple independent dimensions**.

## How this file is organized

One CSV, one row per account (57,879 rows × 8 columns), covering the full population of all three games.

| Column | Type | Description |
|---|---|---|
| `account` | str | On-chain account identifier (public data; pseudonymous by design of the underlying chains) |
| `game` | str | `alien_worlds` \| `defi_kingdoms` \| `splinterlands` |
| `h1` … `h4` | bool | Per-rule outcomes as defined above |
| `heuristic_score` | int | Number of satisfied rules (0–4) |
| `candidate_label` | int | `1` (score ≥ 3) · `0` (score 0) · `-1` (score 1–2, ambiguous zone prioritized for careful review) |

### Heuristic statistics

| Quantity | Value | Paper reference |
|---|---|---|
| Score 0 (`candidate_label = 0`) | 50,229 (86.8%) | §3.1: "86.8% … satisfy none" |
| Ambiguous 1–2-rule zone (`-1`) | 6,855 (11.8%) | §3.1: "11.8% in the ambiguous 1–2-rule zone" |
| Score ≥ 3 (`candidate_label = 1`) | 795 (1.4%) | §3.1: "1.4% satisfy at least three" |
| Per-game account counts | 54,298 / 3,257 / 324 | Table 4 |

Per-rule firing rates: H1 = 1,136 (2.0%) · H2 = 6,696 (11.6%) · H3 = 3,270 (5.6%) · H4 = 2,418 (4.2%).

## Direct evidence that the heuristic did not determine the final labels

If the annotators had merely recovered the heuristic rules, the strong candidates of this file (`candidate_label = 1`) would coincide with the final bot labels. In practice they diverge substantially:

| Game | Strong heuristic candidates (score ≥ 3, this file) | Final annotator-confirmed bots (paper, Table 4) |
|---|---|---|
| Alien Worlds | **21** | **3,440** |
| DeFi Kingdoms | **756** | **107** |
| Splinterlands | **18** | **4** |

- **Under-flagging (Alien Worlds):** the heuristic surfaces only 21 strong candidates, yet the annotators confirmed 3,440 bots — the overwhelming majority of confirmed bots were identified from raw behavior, not from the rules.
- **Over-flagging (DeFi Kingdoms):** the heuristic flags 756 strong candidates, yet the annotators confirmed only 107 bots. This is consistent with the paper's observation (§5.3) that DeFi Kingdoms' limited action vocabulary compresses action-type entropy for *humans* as well, causing rule H2 to fire broadly; the annotators, judging the full profile, rejected most of these candidates.

This asymmetry is direct, quantitative evidence for the paper's circularity defense (§4.4, *Addressing Potential Circularity*): the ground truth reflects annotator judgment over raw on-chain behavior rather than a simple recovery of the heuristic rules.

## Citation
If you use this dataset in your research, please cite the following paper:
Update soon

```bibtex
@inproceedings{00,
  title     = {00},
  author    = {Anonymous},
  booktitle = {00},
  year      = {2026},
  note      = {00}
}
```
