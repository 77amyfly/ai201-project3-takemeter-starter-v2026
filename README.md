# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->
TakeMeter is a text classifier for comments from r/dogs, a large public
subreddit for dog owners. I collected comments from five threads, two [Fluff]
threads and three [Misc Help] threads, and labeled them by what each commenter
is doing in the reply. I then fine-tuned a small language model on those
labels. The aim is to find out whether a model can tell apart comments that
explain their point, comments that only state it, and comments that just tell
a story.

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `argued`

**Definition:**A conclusion, opinion, or piece of advice that is backed
by at least one fact, where a fact can be a number, a reason, a mechanism,
or the writer's own experience.

**Example 1:**
>for perspective I have already spent close to 5k this year on my dog between several emergency room and urgent care vet visits (mostly unrelated issues), pet insurance, food/toys, boarding, grooming. 1k a year is doable only as long as nothing unexpected happens.

**Example 2:**
>First, your sleep is more important than anything. With your dog being a small fragile breed, he should NEVER be jumping off or onto a bed/couch, etc. The short legs give less time to absorb the impact, therefore causing higher risk for knee, wrist, elbow and back injury. A good gage to go by is the height of any object for jumping onto or off of should be no higher than the length of your dog, minus tail & head.This does not apply to puppies, senior dogs dogs, long back breeds (dachshund, Shiztu, etc) or any "delicate " breed. After he and you are deemed healthy, i would start with a small wire crate in the living room. Leave door open at all times. Cover with thin blanket and place extra cushy, soft bedding inside. We want this to be a place he "wants" to go. After set up, give "high value" treat, like a piece of plain chicken to entice him to explore. Do this for a week or 2 until he grows accustomed to his own little man cave. We want him to go in regularly because he likes it, not because he gets a treat. You can slowly cut the treat to a tiny piece, then just affection after he goes in. In other words, his crate becomes a very inviting, positive place. I suggest as others have said, exercise him as much as possible 1 hour before your bedtime. BTW, dogs typically do not rationalize. He will not be wondering why he is not in your bed at night. This my opinion after working for a veterinarian for 26 years. Good luck sweetie. Keep us posted.


### `asserted`

**Definition:**A conclusion, opinion, or piece of advice with no fact
behind it.

**Example 1:**
>Absolutely my 7yr old golden thinks shes a tiny pup and honestly i feel like its a good thing. Keeps her youthful and full of energy.

**Example 2:**
>A good flex is solid IGP-level heelwork on command. "Fuss" and the dog instantly goes into active heeling anytime, anywhere.

### `experience`

**Definition:** Only facts or personal experience, with no conclusion,
opinion, or advice.

**Example 1:**
> Mine's 80 pounds and still tries to climb onto my shoulder like a parrot
> whenever I sit on the couch

**Example 2:**
> my Rottweiler would always come curl up in my lap whenever I would sit
> down crisscross applesauce. She did that until she was 12 when she passed
> away


### The hardest boundary

**Which two labels:**`argued` and `experience`


