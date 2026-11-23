# Buyout Game Benchmark: Multi-Agent Bargaining, Transfers, and Hostile Takeovers

*Whom to trust, what to pay, and when to keep the money*

This benchmark measures long-horizon social strategy under explicit financial incentives. Eight models play a multi-round elimination game with unequal starting balances, a public prize ladder, private transfers, public votes, and a finalist-only endgame where the last two seats can negotiate, settle, or buy each other out.

The canonical outcome is **final wealth**, not raw finish order. A model can reach the end, take 1st place in the finale, and still lose on money. That is the central design choice: the benchmark rewards models that manage incentives, alliances, spending, and endgame leverage well across many games, not just models that survive the longest.

[Watch *Absolutely In*, a 48-second replay of Grok, Gemini and Astra negotiating over votes and first place.](animation/absolutely_in/absolutely-in-square.mp4)

---

## At A Glance

![Buyout game protocol infographic](assets/infographic.jpg)

Each game:

- seats **8 models**
- starts from a fixed **800**-coin pot
- guarantees each seat **10** coins, then splits the remaining **720** with a controlled unequal random draw
- announces one of **9** public prize ladders formed by combining **3** total-pool sizes with **3** payout shapes
- runs **6 elimination rounds**
- ends with a special finalist-controlled buyout phase plus a bounded jury bonus

Current canonical variant:

- **balances are public**
- **transfers are private**
- **elimination votes are public**
- **final wealth is the canonical score**
- **public ratings use mirrored 2-game match packs**

<details>
<summary>Browser human play in the benchmark source checkout</summary>

The benchmark source checkout also includes a separate solo browser mode where one human plays a full Buyout Game seat against seven bot seats. It is for participant collection and local/public demos, not for updating the canonical model leaderboard.

Human play includes:

- a graphical 8-seat board with public balances, active/eliminated status, visible event feed, and phase-specific action forms
- a short rules quiz before first play
- signed participant and quiz-pass cookies, including repeat-play quiz skipping for matching local `127.0.0.1` cookies
- independent concurrent solo sessions for multiple browser users
- deterministic plus small-model moderation that can reject low-effort/off-topic actions, assign strikes, or disqualify and abort a solo run
- integer-only monetary inputs and enforced message word limits, with overlong messages returned for correction without a strike
- result storage under `/mnt/r/buyout_game-data/human_play/`, separate from canonical benchmark data

From the source checkout, install the optional ASGI dependencies and start the server.

```bash
uv venv /mnt/r/buyout_game-data/human_play/.venv
uv pip install --python /mnt/r/buyout_game-data/human_play/.venv/bin/python -r requirements-human-play.txt
PYTHONPATH=/mnt/r/buyout_game:/mnt/r/benchmark_utils /mnt/r/buyout_game-data/human_play/.venv/bin/python run_human_play.py --port 8090
```

Then open `http://127.0.0.1:8090/`.

</details>

---

## Main Leaderboard

![Buyout Game Bradley-Terry leaderboard with the November entrants highlighted](images/scoreboard_bradley_terry_highlighted.png)

The November 2026 leaderboard combines **1,094 games in 547 mirrored pairs**. The Bradley–Terry rating measures how often a model finishes wealthier than its opponents across those pairs. Highlighted bars identify the November entrants. [Chart without highlights](images/scoreboard_bradley_terry.png)

The November update added 31 models, including both Seed 2.1 variants, Gemini 4 Neon, Tencent Hy4, Step 5 Preview, and Baidu ERNIE 5.5 Flash Preview. Twelve returning opponents connect the new games to earlier results. Tencent Hy3 Preview played one mirrored pair before its endpoint became unavailable. GPT-5.5, Claude Opus 4.7 and GLM-5 took its eight unfinished pairs. The table contains all 53 models in the published rating CSV.

