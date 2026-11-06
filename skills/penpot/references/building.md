# Building with Penpot

Use the connected MCP overview and API documentation as the authority for signatures and supported properties. These patterns supplement them; they are not a frozen API catalog.

## Inspect only what the task needs

- List pages using `penpotUtils.getPages()` and resolve the chosen page by ID with `penpotUtils.getPageById(id)`. Listing first detects duplicate page names that a first-match helper would hide.
- Inspect a page with `penpotUtils.shapeStructure(page.root, 3)`, then drill into relevant boards. Scope `penpotUtils.findShapes(predicate, root)` to that page or board rather than searching globally by name.
- Inspect `penpot.library.local` and `penpot.library.connected` for components, colors, and typographies. Include library ID, asset ID, path, and name in candidate summaries so similarly named assets remain distinguishable.
- Inspect `penpot.library.local.tokens`, its sets/themes, and `penpotUtils.tokenOverview()`. Follow up on the tokens relevant to the work rather than dumping the entire file.
- Use `storage` for the target file/page IDs, selected shape IDs, and progress across calls. Re-resolve IDs and confirm scope before writing. If storage is lost, rediscover context; do not treat missing progress as completed work.

## Apply real token bindings

Read `TokenCatalog`, `TokenSet`, `TokenTheme`, `TokenProperty`, and the relevant token type when needed. Check active sets, precedence, and aliases before choosing a token. `penpotUtils.findTokensByName(name)` exposes same-name definitions across sets; `findTokenByName(name)` finds the applicable token.

After resolving the intended shape and semantic token, the binding pattern is:

```javascript
// shape and token are already resolved for this batch.
shape.applyToken(token, ["fill"]);
await penpot.waitForLayoutUpdate();
return { id: shape.id, bindings: shape.tokens, fills: shape.fills };
```

Use the explicit `TokenProperty` for the intended effect, such as `fill`, `strokeColor`, `rowGap`, `paddingLeft`, or `fontSize`; check compatibility with the token type. Avoid applying to every property implicitly.

Verify both the binding in `shape.tokens` and the resulting value after updates settle. Setting `shape.fills` or another bound property directly can remove its binding, so apply necessary structural/default styling before token bindings and check the result again after later edits.

When creating a needed token:

- Check the intended set for its name, type, and value before creating it. A same-name definition with a conflicting type/value needs resolution, not a duplicate or an automatic overwrite.
- Follow the existing naming and alias structure. Reference tokens using the documented alias syntax, such as `{color.brand.primary}`, and verify resolution.
- Preserve active sets/themes unless their modification is part of the task. Activation can affect the entire file.
- Library color/typography styles are reusable assets but are not proof of a design-token binding. Use existing styles where appropriate and distinguish them when reporting token coverage.

## Reuse components and assets

Search by function as well as label: an existing field, card, navigation item, or icon may already solve the requirement under a different name. Inspect candidate structure and variants before choosing.

- Create an instance using `component.instance()`. Use a matching variant or documented instance overrides for content and state changes.
- Inspect `variantProps` to select a variant; use `instance.switchVariant(pos, value)` only after discovering the variant property's position and values.
- Preserve component links. Do not detach merely to bypass an inconvenient edit; detach only when the task requires an independent design.
- Reuse image fills and existing icon assets where suitable. Check the tools actually exposed before planning image import; do not assume a remote server can read local files.
- When a new reusable component is needed, inspect `Library.createComponent` and create it from the intended source shapes. Account for their existing placement/parentage; do not move unrelated content to manufacture a source.
- For new variant groups, prefer the documented `penpotUtils.createVariantContainer` helper when available instead of guessing the low-level creation order.

## Layout, geometry, and text

