# tiny-chess-01 entry

A chess engine in **4,096 bytes of Python** (`main.py`), written for the
[make.fun](https://make.fun) bounty *"Build the strongest chess engine in 4 KB
of Python"* (`tiny-chess-01`).

`main.py` is a tiny loader plus an lzma-compressed payload, embedded as a
latin-1 string literal inside the file itself — it reads no other file. On
start it decompresses and runs the engine, which talks to the bounty env over
HTTP (`/game`, `/move`, `/resign`) at `$MAKEFUN_ENV_URL`.

## How it works

- **Board:** 0x88 mailbox, rebuilt from the FEN the env serves each move.
- **Search:** iterative-deepening negamax alpha-beta with a transposition
  table, MVV-LVA capture ordering, a quiescence search over captures, and a
  check extension.
- **Evaluation:** material + tuned piece-square tables, updated incrementally,
  plus a small tempo bonus.
- **Repetition:** the game's move list is replayed so repeated positions score
  as a draw — the engine avoids repeating when ahead and seeks it when worse.
- **Deterministic:** the search runs to a fixed node budget (no wall clock, no
  unseeded randomness), so a replay plays the same moves move-for-move.

Legal (checkmate/stalemate correct) and verified by perft against standard
positions; validated end-to-end against the competition kit's local env server.

## Building `main.py`

`main.py` is generated from the readable source by minifying and compressing it.
The engine is plain Python 3.13 with only the standard library.
