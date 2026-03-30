# Buyout Game Docs

`buyout_game` is the benchmark repo for a money-transfer social-strategy game derived from the `survivor2` project family. The benchmark is intended to study how language models reason about incentives when they have different starting balances, different expected finish payouts, and the ability to move money between players during the game.

## Canonical Documents

When docs disagree, use this order:
1. `GOALS.txt`
2. `AGENTS.md`
3. `DOCS.md`
4. `README.md`
5. `run.txt`

`GOALS.txt` captures the benchmark intent and open design choices. `DOCS.md` is the operator/architecture reference. `README.md` is the public-facing overview. `run.txt` is only scratch/runbook material.

## Current Project State

This repo is intentionally in a scaffolded transition state:
- the repository structure and most runner/analysis code were copied from `/mnt/r/survivor2`,
- the top-level docs and path conventions have been rewritten for `buyout_game`,
- `model_info.py` was replaced with the requested version from `/mnt/r/extended_connections`,
- generic progress/logging helpers were imported from `/mnt/r/debate`.
- the core gameplay engine now runs the v1 buyout rules inside the inherited survivor-style elimination loop.

The inherited files still use elimination-game naming:
- `elimination_game.py`
- `elimination_game_helpers.py`
- `elimination_game_phases.py`
- `elimination_game_voting.py`
- `elimination_game_finals.py`

Treat the naming as inherited scaffolding, not as proof that the old protocol is still active.

## Paths And Layout

- Repo root: `/mnt/r/buyout_game`
- Data root: `/mnt/r/buyout_game-data`
- Published root: `/mnt/r/published/buyout_game`
- Benchmark manifest: `/mnt/r/buyout_game-data/manifests/benchmark_manifest.json`
- Mirrored pack plans: `/mnt/r/buyout_game-data/match_packs/`
- Frozen baseline artifacts: `/mnt/r/buyout_game-data/baselines/`

Use [project_paths.py](/mnt/r/buyout_game/project_paths.py) for centralized repo/data/published roots. The repo should contain source code, prompts, tests, and checked-in docs. Generated logs, CSVs, charts, reports, manifests, and publishable outputs should live under the data root or explicit published root.

## Scoped Report Snapshots

Scoped report snapshots should include a `selection_manifest.json` that records the exact mirrored-pack membership used to build that scope. Treat that manifest as the canonical provenance surface for questions like “which packs were in this report?” and “which new packs can be appended without rerunning older work?”

When canonical analytics aggregate mirrored packs, the unique pack identity must come from the paired `match_pack_game_ids` carried in cleaned `final_results`, not just the human-readable `match_pack_id`. Pack labels intentionally reset across separate scoped runs, so pack-id-only aggregation can silently collapse distinct packs from different runs.

## Family Preferences Carried Over

These preferences come mostly from `survivor2`'s `AGENTS.md`/`DOCS.md`, with some stronger utility conventions imported from `debate` and `sycophancy`.

- Keep docs aligned with code in the same change set when behavior changes.
- Prefer Linux/WSL assumptions. Do not add speculative Windows/macOS abstraction layers without a real need.
- Keep path logic centralized instead of scattering new absolute paths across scripts.
- Prefer stable CLI shapes when they fit: `--port`, `--workers`, `--overwrite` or `--force`, `--range`, `--subset-file`, and explicit output-scope selectors.
- Treat generation/evaluation scripts as resumable by default when feasible; require an explicit overwrite/force flag for destructive rebuilds.
- Preserve raw artifacts when downstream cleaned or aggregate layers are rebuilt.
- Prefer additive reruns, repairs, filtering, and side-by-side scopes over deleting old benchmark outputs.
- Fail loudly on missing or malformed benchmark-critical inputs instead of silently skipping them.
- Validate cached outputs before treating them as complete.
- Prefer runtime configuration over import-time side effects when adding new behavior.
- Prefer `pathlib.Path`, explicit UTF-8 encoding, and shared helper utilities over ad hoc path/string handling.
- Keep public publishing as an explicit stage rather than using working data directories as the publication surface.

## Router And Execution Policy

Shared benchmark-family policy still applies here:
- `8006` is the fast router and is usually best for smoke tests, quick validation, smaller batches, and Gemini runs.
- `8040` is the batch router and is usually best for larger production runs when throughput/cost matter more than latency.
- Gemini often has better turnaround on `8006`.
- Gameplay routing and broker concurrency now use the checked-in parallel roster at `/mnt/r/buyout_game/rosters/participants_parallel.csv`, copied from the persuasion/debate benchmark family. For models listed there, the broker resolves router port and per-model global concurrency from that file at runtime rather than from a single process-wide base URL.
- `BUYOUT_GAME_BASE_URL` remains the fallback router for ad hoc models that are not listed in the parallel roster and for smaller non-game scripts that still use a single shared client.
- `main.py`, `run_match_packs.py`, and `run_preflight.py` now also accept `--port` as an explicit one-run override that forces all benchmark gameplay calls onto one router without editing the checked-in parallel roster. Use that when you want an all-`8006` or all-`8040` run for debugging, smoke tests, or controlled operational comparisons.
- Use long HTTP timeouts for LLM-facing jobs. Quiet periods on `8040` are normal.
- The checked-in client defaults now use very long router-aware timeout budgets. On `8006`, the defaults are `60s` connect, `3h` read, `5m` write, `5m` pool, and `4h` broker reply. On `8040`, the defaults stretch further to `6h` read and `7h` broker reply. Override them only deliberately with `BUYOUT_GAME_*_TIMEOUT_SECONDS`.
- Gameplay LLM calls now also use persuasion-style capped exponential backoff for clearly retryable upstream proxy overload/rate-limit failures, including `429` or `529` envelopes, router payloads carrying proxy codes `1302` or `1305`, and obvious transient transport disconnect markers such as remote-protocol resets or incomplete chunked reads. The current safe defaults are `6` retries with `10s` base backoff capped at `120s`; other gameplay LLM failures still surface immediately as fatal benchmark errors.
- Router JSON error envelopes like `Error: {"error":...}` are now also treated as fatal benchmark errors instead of being published as normal in-game speech, and free-text gameplay phases strip leaked `think`/self-correction artifacts before anything is written into the public or tie-break logs.
- `main.py` now requires both the global LLM broker and the global logger broker to start successfully; it no longer silently downgrades to local semaphores or direct global-score writes if broker startup fails.
- `main.py` now also tears both brokers down from a `finally` path if an unexpected exception escapes the executor loop, so failed runs do not leave broker processes behind until parent exit. Its interrupt handling now mirrors the preferred drain-then-abort pattern: one interrupt stops new submissions and drains already-submitted games, while a second interrupt force-aborts by shutting down the worker pool and terminating or killing any remaining child workers before broker cleanup.
- If the global logger broker cannot append a completed game result to `global_scores.jsonl`, it now attempts to preserve that lost row in a sibling dead-letter file such as `/mnt/r/buyout_game-data/global_scores_failed.jsonl` so score-write failures are recoverable instead of disappearing silently.
- Every gameplay `llm_action` diagnostic now carries a stable benchmark request id in both its start and end events. Fatal end events also preserve the raw fatal error text in `fatal_error_text`, and benchmark calls now forward request metadata into the shared `/mnt/r/connections` router so archived `/mnt/r/llm_logs/...` JSON files can be correlated back to the originating benchmark action by request id.
- Narrow endgame prompt/response observability is now written to per-game sidecars under `/mnt/r/buyout_game-data/endgame_observability/`. These JSONL sidecars capture the exact prompt body, raw model reply, and parsed/published outcome for `tie_break_statement`, `final_two_negotiation`, `final_two_settlement`, `final_two_buyout`, `final_two_statement`, `final_two_bonus_vote`, and `final_two_bonus_vote_repair`, without bloating the main raw game log.
- Worker-returned gameplay results now cross the `ProcessPoolExecutor` boundary as lightweight serialized seat summaries rather than live `LLMPlayer` objects. The endgame observability sink is still cleared on the live seat objects before game exit, but the parent runners no longer depend on pickling the full in-memory player state just to print completion status.
- Abort reasons triggered by benchmark-owned LLM failures now include both the seat and model name, for example `Private DM error from P2 (glm-5)` rather than only `P2`, so live runner output and later log/status inspection point directly at the failing model.

