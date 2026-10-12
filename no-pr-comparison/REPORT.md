# Corrected comparison: upstream main WITHOUT the PR vs published PR head

## Exact refs and scope

| | BEFORE — WITHOUT PR | AFTER — WITH PR |
|---|---|---|
| Source commit | `d0dc9e5ff0a33a1962da6aae83e6f56ac72c444f` | `7e6f20db14a054ca64410228b00efdf4509c4766` |
| Checkout | `/home/user/phoenix-without-pr` | `/home/user/phoenix` |
| Git state | Separate detached worktree at exact upstream base | Existing `fix-10969` checkout, unchanged |
| App port | 6008 | 6009 |
| Actual-component harness port | 5180 | 5181 |
| Final source status | Clean; unstaged and staged diffs empty | Clean; unstaged and staged diffs empty |

**OLD means pristine upstream main without any PR changes.** No PR patches were applied to the baseline. The previous `design-revision/before/` assets compare an earlier PR iteration and remain untouched as historical evidence; they are not included or relabeled as main in this comparison.

This was screenshot correction only. No product/source edits, source commits, reset/stash, push, comments, PR writes, new PRs, merge, public hosting, or app-docs work. Original backups and existing unrelated worktrees were preserved. Only ignored install/build outputs were created in the clean baseline and PR checkouts. All new automation, fixtures, harnesses and evidence are under this artifact directory.

## Fresh builds and source provenance

Both source refs were rebuilt now using **Node v24.21.0** from nvm:

```bash
source /home/user/.nvm/nvm.sh
nvm use 24.21.0
cd /home/user/phoenix-without-pr
make install-node
make build-frontend

cd /home/user/phoenix
make build-frontend
```

Both builds succeeded. Logs: `install-before.log`, `build-before.log`, `build-after.log`. Existing module-directive warnings were not changed. No full product test suite was rerun, as this task did not change source.

`source-proof.json` records both exact HEADs, clean source status, SHA-256 hashes of all rollout source modules, and manifest hashes. Each recorded source file was compared byte-for-byte with `git show <exact-ref>:<path>`. `before-status-final.txt` and `after-status-final.txt` are empty, and both staged/unstaged diff checks passed.

Each app is a separate fresh Python process, started **after its own build**, with its own source path and database copy. The existing Python interpreter/dependencies are reused, but `PYTHONPATH` explicitly selects the respective tree. Artifact-side `serve.py` asserts and logs the resolved `phoenix.__file__` before launching the real server:

- BEFORE: `/home/user/phoenix-without-pr/src/phoenix/__init__.py`
- AFTER: `/home/user/phoenix/src/phoenix/__init__.py`

The served HTML and entry JS were checked against each tree's new build manifest and exact bytes on disk:

| | Entry asset | SHA-256 of served entry JS |
|---|---|---|
| BEFORE | `/assets/index-xQYroxf4.js` | `0ca376787fd9c7c3c5a575a2d75b0fdb84a7387f452382485e9ea3500fb280b5` |
| AFTER | `/assets/index-CjASSP9I.js` | `f303334fbe3f7a4b73bc4e1684e2bb44f0a1eca417145576658d636a7d626782` |

Proof: `served-provenance.json`, `served-{before,after}.html`, `manifest-{before,after}.json`, and `server-{before,after}.log`. Capture records also identify the actual module entry loaded by each app page. Fresh browser contexts prevent cross-side browser storage/cache reuse. No baseline page was served from a cached PR build.

### Harness import proof

Both sides of the orphan playground dialog use an equally labeled **actual production-component harness**, not recreated UI or a fake application route. Each harness imports its respective production component, Drawer, GlobalStyles, ThemeProvider and Relay environment and queries its own local backend. The Vite module responses contain these exact imports:

- BEFORE: `/@fs/home/user/phoenix-without-pr/js/app/src/pages/playground/PlaygroundExperimentRunDetailsDialog.tsx`
- AFTER: `/@fs/home/user/phoenix/js/app/src/pages/playground/PlaygroundExperimentRunDetailsDialog.tsx`

`harness-{before,after}-served-main.js` and `harness-{before,after}-served-dialog.js` preserve the served modules; `served-provenance.json` hashes them. Browser module lists in `browser-{before,after}-{light,dark}.json` confirm that neither harness imports the opposite tree's application source. The baseline's original RunError/red Text is rendered unchanged; the PR's compact PrettyText/danger Alert is rendered unchanged. There is **no full routed playground E2E claim**.

## Identical frozen fixtures

A transactionally consistent SQLite backup of the existing synthetic database was taken using SQLite's backup API, without modifying the original database. The snapshot was duplicated byte-for-byte into `data-before/phoenix.db` and `data-after/phoenix.db` before either server started.

Frozen snapshot: `fixtures/frozen.db`

SHA-256: `00fd377b490d6b38416008fb71a7d01bd6a51b71c58b3cbc15dbd352a833294e`

The 13 relevant fixture tables were hashed in a canonical, ID-sorted representation: projects, traces, spans, span annotations, document annotations, datasets, dataset versions, dataset examples/revisions, experiments, experiment runs, and experiment-run annotations. Their counts and hashes remained **identical to the frozen snapshot in both runtime databases after capture**. See `fixtures/tables-frozen.json` and `fixtures/final-verification.json`. Fixture IDs and values are also recorded in `fixtures/seed-data.json` and the capture records.

