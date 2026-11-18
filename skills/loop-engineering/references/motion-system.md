# Motion System — E1's cinematic reference

Lock these tokens at the start of any project that moves. Every animation derives from them. Tune the values per brief, but never hand-pick a one-off duration/easing per element.

---

## 1. Tokens (defaults — adjust once, per project, then obey)

```css
:root {
  /* Duration scale — pick from these five, nothing in between */
  --dur-instant: 120ms;   /* state feedback: hover, focus, toggle */
  --dur-fast:    200ms;   /* small UI: dropdown, tooltip, tab swap */
  --dur-base:    320ms;   /* standard: card enter, panel open, modal */
  --dur-slow:    500ms;   /* section reveal, layout transition */
  --dur-hero:    800ms;   /* the ONE hero moment per screen */

  /* Easing set — each has one job */
  --ease-out:    cubic-bezier(0.16, 1, 0.3, 1);      /* entrances, reveals (expo out) */
  --ease-in-out: cubic-bezier(0.65, 0, 0.35, 1);     /* A->B state transitions */
  --ease-in:     cubic-bezier(0.7, 0, 0.84, 0);      /* exits only */
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);  /* playful/interactive — use sparingly */

  /* Travel — how far things move */
  --move-sm: 8px;    /* micro feedback */
  --move-md: 24px;   /* standard reveal rise */
  --move-lg: 48px;   /* hero / large block */

  /* Choreography */
  --stagger: 60ms;         /* 40-80ms between siblings */
  --stagger-max: 6;        /* cap the chain; beyond this, animate as a group */

  /* Depth planes (parallax + shadow scale together) */
  --depth-bg:  0.15;   /* parallax factor */
  --depth-mid: 0.4;
  --depth-fg:  1;
}
```

**Rule:** three durations on one screen is normal, seven is a finding.

---

## 2. Choreography recipes

| Pattern | Recipe |
|---|---|
| **Section reveal on scroll** | IntersectionObserver, `threshold ~0.2`, fire **once** (unobserve after). Lead element: `opacity 0->1` + `translateY(var(--move-md)) -> 0` over `--dur-slow` `--ease-out`. Siblings follow at `--stagger`, capped at `--stagger-max`. |
| **Masked text reveal** | Wrap the line, `overflow:hidden`, inner span `translateY(100%) -> 0`, `--dur-hero` `--ease-out`, per-line stagger `--stagger`. Never per-character on body copy — headlines only. |
| **Hero push-in** | `scale(1.04) -> 1` + `opacity 0 -> 1`, `--dur-hero`, `--ease-out`. Camera-like restraint: never above `1.06`. |
| **Parallax depth** | Move layers by `scrollDelta * --depth-*`. Cap total travel so nothing detaches from its section. `transform: translate3d()` only. |
| **Match cut / shared element** | The element that exists in both states keeps its identity: animate position+scale between measured boxes (FLIP), `--dur-base`, `--ease-in-out`. Everything else cross-dissolves under it. |
| **Modal / panel** | Backdrop `opacity` `--dur-fast`; panel `translateY(var(--move-md)) + scale(0.98) -> 0/1` over `--dur-base` `--ease-out`. Exit uses `--ease-in` at `--dur-fast` — exits are always faster than entrances. |
| **Tab / state swap** | Outgoing `--dur-instant` fade out, incoming `--dur-fast` fade + `--move-sm` rise. Never a full crossfade at equal timing (reads as a glitch). |
| **Loading -> content** | Skeleton holds the exact final box (no layout shift), cross-dissolve `--dur-fast`. Never a spinner replaced by a jumping layout. |
| **List add/remove** | FLIP the survivors. Removed item: `--dur-fast` `--ease-in`. Added item: `--dur-base` `--ease-out` after the reflow settles. |

**Sequencing law:** background/plate -> structure -> content -> accents. Parent before child. One thing leads; everything else supports.

---

## 3. Cinematic techniques (use 2-3 per project, not all)

- **Depth staging** — 3 planes minimum (bg / mid / fg), each with its own parallax factor and shadow weight.
- **Light as a character** — gradient or glow that shifts on scroll or hover; sells volume better than any shadow preset.
- **Grain / texture** — a low-opacity noise overlay kills the "flat template" read. Keep under 4% opacity.
- **Held beats** — deliberately delay a supporting element by 150-250ms after the lead. Silence is part of rhythm.
- **Weight through easing** — heavy objects: longer duration, softer out-curve. Light objects: fast, snappy.
- **Reveal by mask, not by fade** — `clip-path` wipes read far more expensive than an opacity fade.
- **Continuity** — if two screens share an element, animate it between them; never let it blink out and back.

---

## 4. Performance rules (pass/fail)

- Animate **only**: `transform`, `opacity`, `clip-path`, `filter` (sparingly).
- **Never** animate: `width`, `height`, `top`, `left`, `right`, `bottom`, `margin`, `padding`, `border-width`, `font-size`.
- `will-change` applied just before the animation and removed after — never left on statically, never on many elements.
- Scroll work goes through IntersectionObserver or `requestAnimationFrame`; never raw per-event scroll handlers doing layout reads.
- Batch reads before writes (measure everything, then mutate) to avoid layout thrash.
- Target 60fps; verify with a performance profile or frame capture, not by eyeballing.
- Heavy animation libraries are dynamically imported, never in the critical bundle.
- Images/video inside animated blocks have explicit dimensions (CLS = 0).

---

## 5. Accessibility (non-negotiable)

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```
- Reduced motion must land on the **complete, visible final state** — never leave an element stuck at `opacity: 0`. Test this explicitly.
- Scroll-triggered content must still exist for users who never trigger it (and for crawlers).
- Focus is never lost or trapped mid-transition; `:focus-visible` states are designed.
- Nothing flashes more than 3x/second. No infinite attention-grabbing loops in the reading path.
- Animation never gates interaction: a click during an intro is honored, not swallowed.

---

## 6. Anti-patterns (automatic E2/E3 findings)

1. A one-off duration or easing that isn't in the token set.
2. Everything on the screen animating simultaneously.
3. Two or more "hero" moments competing on one screen.
4. `scale(1.5)` / bouncing / spring on everything — theatrics instead of direction.
5. Animating layout properties (jank guaranteed).
6. Scroll reveal that re-fires every time it re-enters the viewport (unless deliberately looped).
7. Intro animation the user must sit through before they can act.
8. `prefers-reduced-motion` ignored, or "handled" by leaving elements invisible.
9. Hover states designed, focus states forgotten.
10. Motion added because the page felt boring — instead of fixing the layout and typography.