For expensive multi-model runs, prefer the wrapper pattern used in `debate` and `sycophancy`:
- keep the underlying worker script simple and resumable,
- put mixed-port orchestration in a separate wrapper,
- use an explicit run-plan file with `model`, `port`, `workers`, and optional `enabled`,
- centralize parent logging and throttle noisy child progress lines,
- prefer graceful drain-then-abort interrupt handling, and make sure a force-abort path explicitly tears down child workers instead of leaving orphaned processes behind.

The imported survivor2 scaffold does not yet follow that pattern everywhere. Use it as the direction for future refactors, not as permission to silently rewrite benchmark behavior.

## Model Metadata And Roster Rules

- `main.py` still uses `MODEL_WEIGHT_CONFIG` as the active gameplay roster, but the live roster is now a local snapshot of the current `/mnt/r/debate` active participant roster rather than the older inherited scaffold list.
- `/mnt/r/buyout_game/rosters/participants_parallel.csv` is now the checked-in source of truth for the active roster's per-model router port and broker concurrency cap. The current values intentionally match the persuasion benchmark's parallel roster.
- Benchmark-owned model calls now use a fixed `temperature=1.0` invariant across gameplay, preflight, and the repo's direct prompt-evaluation helpers rather than ad hoc per-script temperatures.
- `model_info.py` is the central source of display names, categories, colors, and highlight flags.
- `config.py` still contains `SUPPRESSED_MODELS` for chart/report suppression, plus legacy fallback concurrency overrides for ad hoc models outside the checked-in parallel roster; suppression is a presentation choice, not a data-deletion instruction.
- Historical outputs should remain analyzable even as the active roster changes.

## Buyout-Game Protocol Targets

The intended benchmark behavior is captured in `GOALS.txt`. The current v1 state is:
- each player receives a random starting balance,
- starting balances come from a fixed total pot with a per-player floor,
- the extra balance above that floor is split with a Gamma/Dirichlet-style draw controlled by `STARTING_BALANCE_CONCENTRATION` (currently `6.0` for flatter, less noisy starts),
- if balances are private, the engine can also reveal a coarse public round-0 hint with bucket counts for the table's starting-balance mix while still hiding seat-level balances,
- each finish position pays a prize from a public 3-by-3 regime bank:
  prize-pool ratios `0.7x`, `0.9x`, `1.1x` crossed with spreads `ultra_top_heavy`, `top_heavy`, and `moderate`,
