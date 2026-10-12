# PrettyText design revision — local review only

## Scope and design-rule mapping

Revision base: published PR #11012 head `7e1a11ea159aff31b1a58d778129ca4bc66b34fb`. No rebase or unrelated cleanup. The pre-edit audit and authoritative references are in `AUDIT.md`.

- **Typography / tokens / reuse:** `PrettyText variant="compact"` uses the existing Text primitive, default body typography for prose and malformed JSON, and mono typography only for JSON. It inherits its surface's semantic text color, without editor chrome, gutters, syntax highlighting, added background/border/padding, or an inner scrollport. Existing PrettyText callers remain on their original default code rendering. No JSONText global changes: its empty-object/single-key/primitive transformations would lose required information here. The shared usePrettyText hook remains the only parser.
- **Tooltip/table layout:** AnnotationTooltip uses side placement (`end`, with normal collision flipping). Explanation content remains static tooltip content, with no added tab stops or navigation. Expanded PrettyTextCell uses compact text; existing collapsed preview, row toggle, resizing, sorting and pinned delete controls are preserved. No mobile table redesign.
- **Document annotation:** same compact presentation, no new nested card or scrollbox. Existing document card color, annotation metadata/actions, and header copy controls remain untouched.
- **Errors:** real `Alert variant="danger" banner`, titled “Experiment run failed”, immediately after the fixed DialogHeader and before input/reference/output content. It occupies the first position in the single scrollable dialog body. Plain and JSON errors inherit the Alert's danger text treatment. A semantic alert wrapper retains a persistent accessible error, not a toast or hand-styled icon.
- **Dialog:** standard DialogTitle + IDBadge supplies a real accessible name. Explicit close label. The read-only detail body has one keyboard-scrollable region instead of percentage-height panel scrollports and nested IO max-height scrollports; outer Drawer resizing is unchanged. Normal output still follows the original falsey-error branch. Input/reference/output use existing Cards; each copy button is in the header extra slot and last there. Error copying occurs once in fixed DialogTitleExtra so long error text cannot push it below the fold. No controls inserted into a tooltip or hover-only surface.
- **BEM:** `pretty-text`, `pretty-text--compact`, `playground-run-details__body`; no new global tokens, fonts, colors, dependencies, or theme changes.

## Keyboard finding — known limitation, not concealed

The attempted `tabIndex=0` explanation region inside RichTooltip **did not permit keyboard scrolling**: Tab from its trigger dismissed the tooltip before scrolling was possible. See `tooltip-keyboard-initial.json`. That addition was removed. The original bounded View was restored; a unit regression ensures AnnotationDetailsContent has no explicit tabindex/contenteditable controls. `tooltip-keyboard-final.json` records zero explicit focusable descendants and the normal dismissal on Tab, with no new focus trap.

Long RichTooltip content remains not independently keyboard-scrollable after leaving its trigger. This is a limitation of the existing tooltip interaction model, not claimed as fixed. Full content remains in the DOM; pointer scrolling and separately expanded table/document detail views retain the data. No Popover/navigation model was invented. This limitation is explicitly left for the user's review rather than expanding this revision.

## Browser evidence

`index.html` provides light/dark, before/after pairs. PNGs are under `before/{light,dark}` and `after/{light,dark}`. Four touchpoints each have representative JSON, natural prose and long-content screenshots, plus collapsed-table evidence.

Three surfaces use the actual seeded full Phoenix app: annotation details in dataset example evaluation tooltips, span Info → Annotations → Table, and retriever document annotations. The **orphan playground dialog uses a clearly labeled production-component harness**, importing the actual Dialog/Drawer/styles and production Relay environment and fetching the existing synthetic run records from the local backend. It is not claimed as routed playground E2E coverage.

### Specific additional proof

- `after/light/table-resized-complete.png`, `after/dark/table-resized-complete.png`: actual explanation-header drag from 400px to approximately 269px, horizontal viewport aligned so the entire JSON cell is visible clear of the pinned action. `browser-validation.json` records per-line DOM Range rectangles, cell bounds, viewport and pinned-action bounds. Assertions check every rendered line is within the cell and not occluded by the pinned action, the complete JSON block is in the viewport, no horizontal/vertical content clipping, and the formatted value is unchanged. Actual resize, expanded/collapsed toggle and keyboard sorting are exercised; deletion controls remain present (no destructive deletion performed).
- Document JSON, malformed JSON and prose retain their values and line breaks; long unbroken plain/JSON content wraps inside its own natural height. Measured explanation elements have no inner scrollport or clipped content.
- Errors: real clipboard comparisons verify original error copy and normal-output copy; computed plain-error color equals the actual Alert color in both themes. JSON errors have no CodeMirror descendants. Long JSON is keyboard-scrollable with End within the sole dialog body; there are no active nested vertical scrollports. See `after/*/dialog-harness-long-json*.png`.
- No browser pageerrors in the final validation. An earlier QA init script attempted localStorage on an opaque clipboard-helper document; restricting that test-only initialization to http documents resolved the test-harness error. An initial rebuilt app used a cached server asset manifest; restarting the local backend after the build resolved stale module paths.