- `width` and `height` are read-only; use `shape.resize(width, height)`.
- `x` and `y` are absolute page coordinates. `parentX` and `parentY` are computed/read-only; use `penpotUtils.setParentXY(shape, x, y)` for parent-relative placement. Check layout constraints before setting positions.
- Reparent with `appendChild` or `insertChild`, not `remove()`. Reparenting preserves absolute position; set the intended relative position afterward when needed.
- Inspect existing `board.flex` or `board.grid`. Layout-controlled children should be positioned through layout settings, gaps, padding, and child sizing instead of manual coordinates.
- Use `penpotUtils.addFlexLayout(container, dir)` when converting a container with existing children; it preserves their visual order. For new containers, use the documented layout methods. Do not assume every column layout reverses child order.
- Use documented Flex/Grid properties such as `rowGap`, `columnGap`, and side padding. Inspect `LayoutChildProperties` for child sizing and margins; their names differ from token property names.
- Use `auto`, `fill`, or `fix` sizing deliberately. Avoid invisible spacer rectangles when layout gaps/padding express the design.
- `penpot.createText()` can return null; check it before styling. `resize()` changes the text box, not font size, and resets `growType` to `fixed`. Restore `auto-width` or `auto-height` when that is the intended behavior.
- Use existing typography styles and confirm fonts/variants through `penpot.fonts`. Do not guess an installed weight or confuse library typography property names with text-shape properties.
- Await `penpot.waitForLayoutUpdate()` before reading geometry affected by a change in the same call. Check rendered `textBounds`, clipping, and wrapping rather than only formal width/height.
- Replace fill/stroke arrays as documented rather than mutating individual read-only entries. Check current `Fill`, `Stroke`, `Shadow`, and `Blur` definitions when adding effects; avoid copying version-specific effect recipes blindly.

## Batches, retries, and verification

Use one logical change per call, sized to the work and tool limits. A loop over an entire file is still one large call, even if its loop variable is named `batch`.

At the start of each mutation, confirm the active file/page IDs match the recorded target. Return changed shape/asset IDs and re-read those objects in a subsequent call. After a partial failure, inspect what persisted and finish only the remaining work. Search before creating; do not clear and rebuild a page as a retry strategy.

Check the changed subtree for:

- Correct parentage, intended layer order, and retained component links.
- Expected token bindings and resolved values, with no unintended shared definition changes.
- Text wrapping, clipping, overlap, and containment. Decide whether content or container sizing is wrong before adjusting either.
- Consistency with nearby content, including readable text, contrast, and the requested interaction states.

Export the affected board/shape with `export_shape` and inspect the image when possible. Structural checks and visual inspection answer different questions; neither alone proves the other passed.

## Prototyping, when requested

Inspect existing flows and interactions, including those on nested controls. Read `Page.createFlow`, `ShapeBase.addInteraction`, and the required action/animation types before wiring.

Resolve trigger shapes and destination boards by ID within the intended scope. Add navigation to the actual control unless the whole board is meant to be clickable. Reuse existing equivalent interactions instead of duplicating them on retries, and preserve unrelated interactions.

Use the file's existing motion conventions. Do not impose fixed durations or rename boards to a new flow naming scheme. For overlays, verify opening, closing, and outside-click behavior required by the brief.

Verify destination IDs for navigation/overlay actions. Actions such as close-overlay or previous-screen do not require an explicit destination. Inspect nested controls as well as boards; a board with no direct interaction is not necessarily a dead end. Report which paths were structurally checked and whether playback was actually tested.

## Source and adaptation

Reviewed the upstream [SKILL.md](https://github.com/ar27111994/penpot-mcp/blob/main/penpot-mcp/SKILL.md) and all four [references](https://github.com/ar27111994/penpot-mcp/tree/main/penpot-mcp/references) on 2026-11-06:

- `penpot-api-patterns.md`: retained discovery, page switching, token application, reuse, geometry/text, session state, and retry patterns; corrected conflicting API claims against connected MCP documentation.
- `design-system-workflows.md`: retained inventory, semantic token mapping, and reuse-before-creation; omitted prescribed palettes/scales, full-system audits, and per-batch approval gates.
- `prototyping-workflows.md`: retained scoped triggers/destinations and interaction verification; omitted forced naming, bulk resets, and arbitrary coverage scores.
- `design-to-code-workflows.md`: reviewed but not included; application code generation is outside this skill's page-design workflow.

Setup, credential handling, model selection, generic prompt templates, and platform-specific starter layouts were also omitted. Adapted material is covered by the [upstream MIT license](../LICENSE).