- the prize ladder is strictly ordered from first to last and is publicly known at the start,
- players can transfer money during each round,
- v1 uses public balances and private transfers,
- the main round public statement phase is sequential with randomized speaker order each round in standalone games, so later speakers can react to earlier same-phase statements; official mirrored packs now precompute complementary round-by-round public-statement orders across the linked games to balance early/late speaking exposure,
- each active seat gets one transfer-phase LLM response per round and may list at most 2 transfers to 2 distinct recipients, with at most one transfer line per recipient,
- private networking now uses direct DM selection instead of preference ranking / global matching,
- in each private DM subround, outbound DMs are simultaneous: all seats choose their target from the same frozen pre-subround state, then all DMs are delivered at once, then reply choices are collected from that same delivered-DM state before any reply is logged; every active seat may send one outbound DM or choose silence (NONE), and seats with non-mutual inbound DMs may reply to up to 2 of them,
- private DM / reply parsing now tolerates one narrow malformed-tag pattern seen in live runs, where a tag-start line is missing the opening `<` (for example `message>...`), and if a private DM or private reply is still invalid after parsing, the engine issues one format-only repair retry before treating the action as failed; successful reply parses with extra invalid blocks are still salvaged without triggering a repair retry,
- the canonical 8-player private-DM schedule is 4,4,3,3,3,2 across the six elimination rounds,
- transfers are simultaneous: all seats decide from a frozen pre-phase balance snapshot, then all transfers are applied at once; a sender may target at most 2 distinct recipients with one transfer line per recipient, and the sender's outgoing budget is clamped to the snapshot balance so received money from the same phase cannot be re-spent; transfers are binding, irreversible, integer-only, and cannot create debt; in the current canonical setup there is no separate per-transfer cap beyond current balance; when transfers are private, directly involved recipients also see only their own applied transfer events plus the sender's short transfer-phase message, while the sender alone sees the whole-phase transfer summary,
- tie-break statements are simultaneous: all tied seats write from the same frozen history state before any same-phase statement is revealed,
- the final two now uses a hostile-takeover ending with explicit post-resolution economics disclosure and a short public-appeal phase: the finalists get 2 exclusive simultaneous-exchange negotiation rounds, then simultaneous structured settlement submissions that execute when both name the same 1st-place finalist and the designated 1st-place finalist's maximum willingness to pay overlaps the designated 2nd-place finalist's minimum willingness to accept, with execution at the 2nd-place finalist's minimum acceptable amount and payment capped by min(current balance of the designated 1st-place finalist, base_prize_gap); the binding prompt now asks finalists for a role-neutral `<my_settlement_amount>` field, while the parser still accepts the legacy `<payment_from_first_to_second>` tag during the transition, prefers `<my_settlement_amount>` if both amount tags are present, and still rejects malformed or out-of-range settlements so the finale falls through to the sealed-buyout stage when parsing fails; after that comes a capped sealed-buyout fallback if no such settlement executes, then a public disclosure of who got 1st, who got 2nd, the executed settlement payment or winning bid, and the post-resolution finalist balances, then one short simultaneous public appeal from each finalist, and finally the already-eliminated jurors reallocate a bounded reserved bonus pool between the two finalists without changing placement; if fallback bids and pre-bid balances both tie, the tied finalist with more cumulative earlier-round elimination votes takes 2nd place,
- final-two jury bonus votes are collected from a frozen post-resolution history state after those public appeals, jurors are prompted to reply with a single finalist seat label, the parser accepts any unambiguous finalist mention and may issue one repair clarification on ambiguity, invalid bonus votes are still logged as skipped rather than defaulted to a finalist, the reserved bonus pool now uses `J = min(max(0, G - 1), floor(G / 3))`, and the valid-vote split is shrunk back toward 50-50 in proportion to turnout with half-up rounding applied in resolved 1st/2nd order; there is no separate juror side-stake payout,
- during final-two jury bonus prompt rendering, the generic active-seat and visible-balance context is pinned to the two finalists so jurors see a consistent two-finalist endgame state even though placement is already fixed, and the juror recap reuses the public resolution summary without adding extra terminal punctuation,
- final-two prompts now keep the dedicated payoff / resolution sections as the source of exact endgame numbers, while the `Current Summary` block stays compact and phase-local instead of restating the same financial context a second time,
- the normal private-DM cap is 90 words, finalist-only hostile-takeover negotiation messages now have a 100-word hard cap with 60-90 word target guidance, tie-break statements now have a 110-word hard cap with 70-100 word target guidance, and finalist public appeals now have a 130-word hard cap with 70-110 word target guidance so endgame appeals are less likely to clip mid-thought,
- player-facing prompts explicitly surface vote visibility (`public` or `private`) alongside balance/transfer context for the current run,
- player-facing public prompts explicitly tell each seat its speaking position in the sequential public phase, while prompt seat lists and valid-target lists use stable seat order (`P1`, `P2`, ...),
- the shared player rules text now describes public-statement order accurately for both standalone games and official mirrored packs instead of implying that every run uses only per-round RNG order,
- when starting balances are public, the shared starting-balance hint now points players back to the public starting-balance record in history/recap rather than incorrectly pointing at current visible balances later in the game,
- the shared transfer/voting prompt text now makes five mechanics more explicit: post-transfer public balance deltas are net of all simultaneous sends and receives rather than gross transfer totals, self-votes or other unlisted vote targets are invalid/skipped rather than silently interpreted, eliminating a seat does not confiscate or redistribute that seat's balance to the survivors, transfers/eliminations never change the published prize ladder or total prize pool, and a seat with 0 balance remains fully active until eliminated even though it cannot send positive transfers and would currently have a 0 fallback buyout cap,
- prompt edits should improve objective clarity, mechanics clarity, and economic-state visibility without prescribing a benchmark-approved style of play; avoid strategy-coaching language that tells the model how it should frame public speech, negotiate privately, vote, or transfer beyond the actual game rules and current game state,
- the checked-in public prompt should stay neutral in that same way; it should not contain generic coaching lines like telling the model to focus on deals, leverage, or threat management,
- the human-readable snapshot renderer should preserve current final-two appeal events from raw logs; finalist public appeals are part of late-game QA and should not disappear just because the raw event name changed from older `final2` naming,
- prompt-history retention is now status-aware: active seats are capped at 960 visible lines / 96,000 characters with current-round priority, while eliminated seats keep their full visible history without truncation so jurors do not lose early-game strategic context,
- canonical scoring is final wealth,
- exact equal final-wealth ties are tie-aware rather than broken arbitrarily: tied seats share one wealth rank, all tied wealth winners are exported in `winners*`, and tied seats receive the average of the occupied partial-point slots,
- finish order is still logged separately and still matters because it determines finish prizes.

Current analytics semantics:
- scoreboards, canonical ranks, partial scores, rank-distribution charts, and rank-1 counts are wealth-based,
- complete mirrored 2-game match packs are now the canonical cross-game rating unit for leaderboard updates; standalone games remain visible in logs and diagnostics but are excluded from canonical leaderboard layers by default,
- `scoreboard_ts.csv` and `scoreboard_bt.csv` now report `games` and wealth-winner counts over the underlying valid games even when mirrored packs collapse into one rating unit for rating updates; both files also carry `rating_units` so pack-collapsed sample counts remain visible,
- the canonical published leaderboard is now `scoreboard_bt.csv` (Bradley-Terry over wealth-derived pairwise outcomes), while `scoreboard_ts.csv` remains an official secondary uncertainty-aware view on the same wealth-based results,
- the main rating updater, rating-input loader, pairwise wealth-partial stats, and scoreboard animation layer now share the same `RATING_EPSILON` tie tolerance so tiny floating-point deltas do not create contradictory tie handling across leaderboard layers,
- when a cleaned log contains multiple `final_results` entries, the latest `final_results` entry is treated as authoritative across canonical ratings, adjusted / excess leaderboard layers, and downstream stats generation so append-only repaired logs resolve consistently, and `fix_jsonl_logs.py` now also rewrites cleaned outputs to keep only that repaired latest `final_results` block,
- `fix_jsonl_logs.py` repairs raw logs from `/mnt/r/buyout_game-data/logs` by default, writes repaired outputs under `/mnt/r/buyout_game-data/logs_fixed`, and uses `--copy-to-logs` to publish those repaired files into a cleaned-log destination; when `--plan` scopes repair to one match-pack run, `--copy-dest-dir` is required so protocol-change smoke runs cannot silently pollute shared canonical `logs_good/`; the repair pass now also prints a summary of repaired/skipped logs, dropped duplicate elimination rows, and any copy failures so scoped repairs do not silently lose data on the way into `logs_good/`,
- latest-log selection now ignores malformed JSONL candidates, so a newer corrupted cleaned log does not eclipse an older valid log for the same `game_id`,
- `update_ratings_main.py` now uses an explicit deterministic shuffle seed for its single-pass TrueSkill update order, so repeated rebuilds from the same cleaned logs produce reproducible `player_ratings.json` outputs unless an operator deliberately changes `--seed`,
- `update_ratings_multipass.py` now fails loudly when any worker pass errors instead of silently publishing partial results from the surviving passes,
- the repo now also writes an official companion adjusted leaderboard layer from continuous `final_wealth_share` outcomes:
  `adjusted_leaderboard_inputs.csv`, `scoreboard_adjusted.csv`, `scoreboard_adjusted.json`, and `adjusted_leaderboard_validation.json`,