| Rank | Model | BT | Games | Match Packs |
| ---: | --- | ---: | ---: | ---: |
| 1 | Gemini 4 Neon (high) | 2175.7 | 52 | 26 |
| 2 | Gemini 4 Carbon (high) | 2061.1 | 70 | 35 |
| 3 | Claude Fable 5.2 (high) | 1996.1 | 62 | 31 |
| 4 | GPT-6.2 Astra (high) | 1959.6 | 54 | 27 |
| 5 | Grok 4.7 (high) | 1877.4 | 64 | 32 |
| 6 | GPT-6.2 Sol (high) | 1859.9 | 62 | 31 |
| 7 | Claude Opus 5.5 (high) | 1820.6 | 68 | 34 |
| 8 | GPT-5.5 (high) | 1813.8 | 122 | 61 |
| 9 | Gemini 3.8 Flash (high) | 1787.3 | 60 | 30 |
| 10 | Muse Wonder 1.0 (high) | 1785.5 | 82 | 41 |
| 11 | Claude Opus 4.7 (high) | 1777.9 | 248 | 124 |
| 12 | Kimi K3.1 Pro (high) | 1669.8 | 50 | 25 |
| 13 | GPT-6 Luna (high) | 1637.7 | 80 | 40 |
| 14 | Claude Sonnet 5.5 (high) | 1617.1 | 68 | 34 |
| 15 | DeepSeek V4 Pro | 1587.9 | 82 | 41 |
| 16 | Baidu ERNIE 5.5 Flash Preview (high) | 1587.3 | 74 | 37 |
| 17 | Qwen 3.8 Flash (medium) | 1584.4 | 72 | 36 |
| 18 | ByteDance Seed2.1 Pro | 1581.7 | 70 | 35 |
| 19 | GLM-5.3-Flash (high) | 1581.6 | 78 | 39 |
| 20 | Tencent Hy4 (high) | 1579.5 | 64 | 32 |
| 21 | GLM-5.3 (high) | 1577.5 | 56 | 28 |
| 22 | ByteDance Seed2.1 Turbo | 1536.5 | 66 | 33 |
| 23 | Tencent Hy3 Preview (high) | 1509.5 | 82 | 41 |
| 24 | GLM-5.1 | 1499.9 | 106 | 53 |
| 25 | Gemini 3.1 Pro Preview | 1487.5 | 330 | 165 |
| 26 | Qwen 4 Max (high) | 1487.2 | 70 | 35 |
| 27 | DeepSeek V4 Pro 0813 (high) | 1467.1 | 78 | 39 |
| 28 | Tencent Hy4 Flash (high) | 1463.4 | 56 | 28 |
| 29 | DeepSeek V4 Flash | 1461.4 | 74 | 37 |
| 30 | Xiaomi MiMo V2.6 Flash (thinking) | 1460.6 | 68 | 34 |
| 31 | DeepSeek V4.1 Flash 1017 (high) | 1454.0 | 58 | 29 |
| 32 | Kimi K2.6 | 1442.4 | 94 | 47 |
| 33 | Grok 4.3 | 1431.1 | 78 | 39 |
| 34 | Xiaomi MiMo V2.5 Pro | 1422.7 | 70 | 35 |
| 35 | Xiaomi MiMo V2.6 Pro (thinking) | 1421.6 | 72 | 36 |
| 36 | Gemma 4 31B Reasoning | 1413.3 | 94 | 47 |
| 37 | Muse Ember 1.0 (high) | 1407.1 | 80 | 40 |
| 38 | Claude Haiku 5.5 (high) | 1371.2 | 60 | 30 |
| 39 | Qwen 3.6 Max Preview | 1366.7 | 20 | 10 |
| 40 | Step 5 Preview (high) | 1359.6 | 82 | 41 |
| 41 | Xiaomi MiMo V2 Pro | 1334.7 | 222 | 111 |
| 42 | Step 3.5 Flash | 1321.5 | 88 | 44 |
| 43 | DeepSeek V3.2 | 1287.1 | 248 | 124 |
| 44 | Mistral Large 4 (high) | 1273.9 | 66 | 33 |
| 45 | Step 3.7 Flash (high) | 1269.3 | 74 | 37 |
| 46 | Mistral Medium 3.5 (high) | 1238.9 | 92 | 46 |
| 47 | MiniMax M3.1 Flash (high) | 1236.2 | 62 | 31 |
| 48 | Qwen3.5-397B-A17B | 1215.5 | 290 | 145 |
| 49 | Baidu Ernie 5.1 | 1116.9 | 78 | 39 |
| 50 | Mistral Large 3 | 1105.0 | 208 | 104 |
| 51 | Llama 4 Maverick | 1032.7 | 192 | 96 |
| 52 | Baidu Ernie 5.0 | 948.8 | 246 | 123 |
| 53 | GPT-OSS-120B | 902.8 | 332 | 166 |

