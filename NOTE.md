# The conditions of an observation are part of the observation

**Five kinds, five arrivals, and a ladder for checking the checks.**

---

> ## ⚠ STATUS: DRAFT — UNDER REVIEW.
>
> A **shared base**, written down so a week of findings is not lost across separate PR
> threads. Not a working-group position; not finished.
>
> | | |
> |---|---|
> | **Settled** | the four incidents. Each links to primary evidence and is independently checkable |
> | **Settled** | composition is **not** a condition. Its own author endorsed our objection rather than defending his section |
> | **Settled 2026-08-15** | §6's test was broken by Pavlo using babyblueviper1's own fourth arrival, repaired to three kinds, split to four — and the disputed arrival was conceded by its proposer to be an external-state fact, not a condition |
> | **Closed 2026-08-16** | the timing gap in *independently grounded*. Registry state is now as-of-scoped and bound into `decision_ref` (§4c) — counted as the fifth arrival only because it was closed, not merely named |
> | **Open** | `A_i` oracle-independence. Pavlo's three-role separation (§9.1) is the shape; babyblueviper1 is bringing a concrete proposal. Deliberately NOT collapsed into the rung above |
> | **Leaning, not decided** | that the rule stays **narrow** — about reads, not "claim context". Two votes, no decision — and §6.1, if it holds, argues the other way |
>
> Authorship is by offer, not assumption. See [AUTHORS.md](AUTHORS.md).
>
> Disagreement has improved this three times in one day: composition was not a third
> arrival; there was no test for what a condition is; and the test, once written, did not
> survive its first counterexample. Nothing here has been quietly rewritten — every
> correction is attributed and the losing position is still legible.

---

## 1. The rule

**A reading is incomplete without the conditions that produced it.**

Publish the value alone and a reader cannot place it — not in time, not against a
requester, not against the state that made it true. The value looks identical whether
those conditions were sound or not, because nothing in it carries them.

The corollary is what caught all of us, independently:

**More observations do not fix it.** If two readings share the condition that was wrong,
their agreement is one observation counted twice. Redundancy answers "do my sources
agree" when the question was "does my source agree with the world", and those come apart
silently, for minutes at a time.

**The rule is deliberately narrow: it is about reads.** §5 is the case that sets the
boundary, and §6 is the test that keeps it there.

## 2. Condition — *when* (babyblueviper1)

On 15 August, two independent ENS APIs served a superseded contenthash for several
minutes after the transaction confirmed. Both agreed. Both were wrong.

The console's currency check read one and reported a transient false-STALE against a
record that was correct. A human read the other and told his own team the transaction had
not landed. It had — the contract said so throughout.