- the adjusted leaderboard input CSV is now written at the canonical match-unit level, so complete mirrored packs appear as one `match_unit_kind=match_pack` unit with one averaged row per model rather than as separate per-game rows,
- the adjusted leaderboard is an official companion leaderboard and does not replace the canonical Bradley-Terry board,
- incomplete or inconsistent mirrored packs remain excluded from the adjusted leaderboard, with lineup-signature mismatches treated as inconsistent match packs, and `adjusted_leaderboard_validation.json` now also records per-model row-count mismatches inside otherwise complete packs instead of discarding them silently,
- the repo now also supports a separate official companion frozen-baseline excess-wealth leaderboard layer: build a baseline artifact under `/mnt/r/buyout_game-data/baselines/`, then score cleaned logs into `excess_wealth_inputs.csv`, `scoreboard_excess_wealth.csv`, `scoreboard_excess_wealth.json`, and `excess_wealth_validation.json`,
- the excess-wealth layer predicts expected final wealth from exogenous features only (current MVP: starting-balance share, prize regime, protocol id, and seat), scores `actual_final_wealth - expected_final_wealth`, and applies the same mirrored-pack completeness checks as the canonical and adjusted leaderboard layers after those per-game expectations are computed,
- incomplete or inconsistent mirrored packs remain excluded from the excess-wealth leaderboard, and `excess_wealth_validation.json` now also records per-model row-count mismatches inside otherwise complete packs instead of discarding them silently,
- the frozen-baseline excess-wealth layer is an explicit stage and is not rebuilt automatically by `generate_stats_data.py`; this avoids accidental baseline refits on the evaluated run,
- `generate_stats_data.py` is now the thin orchestration CLI for stats generation, while the stage implementations live under `stats_pipeline/` (`common.py`, `core.py`, `wealth.py`, `transfers.py`, `runtime.py`) so the long-lived `generate_stats_data.py` function surface can stay stable for tests and operators,
- `generate_stats_data.py`, `analyze.py`, and `plot_elimination_stats.py` now also accept `--data-root` for scoped rebuilds; when set and their more specific path flags are omitted, they read cleaned logs from `<data_root>/logs_good`, write CSV artifacts under `<data_root>/`, and use `<data_root>/images` as the default chart/image destination; `analyze.py` and `plot_elimination_stats.py` now treat `scoreboard_bt.csv` as the primary ordering/filtering surface when it exists and fall back to `scoreboard_ts.csv` for older scopes, the TrueSkill chart's grey band is a Gaussian posterior-density strip centered on `mu` with local opacity proportional to the normal pdf at each x-value rather than a flat-width confidence-interval fill, and the Bradley-Terry chart now uses the same density-strip rendering via a symmetric normal approximation fitted to its reported 95% interval,
- the repo now also writes mandatory stratified reporting from cleaned `final_results` payloads:
  `stratified_leaderboard_inputs.csv`, `scoreboard_by_prize_regime.csv`, `scoreboard_by_regime_family.csv`, `scoreboard_by_starting_balance_rank.csv`, and `scoreboard_by_starting_balance_band.csv`,
- for the starting-balance stratified outputs, `starting_balance_rank` is the within-game descending order of starting balances with deterministic player-id tie-breaks, and the band file groups those ranks into top / middle / bottom quartile-style buckets (`top_2_of_8`, `middle_4_of_8`, `bottom_2_of_8` in canonical 8-player games),
- the repo now also writes objective secondary diagnostic overlays from cleaned logs:
  `secondary_diagnostic_inputs.csv` and `scoreboard_secondary_diagnostics.csv`,
- these secondary diagnostics stay explicitly non-canonical and do not replace final wealth, the canonical Bradley-Terry board, the secondary TrueSkill board, or the official companion adjusted / excess-wealth boards,
- the current overlay definitions are:
  `upset_score` = average wealth partial score on bottom-half starting-balance draws,
  `conversion_score` = average normalized endgame extraction on finalist appearances where the pre-resolution balance lead is at least `ceil(base_prize_gap / 2)`,
  `endgame_extraction_score` = average share of the feasible finalist-controlled base-gap interval actually captured before the jury bonus is added,
  `jury_extraction_score` = average share of the bounded jury pool won after placement is fixed,
