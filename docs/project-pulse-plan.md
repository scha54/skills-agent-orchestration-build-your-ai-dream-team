# Project Pulse implementation plan

## Summary

Build a small, static Project Pulse dashboard so contributors can scan active projects, owners, status, recent activity, priority or risk, and a short summary. Use the existing brief and agent-team roles; do not add a framework or package dependency. The dashboard must be served from `app/` and open `index.html` through the VS Code **Run Project Pulse Dashboard** configuration.

## Roles and file ownership

- **Orchestrator** coordinates the phases below, gives each specialist only its assigned files, resolves the shared markup/style/data contract before parallel work, and verifies the integrated result. It does not implement the app.
- **Planner** has completed this plan; no implementation files are assigned.
- **Designer** owns `app/styles.css`. Define the visual hierarchy, accessible color and status/priority treatments, responsive card layout, typography, spacing, focus states, and polished dashboard styling. Include stable `.dashboard` and `.project-card` hooks, rounded cards, and subtle shadows. Report design tradeoffs and validation recommendations.
- **Coder** owns `app/project-data.json`, `app/index.html`, and `.vscode/launch.json`. Implement deterministic data and semantic markup, connect the page to the stylesheet and JSON data, render the project cards and user-facing fields, handle data-loading errors clearly, and provide the assigned runnable preview configuration. Keep launch JSON strict, with no comments.

## Ordered implementation steps

### 1. Confirm the shared contract

Before delegation, the Orchestrator tells Designer and Coder to use these agreed hooks and fields:

- The page links to `styles.css` and loads `project-data.json` relative to `index.html`.
- A top-level `.dashboard` container holds repeated `.project-card` elements.
- The JSON has a top-level `projects` array. Every item has `name`, `owner`, `status`, `recentActivity`, and `priority`; also include and display a concise `summary` to meet the contributor-friendly brief.
- Render all required fields as readable text. Give status and priority clear labels and do not communicate their meaning by color alone.

This contract prevents CSS/markup mismatches while keeping file ownership separate.

### 2. Build independent foundations in parallel

- **Designer:** create `app/styles.css` against the agreed hooks. Include responsive behavior, readable contrast and spacing, visible keyboard focus, and clear card/status/priority styling.
- **Coder:** create `app/project-data.json` with several representative projects and the agreed fields; keep values useful and consistent. Also create `.vscode/launch.json` with a deterministic **Run Project Pulse Dashboard** configuration using `python3 -m http.server 5500`, `cwd` `${workspaceFolder}/app`, and a `serverReadyAction` that opens `http://localhost:%s/index.html`.

These tasks may run concurrently because they own different files and their interfaces are fixed in Step 1. The launch configuration has no dependency on the visual design or project records.

### 3. Implement the page after the contract and foundations are ready

- **Coder:** create `app/index.html` after Step 1 and in coordination with the completed CSS/data contract. Use the exact title **Project Pulse**, semantic page structure, and accessible loading/error feedback. Fetch and render the JSON projects into `.project-card` elements, including name, owner, status, recent activity, priority, and summary. Keep data rendering safe and provide a clear empty-state if the array has no projects.

This step follows the foundation work so the page can match the actual data schema and Designer's selectors without overwriting Designer-owned CSS.

### 4. Integrate and validate

- **Orchestrator** checks that the four assigned files agree on paths, selectors, fields, and launch settings; request only scoped corrections from the relevant specialist.
- **Coder** validates the JSON and launch files as strict JSON and confirms the launch configuration uses the required command, working directory, and page URL.
- **Designer** reviews the rendered dashboard for hierarchy, responsive layout, focus visibility, contrast, and status/priority clarity.
- **Orchestrator** launches **Run Project Pulse Dashboard** and confirms the browser shows the dashboard at `/index.html`, not a directory listing. Check a normal populated state and a failed/empty data state.

## Dependencies and parallel-work decisions

1. The shared contract in Step 1 precedes all implementation.
2. In Step 2, Designer's CSS and Coder's JSON plus launch configuration can run in parallel: file scopes do not overlap and neither depends on the other's output.
3. Coder's `app/index.html` follows Step 2 so it can integrate the final selectors and schema. If the agreed contract is maintained, CSS and JSON do not need to be merged into a common file.
4. Integration review and browser validation must follow all four files; fixes must be made by their assigned owner and revalidated.
5. Do not have Designer and Coder edit the same file concurrently. Do not build HTML before the shared contract is agreed.

## Edge cases and risks

- A failed JSON request or malformed/unexpected payload should produce a useful visible message rather than a blank dashboard.
- An empty `projects` array should show a friendly empty state.
- Missing or empty field values should not break card rendering; show a sensible fallback or omit the detail without misleading users.
- Status and priority labels should remain understandable without color, including for users with reduced color perception.
- Relative asset/data paths must work when served from `app/`; opening the HTML directly as a `file://` URL is not the supported preview path.
- Port `5500` may already be occupied. If so, stop the other server or choose a consistent replacement port and update both launch settings and URL together.
- Keep `.vscode/launch.json` strict JSON and avoid requiring an uninstalled VS Code extension for the launch configuration.

## Validation expectations

- Confirm all assigned files exist and inspect the rendered page at desktop and narrow/mobile widths.
- Run `python3 -m json.tool app/project-data.json` and `python3 -m json.tool .vscode/launch.json`.
- Check the page title and references to `styles.css` and `project-data.json`; verify `.dashboard`, `.project-card`, `border-radius`, and `box-shadow` are present in the stylesheet.
- Verify project data contains the top-level `projects` array and each record has `name`, `owner`, `status`, `recentActivity`, and `priority`; confirm the summary is visible in the UI.
- Confirm each card displays status, recent activity, and priority, with a useful empty/error state.
- Run **Run Project Pulse Dashboard** and confirm the server starts in `app/` and opens `http://localhost:<port>/index.html`. Stop the preview server after checking it.
- The exercise's Step 3 workflow checks the required file presence, content phrases, and JSON syntax; use those checks as a final compatibility checklist. No build or dependency installation should be needed.

## Open questions

- The brief does not prescribe project names, exact copy, or status/priority vocabulary. Use a small, representative sample and consistent human-readable labels.
- `summary` is not in the required JSON field list, but the brief asks for a contributor-friendly summary; this plan treats it as an additional field and requires it to be displayed.
