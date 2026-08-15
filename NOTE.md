# The conditions of an observation are part of the observation

**Two conditions, one relation, one disputed case — and a test that did not survive a day.**

---

> ## ⚠ STATUS: DRAFT — UNDER REVIEW.
>
> A **shared base**, written down so a week of findings is not lost across separate PR
> threads. Not a working-group position; not finished.
>
> | | |
> |---|---|
> | **Settled** | the four incidents. Each links to primary evidence and is independently checkable |
> | **Settled 2026-08-15** | composition is **not** a fourth condition. Its own author read the draft and endorsed our objection rather than defending his section |
> | **Proposed, with a test** | §6 gives a mechanical criterion for what counts as a condition. It is the load-bearing part now |
> | **Leaning, not decided** | that the rule stays **narrow** — about reads, not about "claim context" broadly. Two independent votes, no decision |
>
> Authorship is by offer, not assumption. See [AUTHORS.md](AUTHORS.md).
>
> Disagreement has already improved this twice: the first draft called composition a
> third arrival, and had no test for what a condition even is.

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

## 4. CONTESTED — registry-membership drift (babyblueviper1, attacked by Pavlo Tvardovskyi)

Offered as a third condition. It may not be one. **This section is under active dispute and
the dispute is the most useful thing in the note** — see §6.1.

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

## 5. The boundary — composition (Pavlo Tvardovskyi)

*Reviewed and corrected by its author, 2026-08-15.*

Extracting a producer-neutral transformation-stability layer from ReceiptOS, the question
was not "is this artifact valid" but "did the claim remain normatively the same as it
moved through another object, projection, renderer, aggregator or independently
implemented producer".

**Missing relation** — his correction, not our rewording:

> *under what verified relationship do these local claims license the composed claim?*

`when`, `who asked` and `as of when` are conditions **of an observation**. This is
structurally different: nothing about the individual readings is unknown. Each local claim
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

Two cases now sit on the same side of the line. That suggests the binary is really **three
kinds**:

| kind | what it is | recovered how | examples |
|---|---|---|---|
| **condition of the act** | a property of the observing itself | you always implicitly have it | *when*, *who asked* |
| **fact about another object** | a property of something else that must itself be observed, with its own conditions | a second observation | registry status, verifier discrimination |
| **relation between claims** | whether local claims license a composed one | a strictly additional computation | composition |

Under this reading, registry drift is not a fourth condition and not quite Pavlo's relation
either: it is a **second observation** being silently folded into the first — which is why
his predicate point bites. *Valid at issuance* and *valid now* are two observations of two
different things, and treating them as one claim with an age is the collapse.

**If this repair holds**, the note's title is wrong and its subject is larger than reads. If
it does not, the honest outcome is that §4 comes out and the note stays at two conditions
and a boundary. Both are better than the note as it stood this morning.

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

1. **Narrow or broad?** Two independent votes for narrow — Pavlo's correction, and
   babyblueviper1's reasoning: *"'claim context' would blur exactly the line that just got
   drawn. A note that's precisely about reads is falsifiable the way §8's vectors already
   are; a note about 'context' broadly invites every future incident to get folded in
   without the same sharp test applying."* Leaning, not decided.
2. **§6 did not survive a day.** Pavlo produced the counterexample §9 asked for, using
   babyblueviper1's own fourth arrival. §6.1 offers a three-kind repair; it needs the same
   treatment §6 just got. The question is now whether "fact about another object" is a real
   third kind or a restatement of one of the other two.
3. **Normative observed-at for `crc.pin-record.v0`?** babyblueviper1 says yes, on the
   grounds that this repo already fails closed elsewhere — an unknown operator set is
   unverifiable, never a guess — and an unbounded record is the same gap left open. **The
   cost, named rather than waved past:** normative means every old unbounded record in the
   pin history becomes formally *invalid* retroactively, not merely weaker. Grandfather
   them as a distinct legacy tier, or accept the retroactive invalidation? That is a group
   call.
4. **A fifth arrival**, particularly one that stresses §6.

---

**Licence:** CC0 1.0 Universal. **Status:** draft, under review.
