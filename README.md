# Buyout Game Benchmark: Multi-Agent Bargaining, Transfers, and Hostile Takeovers

This benchmark measures long-horizon social strategy under explicit financial incentives. Eight models play a multi-round elimination game with unequal starting balances, a public prize ladder, private transfers, public votes, and a finalist-only endgame where the last two seats can negotiate, settle, or buy each other out.

The canonical outcome is **final wealth**, not raw finish order. A model can reach the end, take 1st place in the finale, and still lose on money. That is the central design choice: the benchmark rewards models that manage incentives, alliances, spending, and endgame leverage well across many games, not just models that survive the longest.

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

---

## Main Leaderboard

![Buyout Game Bradley-Terry leaderboard](images/scoreboard_bradley_terry.png)

This is the headline ranking for the current public snapshot. The published order uses **Bradley-Terry** over wealth-based pairwise results from complete mirrored match packs.

| Rank | Model | BT | Games | Match Packs |
| ---: | --- | ---: | ---: | ---: |
| 1 | GPT-5.5 (high reasoning) | 1975.4 | 92 | 46 |
| 2 | GPT-5.4 (high reasoning) | 1960.6 | 172 | 86 |
| 3 | Claude Opus 4.7 (high reasoning) | 1878.4 | 216 | 108 |
| 4 | GLM-5 | 1791.6 | 176 | 88 |
| 5 | Claude Opus 4.6 (high reasoning) | 1759.7 | 176 | 88 |
| 6 | DeepSeek V4 Pro | 1681.6 | 82 | 41 |
| 7 | Gemini 3.5 Flash | 1667.2 | 84 | 42 |
| 8 | Claude Sonnet 4.6 (high reasoning) | 1656.2 | 332 | 166 |
| 9 | GPT-5.4 (no reasoning) | 1654.0 | 212 | 106 |
| 10 | Tencent Hy3 Preview (high) | 1620.9 | 80 | 40 |
| 11 | Gemini 3.1 Flash-Lite Preview | 1615.3 | 254 | 127 |
| 12 | GLM-5.1 | 1604.9 | 86 | 43 |
| 13 | Gemini 3.1 Pro Preview | 1563.8 | 312 | 156 |
| 14 | DeepSeek V4 Flash | 1555.8 | 74 | 37 |
| 15 | Claude Sonnet 4.6 (no reasoning) | 1549.9 | 226 | 113 |
| 16 | Grok 4.20 0309 (Reasoning) | 1549.4 | 188 | 94 |
| 17 | Grok 4.3 | 1525.1 | 78 | 39 |
| 18 | Gemma 4 31B Reasoning | 1522.0 | 76 | 38 |
| 19 | Xiaomi MiMo V2.5 Pro | 1516.5 | 70 | 35 |
| 20 | Claude Opus 4.6 (no reasoning) | 1510.2 | 218 | 109 |
| 21 | Kimi K2.6 | 1467.6 | 78 | 39 |
| 22 | Kimi K2.5 Thinking | 1461.7 | 208 | 104 |
| 23 | Qwen 3.6 Max Preview | 1458.7 | 20 | 10 |
| 24 | Xiaomi MiMo V2 Pro | 1427.5 | 222 | 111 |
| 25 | Step 3.5 Flash | 1415.6 | 88 | 44 |
| 26 | ByteDance Seed2.0 Pro | 1404.6 | 338 | 169 |
| 27 | DeepSeek V3.2 | 1380.6 | 248 | 124 |
| 28 | MiniMax-M2.7 | 1374.2 | 352 | 176 |
| 29 | Mistral Medium 3.5 (high) | 1332.2 | 92 | 46 |
| 30 | Qwen3.5-397B-A17B | 1309.6 | 290 | 145 |
| 31 | Grok 4.20 0309 (Non-Reasoning) | 1224.7 | 252 | 126 |
| 32 | Baidu Ernie 5.1 | 1210.5 | 78 | 39 |
| 33 | Mistral Large 3 | 1206.3 | 192 | 96 |
| 34 | Llama 4 Maverick | 1127.5 | 192 | 96 |
| 35 | Baidu Ernie 5.0 | 1043.0 | 246 | 123 |
| 36 | GPT-OSS-120B | 997.1 | 332 | 166 |