No new LLM calls or evaluator runs were made. Capture only reads existing synthetic records. System/service housekeeping may write unrelated operational tables; fixture hashes, not a misleading post-start whole-database hash, establish data equality.

## Matched capture settings and contexts

- Viewport and PNG dimensions: **1440 × 1000** on both sides.
- Device scale factor **1**; visual viewport scale **1** (100% zoom).
- Locale **en-US**, timezone **UTC**, browser clock fixed to **2026-10-12T09:45:00.000Z**.
- Light/dark theme set identically in fresh browser contexts; fonts awaited before screenshots.
- Three touchpoints are the real seeded app: dataset example evaluation tooltip, span Info → Annotations → Table, and retriever document annotations.
- Same annotation example IDs, run IDs, tooltip triggers and dialog frames. Trigger rectangles and dialog frame rectangles compare exactly between each pair.
- Document cases use the same document-header scroll anchor. Subsequent content moves naturally as the PR changes text formatting; it is not repositioned or resized with injected CSS.
- **Main table comparison:** both sides use the actual header resize interaction from 400px to **320px**. Horizontal `scrollLeft` is **404px** on both, vertical table scroll is **0**, and table width/pinned-action geometry match. JSON is sorted by name ascending; plain and long cases by name descending, identically on both sides. No before-normal-width/after-resized comparison is presented.
- For the main JSON table pair, the cell's x/y origin is identical: **718.40625 / 416.703125**. Baseline raw JSON row height is **113.5625px**; PR formatted JSON row height is **277px**. That height change is genuine rendering behavior, not different viewport/column settings. Every JSON line is visible clear of the pinned action on both sides.

`capture-settings.json`, `captures.json`, and `matched-table-geometry.json` hold the exact measurements. The final independent validator passed all six light/dark JSON/plain/long table geometry pairs, identical annotation trigger positions, identical dialog frames, and PNG dimensions/hashes. The capture script initially serialized requested theme and observed body theme under the same metadata key; that artifact-only naming collision was corrected and the independent verifier reran successfully. No image pixels or measured bounds were edited.

## Coverage — 52 new screenshots

Each side has 26 images: all four touchpoints × light/dark × JSON/plain/long (24), plus collapsed-table light/dark (2).

| Touchpoint | Files under each `before/{theme}` and `after/{theme}` | Context |
|---|---|---|
| Annotation explanations | `annotation-json.png`, `annotation-plain.png`, `annotation-long.png` | Same actual dataset example/evaluation trigger |
| Span feedback | `table-json.png`, `table-plain.png`, `table-long.png`, `table-collapsed.png` | Same actual table, 320px explanation column, matched scrolling/sorting |
| Document evaluations | `document-json.png`, `document-plain.png`, `document-long.png` | Same retrieved document and annotation records |
| Playground run errors | `dialog-harness-json.png`, `dialog-harness-plain.png`, `dialog-harness-long.png` | Equally labeled respective-tree production harnesses |

`index.html` is the new paired comparison gallery. `screenshots-sha256.json` identifies every image and source ref. `capture-verification.log` records the final independent verification result. The long case is the existing long **plain-text** fixture; this is not a claim of exhaustive primitive/malformed/empty/nested-content coverage. The existing JSON fixture does include nested encoded values. Additional JSON-overflow document annotations can appear below the primary document fixture but are not a separate matched-case claim.

## Baseline behavior and honest limitations

- Main's annotation and document explanations are original plain Text, and its table explanations are original PreformattedTextCell. There is no PrettyText rollout patch on the BEFORE side. Unrelated pre-existing JSON views (input/reference/metadata) can still show their normal JSON editors; those are not the changed explanation/error touchpoints.
- Main's long unbroken raw document strings can exceed or be clipped by existing containers. This behavior is preserved in the screenshots, not masked with CSS or rewritten data. Large table rows naturally extend below the viewport; the long screenshots show the same top-of-table context, not a claim that the entire long value fits onscreen. The normal JSON pair is fully visible on both sides at the same geometry.
- The baseline annotation tooltip opens above its trigger; the PR uses side placement and may flip left. This position difference is part of the actual PR. The trigger itself is identical. Both retain the existing bounded explanation region for long tooltip content. No tooltip focus/navigation behavior was patched for screenshots, and keyboard-scroll accessibility was not retested or claimed fixed in this screenshot-only correction.
- The baseline orphan dialog emits its existing missing accessible-title warnings; the PR does not. Both apps emit the existing hydration-fallback warning. Final browser capture logs contain **zero pageerrors** on both sides. No product changes were made to suppress these warnings.
- No new product test claims are carried over from earlier reports. This correction verifies fresh builds, exact source/assets, identical data, matched browser geometry and capture integrity only.

## Review boundary

The supervising assistant visually reviewed the clean-main vs PR-head JSON pairs and checked the corrected baseline and matched table geometry. The human user has not yet reviewed these corrected captures. This report/index completes the screenshot correction. Both exact source trees remain clean and unchanged. No source commits or publication occurred. Work stopped for review.
