# Frontend — React / Next.js / CSS

## Server vs. Client Components (App Router)

- Default every component to a Server Component. Add `"use client"` only at the leaf where
  interactivity (state, effects, event handlers, browser APIs) is actually needed.
- Push client boundaries as far down the tree as possible — wrap only the interactive part in
  a client component, not the whole page.
- Data fetching happens in Server Components/Server Actions directly — avoid client-side
  `useEffect` fetch waterfalls for initial page data.

## Rendering & performance

- `next/image` and `next/font` by default — don't hand-roll image optimization or font loading.
- `next/dynamic` for heavy, rarely-used, or client-only components (charts, editors, modals).
- Memoize (`useMemo`/`useCallback`/`React.memo`) only after identifying an actual re-render cost
  via React DevTools Profiler — not preemptively on every component.
- Virtualize long lists (e.g. `@tanstack/react-virtual`) instead of rendering thousands of DOM
  nodes.

## State

- Server state (fetched from an API) → TanStack Query. Don't duplicate it into local/global
  client state.
- Local UI-only state → `useState`/`useReducer` for component-scoped, Zustand for cross-tree
  UI state.
- Form state → React Hook Form + Zod resolver for anything beyond 2-3 fields.

## Styling

- Tailwind utility classes for layout/spacing/color; extract a component only when a pattern
  repeats 3+ times.
- Design tokens (spacing scale, color palette, type scale) belong in `tailwind.config` — avoid
  magic numbers in class names.

## Accessibility (default consideration, not an afterthought)

- Semantic HTML first (`<button>`, `<nav>`, `<main>`, `<label>`) before reaching for ARIA.
- All interactive elements keyboard-operable; visible focus states never removed without a
  replacement.
- Form inputs always have an associated `<label>`; icon-only buttons get `aria-label`.
- Color contrast meets WCAG AA at minimum for body text.

## Common anti-patterns to flag (briefly, then proceed with the task)

- Entire page marked `"use client"` when only one child needs interactivity.
- Fetching data in `useEffect` when it could be a server-side fetch.
- Inline object/array literals passed as props to memoized children (breaks memoization).
- Index-as-key on lists that can reorder/filter.