- each secondary metric is shipped with explicit opportunity counts and sample-warning columns so the overlay remains auditable and does not masquerade as a primary leaderboard,
- wealth summary outputs report starting balance, pre-prize balance, finish prizes, final wealth, and wealth deltas by model,
- transfer summary outputs report outgoing/incoming transfer behavior and net transfer flow by model; the companion chart orders models by highest average net transfer per game and spells out the outgoing, incoming, and net transfer series explicitly; note that in the current canonical variant transfers are private while balances remain public after each transfer phase,
- transfer integrity outputs now also write `transfer_integrity_signals.csv`, a row-level diagnostic over applied transfers from seats eliminated in that same round, including outbound-share, vote-margin, recipient-vote, a coarse survival-window heuristic, prize-regime metadata, starting-balance exposure fields, explicit final-three flags, an observed-vote final-three pivot test, a conservative final-three counterfactual for whether elimination changes without that transfer when the logs and deterministic tie-break rules are sufficient to resolve it, and whether removing that transfer changes the overall wealth-winner set; when an elimination went through a tie-break, these diagnostics prefer the decisive re-vote stage when available,
- transfer integrity outputs also write `transfer_integrity_summary.csv`, an aggregate view over those same signals with overall, prize-regime, and starting-balance-band slices, including explicit final-three elimination-change, decisive-transfer, and wealth-winner-dependency rates so final-three pivot risk can be monitored before any canonical transfer-rule change is adopted,
- runtime summary outputs report per-model completion rate, abort exposure, non-ok call rates, unfinished-action rates, and average/p95 call latency, and they now use the latest cleaned log per `game_id` from `logs_good/` so stale reruns and raw-log leftovers do not distort operational reporting; these remain CSV-only operational diagnostics in the current canonical plotting stage,
- phase latency outputs report per-model average/p50/p95/max seconds for each prompt phase; these also remain CSV-only operational diagnostics in the current canonical plotting stage,
- final-two analyses, final-loser analyses, and earliest-out counts are survivor-placement-based,
- fallback buyout diagnostics now also write `final2_buyout_diagnostics.csv` and `final2_buyout_tax_summary.csv`; these record both finalists' bids and pre-bid balances, the exact tie-break stage, whether a richer finalist won by tying the poorer finalist's maximum credible bid, and the winner's payment share of both `base_prize_gap` and the poorer finalist's max bid,
- downstream loaders that consume cleaned JSONL logs should choose the latest cleaned log for a given `game_id` rather than merge multiple reruns into one synthetic game,
- animation and quote-extraction helpers now follow the same latest-valid cleaned-log rule, so a newer malformed rerun does not silently blank out an older valid game's derived artifacts,
- `global_scores.jsonl` summary rows now also carry top-level `match_pack_*` metadata when the underlying game came from a mirrored-pack plan so pack structure is still visible in the lightweight global summary file,
- `filter_global_scores.py` now keeps only the latest `global_scores.jsonl` row per valid `game_id` present in `logs_good/`, and when the latest valid cleaned log has authoritative `final_results`, it also requires the kept summary row to match that cleaned result so corrupted reruns do not displace an older valid summary row in `global_scores_good.jsonl`,
- prompt-building scripts should sanitize embedded log text before inserting it into prompt templates so log content cannot close delimiters or inject placeholder-like tags,
- player-facing history snippets label private DM phases as `R<round>.private1/2/3`, distinguish outbound DMs from replies via `PRIVATE` vs `PRIVATE_REPLY`, and label transfer milestones as `R<round>.transfer` / `R<round>.post_transfer` so prompt history avoids exposing raw internal subround codes like `2/3/4/450/451`,
- prompt templates now describe history scope explicitly as public history plus only the seat's visible private history,
- stats and plot rebuilds also refresh the benchmark manifest with protocol metadata and current aggregate artifact paths,
- the benchmark manifest's `logs_good_count` now counts unique cleaned games by `game_id` rather than raw cleaned `.jsonl` files, so cleaned reruns do not inflate the reported canonical dataset size,
- the exported `protocol_id` now also encodes the private-DM schedule and the final-two jury-pool rule parameters (`fraction`, `floor`, and `rounding`) so materially different chat or finale calibrations cannot silently share one protocol identifier downstream,
- some filenames remain inherited for compatibility, so rely on chart titles and CSV columns rather than filenames alone.

Open protocol decisions still remaining:
- whether the private-balance version should be a mode flag or a separate benchmark track,
- how far to push downstream wealth/transfer analysis before replacing inherited filenames entirely,
- whether to keep inherited module names or rename them once the surrounding tooling is updated.

## Immediate Migration Plan

1. Preserve the inherited loop where it is still operationally useful.
2. Continue reworking logs, summaries, and analysis scripts around balances, transfers, and payout outcomes.
3. Decide how the private-balance variant should be surfaced.
4. Keep `GOALS.txt`, `DOCS.md`, and `README.md` updated as the protocol evolves.

## Basic Commands

Current scratch commands live in [run.txt](/mnt/r/buyout_game/run.txt). At this stage the safest common commands are:

```bash
export PYTHONPATH=/mnt/r/buyout_game:$PYTHONPATH
python -m pytest -q
python run_preflight.py
python main.py
python build_match_packs.py --num-packs 5 --label smoke_001 --scheduling-policy weighted_random
python run_match_packs.py --plan /mnt/r/buyout_game-data/match_packs/mirrored_match_packs_smoke_001.jsonl --workers 4
```

