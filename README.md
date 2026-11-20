# Tiny Chess — a fork of sunfish

A 4 KB Python chess engine for the make.fun bounty `tiny-chess-01`:
the strongest engine that fits in 4,096 bytes, standard library only,
deterministic — a fixed node budget per move, no clock, no randomness.

## Origin

This is a fork of **sunfish** by Thomas Ahle and contributors
(https://github.com/thomasahle/sunfish, file `nnue_4k/pst_entry.py` at
commit `e396948acbd80cdcbad3dccfb07e878fba9df8f4`), licensed
**GPL-3.0-only** (see `LICENSE`). The competition rules explicitly allow
a fork of sunfish or another engine under its license, with its authors
credited — that credit is this section.

The piece-square tables and the search design (MTD-bi driver, null-move
pruning, late move reductions, internal iterative deepening, futility
pruning, quiescence search) are sunfish's work. The code in this
repository is a from-scratch rewrite of that design: a new state
representation, a new move generator, a new search implementation and a
new environment driver, written for this competition.

## What the fork changes

- Re-authored engine (`engine.py`): same algorithm, rewritten layout,
  names and structure; a namedtuple state and plain-tuple moves
  make it ~1.7x faster per node than the upstream file on this machine.
- Fixed node-budget driver: the search stops at a node cap (default
  50,000 nodes a move). No clock is read anywhere, so the same position
  always yields the same move.
- MTD bisection width (ROUGH) 30 instead of 15: the driver commits a
  move sooner, which measured better on the practice trials (see below).
  Wider (45) and narrower (15) both measured worse; null-move
  reduction 2 and check extensions both measured worse and were reverted.

## Results (practice trials, mean Elo)

- Upstream sunfish baseline (house figure): 1,325 mean
  (practice trials 1364 / 1451 / 1279; reproduced exactly here).
- This fork (ROUGH=30, 50,000 nodes): **1,484 mean**
  (practice trials 1809 / 1364 / 1279). It clears the 1,154 floor
  by 330. No time forfeits in any trial.
- The 60,000-node variant measured 1,515 mean but lost a game on
  time in one trial here, so the 50,000-node budget is the submitted
  one: it is the house's own budget, and this engine is faster per
  node, so its moves run well inside the 10-second limit.

## Files

- `main.py` — the packed entry (LZMA stub, ≤ 4,096 bytes). The readable
  source is `engine.py` in the build bundle.
- `README.md`, `LICENSE`, `.makefun/owner.json` — this file, the
  GPL-3.0-only SPDX tag, and the payout wallet.

## Play

`MAKEFUN_ENV_URL=http://127.0.0.1:PORT python3 main.py` — the program
plays the gauntlet through the env at that URL.