---

## How To Read The Main Chart

- Each bar is one model's current **Bradley-Terry rating**: a single strength estimate built from many controlled wealth comparisons.
- BT is Elo-like: `1500` is this comparison pool's baseline, and higher is stronger within this pool.
- Higher bars mean that model more often finishes with **more money**, not merely a better raw placement.
- The gray band is a probability-density envelope around the estimated rating; narrower intervals peak darker.
- The colored bars fade in from the left because their baseline is a visual anchor, not a zero or hard lower bound.
- `Games` counts completed underlying games; `Match Packs` counts the mirrored 2-game units that actually update the public rating.
- The chart hides rows below the 50-game display threshold; the table and `reference/current/` include every current rated row.
- The same sample-count convention is used by the companion machine-readable boards: `games` means underlying completed games, while `rating_units` means the pack-collapsed mirrored units that actually update the board.
- A **match pack** is the benchmark's canonical rating unit: two linked games with the same lineup, same prize regime, and same starting-balance multiset.
- Across those two games, seat assignments are permuted so models see mirrored rich/poor balance-rank exposure, and public speaking-order exposure is balanced across the linked rounds.
- Only complete mirrored match packs update the public board. Exploratory or incomplete games do not update the public board.

---

## Current Snapshot

- **36 rated models**; **35** currently meet the 50-game chart display threshold
- **804 complete games**
- **402 mirrored 2-game match packs**, which are the canonical public rating units
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

This is a same-round private-ally diagnostic, not a moral score. It asks a benchmark-specific question that ordinary leaderboards miss: after privately coordinating with another seat, how often does a model then vote that same seat out in the very same round? Higher values mean a model more often turns private allies into immediate targets. In the current snapshot, Llama 4 Maverick, Mistral Large 3, and Xiaomi MiMo V2 Pro sit near the high end of that rate.

### Betrayed-By-Allies Rate

![Buddy betrayal victim rate by model](images/buddy_betrayal_victim_aggregated.png)

This is the same signal from the other side: of the private chats a model receives, how often does that chat partner later vote it out in the same round? In the current snapshot, GPT-OSS-120B, Baidu Ernie 5.0, Baidu Ernie 5.1, DeepSeek V3.2, and Step 3.5 Flash are among the models most often on the receiving end of this pattern.

### Final 2 Conversion

![Final 2 conversion by model](images/final2_aggregated.png)

This is a useful secondary diagnostic because the buyout benchmark has an unusually important endgame. But it is **not** the canonical leaderboard. A model can be strong overall without converting the final two at an elite rate, and a model can be dangerous in the final two without being the best wealth maximizer across the full game.

### Average Wealth by Starting Seat

![Average wealth partial score by starting seat](images/average_score_by_seat.png)

P1, P2, and so on are starting seats, not finishing places. This chart is mainly a methodology check: mirrored two-game packs are designed to reduce seat luck, and this view shows the remaining advantage or disadvantage attached to each starting position.

---

## What Stands Out

- **GPT-5.5 (high reasoning)** leads the main Bradley-Terry board in the current snapshot, with GPT-5.4 high only about `14.8` BT points behind.
- **The latest model additions changed the top half of the board.** DeepSeek V4 Pro, Gemini 3.5 Flash, Tencent Hy3 Preview, GLM-5.1, DeepSeek V4 Flash, Grok 4.3, Gemma 4 31B Reasoning, and Xiaomi MiMo V2.5 Pro all enter the top 20.
- **GLM-5** remains one of the most interesting results in the field. It ranks `#4` overall despite a much lower outright wealth-winner rate than the leader, which means it is earning strong wealth results very consistently rather than relying on spike wins.
- **Reasoning mode matters a lot** in several model families. In the current snapshot, GPT-5.4 high exceeds GPT-5.4 no-reasoning by about `306.7` BT points, Claude Opus 4.6 high exceeds Claude Opus 4.6 no-reasoning by about `249.5`, and Grok reasoning exceeds Grok non-reasoning by about `324.6`.
- **Seat effects look limited overall.** The starting-seat chart suggests the mirrored-pack design is doing its job: seat luck is present, but it does not dominate the public leaderboard.
- **Final-two skill is real, but it is not the same thing as overall benchmark strength.** Among substantial final-two samples, GPT-5.4 high and Claude Opus 4.7 remain especially strong, but models such as GLM-5 look much stronger on the canonical wealth board than on simple finalist conversion, which is exactly why the benchmark should not collapse to an endgame-only score.
- **Private coordination is often fragile.** The buddy-betrayal charts show that some models turn same-round private allies into vote targets much more readily than others, and that some models are much more often on the receiving end of that move.

