# The Unofficial Guide

**Name:** Ojas Sachin Dessai
**Corpus:** `city_guides`

---

# Unit 1

## What This Does
This system is an AI-powered regional travel assistant built using the `city_guides` corpus. It answers highly specific logistics, dining, infrastructure, and accessibility questions about local towns such as Brightwater, Marchwood, and Kestrelford. It leverages a retrieval-augmented generation (RAG) pipeline to pull facts directly from trusted markdown guides, preventing hallucinations and ensuring answers are strictly grounded in source documentation.

## Chunking Strategy
**Chunk size:** 800 characters  
**Overlap:** 0 characters  

I used the default settings of the starter code for this initial pass. When reading through the `city_guides` files, I noticed they are long, structured regional guides averaging over 2,000 characters per document, organized heavily by distinct section headers like `## Getting there` or `## Eat and drink`. A plain 800-character fixed window slices roughly across these logical text segments. While this distribution creates complete paragraphs for some blocks, it cuts blindly through sentences at the tail end of several chunks (e.g., cutting off words like "15-" or "mino"), proving that a custom structural boundary splitter will be required in later milestones to preserve sentence integrity.

## Sample Chunks

### Chunk 1
* **Source File:** `guide_accessibility.md#0`
* **Produced by:** `chunker.py::fallback_split`
* **Text:**
# Getting around the region with limited mobility

An honest assessment rather than a promotional one. Some of these places are
difficult and it is better to know in advance.

## Straightforward

**Thornby Wells** is the easiest town in the region. It is flat, compact, and
everything is within three minutes of everything else. Parking is free for two
hours anywhere in town and the station is central. The pump room and gardens
are level throughout.

**Marchwood** has a modern tram network with level boarding on all four lines,
running every 8 minutes on weekdays. The city museum and covered market are both
step-free. The distances between districts are the main consideration.

**Brightwater** is level along the river and through the centre. The mill museum
is step-free. The station is a 15-

### Chunk 2
* **Source File:** `guide_corry_vale.md#2`
* **Produced by:** `chunker.py::fallback_split`
* **Text:**
the second village is 12th century and always unlocked.

## Where to stay

Perhaps thirty beds in the entire valley, spread across two pubs and a handful of farmhouse rooms. In summer these are booked months ahead. Camping is permitted on two marked fields and nowhere else.

## When to go

May to September. Outside those months the pub in the third village closes, the farm shop reduces its hours, and several footpaths become genuinely boggy rather than merely wet. The road is not gritted above the second village and is impassable in snow.

## Practical notes

Cash is still useful at the market and in smaller places, though cards are
accepted almost everywhere now. Mobile coverage is good in the centre and
patchy on the outskirts. The nearest full hospital is in Brightwater; there is
a mino

### Chunk 3
* **Source File:** `guide_givens_mill.md#0`
* **Produced by:** `chunker.py::fallback_split`
* **Text:**
# Givens Mill

Givens Mill is a village of 700 built around a working watermill that still grinds flour commercially. It is the sort of place people visit for an afternoon and then talk about for longer than the visit lasted.

## Getting there

No station and no bus on Sundays; four buses a day from Brightwater on weekdays, taking 30 minutes. Driving is 20 minutes. The village car park holds about forty cars and is full by 11am on summer Saturdays.

## Getting around

Everything is on one street along the river. The mill is at one end and the church at the other, eight minutes apart. The riverside path continues in both directions for as far as you want to walk.

## Eat and drink

A tearoom attached to the mill, open 10 to 4 daily except Tuesdays, which sells bread made from the flour grou

### Chunk 4
* **Source File:** `guide_kestrelford.md#3`
* **Produced by:** `chunker.py::fallback_split`
* **Text:**
irts. The nearest full hospital is in Brightwater; there is
a minor injuries unit locally with limited hours.

### Chunk 5
* **Source File:** `guide_regional_transport.md#1`
* **Produced by:** `chunker.py::fallback_split`
* **Text:**
oncentrate on weekday daytimes. Sunday service is minimal to non-existent
outside the Brightwater town routes.

The Kestrelford service is hourly on weekdays, two-hourly on Saturdays, and
does not run on Sundays. The Halden Bay coast service runs four times daily
year-round.

## Driving

Roads are good between the towns and poor on the approaches to both Kestrelford
and Halden Bay. The Kestrelford approach is single-track with passing places
for the final eight minutes. The Halden Bay coast road is cut into the cliff
and is slow rather than difficult.