**The decision rule I used every time:**
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->
A comment counts as having a conclusion, opinion, or advice only if it
states one explicitly in its own text. Agreeing with another comment
("I agree completely") or an unclear stance does not count. If there is
no explicit conclusion, the comment is `experience`. If there is one, it
is `argued` when it also contains a fact (a number, a reason, a
mechanism, or the writer's own experience) and `asserted` when it does
not.


---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:**
All 200 rows are comments from r/dogs, a public subreddit that needs no
login. I copied the comment sections of five threads in a browser: two
[Fluff] threads and three [Misc Help] threads.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
I labelled 118 rows myself, and Claude pre-labelled the other 82, which I read and corrected.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `experience` | 91 | 45.5% |
| `argued` | 67 | 33.5% |
| `asserted` | 42 | 21.0% |
| **Total** | 200 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:*Foster a dog. The rescue would pay for medical bills. Then you help a
> dog and can have one safely.
>
> *Could have been:* `argued` or `asserted`
>
> *I chose, because:* `asserted`. It gives advice, but the sentence
> after it only restates the advice instead of giving a reason.

**2.**
> *The post:* simply walking though a town center calmly. i've got a reactive lab
> and a puppy, there's always some level of chaos on walks for me. having
> a reactive dog is hard
>
> *Could have been:* `argued` or `experience`
>
> *I chose, because:* `argued`. It states a conclusion ("having a
> reactive dog is hard") and backs it with the commenter's own
> experience of chaotic walks.

**3.**
> *The post:* Absolutely my 7yr old golden thinks shes a tiny pup and honestly i
> feel like its a good thing. Keeps her youthful and full of energy.
>
> *Could have been:* `asserted` or `experience`
>
> *I chose, because:* `asserted`. It is more than a story about the
> commenter's own dog: it states a personal opinion with no evidence or explanation behind it.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** distilbert-base-uncased

**Settings:** <!-- epochs, learning rate, batch size, seed -->3 epochs, learning rate 2e-5, batch size 16, max length 128 tokens, seed 42. Trained on an Apple Silicon GPU (MPS) torch:  2.14.1.

**Anything I changed from the defaults, and why:** Nothing

**Split sizes:** train 139 / val 31 / test 30. Test split per label:
argued 10, asserted 6, experience 14. `asserted` has fewer than 8
examples in the test split.
<!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* I labelled the first 119 rows myself, then asked
  Claude to pre-label the remaining 101 rows using my three definitions
  and my decision rule.
- *What came back:* A label for every row. 72 of those rows are still in
  the final 200-row file, marked `pre-labeled` in the note column.
- *What I changed:* I read every row and changed 13 of Claude's labels.
  For example, I moved "Foster a dog. The rescue would pay for medical
  bills..." from `argued` to `asserted`, because the second sentence
  only restates the advice.


**Moment 2**

- *What I asked for:* Why a model needs labels at all, and what kinds of
  labels and label names real projects use.
- *What came back:* Real classifiers tend to use short noun or attribute
  names (for example spam / not spam), the model only sees them as
  numbers, so the written definitions matter more than the names. Real
  teams also have several people label the same data and measure how
  often they agree.
- *What I changed:* I chose label names that describe what a comment
  does (`argued`, `asserted`, `experience`) instead of its topic, wrote
  an explicit decision rule.

**Moment 3**

- *What I asked for:* How to read my confusion matrix, and what I could change so the model would learn the differences between my labels.
- *What came back:* Claude pointed out that every error flowed toward `experience`, and suggested that `asserted` (42 rows vs. 91 for `experience`) probably had too few examples for the model to learn. It offered this as a hypothesis to test, not a fact.
- *What I changed:* I tested it with one change: I found and labeled 30 new `asserted` posts myself, using my original definitions, and re-ran the same three seeds. `asserted` F1 rose from 0.000 to 0.154, 0.308 and 0.429, but overall accuracy stayed poor (0.514 / 0.514 / 0.486). I concluded the total dataset is probably too small for the model to learn all three boundaries, not just the one label I added to.


**Pre-labelling disclosure:**

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.333 | 0.633 | +0.300 |
| Macro F1 | 0.167 | 0.462 | +0.295 |
| F1 — `argued` | 0.500 | 0.667 | +0.167 |
| F1 — `asserted` | 0.000 | 0.000 | +0.000 |
| F1 — `experience` | 0.000 | 0.718 | +0.718 |

**What I predicted before I looked:** the trained model will beat the baseline on experience and argued, because training on my labeled posts teaches it the language patterns of those two labels.
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.633 | 0.500 | 0.467 | 0.167 |
| Macro F1 | 0.462 | 0.278 | 0.212 | 0.249 |
| F1 — `argued` | 0.667 | 0.182 | 0.000 | 0.667 |
| F1 — `asserted` | 0.000 | 0.000 | 0.000 | 0.000 |
| F1 — `experience` | 0.718 | 0.651 | 0.636 | 0.082 |

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.70 at every seed | 0.633 | 0.500 | 0.467 | MISSED |
| 2. Every label F1 | ≥ 0.50 at every seed | 0.000 (`asserted`) | 0.000 (`asserted`) | 0.000 (`argued`, `asserted`) | MISSED |
| 3. Label balance in `labels.csv` | no label > 50% or < 20% of rows | 21.0%–45.5% | 21.0%–45.5% | 21.0%–45.5% | MET |
| 4. Second labeler agreement | ≥ 80% (24 of 30) | not tested | not tested | not tested | NOT TESTED |
| 5. Confident third beats unsure third | gap ≥ 0.20 at every seed | 0.9 vs 0.5 (gap 0.40) | 0.8 vs 0.1 (gap 0.70) | 0.6 vs 0.1 (gap 0.50) | MET |


### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->
| true \ predicted | argued | asserted | experience |
|---|---|---|---|
| **argued** | 5 | 0 | 5 |
| **asserted** | 0 | 0 | 6 |
| **experience** | 0 | 0 | 14 |


**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

Biggest confusion: 6 real `asserted` posts called `experience`. and 0 real `experience` posts were called `asserted`. Every error goes toward `experience`: 5 of 10 `argued` posts were also called `experience`, and no real `experience` post was ever called anything else. At seeds 7 and 2024 the pattern got stronger (`argued` → `experience` rose to 9 and 10), so the model collapses toward the majority label.

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->


| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Overall accuracy | ≥ 0.70 at every seed | MISSED | Accuracy was 0.633, 0.500, 0.467 at seeds 42, 7, 2024. No seed reached 0.70; the best seed was 2 correct predictions short (19 of 30 vs. 21 needed). |
| 2 | Every label F1 | ≥ 0.50 at every seed | MISSED | `asserted` F1 was 0.000 at all three seeds. `argued` also fell below 0.50 at seed 7 (0.182) and seed 2024 (0.000). |
| 3 | Label balance in `labels.csv` | No label > 50% or < 20% of rows | MET | experience 45.5% (91), argued 33.5% (67), asserted 21.0% (42). All in range, but `asserted` is only 1 point above the floor. |
| 4 | Second labeler agreement | ≥ 80% (24 of 30) | NOT TESTED | No second person labeled a sample of `labels.csv` from my definitions. |
| 5 | Confident third beats unsure third | Gap ≥ 0.20 at every seed | MET | Gaps were 0.40, 0.70, 0.50 (confident third 0.9 / 0.8 / 0.6 vs. unsure third 0.5 / 0.1 / 0.1). |


**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Criteria 1 and 2 (MISSED): too few examples, not inconsistent labeling.**
The model mostly predicts `experience`. In the seed-42 confusion matrix, all 14 real `experience` posts were correct, but all 6 `asserted` posts and 5 of 10 `argued` posts were also called `experience`. At seeds 7 and 2024 it got worse (`argued` → `experience` rose to 9 and 10), and seed 2024's accuracy (0.467 = 14/30) is exactly what always guessing `experience` would score.

`asserted` has only 42 rows (21%) vs. 91 for `experience`, and the model defaults to the larger label: the confusion is one-directional (6 `asserted` called `experience`, 0 the other way) and `asserted` F1 was 0.000 at every seed, so this is not a lucky or unlucky split. `argued` shows the same collapse, with recall falling from 0.5 to 0.1 to 0.0 across seeds.

My staff agreement check matched 30 of 30, so I found no sign that I apply label boundaries inconsistently. This is indirect evidence, since the staff taxonomy is not the one I used for my own 200 labels, but it points the same way as the confusion matrix. Accuracy also swung 0.167 across seeds (0.633 → 0.467), so a 30-post test split is too small to give a stable measure.


---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 30 / 30 = 100%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements** 0

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->


---

## The Improvement

**What I changed:** I added 30 new `asserted` posts to `labels.csv`.

**Which diagnosis pointed at it:** criteria 1 and 2 

### Run Log — After

| Measure | Seed 42 | Seed 7 | Seed 2024 | Spread |
|---|---|---|---|---|
| Overall accuracy | 0.514 | 0.514 | 0.486 | 0.029 |
| Macro F1 | 0.411 | 0.421 | 0.346 | 0.075 |
| F1 — `argued` | 0.429 | 0.333 | 0.000 | 0.429 |
| F1 — `asserted` | 0.154 | 0.308 | 0.429 | 0.275 |
| F1 — `experience` | 0.651 | 0.622 | 0.609 | 0.042 |

| true \ predicted | argued | asserted | experience |
|---|---|---|---|
| **argued** | 3 | 1 | 6 |
| **asserted** | 1 | 1 | 9 |
| **experience** | 0 | 0 | 14 |


| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Overall accuracy | ≥ 0.70 at every seed | 0.514 | 0.514 | 0.486 | MISSED |
| 2. Every label F1 | ≥ 0.50 at every seed | 0.154 (`asserted`) | 0.308 (`asserted`) | 0.000 (`argued`) | MISSED |
| 3. Label balance in `labels.csv` | no label > 50% or < 20% of rows | 21.0%–45.5% | 21.0%–45.5% | 21.0%–45.5% | MET |
| 4. Second labeler agreement | ≥ 80% (24 of 30) | not tested | not tested | not tested | NOT TESTED |
| 5. Confident third beats unsure third | gap ≥ 0.20 at every seed | 0.909 vs 0.455 (gap 0.45) | 1.000 vs 0.364 (gap 0.64) | 0.818 vs 0.364 (gap 0.45) | MET |


**Did it help, and how do I know:** 

Partly. `asserted` F1 went from 0.000 at all three seeds to 0.154, 0.308 and 0.429, so the extra examples let the model start recognizing the thinnest label. But overall accuracy did not improve (0.633 / 0.500 / 0.467 → 0.514 / 0.514 / 0.486), criteria 1 and 2 are still MISSED, and the model still defaults to `experience`: 8–9 of the 11 `asserted` test posts were still called `experience`. `argued` did not improve (F1 0.000 at seed 2024), which fits the change, since I only added `asserted` examples. Thirty more examples helped but were not enough.


<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->
- **Criterion 1 (accuracy ≥ 0.70):** Still MISSED (best 0.514). I would add many more examples for `asserted` and `argued` (the model still predicts `experience` almost every time). I stopped because the unit allows only one change.
- **Criterion 2 (every label F1 ≥ 0.50):** Still MISSED. `asserted` improved but is below 0.50 at every seed, and `argued` is still 0.000 at seed 2024. Same fix as above, plus a larger test split so these scores are less jumpy.


**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->
I meant `argued`, `asserted` and `experience` to be three distinct kinds of post, but the model learned mostly "is this `experience` or not": every real `experience` post was correct, while most `asserted` and `argued` posts were called `experience` (for example 9 of 11 `asserted` posts at seed 42 after the change). The confusion matrix shows one-way errors, so the model is leaning on the biggest label instead of learning the boundaries between all three.


<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
