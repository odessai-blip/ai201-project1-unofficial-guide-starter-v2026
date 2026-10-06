# Acceptance criteria — The Unofficial Guide

Five criteria that say what "working" means for this system, written in unit 1
**before** any results existed.

An acceptance criterion names a target: a number, a count, a rate, or something
a person could plainly observe. *"Retrieval works"* is an opinion. *"For at
least 4 of my 5 test questions, the top results include a chunk containing the
answer"* is a criterion.

Under each one, write a sentence or two on **why that target** and not a
stricter or looser one. A reason that says something about your corpus or your
pipeline earns credit; *"80% seemed reasonable"* does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## 1. Retrieved chunks contain the answer

For at least 4 of my 5 test questions, the retrieved chunks include one that
contains the answer.

**Why this target:**
The guides are organised by heading, so most answers sit in a single section.
I allowed one miss because some of my questions name one town while the
relevant chunk covers several towns, which may match more weakly.

---

## 2. Every answer names a source

Every answer the system produces names at least one source document.

**Why this target:**
All five, not four, because the grounding instruction already tells the model
to name the file, and an answer with no source can't be checked. If it fails,
the instruction isn't being followed.

---

## 3. The relevance gate stops out-of-corpus questions

When I ask a question my documents clearly don't cover, the relevance gate
stops it and the system returns "I don't have enough information about that" —
in at least 4 of 5 tries.

<!-- The five questions are the ones in `OUT_OF_SCOPE` at the bottom of
     `questions.py`, and `run_eval.py` puts them through the gate and writes
     what happened into your run log. Swap them for your own if you'd rather —
     just keep five of them, or the "4 of 5" above has nothing to be 4 of. -->

**Why this target:**
When I set the 0.60 cutoff, my in-corpus questions scored 0.553 or lower and
my out-of-scope ones scored 0.846 or higher, a clear gap. I picked 4 of 5
rather than 5 of 5 because new chunking shifts the distances and one borderline
question could slip through.


---

## 4. Something about your chunks

At least 4 out of 5 chunks printed by `python app.py chunks -n 5` contain
complete, unbroken sentences rather than cutting off mid-phrase. 



**Why this target:**
I chose 4 out of 5 because some documents use short bullet-point formatting
which might naturally fragment when parsed into raw chunks, but the majority
should remain legible sentences.


---

## 5. Your choice

For at least 4 of my 5 test questions, the AI's generated response is at most
3 sentences long, not counting the source line.


**Why this target:**
The source files are highly compressed travel guides, so any accurate answer
should be able to state its core facts quickly without generating irrelevant
background text. I allow one miss because a question that spans several towns
may need a longer answer, and I don't count the source line so that citing
doesn't use up the limit.


---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 2 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath it, like this:

         ## 1. Retrieved chunks contain the answer

         For at least 4 of my 5 test questions, the retrieved chunks include
         one that contains the answer.

         **Why this target:** ...

         > **Revised in unit 2:** For at least 4 of 5 questions, the top three
         > results contain the answer.
         >
         > **Why revised:** I couldn't judge "the chunks include one that
         > contains the answer" the same way twice — I scored two questions
         > differently on Monday than on Wednesday. The new version is
         > something I can actually check.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "I said 4 of 5 but got 2 of 5, so 2 of 5 is more realistic."

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.

     The whole reason the originals stay visible is so someone can see what you
     said before you knew the answer.
     ───────────────────────────────────────────────────────────────────────── -->