Parking is the constraint rather than driving. Both Halden Bay lots fill by
10am on summer weekends. Kestrelford's lower car park is free and involves a
steep walk up.

## Walking and cycling

The river path from Brightwater runs four miles

## Sample Answer

**Question:** Which specific district in Marchwood contains the best restaurants?

**Source Line:** `guide_marchwood.md`

**Answer:** According to guide_marchwood.md, the best eating is located in the Northgate district, which features about thirty restaurants sitting within four streets. 

**My relevance cutoff:** 0.60

I set my cutoff to 0.60 based on a clear gap between the two test groups. The five in-corpus questions all scored lower than 0.53, while the five out-of-scope questions all scored higher than 0.70. Setting it at 0.60 ensures valid questions pass while protecting the system from hallucinating answers to out-of-scope queries.

| Question | In corpus? | Best distance |
|---|---|---|
| What is the best place for birdwatching? | Yes | 0.32 |
| Is there an airport located in Brightwater? | Yes | 0.41 |
| What time does the Tuesday market in Brightwater square finish? | Yes | 0.44 |
| Which specific district in Marchwood contains the best restaurants? | Yes | 0.48 |
| What is the primary mode of public transportation to the north coast? | Yes | 0.52 |
| How do I bake a chocolate cake from scratch? | No | 0.71 |
| Who won the most recent NFL Super Bowl? | No | 0.74 |
| What are the symptoms of a common cold? | No | 0.78 |
| How do I change the oil in a hybrid car? | No | 0.81 |
| What is the capital city of Japan? | No | 0.85 |

## How I Used AI

**1.** I asked the AI to pressure-test my custom testing rules for criteria.md. It evaluated my phrasing and pointed out where my sentences leaned toward subjective opinions rather than objective tests. Based on that feedback, I changed my criteria to use concrete, measurable targets like explicit sentence-count limits.

**2.** I asked the AI to diagnose a "fatal: Too many arguments" error showing up in my Mac's terminal. It instantly recognized that I was pasting sequential navigation commands (`git clone` and `cd`) directly inline on a single line without adding a proper semicolon separator, which I then corrected to separate the commands.


# Unit 2

<!-- These sections get ADDED to what's already above. Don't delete or rewrite
     unit 1 — the point is that someone can see what you said before you knew
     how it went. -->

## Run Log — Before

<!-- Your five criteria, three runs each. `python run_eval.py --label before`
     runs the questions, puts the OUT_OF_SCOPE ones through the gate, and
     writes it all into results/ for you. Targets come from criteria.md; the
     verdict column is your call.

     Criterion 3 is measured in one deterministic pass rather than three, so
     the same number goes in all three run columns. That's correct, not lazy.

     Milestone 1. -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks complete, unbroken sentences | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Answers under 3 sentences | all 5 | 5/5 | 5/5 | 5/5 | MET |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->
**System under test:** `chunker.py::split_documents` (84 chunks). It replaced the starter's `fallback_split` after my Unit 1 write-up.

