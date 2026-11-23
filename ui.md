# User interface conventions

Use these rules for user-facing web interfaces. A local product overlay may add
stricter requirements, concrete design tokens, supported browsers, and viewport
targets. Preserve an established visual system unless a redesign is requested.

## Establish the product direction

- Read the product brief, existing screens, components, design tokens, and
  assets before designing. Identify the audience, primary task, and important
  states before choosing a layout.
- Reuse the project's components and semantic tokens. Do not introduce a
  competing visual language, one-off values, or a new dependency for a small
  styling change.
- Give each surface a clear hierarchy and one primary action. Use realistic
  product content and assets; do not add decorative elements that compete with
  the task or repeat a generic card layout without a purpose.

## Support light, dark, and system themes

- For every user-facing web interface, provide **Light**, **Dark**, and
  **System** theme options unless an explicit product requirement makes them
  inapplicable. Document any exception.
- Default to **System** when no preference has been saved. Persist the selected
  option, not only the resolved light or dark appearance.
- While **System** is selected, follow `prefers-color-scheme`, including changes
  made during the session. An explicit Light or Dark selection overrides the
  operating-system preference.
- Apply the initial theme before the first paint to avoid a flash of the wrong
  theme. Give the theme control an accessible name and expose the current
  selection programmatically.
- Define semantic color tokens for both themes and set the appropriate
  `color-scheme` so native controls and scrollbars match. Do not create dark
  mode by inverting the light theme.
- Verify text, controls, focus indicators, icons, images, shadows, and every
  interaction state in both themes. Passing contrast in one theme does not prove
  the other theme passes.

## Build desktop first, then adapt

- Implement the primary desktop layout first at the project's main desktop
  viewport. Then add intentional tablet and mobile adaptations. Desktop-first is
  the implementation order, not permission to neglect smaller screens.
- Add breakpoints when the content needs them rather than targeting specific
  devices. Reflow, collapse, or reorder content instead of merely shrinking the
  desktop layout.
- Preserve content and functionality at every supported size. Avoid horizontal
  page scrolling at 320 CSS pixels, except for content that inherently requires
  two-dimensional layout, such as data tables or diagrams.
- Make media responsive and preserve its aspect ratio. Account for long labels,
  large content, empty collections, browser zoom, and safe areas where relevant.
- Do not disable pinch zoom. Keep the interface usable at 200% browser zoom.
  Target at least 44 × 44 CSS pixels for touch controls, with spacing between
  adjacent actions.

## Use consistent typography and spacing

- Use the design system's type scale and spacing tokens. Keep body text
  readable, constrain long-form text to approximately 45–75 characters per line,
  and use relative units that respect user font-size preferences.
- Establish hierarchy through size, weight, alignment, and grouping rather than
  excessive containers or decoration. Keep related elements close and use
  visibly larger gaps between separate groups.
- Keep headings semantically correct and visually appropriate to their role.
  Prevent clipping, unintended truncation, distorted media, and awkward wrapping
  of essential content.

## Make accessibility part of the implementation

- Use semantic HTML: links for navigation, buttons for actions, associated
  labels for form fields, logical headings, and appropriate landmarks. Add ARIA
  only when native semantics are insufficient.
- Make every interaction keyboard operable with a logical focus order and a
  visible `:focus-visible` indicator. Manage focus when opening and closing
  dialogs, and never create an unintentional keyboard trap.
- Meet WCAG AA contrast: 4.5:1 for normal text, 3:1 for large text, and 3:1 for
  meaningful control boundaries, icons, and focus indicators. Recheck every
  pairing in both light and dark themes.
- Never use color as the only way to communicate status, errors, selection, or
  other meaning. Pair it with text, an icon, a pattern, or another cue.
- Provide useful alternative text for meaningful images and empty alternative
  text for decorative images. Respect `prefers-reduced-motion` and keep content
  usable without animation.

## Design complete interaction states

- Define default, hover, active, focus-visible, disabled, and loading states
  where applicable to each control. Do not rely on hover to reveal essential
  information or functionality.
- Design loading, empty, error, and success states for data-driven surfaces.
  Give users specific error messages and a recovery path, and preserve their
  input after failed submissions.
- Acknowledge actions promptly and prevent accidental duplicate submissions.
  Use optimistic updates only when failure can be handled without misleading
  the user.
- Keep labels action-oriented and familiar. Use confirmation or undo for
  destructive actions in proportion to their consequences.

## Verify the rendered interface

- Exercise the real interface at the primary desktop size and representative
  tablet and mobile widths. Inspect Light and Dark explicitly, verify that the
  selected option survives a reload, and confirm that System reacts to a live
  operating-system theme change.
- Test keyboard navigation, focus management, reduced motion, 200% zoom, long
  content, and the important loading, empty, error, and success states. Run the
  project's relevant automated accessibility and UI tests.
- Compare the rendered result with the requested direction, not merely with the
  absence of runtime errors. Fix material visual or interaction problems and
  inspect the affected sizes and states again.
- Distinguish code checks from visual verification. If a browser, device, or
  required state could not be tested, report that limitation instead of claiming
  a pass.

## Sources

This document is a curated synthesis informed by
[TypeUI](https://github.com/bergside/typeui) and
[UIZZE](https://github.com/uizze/uizze), discovered through Awesome OpenCode,
with accessibility requirements checked against
[WCAG 2.2](https://www.w3.org/TR/WCAG22/). Exact reviewed revisions are recorded
in `sources.yaml`. The desktop-first implementation order is a local convention;
narrow-screen, zoom, and accessibility requirements remain mandatory.
