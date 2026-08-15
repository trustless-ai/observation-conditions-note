# The conditions of an observation are part of the observation

**Three conditions, one relation, and a test that separates them.**

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

## 4. Condition — *as of when you ask* (babyblueviper1)

Registry-membership drift, arrived at independently and before reading this note.

A proof is issued at T₀ while the issuer's registry status is valid. Registry membership
changes at T₁ > T₀. The proof stays byte-valid and independently verifiable forever — but
"issued by a registry-valid party" is only true **as of read time**, not issue time, and
nothing in the proof itself says which one you are getting.

Same shape as the block number: recoverable for free at read time, silently dropped if
nobody thinks to record it.

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
| registry status | free, from a read available at the same moment | **condition** |
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
2. **Does §6's test hold against a case none of us has seen?** That is the claim now. A
   condition that is *not* a free byproduct, or a relation that *is* one, breaks it.
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
