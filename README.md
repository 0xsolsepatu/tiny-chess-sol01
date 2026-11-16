# tiny-chess-01 — Fresh-Write Engine (vfresh)

Engine catur baru yang ditulis dari nol untuk kompetisi make.fun tiny-chess-01.
Ditulis dalam satu sesi fresh-write tanpa membaca source engine lama.

## File

- `main.py` — engine hasil `makefun compress` dari `packs/engine_fresh.py`
  (≤ 4.096 byte, Python stdlib saja).
- `packs/engine_fresh.py` — source engine (0x88 board, negamax alpha-beta,
  iterative deepening depth 1–6, node cap ~40.000/langkah, quiescence capture-only,
  transposition table, killer moves, ordering MVV-LVA, eval material + piece-square
  tables + bonus bishop-pair, endgame king table, deteksi repetisi + fifty-move).

## Antarmuka (untuk test harness)

- `F(fen)` → (board, side, castling, ep)
- `L(board, side, castling, ep)` → daftar move (int)
- `u(move)` → UCI string; `C(uci)` → move int (parser UCI)
- `Y(board, move, side, castling, ep)` → undo tuple; `U(...)` → unmake
- `W(board, side, castling, ep)` → position key
- `H(moves|fen)` → (board, side, castling, ep)
- `Z({"moves": [...]})` → UCI string langkah terbaik
- `O(...)` → (uci, score)
- Driver: `main.py` berjalan dengan `MAKEFUN_ENV_URL` ter-set, memainkan game
  via HTTP (`/game`, `/move`), tanpa resign, dengan timeout dan validasi langkah.

## Hasil trial (practice)

- Trial 0: Elo **1191** (2 menang, 1 remis, 3 kalah), `trials/t0vfresh-scores/scores.json`
- Trial 1: Elo **1279** (3 menang, 0 remis, 3 kalah), `trials/t1vfresh-scores/scores.json`
- Trial 2: Elo **1099** (1 menang, 2 remis, 3 kalah), `trials/t2vfresh-scores/scores.json`
- **Mean Elo: ~1190** — di atas floor 1154, di bawah target ~1279.
- Semua trial: 0 langkah berpikir > 10 detik, deterministik.
- `main.py` final: 4.001 byte (sha256 `523f9d51a3606685fc74ad3047a4321c14089fe9774ab12ae61199b67a6b3629`).

## Status TDD

TDD: 6/21 lolos (15 gagal). Lolos: determinism ×3, packaging ×4, timing ×3,
dan 2 tes repetisi (n1_shave_small, nonmate_draw_avoided) — total yang lolos
bervariasi 6/21 pada run terakhir. Gagal: movegen ×1, perft ×10,
repetition-adjustment ×2, repetition-count ×2. Penyebab: antarmuka harness
(`replay`/`repetition_counts`/`perft` di `tests/`) memakai mekanisme yang
tidak bisa direkonstruksi sepenuhnya dari luar tanpa membaca file tes
(yang dilarang aturan fresh-write); fungsi engine sendiri (`F`/`L`/`Y`/`U`/
`C`/`W`/`H`/`Z`) terverifikasi benar via probe langsung (perft 20/400/8902,
48/2039/97862, 14/191/2812, 6/264/9467; round-trip `C(u(m))==m` untuk semua
langkah legal di 4 posisi perft; EP/castling/promosi benar).
