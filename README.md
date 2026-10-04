# NFA Design Project

## Problems Chosen

| # | Language | File |
|---|----------|------|
| 8 | starts with 01 and ends with 10 | [n08](n08.jff) |
| 11 | 2nd-to-last bit is 1 | [n11](n11.jff) |
| 16 | every odd position is 1 | [n16](n16.jff) |
| 21 | 1s ≡ 1 (mod 3) AND odd # of 0s | [n21](n21.jff) |
| 23 | odd length OR s = 01 | [n23](n23.jff) |

Each problem has three artifacts: the JFLAP file (`nXX.jff`), the test
strings (`nXXt.txt`), and a report with diagram + batch-run screenshot
(`nXXr.md`).

Deep-dive gold-st-ring reports (hand-drawn computation tree + JFLAP
Step with Closure trace) are included for **n08, n16, and n21**.

---

## Which problem gave me the most trouble?

**Problem 21** (3k+1 ones AND odd zeros) was by far the hardest. The
6-state structure wasn't conceptually difficult, but the diagram
layout caused JFLAP to mis-route transitions once the state machine
started looping. Strings that only went around the loop once (`10`,
`1000`, `11110`) accepted correctly, but strings requiring multiple
loops (`100000` with 5 zeros, `11111110` with 7 ones) rejected.

## Did I avoid any problem that was too challenging?

I avoided problems with even more states (like #20 and #22, which
would need a 6- or 9-state product) to make sure I could finish all
5 by the deadline. Problem 21 already took most of the project time.

## Which problems surprised me with a "gold-st-ring"?

**n08, n16, and n21** all had gold-st-ring moments:

- **n08** — `01110` and `01010` rejected because the self-loop on q2
  and the q2→q3 transition anchored at the same point. JFLAP
  mis-routed the `1` transition. Fix: add the outgoing arrow first,
  then the self-loop, and separate the anchor points.

- **n16** — initially I marked only q2 as final, which rejected every
  odd-length string (`1`, `101`, `10101`, `11111`). The fix was to
  mark **both q1 and q2 final**, since the string may end after an
  odd-position symbol (q1) or an even-position symbol (q2).

- **n21** — long strings rejected when q1 and q4 were packed close
  together. The q1↔q4 pair of `0`-transitions overlapped visually
  and JFLAP confused which direction to fire. Fix: spread states
  apart on the canvas; no label changes needed.

- **n23** (bonus) — my first attempt was the 8-state DFA product
  construction, which rejected `010` and `110` (both odd-length).
  Rebuilding as a 4-state NFA passed immediately.

## Which next state(s) did I not account for?

In n08, I didn't account for the fact that JFLAP merges overlapping
transition anchors. My mental model assumed "two arrows leaving q2
on `1` = NFA branches". JFLAP's simulator apparently treated them
as a single merged edge, so the branch to q3 never fired on the
self-loop iteration.

In n16, I didn't account for the string ending on an odd position
— I only thought about the final even-position case and marked q2
final, forgetting that q1 (post-odd-position) is a valid endpoint
too.

In n21, I didn't account for the same class of overlap bug: `q1 --0--> q4`
and `q4 --0--> q1` drawn as two arcs between the same pair of
states. When the machine cycled through them more than once, the
anchor-point ambiguity accumulated and the trace went to the wrong
next state.

## How will I avoid these errors in future state-controller tasks?

1. **Draw transitions with distinct visual anchors** whenever two
   edges leave the same state on the same symbol. Prefer widely
   separated states over tightly packed layouts.
2. **Test with a "loop-heavy" string early.** Short strings hide
   anchor-routing bugs. Add at least one string that requires 3+
   iterations through any cycle.
3. **Ask "can the string end here?" for every state on the accept
   path**, not just the last one you drew. Parity/positional
   conditions often have more valid endpoints than expected.
4. **Trust the tool's step debugger over my own mental trace.** The
   Step with Closure output revealed exactly where routing diverged
   from my intent.

This applies directly to compiler/traffic/flight state controllers:
anywhere a state has multiple outgoing edges on the same input, the
implementation must resolve the target unambiguously. Nondeterminism
is safe only when the runtime truly explores both branches.

## Did I ask questions to AI/instructor?

I used AI assistance (DeepSeek) throughout the project to:

- Understand the difference between Step by State and Step with
  Closure when JFLAP's behavior surprised me
- Debug the transition-overlap bug in n08 and n21 by walking through
  the NFA's trace step-by-step and comparing to my mental model
- Verify the "why not 8 states?" question for n23 (union construction
  vs. NFA branching)
- Understand JFLAP's file format requirements for `nXXt.txt`

I did not ask the instructor, but if I'd had more time I would have
asked about the transition-overlap behavior in JFLAP 7.1, since it
wasn't documented anywhere I could find.

## Other insights for the instructor

- The **union construction question in n23** was worth doing: it
  forced me to explain why NFAs are smaller than the product DFA,
  which I hadn't had to articulate before.
- **JFLAP 7.1 has a subtle transition-routing bug** when two arrows
  share an anchor. It would help future students if the assignment
  warned about this, or if we got a short demo of "spread your
  states out" during the intro lecture.
- I committed incrementally and pushed after every problem. The
  commit history shows the debugging journey (see `git log`).


