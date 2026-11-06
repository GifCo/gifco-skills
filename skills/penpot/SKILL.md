---
name: penpot
description: Create, edit, or inspect designs on a Penpot page identified by page ID or page name, using an already connected Penpot MCP session. Prefer existing design tokens, components, and assets.
---

# Penpot

Work in the open Penpot file through the active MCP connection. Assume Penpot is open and the harness already has the required credentials. If the connection is unavailable, report the specific blocker; do not start installation, credential setup, or configuration changes.

## Invocation and page selection

Accept `penpot <page-id or page name> [task]` or `$penpot <page-id or page name> [task]`. Page names may contain spaces; quotes are optional when the intended name is clear.

Examples:

- `penpot Checkout`
- `$penpot "Mobile Screens" refine the checkout form`
- `$penpot 123e4567-e89b-42d3-a456-426614174000 add a settings panel`

1. Read the connected tool's `high_level_overview` once per conversation, then use `penpot_api_info` for the API types/members needed. These are documentation tools; a successful response does not prove a design file is connected. Prefer current tool documentation over remembered or copied API examples.
2. Inspect the connected file and list its pages with their IDs. Resolve the supplied ID exactly, or match the complete page name. If no exact name matches, accept a unique case-insensitive match. For duplicates, missing pages, an unclear file, or an ambiguous boundary between page name and task, show the relevant names/IDs and ask which to use. Do not choose the first fuzzy match or create a missing page automatically.
3. Await `penpot.openPage(targetPage)` in a separate call from design writes. In the next call, verify the active file and page IDs and inspect the target page. Keep those IDs as the task scope; check them before every write batch.
4. If only a page was supplied, inspect it and summarize the available design system and readiness to work. Do not invent a design task. If a task was supplied or established in the conversation, proceed with it.

Pages belong to files. Resolve within the connected file, not every file in the project. If the requested page is in another file that the tools cannot access, explain which file needs to be connected.

## Design tokens and reuse come first

- **Always use design tokens where possible.** Inspect existing token sets, active themes, and bindings before styling. Choose tokens by semantic purpose, not merely by matching values. Bind tokens to supported shape properties; copying a resolved hex value or dimension is not a token binding.
- Preserve existing token bindings and theme behavior. Direct property writes can remove bindings. Do not change shared token definitions or activate another theme just to style a local element.
- **Always look for a suitable asset or component before building from scratch.** Search the local library, connected libraries, and relevant existing page content for components, variants, icons, images, colors, and typography styles. Prefer a component instance and its existing variants/overrides; preserve its link to the source.
- If no suitable token or asset exists, follow the file's established conventions and the user's brief. Add only the missing reusable definitions needed for the task, checking for duplicates first. Use direct values only when a binding is unsupported or no suitable token can reasonably be used, and briefly report the exception. Do not impose a new palette, spacing grid, or component system on an existing design.
- Treat edits to component sources and shared tokens as changes affecting their consumers. Keep page work scoped to instances unless the requested change calls for a shared update.

## Build and verify

Read [Building with Penpot](references/building.md) when creating or modifying designs; consult its prototyping section only when wiring interactions.

Inspect the relevant boards and describe the intended change briefly, then carry out authorized work. Build in small logical batches, returning changed IDs. Re-read after each batch, especially after a timeout or partial failure, before retrying; avoid duplicate shapes, assets, and interactions.

Use the file's naming and layout conventions. Prefer Flex/Grid for structured layouts, maintain component links, and preserve content outside the requested scope.

Verify structure, token bindings, component reuse, and text/layout bounds. Export the changed board or shape for visual inspection when supported. If export fails, report the visual verification gap and complete structural checks; do not claim a visual pass.

Finish with the page name/ID, what changed, what was reused, and any missing tokens/assets or verification gaps. Keep it brief.

Adapted from [Ahmed Rehan's penpot-mcp skill](https://github.com/ar27111994/penpot-mcp/tree/main/penpot-mcp); see [upstream license](LICENSE).
