# The conditions of an observation are part of the observation

**Two conditions, one relation, and the boundary between them.**

---

> ## ⚠ STATUS: DRAFT — UNDER REVIEW. NOT AGREED.
>
> This is a **shared base**, written down so a week of findings is not lost in three
> separate PR threads. It is not a position of the working group, it is not finished,
> and its authorship is not settled.
>
> | | |
> |---|---|
> | **Settled** | the three incidents happened; every one is linked to primary evidence below and independently checkable |
> | **Settled 2026-08-15** | that composition is NOT a third instance. Its own author read the draft and endorsed our objection instead of defending his section — §4 is a missing **relation**, not a missing **condition** |
> | **Open** | whether the general rule is about *observation conditions* or about *claim context* more broadly. §4 is the case that decides it |
> | **Unsettled** | the statement of §1, and the author list |
>
> **Authorship is by offer, not by assumption.** babyblueviper1 offered to co-write
> (crc#75, 2026-08-15); that offer is recorded, not cashed. Pavlo Tvardovskyi has now
> reviewed §4 and corrected it, and has not been asked to co-author. Nobody is listed
> until they say so in this repo — see [AUTHORS.md](AUTHORS.md).
>
> Disagreement is the useful contribution here, and it has already improved this note
> once: the first draft called composition a third arrival, and it is not.

---

## 1. The rule, as currently stated

**A reading is incomplete without the conditions that produced it.**

Whether that is the whole rule, or a special case of something broader about *claim
context*, is the open question this note now exists to test. §4 is the boundary case that
decides it — see §5.

Publish the value alone and you have published something a reader cannot place — cannot
place in time, cannot place against a requester, cannot place within a composition. The
value looks identical whether the conditions were sound or not, because nothing in it
carries them.

The corollary is the part that surprised all three of us independently:

**More observations do not fix it.** If two readings share the condition that was wrong,
their agreement is one observation counted twice. Redundancy answers "do my sources
agree" when the question was "does my source agree with the world", and those come apart
silently and for minutes at a time.

## 2. Arrival one — caches (babyblueviper1)

On 15 August, two independent ENS APIs served a superseded contenthash for several
minutes after the transaction confirmed. Both agreed. Both were wrong.

The console's currency check read one of them and reported a transient false-STALE
against a record that was correct. A human read the other and told his own team the
transaction had not landed. It had — the contract said so throughout.

> "a read agreeing with itself across multiple sources is not the same claim as a read
> agreeing with ground truth — if the sources share an upstream cache/propagation delay,
> 'two APIs said the same thing' and 'the record is current' can diverge for minutes at a
> time, and nothing about querying MORE sources on the same lagging path closes that gap.
> The fix has to be a freshness bound (block number / observed-at timestamp) on the read
> itself, not more redundant reads of it."
> — babyblueviper1, [crc#75](https://github.com/trustless-ai/cross-reference-console/pull/75#issuecomment-5304204998)

**Missing condition:** *when*. **Evidence:** pin record
`pins/bafybeihim4cjh2uqxlctepgibzdhr77rag53mqu6vlces72eyyiqnjjipe.json`,
`verification.block = 25761067`.

## 3. Arrival two — clients (TMerlini)

The same day, comparing what an IPFS gateway serves against the pinned object produced
the finding that `ipfs.io` serves the same CID three different ways:

```
curl/8.7.1                the hidden cdn-cgi beacon, 177569 bytes
a browser user-agent      the pinned bytes exactly,  177277 bytes
python-urllib's default   403 Forbidden
```

So "ipfs.io modifies responses" is true of one client and false of another. The first
record written said it flatly, and was wrong for two clients out of three.

The trap has a second floor: the RPC used to read the contract *also* 403s
python-urllib's default. The checker's own client was silently part of every verdict it
produced, which makes whichever client the checker happens to use an accidental
definition of what the world sees.

**Missing condition:** *who asked*. **Evidence:** same pin record,
`availability.serving`, one entry per (gateway, client), re-derivable by
`reference/check_served_bytes.py --live`.

## 4. The boundary case — composition (Pavlo Tvardovskyi)

*Reviewed by its author 2026-08-15. He confirms this description of the TSEI direction and
supplied the correction below, which changes what this section is FOR.*

Extracting a producer-neutral transformation-stability layer from ReceiptOS, the question
posed was not "is this artifact valid" but "did the claim remain normatively the same as
it moved through another object, projection, renderer, aggregator or independently
implemented producer".

The failure class named there: three local checks each individually true, while the
composed public claim is false — a console whose individual checks were green while the
relationship between them was stale; a verdict locally correct given what it was shown,
while the disclosure of what it was *not* shown drifted separately.

**Missing relation** — and this is his correction, not a rewording of ours:

> *under what verified relationship do these local claims license the composed claim?*

`when` and `who asked` are conditions **of an observation**. This is structurally different:
nothing about the individual readings is unknown. Each local claim is valid, observed under
known conditions, and complete on its own terms. What is missing is a *relationship between
them* that was never independently recomputed.

His own statement of it, preserved verbatim at his request:

> "local validity must not imply composed validity unless the composition relationship itself
> has been independently recomputed and verified."

He is explicit about the limit: closed-world
coverage is mechanically complete over observed structural fields, but relationship-invariant
completeness is still profile-authored — a mechanism can verify a declared invariant and
cannot discover that a missing one ought to have existed.

## 5. Two conditions and one relation — THE OPEN QUESTION

| arrival | value | what was dropped | kind |
|---|---|---|---|
| caches | the resolved contenthash | the block it was read at | condition **of** an observation |
| clients | the served bytes | the client that asked | condition **of** an observation |
| composition | the composed verdict | the verified relationship between local claims | relation **between** claims |

The first two are one rule and we are confident of it. The third is deliberately not
folded in.

**This objection was ours, and its own subject endorsed it rather than defending his
section.** Pavlo, on reading the draft: *"your own objection in §5 is the right one to
keep… the composition case is structurally different."* Two people arriving separately at
the same doubt is the same evidence-shape as the note itself, pointed the other way.

So composition stays, **as the boundary case**, in his framing: it is the test of whether
the general rule is really about *observation conditions* or about *claim context* more
broadly. If claim context, the note is larger than three incidents and needs a different
statement in §1. If observation conditions, the note is about reads, and §4 is a
well-behaved neighbour that shows where the edge is.

What survives either way is the corollary, and it is the reason all three of us were
caught: **adding sources does not help when the sources share what was dropped.** Two APIs
behind one cache. Two clients under one CDN rule. Two legs resting on one unverified
relationship.

## 6. What actually fixes it

Not more reads. A **bound**, carried with the value:

- a **block number or observed-at timestamp**, so a verdict can be placed in time
- the **client the observation was made as**, so a verdict is about the world rather than
  about one requester
- the **recomputed relationship**, so a composed verdict depends on every leg independently

And where a bound is absent, the value must say so. A stale read is unfalsifiable from
inside itself — nothing in the response reveals that it is behind, which is precisely why
redundancy feels like it should help. So an undated verdict must not render like a dated
one. This is the collapsed-state rule applied to conditions:
[collapsed-state-note](https://github.com/trustless-ai/collapsed-state-note).

## 7. Shipped, so this is not only an argument

Each fix is live and re-runnable, which is the point of writing it down here rather than
in prose:

| | |
|---|---|
| dated reads | [crc#84](https://github.com/trustless-ai/cross-reference-console/pull/84) — the call is pinned to a named block; head age bounded against the **local clock**, deliberately, since a second RPC is another read on the same lagging path |
| per-client observations | [crc#81](https://github.com/trustless-ai/cross-reference-console/pull/81) — `check_served_bytes.py`, one observation per (gateway, client), re-derived with `--live` |
| could-not-check ≠ failed | [crc#83](https://github.com/trustless-ai/cross-reference-console/pull/83) — an unreachable upstream stops being a red build |

## 8. Open questions for co-authors

1. **RESOLVED by its subject, 2026-08-15:** composition is structurally different — a
   missing *relation*, not a missing *condition*. It stays as the boundary case.
2. **Now the load-bearing one:** is the general rule about **observation conditions** or
   about **claim context**? §4 is the case that decides it, and the answer changes §1.
3. Is there a fourth arrival? Three is a pattern; four would be a rule — and a fourth that
   is a *relation* rather than a *condition* would settle question 2 on its own.
4. Should the bound be normative for `crc.pin-record.v0` — a record without an
   observed-at block being invalid rather than merely weaker?
5. Does this belong as a note at all, or as a section in an existing one?

---

**Licence:** CC0 1.0 Universal. **Status:** draft, under review, authorship open.
