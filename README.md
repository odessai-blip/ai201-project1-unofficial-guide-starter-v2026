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
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

<!-- Underneath, paste the REAL output for each criterion from one of your
     runs — the actual text your system produced, not a description of it.
     Name the file and function that produced it. -->

## Verdicts

<!-- MET or MISSED for each of the five, against the target you wrote last
     unit — not a new one. Plus a sentence on how you decided. That sentence
     matters most where it was close.

     If your target said 4 of 5 and your runs came out 4, 3, 4, that's a MISS.
     The target has to hold, not show up occasionally.

     Milestone 2. -->

| # | Criterion | Verdict | How I decided |
|---|---|---|---|
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |

## Diagnoses

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

**Why I picked it:**

<!-- Connect it to a specific diagnosis above in one sentence. If you can't,
     you picked a fix because it sounded impressive. -->

### Run Log — After

<!-- Same format, same five criteria, three runs each.
     `python run_eval.py --label after` -->

| Criterion | Target | Run 1 | Run 2 | Run 3 | Verdict |
|---|---|---|---|---|---|
| 1. Retrieved chunk contains the answer | 4 of 5 |  |  |  |  |
| 2. Every answer names a source | 5 of 5 |  |  |  |  |
| 3. Gate stops out-of-corpus questions | 4 of 5 |  |  |  |  |
| 4. | | | | | |
| 5. | | | | | |

**Did it help?**

<!-- Say plainly whether it did, and how you know. If it made things worse,
     say that — a change that backfired, honestly reported, earns full credit
     and is more interesting than one that worked. What matters is that you can
     tell.

     Milestone 4. -->

## What's Still Broken

<!-- For each criterion still missed after your fix: what you'd do about it,
     and why you stopped where you did.

     "I ran out of time" is fine if it's true. Pretending nothing is left is
     not.

     Milestone 5. -->

## What I'd Do Differently

<!-- Knowing what you know now — which of your five criteria would you write
     differently, and why?

     Milestone 5. -->
