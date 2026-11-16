# tiny-chess-01

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/mrsaepudin40/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/mrsaepudin40/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

The strongest chess engine that fits in 4 KB of Python. `main.py` is the whole
engine — 4,078 bytes, standard library only, no model calls, no other file.

## Engine

- **Board:** 0x88, incremental 64-bit Zobrist hash.
- **Search:** iterative deepening, alpha-beta with **PVS at the root** (first
  move full window, later moves null-window with a full-window re-search on
  fail-high), quiescence on captures, promotions and check replies, and a
  transposition table with killer moves.
- **Ordering:** TT move, then captures by MVV-LVA, then killers; the root
  keeps the previous iteration's order on ties (stable sort).
- **Eval:** material (P100 N320 B330 R500 Q900), piece-square tables, bishop
  pair, doubled/isolated/passed pawns, king shield, and a +10 tempo bonus.
- **Determinism:** a **fixed budget of 60,000 nodes per move** — no wall
  clock, no unseeded randomness — so a replay is byte-identical. About
  1.5 s per move, well inside the 10 s move limit.
- **Packing:** the engine is minified (comments and docstrings stripped,
  identifiers renamed to single letters) and embedded as an lzma payload in
  latin-1, so `main.py` stays under the 4,096-byte cap.

## Run it

`main.py` talks to the house env over HTTP alone:

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:8080 python3 main.py
```

It plays the gauntlet game by game (`POST /game`, `/move`, `/resign`) until
the env reports the trial done.

## Verify it

- Perft from the start position: depth 4 = 197,281 nodes (and kiwipete,
  position 3–6 all match the known counts).
- Mate-in-1 is found, a free queen is taken, and no piece is hung in the
  opening.
- Practice trial-0 (packed, against the local gauntlet): see the commit
  history for the graded Elo.

---

<sub>Entered on [make.fun](https://make.fun/?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
