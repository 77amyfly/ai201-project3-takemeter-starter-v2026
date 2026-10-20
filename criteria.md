# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1.Overall accuracy on the held-out test split is at least 0.70, at every seed tested.


**Why this target:**

With three labels, random guessing gives about 0.33, so 0.70 is well above
chance: it means at least 7 of every 10 comments are labeled correctly. My
test split will have about 30 comments, so one wrong prediction moves
accuracy by about 3.3 points and 21 of 30 correct is the cutoff.

---

## 2.Every label (`argued`, `asserted`, `experience`) has an F1 of at least 0.50 on the held-out test split, at every seed tested.

<!-- Your criterion. -->

**Why this target:**

Random guessing among three labels gives about 0.33, so 0.50 is clearly
above chance. I set it lower than I would for overall accuracy because my
smallest label will only have about 20 examples in the test split, and
an F1 computed on that few moves a lot between seeds.

---

## 3.In `labels.csv`, no label is more than 50% of the rows, and no label is less than 20% of the rows.

<!-- Your criterion. -->


**Why this target:**

I want the three labels to be reasonably balanced so the model has to
learn all of them. If one label were more than 50% of the data, a model
that always guessed it would score above 0.50 accuracy without learning
anything, which is too close to my accuracy target of 0.70. If a label
were less than 20% of the data, at 200 rows that is under 40 rows, and
only about 6 would land in the test split, so its F1 would be too
unstable to trust.

---

## 4.A second labeler who sees only my written label definitions and none of my labels agrees with my label on at least 80% of a random sample of 30 rows of `labels.csv` (24 of 30).

<!-- Your criterion. -->

**Why this target:**

A good label definition should give the same answer no matter who applies
it, otherwise the model has no stable signal to learn. 80% means at most 6
of 30 comments are labeled differently. I did not set it higher because
whether a conclusion counts as "explicit" is the one call another person
may make differently from me.

---

## 5.Accuracy on the most confident third of predictions is at least 0.20 higher than on the least confident third, at every seed tested.

<!-- Your criterion. -->

**Why this target:**

My test split has about 30 comments, so each third has about 10 and one
wrong prediction moves its accuracy by 0.10. A gap of 0.20 means at
least 2 more correct predictions in the confident third than in the
unsure third, which is more than one comment's worth of noise. If the two
thirds were equally accurate, the model's confidence could not be used to
decide which predictions to trust.


---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
