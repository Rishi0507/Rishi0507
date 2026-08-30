# Score the decision, not the outcome

**Rishi Popawala** · AI software engineer · Mumbai
DJ Sanghvi College of Engineering, Information Technology, 2024–28

Three of the things I've built this year look unrelated: an exam-hall video pipeline, a menu diagnosis service, a daily cricket puzzle. They are the same argument each time. A system that makes a judgement should be scored on the judgement rather than the result, should say out loud where its answer came from, and should decline to answer when the data doesn't support one.

---

## OJAS · Objective Judgement for Academic Sincerity

*Offline exam-hall video analytics. Built for PS2 at the DrishtiAI hackathon.*

800 centres, 4 cameras, 3 hours: 9,600 camera-hours per exam session, roughly 400 days of continuous viewing for one person. Nobody watches all of it. So the problem is not "detect cheating." It is deciding where the few reviewer-hours that actually exist should go, and evidencing that decision well enough to survive an appeal. OJAS ranks footage and a human confirms or dismisses. A wrong, unappealable accusation is a worse failure than a missed one, and every threshold in the codebase falls out of that one sentence.

Three stages, split by how expensive each one is to iterate on. **Python** for the classical CV: a motion ensemble of frame differencing, MOG2/KNN background subtraction and Farneback optical flow, weighted so that each method's failure mode is covered by the other two, plus spectral-residual saliency lifted from 2-D image work and applied instead to the 1-D motion time series, which buys sudden non-periodic motion for one FFT pass over the whole video. **Go** for detection, anonymised tracking, pose micro-motion and CLIP verification, compiled to a single static binary an exam centre can run with no Python environment on the machine. **Next.js** for the review surface, where verdicts are written into an Ed25519-signed hash-chain custody ledger and exported as signed incident reports. The calibration engine refuses to propose a threshold when the data won't support one.

`Python` `Go` `Next.js` `OpenCV` `YOLOv8n` `ByteTrack` `CLIP` `Ed25519`

[→ repo](https://github.com/Rishi0507/Ojas-Objective-Judgement-for-Academic-Security-Drishti-AI)

---

## Autopsy

*Diagnoses why a menu item is underperforming, then rewrites the listing three ways with a reason attached to every change.*

Retrieval pulls the strongest comparable items in the same category, ranked on a blend of text similarity and how well those items are genuinely selling. One LLM call returns a structured diagnosis grounded in them: pricing problem, photo problem, weak description, or placement. A second, chained call turns that diagnosis into three rewrites with deliberately different angles and a rationale for each.

The part I care about is the honesty layer. It runs with zero API keys, and without one every call falls through to a deterministic generator reasoning over exactly the data a real model would see. Every response is stamped `"generation_mode": "live"` or `"mock"`, so a fallback can never quietly pass as a generation. The benchmark harness asks whether the retrieval step actually makes diagnoses more specific than a zero-shot version, and keeps pipeline validation separate from model-quality evaluation, because those are two different questions and conflating them is how a demo starts lying. The dataset is structured and synthetic on purpose, for provenance and ToS reasons, and the README says so before it says anything else.

`FastAPI` `RAG (TF-IDF)` `Structured output` `Eval harness` `Python`

[→ repo](https://github.com/Rishi0507/Autopsy)

---

## Pavilion

*A daily T20 cricket puzzle. One target score per day, identical for every player in the world.*

You play both innings of that target. First you defend it with five bowlers and a four-over limit each, then you chase it on a budget of six attacking overs. It counts as solved only when both halves are won.

**The target is solved, not chosen.** Overnight, a generator simulates thousands of full games at candidate targets and keeps only situations where a competent player wins between 35 and 65 percent of the time and where the bowling choice measurably changes the result. A target you would win nine times in ten is not a puzzle. The search covers the whole tuple of target, attack, chasing side and venue, because the attack you are dealt is part of the problem rather than set dressing.

**The randomness is pre-committed.** The dice are not rolled when you click. Every ball of the day has a fixed coordinate of day, innings, over and delivery, and its random number is derived from the day's key plus that coordinate. Ball four of over twelve carries the same number whether you reach it or not, so replaying cannot fish for a better outcome and every player genuinely meets the same deliveries. Your choices move the probabilities, not the dice.

Which is what makes the scoring possible. Summing win-probability changes across an innings telescopes to the final result, so it measures luck. Instead, before each ball the engine evaluates every option that was available at that moment and scores the gap between what you chose and the best thing you could have chosen. A careful player who loses can score positively.

Go end to end with no cgo, so the server is one static binary: SQLite is the pure-Go implementation and model inference is a hand-written tree evaluator rather than ONNX, with fixed parity fixtures asserting the Go and Python paths agree. Ball-by-ball data comes from Cricsheet under ODC-BY 1.0; batting hand and bowling type resolve through Wikidata and therefore carry CC BY-SA. The two are kept in separate files so the share-alike obligation stays scoped to the attribute table instead of spreading into the corpus and the models.

`Go` `Monte Carlo` `Empirical Bayes` `Python (uv)` `SQLite` `Make`

---

### The same idea, three times

|  | What it decides | What it refuses to do |
| --- | --- | --- |
| **OJAS** | which minutes of footage a human should spend time on | call it cheating, or set a threshold the data won't carry |
| **Autopsy** | why a listing underperforms, and what to change | let a fallback response pass as a model's |
| **Pavilion** | how good your call was against the calls you had | roll the dice after you've made the choice |

---

## Also in here

- **[Referee](https://github.com/Rishi0507/Referee)** — a benchmark harness for AI agent architectures. Fixed tasks with machine-verifiable ground truth, and identical prompts, tool access and grading on every run, so what's being compared is the architecture and nothing else.
- **[Trace.ai](https://github.com/Rishi0507/Trace.ai)** — predicts recurring inflate-then-discount cycles per seller: seller-grouped XGBoost over STL-decomposed price history, catalogue-wide discount concurrency and seasonality, benchmarked against a single-product z-score baseline. The labels are simulated, and the README leads with that. An existence proof, not validated ground truth.
- **[IFAS](https://github.com/Rishi0507/IFAS-Intelligent-Footfall-Analysis-System-for-Retail-Environments)** — retail footfall from a single overhead CCTV feed, on a dataset I annotated by hand. Occlusion is most of the problem.
- **[Spine-Guard](https://github.com/abhishek-pandey7/Spine-Guard)** — real-time posture feedback for spinal rehab over a WebSocket, with a collaborator. Landmarks, joint angles, thresholds, alerts.
- **[micrograd](https://github.com/Rishi0507/micrograd)**, **[LSTM-TextGen](https://github.com/Rishi0507/LSTM-TextGen)** — written to understand the thing rather than to import it.

## Before this

**Blynt** *(sunset)* — co-built and shipped a cross-platform social app end to end, architecture through App Store review. Auth, real-time feeds, push notifications, deployment. Around 760 users and 80 daily active at its peak. Strangers using something you made is a different class of feedback from a green test suite.

## Reach me

[rishipopawala@gmail.com](mailto:rishipopawala@gmail.com) · [LinkedIn](https://linkedin.com/in/rishi-popawala-077624333)

*If you want to know why something here is built the way it is, it's written down in the repo. Usually that's the more interesting file.*
