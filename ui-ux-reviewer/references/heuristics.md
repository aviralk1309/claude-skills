# Heuristics for User Flow Review

## Guiding frame before diving into individual heuristics

**The UX Pyramid** — review from the bottom up, not just the top:
```
Looks Good          ← most beginner reviews stop here
Feels Good
Easy to Learn
Easy to Use
Solves Real Problem  ← start here
```
A visually polished screen that doesn't solve the user's actual problem is a bigger failure than
a plain screen that does. Always confirm the flow solves the real task before critiquing polish.

**The Golden Rule** — users should never have to stop and think "what does this do?" If a finding
boils down to a moment of user hesitation/confusion, that's a real UX failure worth flagging even
if every individual element is otherwise "correct."

**Doherty Threshold** — system response should happen within ~400ms to feel seamless and keep the
user's attention. Slower than that needs a loading/skeleton/progress indicator — silence past
400ms reads as broken, not just slow.

## Nielsen's 10 Usability Heuristics

1. **Visibility of system status** — user always knows what's happening (loading spinners,
   progress indicators, confirmation after actions).
2. **Match between system and real world** — familiar language/concepts, not internal jargon.
3. **User control and freedom** — clear "undo"/"back"/"cancel" — never trap users in a flow.
4. **Consistency and standards** — same action = same result everywhere in the product.
5. **Error prevention** — better than good error messages: prevent the error (confirmation on
   destructive actions, inline validation before submit).
6. **Recognition rather than recall** — show options, don't make users remember them (visible
   nav, autofill, recently used).
7. **Flexibility and efficiency of use** — accelerators for experts (shortcuts, saved filters)
   without cluttering novice experience.
8. **Aesthetic and minimalist design** — every extra element competes for attention; cut
   anything not supporting the primary task.
9. **Help users recognize, diagnose, and recover from errors** — plain language, precise error
   location, suggested fix — not error codes alone.
10. **Help and documentation** — searchable, contextual, task-focused when needed (ideally the UI
    is self-explanatory enough not to need it).

## Supplementary laws

- **Jakob's Law** — users spend most time on other products; they expect yours to work the same
  way. Flag novel interaction patterns unless there's a strong reason (e.g. hamburger icon in
  unexpected place, custom scroll behavior, non-standard form layout).
- **Fitts's Law** — time to reach a target is a function of distance and size. Flag frequently
  used actions that are small or far from natural cursor/thumb position (e.g. tiny "submit" far
  from the form it submits, primary CTA smaller than secondary).
- **Hick's Law** — decision time increases with number/complexity of choices. Flag menus/flows
  with too many equally-weighted options with no grouping or default/recommended path.
- **Miller's Law (7±2)** — working memory limit; flag flows requiring users to hold too much
  info across steps (e.g. multi-step form referencing data shown 3 screens earlier).
- **Peak-End Rule** — users judge an experience mostly by its peak moment and its ending. In
  flow reviews, pay special attention to how a flow *ends* (confirmation, success state) —
  a weak/abrupt ending drags down perception of an otherwise good flow.

## How to apply during review

For each key user flow (signup, checkout, core task):
1. Walk it step by step as a first-time user would.
2. At each step, ask: what does the user need to know here, and is it visible?
3. Note any step where the user could get stuck, confused, or lose data.
4. Check the ending — is success/completion clearly communicated?
5. Map friction points to specific heuristic violated (gives the finding a concrete "why").