`python main.py` now runs the v1 buyout protocol inside the inherited survivor-style elimination loop.
It remains useful for protocol debugging and ad hoc inspection, but canonical scored evaluation should use `build_match_packs.py` plus `run_match_packs.py` so complete mirrored 2-game packs are the units that later leaderboard rebuilds consume by default.
`python run_preflight.py` pings the active roster on each model's resolved router port from the checked-in parallel roster when available, falls back to `BUYOUT_GAME_BASE_URL` for anything not listed there, checks that the active roster is large enough for the configured table size, and writes a JSON readiness report under `/mnt/r/buyout_game-data/preflight/` with per-model `resolved_port` and `resolved_base_url` fields. When `--models` is used, the report now also checks that the selected probe list is non-empty and large enough to seat one full game; smaller smoke subsets can still be pinged, but they report `selection_ok=false` and `ready=false` instead of looking production-ready. `python run_preflight.py --port 8006` is the clean way to validate an all-`8006` run without rewriting `participants_parallel.csv`.
`python build_match_packs.py` writes an explicit mirrored-pack JSONL plan under `/mnt/r/buyout_game-data/match_packs/` and prints a per-model summary of planned mirrored-pack appearances and game appearances for the new plan. Long adaptive builds now also emit timed progress lines while loading the scheduling snapshot, indexing / scanning scheduler history, and constructing packs, so operators can tell whether the builder is still working and which stage is taking time.
By default the builder now uses `weighted_random`. Adaptive lineup scheduling is still available, but it must be pointed at an explicit rating/history scope instead of silently reusing the shared global defaults after a protocol change. Use `--scheduling-policy adaptive` together with an explicit current-scope snapshot such as `--ratings-json .../player_ratings_multipass.json` (or `--scoreboard-ts-csv .../scoreboard_ts.csv`) and either `--no-scheduler-history` or explicit `--scheduler-history-plan-glob` plus `--scheduler-history-logs-dir`. Only use `--allow-global-adaptive-defaults` when the shared global rating/history surface is intentionally canonical. Published leaderboard primacy is now Bradley-Terry, but the adaptive scheduler still consumes the TrueSkill-family snapshot path because it needs `mu` plus `sigma` uncertainty estimates, not just a point estimate. The adaptive policy still prefers uncertain adjacent-strength comparisons while keeping the canonical pack size fixed at 2 games, but it now uses a lighter one-slot anchor path aimed mainly at weakly connected or newly reintroduced models, adds an explicit saturation penalty so oversampled models do not dominate later plans, and reserves a small fraction of packs for top-tier adjacent resolution instead of relying on heavy anchor reuse alone. Its history seeding still scans prior plan files plus matching completed canonical cleaned logs to avoid starting from zero every plan build.
`python run_match_packs.py` executes that plan as resumable pack slots, skipping a `game_id` only when the latest raw log for that game already contains non-aborted `final_results`, unless `--force` is supplied. When `--logs-dir` is set, that directory is now both the resumable skip scope and the destination for newly written per-game logs. `--port 8006` or `--port 8040` forces all gameplay calls in that run onto one router without mutating the checked-in per-model roster file. Its console lifecycle/status lines now carry a wall-clock timestamp prefix at one-second resolution so long-running batch output can be correlated with router and log-side events more easily.
`python status_match_packs.py --plan ...` is the operator-side live status view for those runs. It reads the same plan plus the latest raw log per `game_id` from the chosen `--logs-dir`, prints in-progress rows by default with current round/subround/phase, and ends with a compact summary table, an abort-reason breakdown, an aborts-by-model breakdown, live LLM latency summaries, and an in-progress phase breakdown. The latency sections reuse the per-call `llm_action.duration_seconds` values already written into raw game logs and summarize them by model and by coarse phase family (`public`, `dm`, `transfer`, `vote`, `final_two`, and similar). The model-latency table now also shows the effective router `port` for each model: if the selected plan/logs pair matches a live `run_match_packs.py --port ...` process, it shows that forced port; otherwise it falls back to the currently resolved per-model port from the checked-in parallel roster or default base URL. Abort reasons are summarized across all aborted rows even when the main table is still filtered to in-progress rows, so the default view still explains why pack slots have failed; the model breakdown resolves the culprit model from the abort reason's `P#` seat reference and falls back to the plan's seat map when the reason omits the explicit `(model)` suffix. If `--plan` is omitted, the script now attempts to infer the active run context from a live `run_match_packs.py` process and uses that process's `--plan` and `--logs-dir`; if multiple distinct active runs exist, it fails loudly and requires an explicit `--plan`. Add `--participants` to include the seat-to-model map for each row, or `--all` to also print pending / completed / aborted plan rows instead of only active ones. Use `--port ...` on the status command only when you need to override the displayed latency-table port resolution manually.
Interrupting it once requests a graceful drain of already-submitted pack slots. Interrupting it a second time now force-aborts by shutting down the worker pool and explicitly terminating or killing any remaining child workers before the parent exits, instead of leaving orphaned `run_match_packs.py` processes behind.
When a submitted pack slot returns without a final order, the runner now checks the latest per-game log before printing status, so aborted games are reported with their logged abort reason instead of the older generic `completed, no final order` message.
Both `main.py` and `run_match_packs.py` now reset any stale in-process graceful-shutdown flag at the start of each invocation, so repeated calls from the same Python process are not silently treated as already interrupted.
Broker shutdown cleanup now logs warning lines to stderr if queue or manager cleanup fails, instead of swallowing those cleanup exceptions silently. Manager-backed broker queues do not expose a `close()` method, so shutdown now skips that call when unsupported rather than emitting a spurious warning on otherwise clean exits.
`python backtest_match_pack_scheduling.py` now runs an explicit scheduler-policy backtest under `/mnt/r/buyout_game-data/scheduler_backtests/`. It uses the real mirrored-pack planner, simulates pack outcomes from a latent truth snapshot, updates provisional TrueSkill after each pack, and writes one `_steps.csv`, one `_summary.csv`, and one `_metadata.json` bundle per label. By default it compares `adaptive_adjacent_anchors_v1` against `weighted_random` at equal 2-game-pack budgets, reports rank-stability metrics against the truth ranking (`spearman_rho_to_truth`, `mean_abs_rank_error_to_truth`, `top1_match_to_truth`), uncertainty reduction metrics (`mean_sigma`, `median_sigma`, `sigma_reduction_fraction`), and exposure-fairness metrics (`exposure_gini`, `exposure_cv`, `weighted_exposure_mae`, `weighted_exposure_rmse`, `weighted_exposure_ratio_stddev`). Truth is loaded first from the latest rating snapshot (`player_ratings_multipass.json`, then `player_ratings.json`, then `scoreboard_ts.csv`), and if none exists the tool falls back to deriving truth from canonical logs via multi-pass TrueSkill. That fallback remains TrueSkill-based even though the published leaderboard is now Bradley-Terry, because the scheduler backtest still needs uncertainty-aware `mu`/`sigma` state. The CLI also mirrors the live adaptive builder's completed-pack history seeding, so `--scheduler-history-plan-glob`, `--scheduler-history-logs-dir`, and `--no-scheduler-history` work here too.
`python smoke_diagnostics.py --logs-dir ...` summarizes the latest cleaned log per `game_id` in the target directory so rerun leftovers do not show up as duplicate smoke summaries.
The quote-extraction helpers under `/mnt/r/buyout_game/quotes/` write prompt files under `/mnt/r/buyout_game-data/quotes/extract_prompts/` and evaluator responses under `/mnt/r/buyout_game-data/quotes/extract_responses/<model>/`. `prepare_extract.py` now removes stale prompt files for a requested `game_id` before rewriting the latest cleaned prompt, uses the latest valid cleaned log when a newer rerun is malformed, and still lets aborted latest logs clear obsolete prompts, so cleaned reruns or aborted replacements do not leave duplicate or obsolete quote prompts behind. `collect_quotes.py` still accepts older flat response files directly under `extract_responses/`, its seat-map recovery now falls back by `game_id` to the latest valid cleaned log when the exact referenced cleaned log is missing or malformed so older evaluator outputs remain readable after cleaned-log repair or rerun churn, and malformed partial quote blocks are now dropped block-wise instead of shifting later ratings/speakers onto the wrong quote row.
`python generate_game_summary_prompts.py` still targets finished cleaned logs by default, but its seat-map recovery now also falls back to ordinary event payloads, so ad hoc in-progress raw-log snapshots can still render readable model/seat labels before `final_results` exists. Its condensed logs now also normalize invalid/skipped vote sentinels, suppress exact duplicate balance snapshots, rewrite `private_no_reply` lines into a human-readable form, default to semantic phase labels such as `Round 4 After Transfers` and `Round 4 Tie-Break Speeches` instead of opaque raw subround codes, render transfer-phase summaries from the actual sender→recipient transfer details instead of raw requested/applied counter jargon, format balance snapshots as compact seat-first `P4 154 | P5 140 ...` lists while keeping full model↔seat identity in the top-of-file seat map, render that seat map in `P1..Pn` order for easier human lookup, recover tie-break speech actor/text from either the older `actor`/`content` shape or the live `player_id`/`message` shape, use the latest valid cleaned log per `game_id`, and treat the latest `final_results` entry in an append-only repaired log as authoritative so rendered summaries do not show contradictory old and new endings. The default `--transfer-view summary` omits the per-transfer detail lines so human review does not see duplicate transfer information; `--transfer-view detail` or `--transfer-view both` can restore those detail rows when needed. `--include-aborted` is now an explicit opt-in for smoke/debug rendering, so the script still skips aborted games by default when it is being used to generate analyst-style finished-game post-mortem prompts.
`python run_llm_on_game_summaries.py` and `python run_llm_on_summary_prompts.py` still process all queued tasks and print per-task failures, but they now return a non-zero exit code if any worker task fails so wrapper scripts and CI can detect partial evaluator failures. Both runners now also accept `--port` as an explicit one-run router override instead of forcing operators to edit `BUYOUT_GAME_BASE_URL`, their default evaluator model has been updated to `gpt-5.4-medium`, and they now emit richer per-task progress lines with running done/skipped/failed totals instead of only a bare trailing counter. `python split_player_reports.py` now likewise prints source-level startup info plus per-response-file progress so larger dossier-prep runs do not appear idle between the start and final summary.
`python animation/animate_elimination_main.py` remains the canonical animation entrypoint, but it now renders a buyout-specific omniscient replay from the latest valid cleaned logs. The default frame output root is `/mnt/r/buyout_game-data/frames_buyout/`. The CLI still accepts `--logs-dir`, `--games-per-chunk`, `--summary-only`, `--start-frame`, and `--end-frame`, and now also accepts `--max-workers` plus `--overwrite`. Full renders still clear old PNG frames by default, but partial frame-range rerenders now preserve existing PNGs outside the requested range unless `--overwrite` is supplied. Internally the animation now follows the same cleaner `data_loader -> prepare -> render` split used in sibling benchmarks, uses a diplomat-style 2x2 layout (seat board, transcript feed, economics panel, running leaderboard), replays explicit buyout state beats such as `balance_snapshot`, `transfer_phase`, post-transfer balances, final-two public economics, and final wealth, keeps settlement submissions and buyout bids sealed until the execution reveal, normalizes raw seat references into dense internal animation indices while preserving the original `P#` seat labels so sparse-seat or short-ID logs still replay correctly, scores the running leaderboard with the shared secondary TrueSkill environment from `ts_env.py`, and still highlights the placement / survivor winner on the board even when the wealth winner differs.
For report-bundle style analyst outputs similar to the sibling persuasion / debate / diplomat benchmarks, the repo now also supports a structured bundle root under `/mnt/r/buyout_game-data/reports/<label>/`. Use `python build_buyout_quote_candidates.py --output-label latest`, then `python run_buyout_quote_candidates.py --output-label latest --model ...`, then `python build_buyout_quote_gallery.py --output-label latest` plus `python run_buyout_quote_gallery.py --output-label latest --model ...` to build an overall quote gallery under `<bundle>/quotes/`. Use `python build_buyout_dossiers.py --output-label latest` plus `python run_buyout_dossiers.py --output-label latest --model ...` to build per-model dossiers under `<bundle>/dossiers/` from the existing `player_reports/` tree, optionally enriched by the quote bundle when `<bundle>/quotes/` already exists. The quote-candidate prompt is intentionally harsh rather than merely conservative: in a typical full transcript, 0 to 2 quotes is the expected outcome, 3 is rare, false negatives are preferred over false positives, and the extractor should keep only lines whose wording is genuinely standout rather than merely useful strategic evidence. Returned quotes should normally all be score `8+`, with `10` reserved for truly anthology-level lines.
The report bundle stages now fail loudly on missing upstream manifests or missing quote-candidate bundles instead of silently emitting empty gallery or quote-free dossier outputs from a mistyped or half-built quotes directory. Quote-candidate source material is limited to actual speaker lines from the condensed log; system narration such as balance snapshots is excluded from candidate selection. When quote enrichment is enabled for dossiers, the quote-candidate stage must be complete; placeholder or partially filled quote outputs now block dossier bundle construction instead of producing stale partial coverage.
`python run_buyout_quote_candidates.py` now also emits per-game completion progress lines with running written/skipped/dry-run/failed totals, so long batch quote runs show visible forward progress instead of only the startup block and final summary.
The report runners still default to resumable skip-existing behavior, but freshness is now based on the generated prompt/source/context inputs as well as output validity. Rebuilding quote-candidate prompts, quote-candidate contexts, the quote gallery prompt, or dossier source/prompt files and rerunning the corresponding `run_*.py` script without `--overwrite` will refresh stale outputs automatically. `--dry-run` on the quote-candidate, quote-gallery, and dossier runners now leaves the placeholder outputs intact instead of writing a superficially valid final artifact that would later be skipped.
For manual prompt QA without any live LLM calls, run:

