# Project Pulse Implementation Plan

## 1. Summary

Project Pulse is a small static dashboard that helps Mona's contributors see, at a
glance, which projects are active, who owns them, their current status, recent
activity, and priority or risk level. The dashboard is built from three static app
files (`app/index.html`, `app/styles.css`, `app/project-data.json`) plus a
`.vscode/launch.json` that adds a **Run Project Pulse Dashboard** configuration.
The launch configuration serves the `app/` directory and opens
`http://localhost:%s/index.html` so learners see the polished dashboard, not a
directory listing.

The work is delivered by the agent team defined in `docs/agent-team.md`:
Orchestrator (Claude Opus 4.7), Planner (Claude Opus 4.7), Designer (Gemini 3.1
Pro), and Coder (GPT-5.5). The Orchestrator turns this plan into non-overlapping
phases, the Designer owns the visual system and accessibility, and the Coder
implements the static files and the launch configuration.

## 2. Ordered Implementation Steps

1. **Lock the data contract.** Define the shape of `app/project-data.json`
   (top-level `projects` array; each entry has `name`, `owner`, `status`,
   `recentActivity`, `priority`). This is the single source of truth every other
   file depends on.
2. **Designer direction.** Designer produces the visual system: information
   hierarchy, card layout, status-badge treatment, priority treatment, spacing,
   typography, color contrast, focus states, and responsive breakpoints. Output
   is a written direction that names required CSS hooks (`.dashboard`,
   `.project-card`, status/priority modifier classes) and accessibility rules
   (semantic landmarks, alt/aria, contrast ratios).
3. **Coder writes `app/project-data.json`.** A minimum of three sample projects
   that exercise each `status` value and each `priority` value the Designer
   named, plus at least one long title and one long `recentActivity` string to
   stress the layout.
4. **Coder writes `app/index.html`.** Semantic markup with an exact `Project
   Pulse` title, `<link>` to `styles.css`, reference to `project-data.json`,
   a `.dashboard` container, and visible project cards using the
   `project-card` class. Each card renders `name`, `owner`, `status`,
   `recentActivity`, and `priority`.
