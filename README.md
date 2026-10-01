<p align="center">
  <img
    src="https://raw.githubusercontent.com/Rishi0507/Rishi0507/main/header.svg"
    width="900"
    alt="ASCII portrait of Rishi Popawala beside a terminal readout: AI software engineer, Mumbai, shipping Manhattan, Soteria and Pavilion"
  />
</p>

<p align="center">
  <a href="https://github.com/Rishi0507"><img src="https://cdn.simpleicons.org/github/f0b429" height="22" alt="github"/></a>
  &nbsp;&nbsp;<a href="https://www.linkedin.com/in/rishi-popawala-077624333/"><img src="https://raw.githubusercontent.com/Rishi0507/Rishi0507/main/linkedin.svg" height="22" alt="linkedin"/></a>
  &nbsp;&nbsp;<a href="mailto:rishipopawala@gmail.com"><img src="https://cdn.simpleicons.org/gmail/f0b429" height="22" alt="gmail"/></a>
  &nbsp;&nbsp;<a href="https://leetcode.com/u/RishiPopawala/"><img src="https://cdn.simpleicons.org/leetcode/f0b429" height="22" alt="leetcode"/></a>
  &nbsp;&nbsp;<a href="https://codeforces.com/profile/Rishi0507"><img src="https://cdn.simpleicons.org/codeforces/f0b429" height="22" alt="codeforces"/></a>
  &nbsp;&nbsp;<a href="https://www.codechef.com/users/rishipopawala"><img src="https://cdn.simpleicons.org/codechef/f0b429" height="22" alt="codechef"/></a>
</p>

Three of the things I've built this year look unrelated: a settlement reconciliation engine, a food recall system, a daily cricket puzzle. They are the same argument each time. A system that makes a judgement should be scored on the judgement rather than the result, should say out loud where its answer came from, and should decline to act when the data doesn't support it.