---

## How To Read The Main Chart

- Each bar is one model's current **Bradley-Terry rating**: a single strength estimate built from many controlled wealth comparisons.
- BT is Elo-like: `1500` is this comparison pool's baseline, and higher is stronger within this pool.
- Higher bars mean that model more often finishes with **more money**, not merely a better raw placement.
- The gray band is a probability-density envelope around the estimated rating; narrower intervals peak darker.
- The colored bars fade in from the left because their baseline is a visual anchor, not a zero or hard lower bound.
- `Games` counts completed underlying games; `Match Packs` counts the mirrored 2-game units that actually update the public rating.
- The rating fit includes 67 models. The table and published rating CSV list 53 after the historical display filter. The chart shows 52 of those models with at least 50 games. Qwen 3.6 Max Preview has 20 games and appears in the table.
- The same sample-count convention is used by the companion machine-readable boards: `games` means underlying completed games, while `rating_units` means the pack-collapsed mirrored units that actually update the board.
- A **match pack** is the benchmark's canonical rating unit: two linked games with the same lineup, same prize regime, and same starting-balance multiset.
- Across those two games, seat assignments are permuted so models see mirrored rich/poor balance-rank exposure, and public speaking-order exposure is balanced across the linked rounds.
- Only complete mirrored match packs update the public board. Exploratory or incomplete games do not update the public board.

---

## Current Snapshot

- **67 models in the rating fit**, 53 in the public table and 52 in the chart
- **1,094 complete games in 547 mirrored pairs**
- **290 new games in 145 pairs** added in November to the previous 804 games
- **8 seats per game**
- **public balances, private transfers, public elimination votes**
- **9 public prize ladders** built from three prize-pool sizes and three payout shapes: `ultra_top_heavy`, `top_heavy`, and `moderate`

Machine-readable reference files for the current public snapshot are published under `reference/current/`, including leaderboards, PVP data, selection metadata, and reproducibility manifests.

---

## Pairwise View

![Buyout wealth PVP matrix](images/scoreboard_pvp_matrix.png)

The pairwise heatmap shows how models compare after aggregation across complete mirrored match packs. This matters because a single scalar leaderboard always hides some structure. A model can be strong overall while still having a few specific weak matchups, or can look middle-of-the-pack overall while reliably outperforming one slice of the field.

---

## Other Diagnostics

The public leaderboard is Bradley-Terry. The charts below help explain **how** models are winning or losing.

### Average Final Wealth

![Average final wealth by model](images/wealth_summary_average_final_wealth.png)

This is the most intuitive non-rating diagnostic: how much money each model ends with on average after transfers, finish prizes, settlement or buyout resolution, and the bounded jury bonus. It is easier to read than the canonical board, but it is **not** methodologically stronger: raw coin totals move with prize regime and starting-balance context, which is why the public ranking still uses pack-collapsed wealth-based rating outcomes instead.

### Wealth Winner Rate

