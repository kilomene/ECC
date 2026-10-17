# Design System Generation, Audit, and AI-Slop Detection

## Generate a design system

- Scan the existing CSS or ThemeData for the colors, typography, spacing, border radius, shadows, and breakpoints actually in use. Use `muse.exec` with grep or ripgrep to list every hex color, font size, spacing value, radius, and shadow.

- Write the token set to `design-tokens.json`, to CSS custom properties, and to Dart theme constants. Stay framework-agnostic here: plain CSS custom properties and Dart constants, never Tailwind classes.

- Write `DESIGN.md` with one short rationale paragraph per token group. Explain why each choice fits this product in particular.

- Build an interactive preview HTML page and deliver it through the `artifact` HTML tool. Keep the page self-contained with no external dependencies.

- Research competitors with `browser.search` and `browser.open` only when asked, and only for Osh's own public products. Never pull styling cues from competitors without his go-ahead.

- Apply the delivery_boundary skill as the gate: tokens plus preview are produced and approved before the UI ships.

## Audit an existing UI

- Score color consistency 0 to 10 by counting every hex or named color that falls outside the palette. Give each deviation an exact file and line fix.

- Score typography hierarchy 0 to 10 by checking that headings descend cleanly from h1 through body to caption. Flag raw inline font sizes and replace them with theme styles.

- Score spacing rhythm 0 to 10 by checking that margins and padding follow one scale. Replace arbitrary pixel values with the nearest token.

- Score component consistency 0 to 10 by comparing elements that do the same job. Unify their markup and styles into one shared pattern.

- Score responsive behavior 0 to 10 by checking every breakpoint for breakage. Fix overflow with flexible layout widgets or fluid CSS, not with locked widths.

- Score dark mode 0 to 10 by testing every screen in dark theme. Remove any assumption of a light background.

- Score animation 0 to 10 by checking that each animation serves the interaction. Remove gratuitous motion and keep transitions under 300 milliseconds.

- Score accessibility 0 to 10 by checking text contrast against 4.5 to 1, tap targets against 48 pixels, and focus states on every interactive element.

- Score information density 0 to 10 by checking for cluttered screens and cramped components. Give content room to breathe without adding empty chrome.

- Score polish 0 to 10 by checking hover states, transitions, loading states, and empty states. Every interactive element gets feedback and every async view gets all four states.

## Detect AI-slop patterns

- Reject gradients applied to everything without a purpose. A gradient needs a reason to exist.

- Reject default purple-to-blue color palettes. They are the signature of an untouched generator.

- Reject glassmorphism with no purpose behind it. Frosted cards that sit on empty backgrounds are decoration pretending to be design.

- Reject rounded corners on elements that should be square. Shape follows the object, not the trend.

- Reject animations that fire on every scroll without guiding attention. Motion earns its place or it leaves.

- Reject generic heroes with centered text floating over a stock gradient. Give the top of the page one real message and one real visual.

- Hold every design to Osh's premium-pack standard of one cohesive styling across all designs and icons. Generic output is not a style.

## Available connectors

- Use `muse.connector_search` to find other connectors and `muse.skill_search` to find related skills.

- If nothing here fits, look on the web for a maintained API or MCP server and set it up with [Custom Connectors and MCPs](/opt/muse/skills/custom-connector-setup/SKILL.md), or build a custom connector.