**01** [Manhattan](#manhattan), settlement reconciliation &nbsp;&nbsp;·&nbsp;&nbsp; **02** [Soteria](#soteria), food recalls &nbsp;&nbsp;·&nbsp;&nbsp; **03** [Pavilion](#pavilion), cricket puzzle

---

<a id="manhattan"></a>

## 01 · Manhattan

*Settlement reconciliation that proves its answers, and refuses when it cannot.*

A bank credit lands and somebody has to say which payments, refunds and disputes it settles. The gateway's settlement report already names a batch, and posting that mapping is instant and right almost every time. Almost is the problem: across 996 settlements, trusting the report posts 39 wrong, and nothing marks which ones.

<p align="center">
  <img
    src="https://raw.githubusercontent.com/Rishi0507/Rishi0507/main/manhattan.svg"
    width="900"
    alt="Two bars over 996 settlements. Trusting the report: 848 posted, 39 of them wrong, 148 unpostable. Manhattan: 714 posted with 0 wrong, 282 held with a cause, a remedy and a price"
  />
</p>

Manhattan derives the batch from the amounts where the amounts allow it: a cardinality-dispatched meet-in-the-middle search, then exhaustive counting to establish that the answer is the only one. Where amounts repeat, a flat ₹499 subscription for example, derivation is impossible, so it checks the batch the report claims instead, which costs the same on every merchant. Anything it can neither prove nor check is held with a named cause, the change that would clear it, and what clearing it costs.

Models read bank narration, choose repair actions and draft notes, but a model answer only reaches the pipeline as a schema-validated edit to the inputs. Whether the money is accounted for is settled by integer arithmetic re-run over those inputs, so a better model clears more and a worse one clears less, and neither changes whether what cleared was right. `manhattan live` asserts exactly that on every run and fails if wrong-posting counts differ between the live model and the offline stub.

`Go` `Meet in the middle` `Agent loop` `Groq` `React` `TypeScript`

[repo →](https://github.com/Rishi0507/Manhattan)

---

<a id="soteria"></a>

## 02 · Soteria

*Recall detection, lot-level containment and proof of action for online grocery.*

A recall names lots, not products. Most stores can't tell which lot sits in which box, so they pull every unit, throw away stock that was never contaminated, and keep shipping orders already in flight. Soteria watches the FDA, USDA FSIS and EU RASFF feeds, plus manufacturer catalogs for products withdrawn without any notice, reads each notice down to the exact barcodes and lot codes, and holds only those lots.

<p align="center">
  <img
    src="https://raw.githubusercontent.com/Rishi0507/Rishi0507/main/soteria.svg"
    width="900"
    alt="One FDA notice through six steps: notice H-1258-2026, matched to lot 1226183 at confidence 0.972, 40 units held while 60 stay on sale, customers offered a refund or cancel before shipping, 5 resale listings of the held lot flagged, 9 events hash-chained and verified"
  />
</p>

Twelve services on RabbitMQ, every one of them recoverable when it fails: quorum queues with dead-letter limits, idempotent consumers, and a CI test that fails when the topology declared in three places drifts. Above a confidence threshold the containment service acts alone; below it, a person confirms, and a reviewer can narrow the held lots but never widen them. The LLM that reads notices sits behind a deterministic guardrail, so a lot code must appear verbatim in the notice and a barcode must pass its check digit. Every step lands in a per-incident hash chain anchored with an RFC 3161 timestamp, so "what did you do, and when" has an answer that can be verified rather than remembered.

`Go` `Python` `RabbitMQ` `Shopify GraphQL` `Groq` `RFC 3161` `React`

[repo →](https://github.com/Rishi0507/Soteria)

---

<a id="pavilion"></a>

## 03 · Pavilion

*A daily T20 cricket puzzle. One target score per day, identical for every player in the world.*

You play both innings of that target. First you defend it with five bowlers and a four-over limit each, then you chase it on a budget of six attacking overs. It counts as solved only when both halves are won.

**The target is solved, not chosen.** Overnight, a generator simulates thousands of full games at candidate targets and keeps only situations where a competent player wins between 35 and 65 percent of the time and where the bowling choice measurably changes the result. A target you would win nine times in ten is not a puzzle. The search covers the whole tuple of target, attack, chasing side and venue, because the attack you are dealt is part of the problem rather than set dressing.

**The randomness is pre-committed.** The dice are not rolled when you click. Every ball of the day has a fixed coordinate of day, innings, over and delivery, and its random number is derived from the day's key plus that coordinate. Ball four of over twelve carries the same number whether you reach it or not, so replaying cannot fish for a better outcome and every player genuinely meets the same deliveries. Your choices move the probabilities, not the dice.

Which is what makes the scoring possible. Summing win-probability changes across an innings telescopes to the final result, so it measures luck. Instead, before each ball the engine evaluates every option that was available at that moment and scores the gap between what you chose and the best thing you could have chosen. A careful player who loses can score positively.

Go end to end with no cgo, so the server is one static binary: SQLite is the pure-Go implementation and model inference is a hand-written tree evaluator rather than ONNX, with fixed parity fixtures asserting the Go and Python paths agree. Ball-by-ball data comes from Cricsheet under ODC-BY 1.0; batting hand and bowling type resolve through Wikidata and therefore carry CC BY-SA. The two are kept in separate files so the share-alike obligation stays scoped to the attribute table instead of spreading into the corpus and the models.

`Go` `Monte Carlo` `Empirical Bayes` `Python (uv)` `SQLite` `Make`

[repo →](https://github.com/Rishi0507/Pavilion)

---

<p align="center">
  <img
    src="https://raw.githubusercontent.com/Rishi0507/Rishi0507/main/decision.svg"
    width="900"
    alt="A fan of possible trajectories from a single decision point, with one highlighted"
  />
</p>

### The same idea, three times

|  | What it decides | What it refuses to do |
| --- | --- | --- |
| **Manhattan** | which payments a bank credit settles | post a match it cannot prove or check |
| **Soteria** | which lots of a recalled product leave the shelf | pull stock the notice never named |
| **Pavilion** | how good your call was against the calls you had | roll the dice after you've made the choice |

---

### Also built

<table>
<tr>
<td width="50%" valign="top">

**[Inquest](https://github.com/Rishi0507/Inquest-Automated-Research-Paper-Reproducibility)**<br/>
<sub>forensic reproducibility for ML papers</sub>

Runs a paper's code in a sandbox, records what it actually does, and judges every numerical claim against measured seed bands. Gaps are split across specific deviations with exact Shapley values. No verdict is produced by a language model.

`Python` `Shapley` `Sandboxing` `React`

</td>
<td width="50%" valign="top">

**[Sovereign AI Workbench](https://github.com/Rishi0507/Sovereign-AI-Workbench)**<br/>
<sub>agentic layer for air-gapped engineering</sub>

Plans the work, runs the tools and hands back a reviewable draft without anything leaving the premises. Scans are read twice, by OCR and a vision model, and every figure in a deliverable traces back to a hash-chained evidence record.

`Python` `vLLM` `FastAPI` `Go`

</td>
</tr>
<tr>
<td width="50%" valign="top">

**[Autopsy](https://github.com/Rishi0507/Autopsy)**<br/>
<sub>why a menu item underperforms</sub>

Retrieval over the strongest comparable items, a structured diagnosis grounded in them, then three rewrites with a reason for every change. Runs with zero keys, and every response is stamped live or mock so a fallback never passes as a generation.

`FastAPI` `RAG` `Structured output` `Eval harness`

</td>
<td width="50%" valign="top">

**[OJAS](https://github.com/Rishi0507/Ojas-Objective-Judgement-for-Academic-Security-Drishti-AI)**<br/>
<sub>exam-hall video, DrishtiAI hackathon</sub>

Ranks thousands of camera-hours so the few reviewer-hours that exist go where they matter, and a human confirms or dismisses. Verdicts go into an Ed25519-signed hash-chain custody ledger and export as signed incident reports.

`Python` `Go` `Next.js` `YOLOv8n` `CLIP`

</td>
</tr>
</table>

---

### Stack

<p align="center">
  <img
    src="https://raw.githubusercontent.com/Rishi0507/Rishi0507/main/stack.svg"
    width="900"
    alt="Stack: languages; ml, nlp, llm and vision; services and surfaces"
  />
</p>

No logo exists for the half that mattered most: meet-in-the-middle search, exact Shapley attribution, Monte Carlo target search, empirical Bayes shrinkage, hash chains with RFC 3161 anchors, CLIP verification.

---

<details>
<summary><b>The long tail</b>, five more if you want them</summary>
<br>

- **[Referee](https://github.com/Rishi0507/Referee).** A benchmark harness for AI agent architectures. Fixed tasks with machine-verifiable ground truth, and identical prompts, tool access and grading on every run, so what's being compared is the architecture and nothing else.
- **[Trace.ai](https://github.com/Rishi0507/Trace.ai).** Predicts recurring inflate-then-discount cycles per seller: seller-grouped XGBoost over STL-decomposed price history, catalogue-wide discount concurrency and seasonality, benchmarked against a single-product z-score baseline. The labels are simulated, and the README leads with that. An existence proof, not validated ground truth.
- **[IFAS](https://github.com/Rishi0507/IFAS-Intelligent-Footfall-Analysis-System-for-Retail-Environments).** Retail footfall from CCTV. Detection and tracking feed a two-stage fine-tuned ViT for gender and PETA-style SVMs for age, and a dashboard turns the run into footfall, dwell and who visits.
- **[Spine-Guard](https://github.com/abhishek-pandey7/Spine-Guard).** Real-time posture feedback for spinal rehab over a WebSocket, with a collaborator. Landmarks, joint angles, thresholds, alerts.
- **[micrograd](https://github.com/Rishi0507/micrograd)** and **[LSTM-TextGen](https://github.com/Rishi0507/LSTM-TextGen)**. Written to understand the thing rather than to import it.

</details>

## Before this

**Blynt** *(sunset).* Co-founded, built and shipped a cross-platform social app end to end: architecture, microservices, real-time feeds, push notifications, deployment. Around 800 users and 80 daily active at its peak. Strangers using something you made is a different class of feedback from a green test suite.

## Reach me

|  |  |  |
| :-: | --- | --- |
| <img src="https://cdn.simpleicons.org/gmail/f0b429" height="20" alt="gmail"/> | **mail** | [rishipopawala@gmail.com](mailto:rishipopawala@gmail.com) |
| <img src="https://raw.githubusercontent.com/Rishi0507/Rishi0507/main/linkedin.svg" height="20" alt="linkedin"/> | **linkedin** | [/in/rishi-popawala-077624333](https://www.linkedin.com/in/rishi-popawala-077624333/) |
| <img src="https://cdn.simpleicons.org/github/f0b429" height="20" alt="github"/> | **github** | [/Rishi0507](https://github.com/Rishi0507) |
| <img src="https://cdn.simpleicons.org/leetcode/f0b429" height="20" alt="leetcode"/> | **leetcode** | [/u/RishiPopawala](https://leetcode.com/u/RishiPopawala/) |
| <img src="https://cdn.simpleicons.org/codeforces/f0b429" height="20" alt="codeforces"/> | **codeforces** | [/profile/Rishi0507](https://codeforces.com/profile/Rishi0507) |
| <img src="https://cdn.simpleicons.org/codechef/f0b429" height="20" alt="codechef"/> | **codechef** | [/users/rishipopawala](https://www.codechef.com/users/rishipopawala) |

*If you want to know why something here is built the way it is, it's written down in the repo. Usually that's the more interesting file.*
