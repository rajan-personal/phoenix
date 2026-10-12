# PrettyText rollout — screenshot and validation coverage

All screenshots are real Chromium renders, not generated images or recreated component mockups.

## Seeded full Phoenix app

Backend: the checked-out Phoenix source, SQLite in `data/`, production frontend built using `make build-frontend`. Seed scripts call local REST APIs to create real spans, span/document annotations, a dataset, experiments, runs, and evaluations. No LLM credentials or external model calls.

| Rollout touchpoint | JSON | Plain text | Additional evidence |
|---|---|---|---|
| AnnotationDetailsContent, reached through dataset example → Experiment Runs → keyboard-focused evaluation tooltip | [JSON](screenshots/annotation-json.png) | [Plain](screenshots/annotation-plain-text.png) | [Malformed](screenshots/annotation-malformed-json.png), [empty](screenshots/annotation-empty.png), [overflow](screenshots/annotation-overflow.png), [scroll to bottom](screenshots/annotation-overflow-bottom.png) |
| Span feedback, now SpanAnnotationsTable → PrettyTextCell; actual span Info → Annotations → Table | [Expanded](screenshots/table-expanded.png), [resized JSON](screenshots/table-resized-json.png) | [Plain row](screenshots/table-plain-text.png) | [Collapsed](screenshots/table-collapsed.png), [re-collapsed after resizing](screenshots/table-recollapsed-after-resize.png); malformed and empty cells included in these views |
| Document evaluation explanation, now DocumentAnnotationItem; actual retriever span Info → Output → Documents | [JSON](screenshots/document-json.png) | [Plain](screenshots/document-plain-text.png) | [Malformed](screenshots/document-malformed-json.png), [empty](screenshots/document-empty.png), [long plain text](screenshots/document-overflow.png), [long JSON](screenshots/document-long-json.png), [JSON bottom](screenshots/document-long-json-bottom.png) |

### Actual browser assertions

- **Table:** collapsed rows do not mount JSON editors. Expand wraps JSON, including arrays/nested JSON/stringified nested values. Dragged the actual explanation-column resize handle from 400px to approximately 239px. Editor width 207px; content width and scrollWidth both 172px; scroller clientHeight and scrollHeight both 361px: no horizontal overflow or vertical clipping of the pretty view. The collapse control remains reachable/clickable after scrolling to the long rows. Collapsed row heights return exactly to `[47, 47, 47, 47, 46]` px after the resize/expand/collapse sequence.
- **Documents:** all measured plain/JSON blocks stay within their containers. Long unbroken JSON (150 repeated tokens and 25 nested objects) has no horizontal overflow in any ancestor, including the page. Its final `END_OF_JSON` line is reachable by scrolling. Long plain text wraps and retains its line breaks. Empty explanation omits the explanation section.
- **Annotation details:** object JSON highlights/wraps, malformed JSON stays literal text, empty explanation omits the section; tooltip geometry shows no horizontal overflow. Long explanations retain the existing 300px scroll region. Keyboard focus was used to reveal real tooltips; hover alone did not reliably open them in automation.

Machine-readable measurements: `seeded-browser-validation.json`, `document-long-json-validation.json`, `annotation-browser-validation.json`, `annotation-overflow-validation.json`. Reproducible scripts: `seeded-browser-qa.mjs`, `long-json-browser-qa.mjs`, `annotation-browser-qa.mjs`, `annotation-overflow-browser-qa.mjs`.

## Production-component harness: playground experiment run dialog

**Not full end-to-end playground coverage.** Current upstream has no production caller/import of `PlaygroundExperimentRunDetailsDialog` (verified by repository search). Rather than invent a route or broaden the PR, `harness/` imports the actual production component, actual Drawer/ThemeProvider/GlobalStyles, and actual Relay environment. It fetches seeded run IDs through the local Phoenix GraphQL server. There is no component recreation or mocked response in the browser harness. Each screenshot visibly labels the harness and its coverage limitation.

- [JSON API error](screenshots/dialog-harness-json.png)
- [Plain error, danger styling](screenshots/dialog-harness-plain-text.png)
- [Malformed JSON error](screenshots/dialog-harness-malformed-json.png)
- [Empty error: normal output path](screenshots/dialog-harness-empty.png)
- [Long plain error](screenshots/dialog-harness-overflow.png)
- [Long JSON at 900px viewport](screenshots/dialog-harness-long-json-narrow.png)
- [Long JSON scroll to final error line](screenshots/dialog-harness-long-json-bottom.png)

Assertions: plain-error computed color equals `--global-color-danger`, both `rgb(249, 99, 76)`; original alert icon remains; input/reference-output panels and latency/annotation aside remain. No horizontal page overflow at 1400px or in the long-JSON error's ancestors at 900px. `END_OF_ERROR` is reachable by scrolling the dialog's output region. Empty error follows the existing normal-output branch. No browser pageerrors during the main seeded table/document or dialog checks.

Measurements: `dialog-browser-validation.json`, `dialog-long-json-validation.json`. Scripts: `dialog-browser-qa.mjs`, `dialog-long-browser-qa.mjs`; harness server command/config in `harness/vite.config.mts`.

### Known upstream limitation

The orphan dialog emits React Aria's existing missing accessible-title warning in the standalone harness (its title uses TitleWithID, not a DialogTitle/Heading slot). This was not introduced by the rendering change and was not expanded into an unrelated title/route refactor. Automated browser failures during setup (strict selectors, tooltip timing, and CodeMirror viewport virtualization) were corrected in QA scripts; they were not product-code fixes. Initial harness sizing did not provide a Drawer; it was corrected to the actual production Drawer before retaining final screenshots.

## Reproduction

The persistent local backend runs on port 6006; harness Vite runs on 5174. Both were detached using `setsid`. No public preview was published. Use a private Boat port forward if reviewing remotely. `seed.py` documents the initial data creation; the dataset-upload endpoint returns null, so the completed run uses `seed-experiments.py` to retrieve the dataset ID and seed runs. `seed-evals.py` adds evaluator explanations. `long-json-browser-qa.mjs` and `seed-long-error.py` add long-JSON cases. This is synthetic QA data only.

`index.html` is a local screenshot gallery. All evidence stays outside committed source.
