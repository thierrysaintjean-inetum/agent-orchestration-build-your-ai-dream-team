# Project Pulse Implementation Plan

## Summary

The goal is to deliver a lightweight static Project Pulse dashboard for contributors. The dashboard will make project activity easy to scan by showing project names, owners, statuses, recent activity, and priority or risk levels in polished project cards. The implementation will consist of `app/index.html`, `app/styles.css`, `app/project-data.json`, and a VS Code preview configuration at `.vscode/launch.json`.

This is an implementation plan only. It does not implement or edit application code.

## Ownership and Responsibilities

### Orchestrator

- Coordinates the work, assigns non-overlapping file scopes, and integrates the result.
- Requires the Planner’s file assignments and dependency sequence to remain the source of truth.
- Reviews the final handoff for internal consistency and launch behavior.

### Designer

- Owns the visual and interaction direction for the dashboard.
- Determines the information hierarchy, card structure, status-badge treatment, priority emphasis, readable spacing, typography, contrast, accessibility, and responsive behavior.
- Provides deterministic styling hooks including `.dashboard` and `.project-card`, and plans polished use of `border-radius`, `box-shadow`, and responsive layout.
- Reviews `app/index.html` and `app/styles.css` only from the design perspective, while the Coder owns the actual file edits.

### Coder

- Implements `app/index.html`, `app/styles.css`, `app/project-data.json`, and `.vscode/launch.json` only within the scopes assigned by the Orchestrator.
- Builds semantic, accessible HTML that presents visible project cards from the data.
- Creates deterministic CSS hooks and responsive styling.
- Uses strict, valid JSON for project data and launch configuration.
- Creates the VS Code launch configuration that serves the `app/` directory and opens `index.html`, rather than a directory listing.

## Ordered Implementation Steps

### Phase 1: Define the contract

1. Confirm the empty or existing repository state and the requirements in `.github/project-pulse-brief.md` and `.github/agents/`.
2. Agree on the dashboard structure and data contract:
   - `app/project-data.json` has a top-level `projects` array.
   - Every project includes `name`, `owner`, `status`, `recentActivity`, and `priority`.
   - `app/index.html` uses the exact title **Project Pulse** and a `.dashboard`-associated layout.
3. Establish the launch contract:
   - The configuration name is **Run Project Pulse Dashboard**.
   - It serves from `app/` with `python3 -m http.server 5500`.
   - It opens `http://localhost:%s/index.html`.

**Dependencies:** None; this phase is the approved starting contract.

### Phase 2: Prepare independent design and data work

1. **Designer responsibility:** Define the card layout, status-badge colors and labels, priority/risk emphasis, spacing, responsive breakpoints, and accessibility behaviors. These decisions are design specifications for the Coder, not a separate design file.
2. **Coder responsibility for `app/project-data.json`:** Create representative project records with the required fields. This establishes stable content for `app/index.html` to render.
3. **Coder responsibility for `.vscode/launch.json`:** Define strict JSON with no comments. Set the working directory to `${workspaceFolder}/app`, use `python3 -m http.server 5500`, and open `http://localhost:%s/index.html`.

**Parallel work:** The Designer’s visual specification and Coder work on `app/project-data.json` can proceed in parallel, because they do not modify the same file. `.vscode/launch.json` can also be prepared in parallel with data work because it is independent of project-record content. The Coder must keep all launch JSON strict and must not change HTML or CSS while assigned only to these files.

**Dependencies:** Phase 2 depends on Phase 1. Launch configuration work has no dependency on card content beyond the fixed port and URL contract.

### Phase 3: Build the frontend

1. **Coder responsibility for `app/index.html`:** Create the Project Pulse page, link `styles.css`, reference or load `project-data.json`, and render a visible card for every project. Give each card the class `project-card`; show status, recent activity, and priority. Use semantic headings and accessible markup.
2. **Coder responsibility for `app/styles.css`:** Create the polished dashboard presentation, including a `.dashboard` selector, a `.project-card` selector, readable typography, spacing, status and priority affordances, `border-radius`, `box-shadow`, and responsive layout.

**Parallel work:** `app/index.html` and `app/styles.css` may be developed in parallel only after the agreed markup hooks and data shape are fixed. The Designer may provide or review the visual specification while the Coder edits these separate files.

**Dependencies:** Frontend work depends on the data contract and visual specification. `app/index.html` depends on `app/project-data.json` being valid and containing the required fields. `app/styles.css` depends on the HTML class and layout hooks (`.dashboard`, `.project-card`) being agreed. HTML and CSS can proceed together, but CSS must match the final markup.