**Runs:** `python run_eval.py --label before`, top-k 5, cutoff 0.6, 3 runs per question. My first run (`results/run_2026-10-06_0218_before.md`) scored 2 of 5 because of a scorer bug (the answer wasn't lowercased before matching). I fixed it and re-ran (`..._0222_before.md`). The table above uses the corrected run.

**Real output** (from `run_eval.py::main`):

Q: Which specific district in Marchwood contains the best restaurants?
A: The Northgate district contains the best restaurants in Marchwood (source: `guide_marchwood.md`).

Q: What time does the farm shop in Corry Vale close?
A: The farm shop in Corry Vale closes at 4pm. Sources: `guide_corry_vale.md` and `guide_eating.md`

Gate (`run_eval.py::check_out_of_scope`): refused 5 of 5, distances 0.846 to 0.997.

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 | Retrieved chunks contain the answer | MET | 5/5 on all three runs against a target of 4 of 5. I checked that "Northgate" is in the retrieved chunk text, and the right guide was retrieved for every question. |
| 2 | Every answer names a source | MET | All 15 answers (5 questions x 3 runs) named at least one guide file. |
| 3 | Gate stops out-of-corpus questions | MET | The gate refused 5 of 5 (best distances 0.846 to 0.997) against a target of 4 of 5. |
| 4 | Chunks complete, unbroken | MET | All 5 chunks from `python app.py chunks -n 5` start at a section heading and end at the end of a sentence. |
| 5 | Answers under 3 sentences | MET | Every answer was one sentence, not counting the "Source:" line. |
## Diagnoses
**Nothing was missed.** All five criteria were MET on all three runs.

**The one failure was in my measurement, not my system.** My first run (`results/run_2026-10-06_0218_before.md`) scored only 2 of 5. The answers were correct, but `scorer.py::judge` lowercased the `expects` phrase and not the answer, so "Elder Ness", "Sundays" and "Northgate" never matched. The two that passed ("4pm", "1pm") have no capital letters. This was a scoring bug, not a pipeline stage. I fixed it, re-ran, and got 5/5. I kept both result files as evidence.

**My targets were probably too easy.**
- Criterion 4 (chunks) was the safest. My chunker splits on section headings, so complete chunks were almost guaranteed. I would tighten it to 5 of 5, with each chunk able to answer a question on its own.
- Criterion 5 (under 3 sentences) measures brevity, not correctness. I would replace it with a stricter accuracy check.

**Closest call: birdwatching.** Its best distance was 0.553 against the 0.60 cutoff, only 0.047 of headroom. It is also a one-document topic: only `guide_elder_ness.md` mentions birds, and the guide says "bird observatory" rather than "birdwatching". A slightly different phrasing could push it past the gate and cause a wrongful refusal. That is the weakest spot, and it points at retrieval.
<!-- For each miss: which stage caused it, and how. The stage alone isn't
     enough — you need the mechanism.

     Not a diagnosis: "Question 3 didn't work."
     A diagnosis:     "Question 3 asks about laundry costs. The answer is in
                       one sentence that got split across two chunks, so
                       neither chunk on its own contains it."

     The five stages: loading → chunking → embedding → retrieval → generation.

     Look for a pattern. If three misses all ask about numbers, that's one
     problem, not three.

     Missed nothing? Say so, then say honestly whether your targets were set
     low, and which one you'd tighten and to what.

     Milestone 3. -->

## The Improvement

**What I changed:**
I added BM25 keyword search (using `rank-bm25`) alongside the semantic search in `store.py::search`, and merged the two ranked lists with reciprocal rank fusion. The relevance gate still uses the semantic distance with the same 0.60 cutoff, so BM25 only affects which chunks reach the model. I changed nothing else: same chunker, same top-k, same questions.

**Why I picked it:**
My closest call in the before run was birdwatching, with a best distance of 0.553 against the 0.60 cutoff. The Elder Ness guide says "bird observatory" rather than "birdwatching", so keyword matching should help that chunk rank higher without relying only on meaning.

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 2. Every answer names a source | 5 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 3. Gate stops out-of-corpus questions | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 4. Chunks complete, unbroken sentences | 4 of 5 | 5/5 | 5/5 | 5/5 | MET |
| 5. Answers under 3 sentences | all 5 | 5/5 | 5/5 | 5/5 | MET |

Evidence: `results/run_2026-10-06_0241_after.md` (produced by `run_eval.py::main`, retrieval from `store.py::search` with BM25 hybrid search).
**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->
No measurable improvement. All five criteria were 5/5 before and after, and the best distances were identical because the relevance gate still reads the semantic distance and BM25 only changes which chunks reach the model. The retrieved sets did change: for three of five questions BM25 added a guide that wasn't needed (`guide_marchwood.md` for birdwatching, `guide_regional_transport.md` for the Brightwater market, `guide_thornby_wells.md` for the Marchwood restaurants question), so retrieval got slightly noisier, not better. The answers stayed correct because the right chunk was already in the top results. The birdwatching margin (0.553 against the 0.60 cutoff) is unchanged, so this change did not address the weak spot I diagnosed. Hybrid search is aimed at questions where semantic search misses exact terms, and my questions didn't have that problem.
## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->
Nothing missed a target, but two problems remain. The birdwatching margin is unchanged (best distance 0.553 against the 0.60 cutoff, only 0.047 of headroom), so a rephrased question could be wrongly refused. I stopped because hybrid search doesn't touch the gate, and tuning the cutoff would have been a second change. Also, hybrid search added unneeded guides to three questions' retrieved sets, so I would not keep it as it stands.
## What I'd Do Differently
I would tighten criterion 4 to 5 of 5 and require that each chunk can answer a question on its own, because my section-based chunker made 4 of 5 almost guaranteed. I would replace criterion 5 (answers under 3 sentences) with an accuracy check against the `expects` phrases, since brevity says little about quality. I would also define "under 3 sentences" precisely, including whether the source line counts.
<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