```bash
export PYTHONPATH=/mnt/r/buyout_game:$PYTHONPATH
python capture_protocol_prompts.py --run-label prompt_review_001
```

Or write directly to an explicit run directory:

```bash
export PYTHONPATH=/mnt/r/buyout_game:$PYTHONPATH
python capture_protocol_prompts.py --output-dir /tmp/buyout_prompt_review/current --overwrite
```

`--output-dir` must point to a directory path. If that path already exists as a regular file, the runner now fails loudly instead of trying to treat it like a capture directory.

This writes a deterministic review packet under `/mnt/r/buyout_game-data/prompt_capture/<run-label>/` using real `LLMPlayer` prompt rendering with scripted stub responses instead of external LLM calls. Each run includes full rendered prompt `.txt` files, `prompt_manifest.jsonl`, `summary.json`, and `review_packet.md` so prompts can be reviewed for completeness, privacy correctness, and clarity.

To rebuild the parallel continuous leaderboard directly:

```bash
export PYTHONPATH=/mnt/r/buyout_game:$PYTHONPATH
python update_ratings_adjusted.py
```

To build and apply a frozen excess-wealth baseline explicitly:

```bash
export PYTHONPATH=/mnt/r/buyout_game:$PYTHONPATH
python build_excess_wealth_baseline.py --baseline-id anchor_v1
python update_ratings_excess_wealth.py \
  --baseline-artifact /mnt/r/buyout_game-data/baselines/excess_wealth_baseline_anchor_v1.json
```