### Phase 4: Integrate and validate

1. The Orchestrator verifies that the HTML links to `styles.css`, loads or references `project-data.json`, renders project cards, and includes status, `recentActivity`, and priority values.
2. Verify that CSS includes `.dashboard`, `.project-card`, `border-radius`, `box-shadow`, and responsive rules.
3. Parse `app/project-data.json` and `.vscode/launch.json` as JSON and confirm the launch configuration opens the dashboard URL rather than a directory root.
4. Run or review the VS Code **Run Project Pulse Dashboard** launch configuration and confirm the browser reaches `http://localhost:5500/index.html` and shows the frontend.

**Sequential handoff:** Integration follows completion of all four file assignments. It must not begin while any app file is still being changed by an agent.

## File Assignments

| File | Owner | Responsibility | Primary dependencies |
|---|---|---|---|
| `app/index.html` | Coder | Semantic dashboard markup; exact Project Pulse title; stylesheet and data references; visible project cards and required project fields | Data contract in `app/project-data.json`; agreed `.dashboard` and `.project-card` hooks |
| `app/styles.css` | Coder | Polished, accessible, responsive dashboard styling; `.dashboard`; `.project-card`; badges, rounded cards, shadows, and priority treatment | Markup structure in `app/index.html`; Designer specification |
| `app/project-data.json` | Coder | Top-level `projects` array with `name`, `owner`, `status`, `recentActivity`, and `priority` for each project | Phase 1 data contract |
| `.vscode/launch.json` | Coder | Strict JSON launch definition named **Run Project Pulse Dashboard**; app directory working directory; HTTP server command; server-ready URL to `index.html` | Fixed launch requirements in the brief |

## Dependencies and Ordering

1. **Contract first:** Read the brief and agent definitions before assigning work.
2. **Data and specification:** Finalize project fields and visual decisions before markup implementation.
3. **Markup and styles:** Implement HTML and CSS after the shared hooks and data shape are fixed.
4. **Launch configuration:** May be created in parallel with data work, but integration occurs only after the app files exist.
5. **Integration and validation:** Run only after HTML, CSS, data, and launch JSON are complete.

## Explicit Parallel Work Decisions

- **Parallel:** Designer visual specification and Coder preparation of `app/project-data.json`.
- **Parallel:** Coder preparation of `.vscode/launch.json` and Coder preparation of `app/project-data.json` only as separate file assignments, with no same-file overlap.
- **Parallel:** `app/index.html` and `app/styles.css` after markup hooks and the data shape are approved.
- **Sequential:** Contract approval, HTML/CSS/data integration, launch setup verification, and final browser validation.
- **Not parallel:** No agent should edit the same file concurrently. No app integration should begin until all assigned file edits stop.

## Edge Cases and Risks

- Missing or invalid project fields could leave cards incomplete; validate every project record.
- Malformed JSON would prevent the dashboard or launch setup from working; parse both JSON files explicitly.
- CSS-only changes must not remove HTML hooks or accessibility semantics.
- Responsive layouts must remain readable at narrow and wide widths.
- Status and priority values need clear contrast, not color alone.
- The launch configuration must use the app directory as the server root and a direct URL ending in `/index.html`; it must not open a directory listing.
- Static data loading must use a browser-compatible approach with no server requirements beyond the launch command.

## Validation Expectations

The final implementation is complete when all of the following validation checks pass:

- `docs/project-pulse-plan.md` exists and records this plan.
- `app/index.html` exists and contains the exact title **Project Pulse**.
- `app/index.html` links `styles.css` and loads or references `project-data.json`.
- `app/index.html` renders project cards using the `project-card` class and visibly presents status, `recentActivity`, and priority.
- `app/styles.css` exists and contains `.dashboard`, `.project-card`, `border-radius`, `box-shadow`, and responsive styling.
- `app/project-data.json` parses as JSON and has a top-level `projects` array.
- Every entry in `app/project-data.json` includes `name`, `owner`, `status`, `recentActivity`, and `priority`.
- `.vscode/launch.json` parses as strict JSON with no comments and is named **Run Project Pulse Dashboard**.
- Launch configuration serves from `app/`, executes `python3 -m http.server 5500`, and opens `http://localhost:%s/index.html`.
- The dashboard renders in a browser as a polished frontend, not a server directory listing.
- A final integration review confirms no out-of-scope files were changed.

## Open Questions

- The sample project names and status labels are unspecified, so the Coder may use concise representative contributor-friendly values while retaining the required schema.
- No existing application pattern is present in `app/`, so the implementation should establish a clear, self-contained static structure.