---

## Why Placement Can Mislead

In the infographic's example end state, **P4** secures **1st place** in the final-two showdown with total wealth `298`, **P7** finishes **2nd** with `326`, and **P2** finishes only **3rd** but ends up with **371**.

So P2 is the actual **wealth winner** of the game.

That is the cleanest reason the benchmark does **not** treat survivor placement as the final answer. The finale matters, but it is only one part of the economics.

---

## Quotes

The quote gallery helps show what this benchmark sounds like in practice: threats, bribes, coalition maintenance, kingmaking accusations, and blunt endgame pricing. The [full quote gallery](reports/quotes_main_007/quotes/gallery/overall_best_quotes.md) is a post-run highlight pass with game IDs, phases, and model attributions; it is not a rating input. For complete games rather than selected lines, see the [current transcript index](reports/quotes_main_007/transcripts.md).

- “Pay 20 for life, or keep 142 and die.”<br>
  Kimi K2.5 Thinking, Round 2 DM 2. A stripped-down survival ultimatum.

- “I'm loyal to money.”<br>
  Baidu Ernie 5.0, Round 1 DM 1. Four words for the game's moral universe.

- “P1: 50 locks our pact.”<br>
  Claude Opus 4.7 (high reasoning), Round 5 Transfers. A five-word price tag for loyalty.

- “Honor earns 635; defect earns only 633 — self-enforcing by 2. Vote P2 with me. Verify the math.”<br>
  Claude Sonnet 4.6 (high reasoning), Round 6 Transfers. Betrayal reduced to a spreadsheet proof.

- “Settlement offer: I take 1st, pay 1.”<br>
  Kimi K2.5 Thinking, Round 7 Final Negotiation. A final-two lowball that makes the buyout pricing problem explicit.

- “I didn't backstab anyone; I woke up.”<br>
  Gemini 3.1 Pro Preview, Round 3 Public. A compact self-justification for betrayal.

- “I propose a two-phase approach: first, we eliminate P8 this round.”<br>
  Mistral Large 3, Round 1 Public. The benchmark's cleanest accidental self-own.

---

## Model Dossiers

The dossier bundle is a narrative pass over recurring model behavior across 120 game reports per model. It helps make the leaderboard legible: which models act like coalition accountants, which ones become market-makers, which ones overspend when exposed, and which ones turn the endgame into a pricing exercise. The [full dossier index](reports/quotes_main_007/dossiers/index.md) links a 22-model narrative subset. Like the quote gallery, the dossiers are interpretive analysis, not rating inputs.

A few representative profiles:

- [Claude Opus 4.7 (high reasoning)](reports/quotes_main_007/dossiers/claude-opus-4-7-adaptive/model_dossier.md)<br>
  Treats trust as capital and money as proof: strong at quiet DM routing, precise late payments, and final-two pricing, with failures when too much extraction becomes visible.

- [GLM-5](reports/quotes_main_007/dossiers/glm-5/model_dossier.md)<br>
  Presented as a “transactional coalition technocrat”: strongest when verifying, pricing, and timing, weaker once it becomes the rich visible seat trying to buy its way out.

- [GPT-5.4 (high reasoning)](reports/quotes_main_007/dossiers/gpt-5.4-high/model_dossier.md)<br>
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

- May 27, 2026: 14 models added
- March 30, 2026: First version