> "a read agreeing with itself across multiple sources is not the same claim as a read
> agreeing with ground truth — if the sources share an upstream cache/propagation delay,
> 'two APIs said the same thing' and 'the record is current' can diverge for minutes at a
> time, and nothing about querying MORE sources on the same lagging path closes that gap.
> The fix has to be a freshness bound (block number / observed-at timestamp) on the read
> itself, not more redundant reads of it."
> — [crc#75](https://github.com/trustless-ai/cross-reference-console/pull/75#issuecomment-5304204998)

**Evidence:** pin record `bafybeihim4cjh…json`, `verification.block = 25761067`.

## 3. Condition — *who asked* (TMerlini)

Comparing what an IPFS gateway serves against the pinned object produced the finding that
`ipfs.io` serves the same CID three ways:

```
curl/8.7.1                the hidden cdn-cgi beacon, 177569 bytes
a browser user-agent      the pinned bytes exactly,  177277 bytes
python-urllib's default   403 Forbidden
```

"ipfs.io modifies responses" is therefore true of one client and false of another. The
first record written said it flatly, and was wrong for two clients out of three.

The trap has a second floor: the RPC used to read the contract *also* 403s
python-urllib's default. The checker's own client was silently part of every verdict it
produced — which makes whichever client the checker happens to use an accidental
definition of what the world sees.

**Evidence:** same record, `availability.serving`, one entry per (gateway, client),
re-derivable with `reference/check_served_bytes.py --live`.

## 4. RESOLVED — registry-membership drift is an external-state fact

Offered as a third condition by babyblueviper1, attacked by Pavlo Tvardovskyi, and **conceded
by its own author within the hour**:

> "registry-NOW is not a free byproduct of reading the proof — it requires a genuinely separate
> read (go check the registry, now), so it fails my own §6 test as I stated it. It belongs in
> *external-state fact* (Pavlo's split), not *condition of the act*. The predicate distinction
> is the sharper, correct version of what I was reaching for with 'drift', and I'd rather have
> his framing in the note than mine."
> — babyblueviper1, 2026-08-15

So it is not a third condition. It is the worked example of the second kind, and it is here
because the route it took — proposed, attacked with the proposer's own criterion, conceded by
the proposer — is the only reason the four kinds exist at all.

A proof is issued at T₀ while the issuer's registry status is valid. Registry membership
changes at T₁ > T₀. The proof stays byte-valid and independently verifiable forever — but
"issued by a registry-valid party" is only true **as of read time**, not issue time, and
nothing in the proof itself says which one you are getting.

Offered as the same shape as the block number: recoverable at read time, silently dropped
if nobody thinks to record it.

**Pavlo's objection, 2026-08-15 — and it lands:**

> "To know it, I have to read the proof and then perform another registry-state
> read/check. *Available at the same moment* is not the same thing as *a free byproduct of
> the same observation*. So under §6 as currently written, registry status seems closer to
> the separate-additional-check side than to the condition side."

**And a predicate distinction he draws that matters independently of §6:**

> "*issuer was registry-valid at issuance* and *issuer is registry-valid now* are different
> claims. The first can remain historically true after registry membership changes; the
> second can become false. I would not describe that as one claim becoming stale, because
> that risks collapsing timing state into validity state."

That last sentence is this working group's own rule turned on this note. Two claims are
being treated as one claim with an age, which is precisely the collapse
[collapsed-state-note](https://github.com/trustless-ai/collapsed-state-note) is about.

## 4b. A FIFTH KIND — historical fact, recoverable only by a witness

Arrived at 2026-08-16 by an agent failing at it, in a different tool, unprompted.

Pavlo's local git config read `user.name = Pablo Tvardovskyi`. An agent found it and explained
confidently that this was "the repo config's own historical typo" and "not something I
introduced this session".

> "The evidence didn't prove either of those things. My commit history only shows that earlier
> commits used *Pavlo* and then at some point later commits started using *Pablo*. So the actual
> evidence was **observed state → provenance unknown**, but the agent upgraded it to **observed
> state → invented provenance**. Recomputing the current value still doesn't recompute its
> history."
> — Pavlo Tvardovskyi

**This does not fit the four kinds, and the reason is sharp.** An external-state fact is
recoverable by a second observation: go and read the registry, now. A historical fact is
recoverable by **no observation whatsoever** — the transition is over. Only something that
witnessed it at the time can establish it.

babyblueviper1, connecting it to the night's other thread:

> "'the value is X' is recomputable, 'here's why it became X' isn't, unless something actually
> witnessed the transition. The agent's confident 'not something I introduced this session' is a
> provenance CLAIM dressed as a provenance FACT — same failure mode as a self-reported
> `source_class`, just with a causal story instead of an authority claim."

| kind | recovered by |
|---|---|
| condition of the act | you always implicitly have it |
| external-state fact | a second observation, available now |
| **historical fact** | **a contemporaneous witness, or not at all** |
| evaluator discrimination | known-good / known-bad conformance cases |
| relation between claims | a strictly additional computation |

## 4c. THE FIFTH ARRIVAL — and it is the remedy for §4b

babyblueviper1 closed the timing hole overnight rather than leaving it named, which was his own
condition for counting it:

- the mediator registry moved from a gitignored file keyed by raw Bearer token to a **public,
  git-tracked file keyed by non-secret `mediator_id`** — so **git history on that file is the
  independently-checkable as-of record**;
- `verified_at`, `registry_as_of` and `registry_snapshot_sha256` are bound into `decision_ref`
  (policy v11);
- `registry_as_of` equals `verified_at` only when the elevation actually held, else null, and is
  **always present as a preimage key** — an absent field cannot be confused with a negative one;
- old proofs unaffected: they recompute against their own field list.

Pavlo, naming what it is:

> "not 'registry-valid' as an aging property, but a **time-scoped authority claim whose
> historical registry state is itself bound into the proof**. That closes the timing hole in
> independent-mediator grounding without pretending it closes the separate A_i
> oracle-independence problem."

**And it is exactly the remedy §4b demands.** A git-tracked history is a contemporaneous witness
to a transition that no later observation could recover. The fifth kind and the fifth arrival are
the same shape seen from opposite ends — one is the gap, the other is what filling it looks like.

**Verification status, stated rather than assumed.** The commit named (`4e8f304`) is not in a
repository we can read, and the newest verdict proof on the public ledger (entry 243) is
`policy_version = invinoveritas.review.v10` and carries neither `registry_as_of` nor
`registry_snapshot_sha256`. That is consistent with v11 being deployed with no v11 entry
published yet — **absence here is not disproof**, and the field-detection was confirmed working
by correctly finding `verified_at` and `source_class` in the same entries. Recorded as
*not yet independently observable*, which is the honest state and, pleasingly, the note's own
subject one more time.

## 5. The boundary — composition (Pavlo Tvardovskyi)

*Reviewed and corrected by its author, 2026-08-15.*

Extracting a producer-neutral transformation-stability layer from ReceiptOS, the question
was not "is this artifact valid" but "did the claim remain normatively the same as it
moved through another object, projection, renderer, aggregator or independently
implemented producer".

**Missing relation** — his correction, not our rewording:

> *under what verified relationship do these local claims license the composed claim?*

`when` and `who asked` are conditions **of an observation** (`as of when` is disputed —
§4). This is structurally different: nothing about the individual readings is unknown. Each local claim
is valid, observed under known conditions, complete on its own terms. What is missing is a
*relationship between them* that was never independently recomputed.

> "local validity must not imply composed validity unless the composition relationship
> itself has been independently recomputed and verified."

He is explicit about the limit: closed-world coverage is mechanically complete over
observed structural fields, but relationship-invariant completeness is still
profile-authored — a mechanism can verify a declared invariant and cannot discover that a
missing one ought to have existed.

## 6. THE TEST — free byproduct, or separate computation?

The first draft separated conditions from relations by intuition. This is a criterion, and
it is now the load-bearing part of the note:

> **Is it recoverable as a free byproduct of making the same observation, or does
> establishing it require a separate, additional check that could have been skipped
> entirely?**
> — babyblueviper1

| | recoverable how | kind |
|---|---|---|
| block number | free, from the same RPC call that returned the value | **condition** |
| client identity | free, from the same request context — you always know who you asked | **condition** |
| registry status | **DISPUTED** — requires a second, separate read of a different object | **§6.1** |
| composition validity | **never free.** "Does A compose validly with B" is a strictly additional computation | **relation** |

A genuine condition is something you **always implicitly have** and can choose to disclose
or drop. A relation is something you have to go **compute**, separately, and can fail to
compute at all.

The distinction also explains why "more sources" fails in *two different ways*:

- more sources sharing a dropped **condition** merely repeat the same undated, unclientted
  read;
- more locally-valid **claims** sharing an unverified **relation** merely add another green
  light nobody checked against the others.

And it accounts for the good faith in every incident here. **Nobody forgot to record a
condition in the composition case — the additional check simply never ran**, while each
author's own check stayed honestly green.

### 6.1 The test is contested, and here is a candidate repair

§6 was proposed by babyblueviper1 and attacked by Pavlo within a day. Neither of them is
listed as agreeing with what follows; it is our attempt at a repair, offered for the same
treatment.

**A second case fails §6 the same way**, from a third thread and a fourth person. giskard09
and kenneives, on the composed-attestation rubric: a verifier that has never been observed
to return `true` cannot be trusted when it returns `false`. Establishing that a checker
*discriminates* is not a byproduct of running it — it needs a separate known-good vector.
It shipped as a real defect: this repo's BIP-340 verifier rejected a valid third-party cell,
and a hand-rolled replacement returned `false` too, and was one sentence from being
reported upstream as someone else's bad signature
([crc#82](https://github.com/trustless-ai/cross-reference-console/pull/82)).

Two cases sat on the same side of the line, which looked like a third kind. Pavlo then split
that row too, and the split holds: **registry status and verifier discrimination are produced
by different epistemic operations.**

> "Verifier discrimination is not primarily a fact about another object; it is evidence about
> whether the decision procedure *itself* is capable of discriminating the predicate it claims
> to evaluate. A checker that has never demonstrated a true case is not merely missing another
> observation. **Its negative result lacks conformance evidence.**"
> — Pavlo Tvardovskyi, 2026-08-15

So the current candidate is **four kinds**, not three:

| kind | established by | examples |
|---|---|---|
| **condition of the act** | you always implicitly have it | *when*, *who asked* |
| **external-state fact** | a second observation, with its own conditions | registry status |
| **evaluator discrimination** | known-good / known-bad conformance cases | a verifier's negative result |
| **relation between claims** | a strictly additional computation | composition |

Registry drift is a **second observation folded into the first**, which is why the predicate
point bites: *valid at issuance* and *valid now* are observations of two different things.
Verifier discrimination is not an observation at all — it is a property of the *procedure*,
and its absence does not make a `false` wrong so much as **unbacked**.

### The rule the fourth row rests on

> **Declared does not imply discriminating.** For a declared invariant or verifier predicate
> to carry weight, a targeted case should exist in which that predicate is known to change
> the outcome — ideally with attribution to that predicate specifically.
> — Pavlo Tvardovskyi

It does not discover missing invariants. It establishes only that a declared checker is not
decorative or permanently one-sided — which is a smaller and achievable claim.

**This one is already mechanised**, which is some evidence it is implementable rather than
merely stateable: `reference/test_operational_gates.py`
([crc#81](https://github.com/trustless-ai/cross-reference-console/pull/81)) mutates a
recomputed input per declared invariant and requires the gate to go red **on the assertion
that names that invariant**, not merely somewhere — the attribution half of the rule. The
matcher itself was then checked against a deliberately wrong assertion name, because a
control that accepts any failure as the right one is decoration of exactly the kind the rule
is about.

### 6.2 The fourth rung is not hypothetical — it is `source_class`

The closed loop (`m_i` and `A_i` authored by the same party) has an existing shape in this
group's running code, which babyblueviper1 connected:

> "`source_class=agent_reported` is precisely *the same party wrote the mutant and the expected
> attribution* — internally consistent, self-checkable, structurally unable to prove the check
> wasn't gamed by its own author. `source_class=independent_mediator` requires the attestation
> to come from a party outside the acting agent's own control, **registry-gated so the caller
> can't self-declare it** — same shape as *derive A_i independently, freeze/hash before
> executing the gate*."

That matters because it moves *independently grounded* from a proposed rung to one with a live
implementation and a registry gate already enforcing it.

**And then the note's own subject reappears inside its ladder.** He surfaced the gap by
connecting the two halves:

> "registry membership itself can drift after a proof issues — so *independently grounded at
> issuance* and *independently grounded now* are not automatically the same claim either. Not
> yet closed on our side. Worth a fifth arrival once it actually is, not before."

The predicate-timing distinction Pavlo drew in §4 recurs one layer up, against the mechanism
built to ground the check. **Not recorded as a fifth arrival**, at his explicit request — an
open gap is not an arrival, and the note has enough of a habit of counting things early.

**The title stays narrow for now**, on Pavlo's advice: the middle rows may yet collapse, and
widening a title on a category that is one day old and still splitting would be premature. If
the four kinds survive attack, the subject is larger than reads and the note should be
renamed then, not before.

## 7. What fixes it

Not more reads. A **bound carried with the value**: an observed-at block or timestamp; the
client the observation was made as; the registry status at read time. For relations, not a
bound at all, but an actual recomputation of the relationship.

And where a bound is absent, the value must **say so**. A stale read is unfalsifiable from
inside itself — nothing in the response reveals that it is behind, which is exactly why
redundancy feels like it should help. So an undated verdict must not render like a dated
one. This is [collapsed-state-note](https://github.com/trustless-ai/collapsed-state-note)
applied to conditions.

## 8. Shipped, so this is not only an argument

| | |
|---|---|
| dated reads | [crc#84](https://github.com/trustless-ai/cross-reference-console/pull/84) — the call is pinned to a named block; head age bounded against the **local clock**, since a second RPC is another read on the same lagging path |
| per-client observations | [crc#81](https://github.com/trustless-ai/cross-reference-console/pull/81) — one observation per (gateway, client), re-derived with `--live` |
| could-not-check ≠ failed | [crc#83](https://github.com/trustless-ai/cross-reference-console/pull/83) — an unreachable upstream stops being a red build |

## 9. Open

1. **The fourth rung — now with a shape, from Pavlo.** Three roles kept distinct, and the
   independence boundary is the thing under test:

   | role | does |
   |---|---|
   | mutant author | constructs `m_i` |
   | predicate / oracle author | derives `A_i` **independently**, from the frozen invariant definitions — not from gate output |
   | gate under test | produces the observed attribution |

   Freeze and hash `m_i`, `A_i`, **the invariant-definition identity, and provenance** before
   execution; compare only afterwards. And his constraint that matters most: **disagreement
   between the independent oracle and the gate must remain an explicit unresolved/conformance
   result, never silently reconciled.** babyblueviper1 is bringing a concrete proposal rather
   than a rushed sketch. Nothing to build until it lands.

2. **The old fourth rung, honestly.** *declared → discriminating → attribution-consistent →
   independently grounded.* The first three are built (crc#86, crc#87). The fourth exists as
   `source_class=independent_mediator` and is not closed, because *independently grounded at
   issuance* and *independently grounded now* are two claims. No fifth arrival is claimed for
   this, at babyblueviper1's request, until it actually is one.
3. **Narrow or broad?** Two independent votes for narrow — Pavlo's correction, and
   babyblueviper1's reasoning: *"'claim context' would blur exactly the line that just got
   drawn. A note that's precisely about reads is falsifiable the way §8's vectors already
   are; a note about 'context' broadly invites every future incident to get folded in
   without the same sharp test applying."* Leaning, not decided.
4. **§6 did not survive a day, and its repair was split the same evening.** The live question
   is whether **external-state fact** and **evaluator discrimination** stay distinct under
   pressure, or whether one collapses into a neighbour. Four kinds is a claim, not a result.
5. **Normative observed-at for `crc.pin-record.v0`?** babyblueviper1 says yes, on the
   grounds that this repo already fails closed elsewhere — an unknown operator set is
   unverifiable, never a guess — and an unbounded record is the same gap left open. **The
   cost, named rather than waved past:** normative means every old unbounded record in the
   pin history becomes formally *invalid* retroactively, not merely weaker. Grandfather
   them as a distinct legacy tier, or accept the retroactive invalidation? That is a group
   call.
6. **A sixth arrival**, particularly one that stresses §6. The fifth is in (§4c) and it was
   required to be *closed* rather than merely named before it counted — a bar worth keeping.

---

**Licence:** CC0 1.0 Universal. **Status:** draft, under review.