Mirrored-pack execution details:
- `build_match_packs.py` currently builds canonical 2-game mirrored packs only.
- The default builder policy now keeps those 2-game packs but schedules their lineups adaptively from the latest available rating snapshot (`player_ratings_multipass.json`, then `player_ratings.json`, then `scoreboard_ts.csv`) instead of treating every possible lineup as equally informative; that fallback remains TrueSkill-based because adaptive scheduling still needs uncertainty estimates even though the published leaderboard is now Bradley-Terry.
- The adaptive policy now focuses new packs on uncertain adjacent-strength comparisons, reserves a small fraction of packs for top-tier adjacent resolution, uses only a light one-slot anchor path by default, and aims those anchors mainly at weakly connected or newly reintroduced models rather than repeatedly stuffing every pack with the same strong references.
- Adaptive lineup scoring now carries an explicit exposure saturation penalty so already oversampled models do not monopolize later tranches; it still carries forward prior model / focus-pair / anchor exposure from completed canonical packs so later plan builds continue balancing the same long-run evaluation stream instead of resetting per invocation.
- The default history source is the existing plan files under `/mnt/r/buyout_game-data/match_packs/*.jsonl`, filtered by whether all `pack_game_ids` in that pack have completed latest logs in `logs_good/` and those logs still match the planned pack metadata; `--scheduler-history-plan-glob`, `--scheduler-history-logs-dir`, and `--no-scheduler-history` control that behavior.
- `--scheduling-policy weighted_random` remains available as an explicit bootstrap / opt-out path.
- `backtest_match_pack_scheduling.py` now provides the matching offline validation harness for those scheduler policies: it generates fresh mirrored packs sequentially with the same planner, simulates two-game pack outcomes from a truth snapshot, and compares policy quality at equal pack budgets instead of requiring larger canonical pack sizes to estimate scheduler value.
- Within one pack, the lineup, exact prize regime, and exact starting-balance multiset are held fixed.
- The two games permute seat assignments and mirror balance-rank exposure so a model that is rich in one slot is poor in the other.
- Official mirrored packs also carry canonical round-by-round public-statement order templates; slot 2 uses the complementary order for each round, and the engine filters that template down to whichever seats remain active.
- Pack games now validate those public-statement order templates strictly: unknown seat labels, duplicate seat labels, or missing active seats raise an error instead of being silently ignored.
- `load_match_pack_plan()` now also validates pack-level shared metadata before any run starts: the slots of one pack must agree on shared regime / balance-template payloads, retain mutual partner links, and preserve mirrored balance-rank exposure rather than only passing row-by-row shape checks.
- `run_match_packs.py` passes explicit seat assignments, starting balances, prize regime, and engine seed into the inherited engine instead of using the default per-`game_id` random draws.
- Each packed game's `final_results` now includes `match_pack_id`, `match_pack_slot`, `match_pack_size`, `match_pack_lineup_signature`, the pack's public-statement order metadata, and related pack metadata so downstream aggregation can validate completeness without needing the original plan file.

Mirrored-pack semantics:
- treat mirrored packs as a controlled evaluation wrapper around the canonical game, not as a different gameplay protocol,
- for canonical 8-seat packs, balance-rank exposure is mirrored by complement: rank `1` in one slot maps to rank `8` in the partner slot, rank `2` to `7`, rank `3` to `6`, and rank `4` to `5`,
- the seat permutation is required to differ across the two slots, so the same model does not keep the same seat assignment in both games,
- complete packs are the canonical rating unit; incomplete or inconsistent packs remain visible in validation outputs but are excluded from pack-collapsed leaderboard layers, and standalone games are likewise excluded from canonical leaderboard rebuilds unless an operator explicitly uses an `--include-standalone-games` override for non-canonical exploratory work.

Mirrored-pack JSONL plan shape:
- `plan_type`: currently `mirrored_match_pack`
- `pack_id`, `pack_slot`, `pack_size`, `game_id`: identify the pack and the specific game within it; `pack_size` is currently fixed at `2`
- `seat_models`: explicit seat-to-model assignment for that game
- `starting_balances_by_seat`: explicit seat-to-balance assignment injected into the engine
- `starting_balance_ranks_by_seat`: within-game descending balance-rank labels for auditability
- `starting_balance_multiset`: the shared sorted balance template reused across both slots
- `prize_regime`: explicit regime payload with `regime_id`, `prize_pool_ratio`, `prize_pool_total`, `spread_label`, and `ladder`
- `engine_seed`: explicit engine seed for that packed game
- `lineup_models` and `lineup_signature`: redundant lineup identifiers used for validation and downstream grouping
- `pack_game_ids`, `balance_template_id`, and `mirrored_partner_game_id`: cross-links that let downstream tools verify pair completeness and shared scenario provenance
- `scheduling_policy`, `scheduling_rating_source`, `scheduling_focus_models`, `scheduling_focus_rank_span`, `scheduling_local_window_models`, `scheduling_anchor_pool`, `scheduling_anchor_models`, `scheduling_top_tier_models`, and `scheduling_reserved_top_tier_pack`: audit fields explaining why an adaptive planner selected that lineup; these are shared across both slots of a pack

Plan validation rules:
- plan rows must include positive `pack_slot` and `game_id` values,
- `pack_size` must match the currently supported mirrored-pack size,
- seat labels must be the canonical contiguous set `P1..Pn`,
- `seat_models` and `starting_balances_by_seat` must both be present,
- `starting_balance_ranks_by_seat` must be a full `1..N` permutation that matches the injected seat balances against the shared descending `starting_balance_multiset`,
- the runtime override builder also fails loudly if `starting_balances_by_seat` is missing or does not cover every built seat, instead of surfacing a later raw `KeyError`,
- and the seat keys across `seat_models`, `starting_balances_by_seat`, and `starting_balance_ranks_by_seat` must match exactly.