5. **Designer writes `app/styles.css`** (or Coder implements from Designer's
   spec, per Orchestrator's phase choice). Must include a `.dashboard` selector
   and a `.project-card` selector, plus `border-radius` and `box-shadow` for
   polished visuals, responsive layout, legible status badges, and a clear
   priority treatment.
6. **Coder writes `.vscode/launch.json`.** Strict JSON with no comments; adds a
   configuration named exactly `Run Project Pulse Dashboard` that serves from
   `${workspaceFolder}/app` and opens `http://localhost:%s/index.html` via a
   `serverReadyAction`.
7. **Orchestrator integration check.** Verify all four files are present, the
   dashboard renders cards from the JSON, badges and priority treatment are
   visible and legible, and the launch configuration opens the dashboard.

## 3. File Assignments

| File | Owning agent | Contents / responsibility |
| ---- | ------------ | ------------------------- |
| `app/project-data.json` | Coder | Strict JSON. Top-level `projects` key holding an array. Each project object has `name`, `owner`, `status`, `recentActivity`, `priority`. Sample data exercises every status and priority value defined by Designer. |
| `app/index.html` | Coder | Semantic HTML5. Exact page title `Project Pulse`. `<link rel="stylesheet" href="styles.css">`. Loads/references `project-data.json` and renders it into visible project cards using the `project-card` class inside a `.dashboard` container. Each card shows `name`, `owner`, `status`, `recentActivity`, and `priority`. Accessible landmarks and heading order per Designer direction. |
| `app/styles.css` | Designer (may hand implementation to Coder) | Visual system for the dashboard. Must include `.dashboard` and `.project-card` selectors, `border-radius`, `box-shadow`, responsive layout, status-badge styles, priority treatment, focus-visible states, and accessible color contrast. |
| `.vscode/launch.json` | Coder | Strict JSON with no comments. Configuration named `Run Project Pulse Dashboard`. `cwd` set to `${workspaceFolder}/app`. Command `python3 -m http.server 5500`. `serverReadyAction` that opens `http://localhost:%s/index.html` so the dashboard frontend loads, not a directory listing. |

## 4. Designer Responsibilities

The Designer owns the user experience and visual system for the dashboard. This
includes:

- **UX and information hierarchy** — page title `Project Pulse`, project cards as
  the primary unit, ordering of fields inside each card (name → owner → status →
  recent activity → priority), and scannable typography.
- **Accessibility** — semantic landmarks (`<main>`, `<section>`, headings in
  order), color contrast that meets WCAG AA for text and badges, focus-visible
  styles, non-color signaling for status and priority (icon or shape in addition
  to color), and readable line lengths.
- **Responsive layout** — a card grid that reflows from single-column on narrow
  viewports to multi-column on wider viewports, with readable spacing at every
  breakpoint.
- **Visual system for cards, badges, and priority** — rounded corners, elevation
  via `box-shadow`, spacing scale, badge shapes for each `status` value, and a
  visually distinct priority treatment (e.g., border accent, pill, or icon) that
  makes high-risk items obvious without relying on color alone.
- **CSS hook contract** — names the required class selectors so the Coder's
  markup and the Designer's styles line up. At minimum: `.dashboard`,
  `.project-card`, and modifier classes per status and priority value.

**Files the Designer's direction drives:** `app/styles.css` (owned), plus the
class-name contract and semantic-structure requirements that `app/index.html`
must honor.

## 5. Coder Responsibilities

The Coder implements the static app files and the launch configuration to match
the plan and the Designer's direction. This includes:

- **`app/project-data.json`** — write valid JSON with a top-level `projects`
  array; each entry includes `name`, `owner`, `status`, `recentActivity`,
  `priority`. Provide sample rows that exercise every status and priority the
  Designer named plus one long-title and one long-activity row.
- **`app/index.html`** — write semantic markup, include the exact `Project Pulse`
  title, link `styles.css`, reference/load `project-data.json`, and render
  project cards with the `project-card` class inside a `.dashboard` container.
  Every card visibly renders `status`, `recentActivity`, and `priority`.
- **`app/styles.css`** — when assigned implementation duty by the Orchestrator,
  translate the Designer's spec into CSS using the agreed class hooks. Must
  include `.dashboard`, `.project-card`, `border-radius`, and `box-shadow`, plus
  responsive rules.
- **`.vscode/launch.json`** — create as strict JSON with no comments; add the
  `Run Project Pulse Dashboard` configuration with `cwd`
  `${workspaceFolder}/app`, command `python3 -m http.server 5500`, and a
  `serverReadyAction` that opens `http://localhost:%s/index.html`.

**Files the Coder writes:** `app/index.html`, `app/project-data.json`,
`.vscode/launch.json`, and `app/styles.css` if the Orchestrator assigns CSS
implementation to Coder rather than Designer.

## 6. Dependencies Between Steps

| Depends on | Blocks | Why |
| ---------- | ------ | --- |
| Data-shape decision (Step 1) | `app/index.html` markup, `app/project-data.json` sample data, `app/styles.css` badge/priority variants | Field names and enumerated status/priority values must exist before markup, sample data, and modifier classes can be written. |
| Designer direction (Step 2) | `app/styles.css` implementation, `app/index.html` class hooks and semantic structure | CSS selectors, badge shapes, priority treatment, and accessibility rules must be agreed before styling and markup are finalized. |
| `app/project-data.json` (Step 3) | `app/index.html` render logic | The markup needs to know exactly which fields to display and in what order. |
| `app/index.html` (Step 4) | `.vscode/launch.json` (Step 6), Orchestrator integration check (Step 7) | Launch config targets `index.html`; the integration check needs the page to exist. |
| Designer direction + `app/index.html` class hooks (Steps 2 and 4) | `app/styles.css` (Step 5) | CSS depends on both the visual spec and the DOM structure it will style. |
| `app/index.html` present (Step 4) | `.vscode/launch.json` (Step 6) | Launch config's `serverReadyAction` opens `index.html`; the file must exist before the config is meaningful. |

## 7. Parallel vs. Sequential Work

| Track | Kind | Rationale |
| ----- | ---- | --------- |
| Step 1 (data contract) | Sequential, first | Everything downstream reads this contract. |
| Step 2 (Designer direction) and Step 3 (`app/project-data.json`) | **Parallel** | File scopes do not overlap (`app/styles.css` vs. `app/project-data.json`) and neither reads the other's output during authoring. |
| Step 4 (`app/index.html`) | Sequential after Steps 1–3 | Markup encodes the data contract and the Designer's class-hook contract. |
| Step 5 (`app/styles.css`) | Sequential after Steps 2 and 4 | CSS must target the actual DOM structure the Coder produced and reflect the Designer's spec. |
| Step 6 (`.vscode/launch.json`) | **Parallel** with Step 5 once Step 4 is done | `.vscode/launch.json` and `app/styles.css` do not overlap; both only need `app/index.html` to exist. |
| Step 7 (integration check) | Sequential, last | Requires all four files to be in place. |

## 8. Validation Expectations

Manual validation the Orchestrator performs:

- `app/index.html` renders visible project cards populated from
  `app/project-data.json` (no hard-coded card content that ignores the JSON).
- Each card displays `name`, `owner`, `status`, `recentActivity`, and `priority`.
- Status badges and priority treatment are visible and legible; non-color
  signaling is present.
- Layout is responsive at narrow, medium, and wide viewports.
- Text meets WCAG AA contrast against its background.
- Running **Run Project Pulse Dashboard** from VS Code's Run and Debug view
  opens a browser at `http://localhost:5500/index.html` showing the dashboard —
  not a directory listing.

Automated checks aligned with `scripts/validate-exercise.sh` and the step
workflows (the plan must not conflict with any of these):

- `app/project-data.json` parses as JSON and has a top-level `projects` key.
- Each project object includes `name`, `owner`, `status`, `recentActivity`, and
  `priority`.
- `app/index.html` contains the phrase `Project Pulse`, references
  `styles.css`, references `project-data.json`, and uses the `project-card`
  class.
- `app/styles.css` contains a `.dashboard` selector, a `.project-card`
  selector, `border-radius`, and `box-shadow`.
- `.vscode/launch.json` parses as JSON (validated with `python3 -m json.tool`),
  contains a configuration named `Run Project Pulse Dashboard`, references
  `index.html`, and serves from the `app` directory.
- All four files (`app/index.html`, `app/styles.css`, `app/project-data.json`,
  `.vscode/launch.json`) exist at the paths above.

## 9. Edge Cases and Risks

- **Empty data.** `projects` is an empty array. The dashboard must render an
  accessible empty state instead of a blank page.
- **Missing fields.** A project object omits a field (e.g., no `priority`). The
  card should render a neutral fallback rather than breaking layout or leaving
  ambiguous whitespace.
- **Unknown status or priority values.** Data contains a value the Designer's
  system did not style. The card should degrade to a neutral badge and not
  break.
- **Long titles and long recent-activity strings.** Cards must wrap or truncate
  gracefully without overflowing the grid.
- **Many projects.** The grid must remain readable at 20+ cards on a narrow
  viewport.
- **Accessibility contrast.** Status and priority colors must meet WCAG AA and
  must not be the only signal.
- **JSON fetch on `file://`.** If `app/index.html` uses `fetch()` to load
  `project-data.json`, it will fail when opened directly from the filesystem.
  This is exactly why the launch configuration serves the `app/` directory
  over HTTP.
- **Launch config path issues.** `cwd` must be `${workspaceFolder}/app` and
  the URL must include `/index.html`; otherwise the browser lands on a
  directory listing.
- **`.vscode/launch.json` strictness.** Must be strict JSON with no comments
  (the exercise validates it with `python3 -m json.tool`).
- **Port collisions.** Port 5500 may be in use. The learner may need to stop a
  prior server before launching.
- **Duplicated CSS ownership.** If both Designer and Coder edit
  `app/styles.css`, the Orchestrator must sequence them so writes do not
  conflict.

## 10. Open Questions

- Should `app/styles.css` be authored by the Designer or by the Coder from the
  Designer's spec? Either is workable; the Orchestrator must pick one owner to
  prevent conflicting edits.
- Are the enumerated `status` values (e.g., `on-track`, `at-risk`,
  `off-track`) and `priority` values (e.g., `high`, `medium`, `low`) fixed by
  Mona, or does the Designer choose them?
- How should `recentActivity` be represented — free-form string, ISO date, or
  structured object? The plan assumes a short free-form string.
- Is a "last updated" timestamp expected in each card? The brief does not
  require it.
- Should the dashboard include a filter or sort control, or is the first
  release read-only? The brief implies read-only.
- Is port 5500 fixed, or may the launch configuration pick another port if
  5500 is unavailable?