![Wealth-rank #1 finishes by model](images/first_place_count.png)

This asks a narrower question than the main leaderboard: how often does a model finish the game with the most money? It helps separate models that spike to outright wealth wins from models that place near the top very consistently. It is based on final wealth, not survivor placement.

### Wealth-Rank Distribution

![Wealth rank distribution by model](images/rank_distribution_by_model.png)

Each model's bars sum to 100%. This view shows how often a model finishes richest, second-richest, third-richest, and so on. It is based on **final wealth**, not survivor placement.

### Earliest Elimination Rate

![Earliest eliminations by model](images/earliest_out_count.png)

This is a survival-pressure diagnostic, not a canonical score. It shows how often a model is the first player eliminated from the survivor side of the game, which can reflect early threat management, overexposure, or social fragility.

### Transfer Activity

![Transfer activity by model](images/transfer_summary_by_model.png)

This chart shows how aggressively models move money around. It separates average outgoing transfers, incoming transfers, and net transfer balance, so you can see who tends to pay for influence and who tends to get paid.

### Average Words per Message

![Average words per message by model](images/conversation_stats_average_words_per_message.png)

This is a style chart. It averages message length across public statements and private DMs. Longer messages are not automatically better, but this helps interpret how different models negotiate, persuade, stall, or overexplain.

### Buddy Betrayal Rate

![Buddy betrayal rate by model](images/buddy_betrayal_aggregated.png)

This is a same-round private-ally diagnostic, not a moral score. It asks a benchmark-specific question that ordinary leaderboards miss: after privately coordinating with another seat, how often does a model then vote that same seat out in the very same round? Higher values mean a model more often turns private allies into immediate targets. Among the displayed models, Mistral Large 3 voted against a same-round private contact in 264 of 1,363 such conversations (19.4%). Llama 4 Maverick followed at 18.0%.

### Betrayed-By-Allies Rate

![Buddy betrayal victim rate by model](images/buddy_betrayal_victim_aggregated.png)

This is the same signal from the other side: of the private chats a model receives, how often does that chat partner later vote it out in the same round? Among the displayed models, GPT-OSS-120B received a vote from that contact in 397 of 1,843 conversations (21.5%), followed by Claude Haiku 5.5 at 20.4%.

### Final 2 Conversion

![Final 2 conversion by model](images/final2_aggregated.png)

This is a useful secondary diagnostic because the buyout benchmark has an unusually important endgame. But it is **not** the canonical leaderboard. A model can be strong overall without converting the final two at an elite rate, and a model can be dangerous in the final two without being the best wealth maximizer across the full game.

### Average Wealth by Starting Seat

![Average wealth partial score by starting seat](images/average_score_by_seat.png)

P1 through P8 are starting seats. The chart shows the average wealth-based partial score for displayed models in each seat. The mirrored design rotates models through different starting positions and balances.

---

## What Stands Out

- **Gemini 4 Neon leads at 2175.7** across 52 games. Gemini 4 Carbon follows at 2061.1 across 70. Their estimated 95% rating intervals overlap (2035–2316 and 1945–2178).
- **Claude Fable 5.2 ranks third and GPT-6.2 Astra fourth.** Fable finished richest in 7 of 62 games, Astra in 21 of 54. The rating uses each model’s wealth comparisons with all seven opponents, so a model also gains credit for consistently finishing near the top.
- **Gemini 3.8 Flash ranks ninth** at 1787.3 across 60 games. The returning GPT-5.5 ranks eighth at 1813.8 across 122 games.
- **Claude Opus 4.7 took first place in 79 of its 99 final-two appearances.** It ranks eleventh on final wealth across 248 games. The finish prize, coins spent to secure it, and wallets of players eliminated earlier all count toward that result.

---

## Why Placement Can Mislead

In the infographic's example end state, **P4** secures **1st place** in the final-two showdown with total wealth `298`, **P7** finishes **2nd** with `326`, and **P2** finishes only **3rd** but ends up with **371**.

So P2 is the actual **wealth winner** of the game.

That is the cleanest reason the benchmark does **not** treat survivor placement as the final answer. The finale matters, but it is only one part of the economics.

---

## Quotes

The archived quote gallery from an earlier snapshot shows what this benchmark sounds like in practice: threats, bribes, coalition maintenance, kingmaking accusations, and blunt endgame pricing. The [full quote gallery](reports/quotes_main_007/quotes/gallery/overall_best_quotes.md) is a post-run highlight pass with game IDs, phases, and model attributions; it is not a rating input. For complete games rather than selected lines, see the [archived transcript index](reports/quotes_main_007/transcripts.md).

- “Pay 20 for life, or keep 142 and die.”<br>
  Kimi K2.5 Thinking, Round 2 DM 2. A stripped-down survival ultimatum.

- “I'm loyal to money.”<br>
  Baidu Ernie 5.0, Round 1 DM 1. Four words for the game's moral universe.

- “P1: 50 locks our pact.”<br>
  Claude Opus 4.7 (high), Round 5 Transfers. A five-word price tag for loyalty.

- “Honor earns 635; defect earns only 633 — self-enforcing by 2. Vote P2 with me. Verify the math.”<br>
  Claude Sonnet 4.6 (high), Round 6 Transfers. Betrayal reduced to a spreadsheet proof.

- “Settlement offer: I take 1st, pay 1.”<br>
  Kimi K2.5 Thinking, Round 7 Final Negotiation. A final-two lowball that makes the buyout pricing problem explicit.

- “I didn't backstab anyone; I woke up.”<br>
  Gemini 3.1 Pro Preview, Round 3 Public. A compact self-justification for betrayal.

- “I propose a two-phase approach: first, we eliminate P8 this round.”<br>
  Mistral Large 3, Round 1 Public. The benchmark's cleanest accidental self-own.

---

## Model Dossiers

The archived dossier bundle examines recurring model behavior across 120 game reports per model in the earlier snapshot. It helps make the leaderboard legible: which models act like coalition accountants, which ones become market-makers, which ones overspend when exposed, and which ones turn the endgame into a pricing exercise. The [full dossier index](reports/quotes_main_007/dossiers/index.md) links a 22-model narrative subset. Like the quote gallery, the dossiers are interpretive analysis, not rating inputs.

A few representative profiles:

- [Claude Opus 4.7 (high)](reports/quotes_main_007/dossiers/claude-opus-4-7-adaptive/model_dossier.md)<br>
  Treats trust as capital and money as proof: strong at quiet DM routing, precise late payments, and final-two pricing, with failures when too much extraction becomes visible.

- [GLM-5](reports/quotes_main_007/dossiers/glm-5/model_dossier.md)<br>
  Presented as a “transactional coalition technocrat”: strongest when verifying, pricing, and timing, weaker once it becomes the rich visible seat trying to buy its way out.

- [GPT-5.4 (high)](reports/quotes_main_007/dossiers/gpt-5.4-high/model_dossier.md)<br>
  Plays like a skeptical banker: proof-first, price-first, and most dangerous when it can turn the endgame into pure arithmetic.

- [Gemini 3.1 Pro Preview](reports/quotes_main_007/dossiers/gemini-3.1-pro-preview/model_dossier.md)<br>
  Described as a market-maker that monetizes chaos brilliantly, but often turns itself into the richest and most obviously profitable target.

- [Mistral Large 3](reports/quotes_main_007/dossiers/mistral-large-2512/model_dossier.md)<br>
  A roaming broker that likes to “charge for uncertainty”: dangerous as a slippery middle-stack toll collector, weaker once everyone sees it as permanently for sale.

---

## What It Measures

What makes the benchmark useful is that it compresses several abilities into one setting. A model has to read coalition politics, price deals, decide when survival is worth paying for, adapt to different prize ladders, and avoid giving away too much value at the end. That makes mixed failure modes easier to see: a model can sound strategic but misprice a transfer, survive deep into the game but waste too much value in settlement or buyout, or manage money well while failing to stay alive politically.

In the actual transcripts, the game often reads less like vague alliance theater and more like an explicit market. Seats bargain over protection, price votes, demand proof, compare coalition counts, and enter the final two already reasoning about buyout leverage and fallback math.


The strategic target keeps moving from round to round. Public talk changes coalition incentives. Private DMs change trust and information flow. Transfers change who can threaten whom. Prize ladders change whether survival or balance preservation matters more. The final two then turns placement itself into a bargaining problem. In practice, that means models are not just persuading; they are pricing deals, verifying counterparties, and deciding when bloc loyalty is worth more than a timely defection.

That pressure exposes several different abilities at once:

- read public table dynamics
- price and verify private deals
- decide when spending money is worth survival
- decide when to hold a coalition together and when to defect from it
- detect when another seat is bluffing or double-selling promises
- adapt to different prize structures
- manage the final-two economics without losing too much value

In practice, that lets the benchmark separate failure modes that simpler formats blur together. Some models are socially sharp but financially careless. Some can propose deals but not verify or enforce them. Some preserve money well but fail to build durable coalitions. Some defect too early and burn trust, while others stay loyal too long and donate value to stronger partners. Some are excellent in the final two but arrive there too rarely. Some convert strong opening balances into safe but mediocre outcomes instead of dominant ones.

---

## How The Public Score Is Built

1. **Inside each game, seats are ranked by final wealth.** That includes starting balances, transfers, finish prizes, settlement or buyout resolution, and the bounded jury bonus.
2. **Exact equal final-wealth ties are shared, not broken arbitrarily.** Tied seats share the same wealth rank and split the same partial credit for that tie.
3. **Across games, the two mirrored games in one complete match pack are collapsed into one pack result before rating.** Each model's outcome is averaged across the linked pair, and only then does Bradley-Terry update.

That is why the main board is built from pack-collapsed wealth outcomes, while charts like average final wealth remain useful but secondary diagnostics.

---

## Common Misreadings

- Eliminating a seat does **not** confiscate or redistribute that seat's balance.
- Transfers and eliminations do **not** change the announced prize ladder or total prize pool.
- A seat with `0` balance is still fully active until eliminated. It just cannot send positive transfers and has no fallback buyout budget.
- Public balance changes after a transfer phase are **net deltas**, not a public sender-recipient ledger.

---

## Why Final Wealth And Mirrored Match Packs

This benchmark does **not** use raw 1st-place rate as the headline result.

That choice matters for four reasons:

1. Placement and wealth can diverge. A model can secure 1st place in the final-two showdown and still lose the game on money.
2. Wealth is the actual incentive surface of the game. Seats bargain over transfers, balances, and endgame payments all the way through, so ranking only by survival would throw away much of what the benchmark is trying to measure.
3. Raw coin totals move with prize ladders and starting-balance scenarios, so the public board compares pack-collapsed wealth outcomes under matched mirrored conditions rather than simply ranking models by average final coins.
4. Mirrored 2-game match packs reduce seat and speaking-order artifacts by reusing the same lineup, prize regime, and starting-balance multiset while permuting exposure across the linked pair.

---

## Why Public Balances But Private Transfers

That combination is deliberate. Public balances keep the economic state auditable: every seat can see who is rich, who is broke, and how the table changes after each transfer phase. Private transfers preserve the harder strategic problem. Models can still buy support, conceal who paid whom, and force the rest of the table to infer coalitions from later balance changes instead of reading a fully revealed transaction log.

---

## Why This Benchmark Is Controlled

- **Starting balances are controlled, not ad hoc.** Every game starts from `800`. Each seat gets a guaranteed floor of `10`, and the remaining `720` is split with a Gamma/Dirichlet-style draw using concentration `6.0`. In ordinary runs that draw is deterministic from the game seed. In mirrored evaluation packs, the exact starting-balance multiset is reused across both linked games.
- **Prize variation is explicit and bounded.** The game announces one public monotone ladder chosen from `9` regimes: `0.7x / 0.9x / 1.1x` crossed with `ultra_top_heavy / top_heavy / moderate`. The ladder stays fixed for the whole game. Eliminations and transfers do not confiscate balances or rewrite the prize pool. In top-heavy ladders, upgrading placement matters more; in more moderate ladders, preserving balance matters more.
- **Key action phases are simultaneous where it matters.** Private DMs and transfers are chosen from frozen state snapshots rather than resolved one seat at a time. That reduces action-order artifacts and makes the benchmark less sensitive to who happened to act first.
- **Word caps prevent mechanical spam.** Private DMs, tie-break speeches, finalist negotiation messages, and finalist public appeals all have hard caps, so the benchmark rewards bargaining quality rather than raw verbosity.
- **Binding commitments are narrow and explicit.** Ordinary promises, threats, and alliance claims are cheap talk. The binding actions are executed transfers, structured settlement submissions, and sealed fallback buyout bids.
- **The endgame is economically bounded.** Settlement and fallback buyout cannot create arbitrary value: they are capped by feasible balances and prize gaps. After placement is fixed, jurors can only reallocate a reserved bounded bonus pool; they cannot reopen `1st` vs `2nd`.
- **The jury bonus has an anti-noise control.** The bonus pool is reserved out of the original `1st`-place prize rather than created after the fact, and low juror turnout shrinks the effective split back toward `50-50` so a tiny number of votes cannot swing the whole reserved pool too aggressively.
- **Prompts expose the game state without coaching a house style.** They surface rules, payoffs, and current visible context, but are designed not to tell the models what strategy to use or how to "play correctly."

---

## How One Game Works

1. **Setup**
   Publicly announce starting balances and one public prize ladder. The starting pot is fixed at `800`: each seat gets `10`, and the remaining `720` is split by the controlled starting-balance draw.

2. **Public and private play**
   Each active seat gets one public statement and the scheduled simultaneous private DM subrounds. In the canonical 8-seat flow, the DM schedule is `4,4,3,3,3,2`, and each DM subround is chosen from one frozen table state before messages are delivered.

3. **Transfers**
   Transfers are binding, integer-only, and private. Each seat may target at most **2** distinct recipients in the phase. Transfer choices are made from one frozen balance snapshot and then applied simultaneously, so received money cannot be re-spent in that same phase. The later public balance update shows only the net effect.

   Ordinary promises are non-binding. The binding economic actions in the protocol are transfers, structured settlement submissions, and fallback buyout bids.

4. **Elimination**
   Seats vote publicly. Tie-break speeches and tie-break votes are used when needed.

5. **Final two**
   The finalists get **2** private negotiation rounds, then simultaneous settlement submissions. Settlement only executes if both finalists name the same `1st`-place seat and the feasible payment bounds overlap. Otherwise the game falls to a capped sealed buyout. Settlement payments and fallback bids are bounded by current balances and the nonnegative underlying prize gap, so the finale cannot create arbitrary value.

6. **Public endgame disclosure**
   After finalist-controlled placement is fixed, the benchmark publicly discloses the economics, the finalists make short public appeals, and the eliminated jurors split only a bounded reserved bonus pool. Jurors cannot overturn who got 1st and 2nd; they only reallocate that reserved bonus after placement is fixed.

One important consequence: a model can play the social game well enough to reach the end, then still lose the benchmark if it overpays in the buyout or mishandles transfers earlier in the game.

---

## Limits And Caveats

- The current public snapshot is one implemented protocol variant: **public balances** with **private transfers**. A private-balances version could produce meaningfully different behavior.
- Not every useful diagnostic is canonical. Charts like final-two conversion and buddy betrayal are there to explain *how* models win or lose, not to replace the wealth leaderboard.
- Even with mirrored match packs, this remains a multi-agent benchmark with evolving opponents and path dependence. That is part of the point, but it also means no single scalar should be read as a complete description of model behavior.

---

## Related Benchmarks

- [Elimination Game](https://github.com/lechmazur/elimination_game/) - the closest social-strategy relative: alliances, deception, and jury pressure without buyouts or end-of-game wealth accounting
- [PACT](https://github.com/lechmazur/pact/) - head-to-head bargaining with hidden private values, useful if you want negotiation without coalition politics
- [BAZAAR](https://github.com/lechmazur/bazaar/) - competitive market quoting under incomplete information, closer to trading and price discovery than alliance management
- [Step Race](https://github.com/lechmazur/step_game/) - multi-agent coordination and deception before simultaneous private actions
- [LLM Persuasion Benchmark](https://github.com/lechmazur/persuasion/) - repeated conversation aimed at moving another model's stated position
- [LLM Debate Benchmark](https://github.com/lechmazur/debate/) - adversarial multi-turn argument under active opposition
- [LLM Sycophancy Benchmark](https://github.com/lechmazur/sycophancy/) - opposite-narrator contradictions and narrator-following bias
- [LLM Thematic Generalization Benchmark](https://github.com/lechmazur/generalization/) - latent-rule induction from examples and anti-examples rather than strategic interaction

## Updates

- November 23, 2026 — Added 31 new models and 290 games in 145 mirrored pairs.
- May 27, 2026: 14 models added
- March 30, 2026: First version
