# The conditions of an observation are part of the observation

**Three independent arrivals at one rule, in one week.**

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
> | **Proposed** | that they are the same rule. This is the claim under review |
> | **Unsettled** | the statement of the rule, whether §5 generalises honestly, and the author list |
>
> **Authorship is by offer, not by assumption.** babyblueviper1 offered to co-write
> ("worth writing up — three independent directions converging on the same rule in a
> week", crc#75, 2026-08-15). That offer is recorded, not cashed: nobody is listed as
> an author until they say so in this repo. Pavlo Tvardovskyi has not been asked yet;
> §4 describes his finding from a public thread and he may well state it differently.
>
> Disagreement is the useful contribution here. If the three are *not* one rule,
> that is the more interesting note and it should be written instead.

---

## 1. The rule, as currently stated

**A reading is incomplete without the conditions that produced it.**

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

## 4. Arrival three — composition (Pavlo Tvardovskyi)

*Described from a public thread; not yet reviewed by its author.*

Extracting a producer-neutral transformation-stability layer from ReceiptOS, the question
posed was not "is this artifact valid" but "did the claim remain normatively the same as
it moved through another object, projection, renderer, aggregator or independently
implemented producer".

The failure class named there: three local checks each individually true, while the
composed public claim is false — a console whose individual checks were green while the
relationship between them was stale; a verdict locally correct given what it was shown,
while the disclosure of what it was *not* shown drifted separately.

> "local validity must not imply composed validity unless the composition relationship
> itself has been independently recomputed and verified."

**Missing condition:** *under what composition*. He is explicit about the limit: closed-world
coverage is mechanically complete over observed structural fields, but relationship-invariant
completeness is still profile-authored — a mechanism can verify a declared invariant and
cannot discover that a missing one ought to have existed.

## 5. Why they may be one rule — THE CLAIM UNDER REVIEW

Each is a value published without a condition that determined it:

| arrival | value | condition dropped |
|---|---|---|
| caches | the resolved contenthash | the block it was read at |
| clients | the served bytes | the client that asked |
| composition | the composed verdict | the relationship that was recomputed |

And in each, the same corollary holds: adding sources does not help, because the sources
share the dropped condition. Two APIs behind one cache. Two clients hitting one CDN rule.
Two legs of a composition resting on one unverified relationship.

**The strongest objection we have so far**, and it is unresolved: the third may be a
different animal. Time and requester are properties of *a* reading. A composition
relationship is a property *between* readings, and calling both "a condition" may be a
pun rather than a generalisation. §5 stands or falls on that, and it is the single most
useful thing a reviewer could attack.

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

1. Does §5 hold, or is composition a different animal? *(the load-bearing one)*
2. Is there a fourth arrival? Three is a pattern; four would be a rule.
3. Should the bound be normative for `crc.pin-record.v0` — a record without an
   observed-at block being invalid rather than merely weaker?
4. Does this belong as a note at all, or as a section in an existing one?

---

**Licence:** CC0 1.0 Universal. **Status:** draft, under review, authorship open.