Scripts: `capture.mjs`, `validate.mjs`, `tooltip-keyboard-final.mjs`. Measurements: `browser-validation.json`, `tooltip-keyboard-initial.json`, `tooltip-keyboard-final.json`. The pre-change source, live screenshots, and design choices are recorded in `AUDIT.md` before edits.

## Checks

All final checks use **Node v24.21.0**, installed through nvm (`node-version.txt`).

- `make lint-frontend`: passed (`lint.log`).
- `make typecheck-frontend`: passed (`typecheck.log`).
- oxfmt check of all 12 intended paths: passed (`format.log`).
- Focused six-file Vitest suite: **59 passed** (`focused.log`).
- Full frontend suite: **2,618 passed, 12 skipped, 265 files passed**, no failures (`full-tests-final.log`).
- `make build-frontend`: passed (`build.log`).
- React Doctor baseline: 803 pre-existing diagnostics; final `--diff --base 7e1a11ea159aff31b1a58d778129ca4bc66b34fb --include-untracked`: **0 introduced diagnostics** (`react-doctor-baseline.log`, `react-doctor-diff-final.log`). Scoring/supply-chain services deliberately disabled; no new numeric score is claimed.
- `git diff --check`: passed.

Existing Vite config-loader/module-directive warnings and jsdom navigation-not-implemented notices remain, without unrelated cleanup. Python tests and the entire unrelated Playwright suite were not run; backend source was not modified. New regression tests cover lossless primitive/array/empty JSON and nested encoded strings, inert HTML-like text, default presentation compatibility, body vs mono typography, absence of editor controls, table state, named dialog / Alert order / error lifecycle and exact copy values.

## Publication and review boundary

No push, force-push, comment, PR creation, merge or public hosting. No app-docs work. Evidence is outside source. Original user author identity is used for the local revision commit (not Codex). Awaiting visual approval; existing PR #11012 has not been updated by this revision.

## Final Git state and exact diff

- Branch: `fix-10969`; working tree clean.
- Local HEAD: `7e6f20db14a054ca64410228b00efdf4509c4766`.
- Revision base / last published head: `7e1a11ea159aff31b1a58d778129ca4bc66b34fb`.
- Backup of that head: `backup/fix-10969-design-before`. Original `backup/fix-10969-original` remains intact.
- Author: `rajan-personal <39906424+rajan-personal@users.noreply.github.com>`, verified against the original backup. Per-command author configuration only; no Codex author.
- One new local commit, **12 files, 320 insertions / 132 deletions**. Exact patch: `revision.patch`; summary: `diff-stat.txt`. No evidence files are committed.

```text
js/app/src/components/annotation/AnnotationDetailsContent.tsx
js/app/src/components/annotation/AnnotationTooltip.tsx
js/app/src/components/annotation/__tests__/AnnotationDetailsContent.test.tsx
js/app/src/components/table/TextCell.tsx
js/app/src/components/table/__tests__/PrettyTextCell.test.tsx
js/app/src/components/utility/PrettyText.tsx
js/app/src/components/utility/__tests__/PrettyText.test.tsx
js/app/src/pages/playground/PlaygroundExperimentRunDetailsDialog.tsx
js/app/src/pages/playground/__tests__/PlaygroundExperimentRunDetailsDialog.test.tsx
js/app/src/pages/trace/DocumentAnnotationItem.tsx
js/app/src/pages/trace/__tests__/DocumentAnnotationItem.test.tsx
js/app/src/pages/trace/__tests__/SpanAnnotationsTable.test.tsx
```

## Final table and tooltip QA outcome

Both themes: actual resized cell width **269px**, compact text width/scrollWidth **237/237px**, text height/scrollHeight **300/300px**. All **27 DOM Range rectangles** (including whitespace/wrapped-line runs) are inside the visible cell and clear of the pinned action. The full JSON cell spans viewport y=416.7–733.7 at 1440×1000, so the complete value and pinned delete control are visible at once. Exact JSON equality is also asserted. Final table screenshots: `after/{light,dark}/table-resized-complete.png`.

The final tooltip has **zero explicit focusable descendants**. Tab dismisses it on trigger blur, consistent with its existing semantics; PageDown after dismissal does not scroll the removed content. This is the documented known limitation, not a passing keyboard-scroll claim. No implementation/test failures remain, but this unchanged accessibility limitation still requires reviewer awareness.

There are **26 before + 32 after = 58 final comparison/proof PNGs** in the before/after folders; the separate initial/final keyboard investigation PNGs remain alongside their JSON logs for transparency. The gallery links full-resolution files. All final screenshot captures postdate the last production-source edit.
