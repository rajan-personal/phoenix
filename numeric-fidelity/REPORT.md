# PrettyText numerical-display-fidelity fix

## Final state and scope

- **Reviewed source HEAD:** `7b53c86a2f2b80f124e49a433f3cb23a31d50c68`, branch `fix-10969`.
- Parent reviewed PR head: `0f690f0de444d9398a87abcb085515dc84484a6d`.
- Pristine comparison base: `d0dc9e5ff0a33a1962da6aae83e6f56ac72c444f`.
- Exactly one additive source commit: `fix(ui): retain original JSON when numeric previews are lossy`.
- Author and committer: `rajan-personal <39906424+rajan-personal@users.noreply.github.com>`.
- Supervisor approved the five source/test paths before this commit. No subsequent semantic/source edits. This is **supervising-assistant review**, not claimed human visual approval.
- Source checkout and all three other existing worktrees were clean at final capture verification. The coding agent made no push, publication, PR/comment edits, merge or evidence-worktree commit. The supervising assistant subsequently pushed the reviewed source to the existing PR branch without force or merge; current evidence is published separately. No app-docs, backup, historical-evidence or ended host-browser/taskspace337 changes.

## Design and correctness

Compact PrettyText now receives the **original children** before shared formatting. Its private compact child memoizes by those exact source bytes; the default/code child retains unchanged `usePrettyText` and JSONBlock behavior. Shared hook and broad jsonUtils bytes are unchanged.

After existing JSON validation, a small lexical numeric guard skips quoted keys/string contents and compares each raw numeric token's canonical decimal coefficient/exponent with the JavaScript display representation. It rejects nonfinite values, negative zero, unequal decimal values and uncertain exponent arithmetic. It does not implement a second JSON parser, convert numeric data to strings, use BigInt/exponent-sized allocations, regex unescaping, new JSON browser APIs or dependencies. Zero trimming and lexical scanning are linear in bounded input length. Equivalent forms such as `1.0`, `1e3`, `0.1` and safe exponent/decimal values continue formatting normally.

Both native JSON and recursively decoded JSON string candidates pass the guard. A numeric mismatch anywhere—including a later node after a safe decoded sibling—discards the entire preview and displays **the full original source**, with no decoded label. Thus raw `9007199254740993`, `1e400`, `1e-400`, long decimal precision and `-0` are not rounded/replaced. Known-valid fallback retains JSON mono classification. Input too large to validate remains verbatim with plain classification rather than guessing validity.

Bounds remain depth 20 (containers + encoding layers), 2,000 visited values, 100 total parse attempts, 100,000 cumulative parsed characters and 100,000 formatted-output characters. The root input is bounded before parsing; root nesting is checked before stringify. Any budget failure is atomic, without truncating the original. Output is checked after bounded serialization; this is a size gate, not a streaming serializer. String/prose/scalar identity, malformed fallback, prototype-safe cloning, inert text rendering, inherited colors and original clipboard/data semantics are preserved.

Phoenix design mapping: existing native Text/body vs mono typography; readable noninteractive XS decoded-preview label only on actual expansion; inherited semantic color; unchanged pre-wrap/overflow-wrap; no editor chrome, interactive tooltip controls, new scroll regions or redesigned surfaces. No playground changes: base-to-final `js/app/src/pages/playground` diff is empty.

## Exact additive changed paths

```text
js/app/src/components/utility/PrettyText.tsx
js/app/src/components/utility/__tests__/PrettyText.test.tsx
js/app/src/utils/getJSONDisplayPreview.ts
js/app/src/utils/__tests__/getJSONDisplayPreview.test.ts
js/app/src/utils/hasLosslessJSONNumbers.ts
```

Five files, 360 insertions / 44 deletions. `revision.patch`, `diff-stat.txt` and `source-proof.json` record the exact diff/hashes. Other rollout/caller/copy/test paths retain parent bytes. The default formatter deliberately retains its pre-existing numeric policy, with a compatibility test.

## Validation — Node v24.21.0

All source checks below cover the same bytes committed above; both final source refs were freshly rebuilt after the commit.

| Check | Exact result | Evidence |
|---|---|---|
| Helper + PrettyText regressions | **93 passed**, 2 files | `numeric-tests.log` |
| Remaining 7-file focused suite | **130 passed**, 7 files | `focused-tests.log` |
| `make test-frontend` | **2,689 passed, 12 skipped**, 266 files passed, 0 failed (2,701 tests total) | `full-tests.log` |
| `make lint-frontend` | exit 0 | `lint-final.log` |
| `make typecheck-frontend` | exit 0 | `typecheck-final.log` |
| Check-only `pnpm exec oxfmt --check` on the five paths | all five passed, exit 0 | `format.log` |
| `make build-frontend` | passed before commit and again at final committed HEAD; clean main also freshly rebuilt, exit 0 | `build.log`, `build-after-committed.log`, `build-before.log` |
| `git diff --check` parent → final commit | exit 0 | `final-diff-check.log` |
| React Doctor changed scope vs `0f690f0de...` | **0 issues**, 5 files scanned, exit 0 | `react-doctor-current.log` |
| React Doctor changed scope vs pristine `d0dc9e5ff...` | **0 issues**, 15 files scanned, exit 0 | `react-doctor-main.log` |

React Doctor command: `npx -y react-doctor@latest --verbose --scope changed --base <ref> --include-untracked --no-score --no-supply-chain --no-parallel --output-dir <artifact-directory>`. Current CLI uses `--scope changed` / `--base` rather than legacy `--diff`. Both comparisons include the then-untracked guard. Score/API and supply-chain services were explicitly disabled; **no numeric score is claimed**. No findings were suppressed or unrelated cleanup performed.

Literal regression expectations cover unsafe integers, overflow, underflow, long decimals, negative zero, native roots/objects, encoded/multiply encoded arrays, mixed safe/unsafe atomic fallback, equivalent safe values/subnormals/exponents, quoted numeric strings/keys, Unicode/escaping/prototypes, malformed numeric grammar, long-number and work budgets, raw-input memo invalidation, unchanged default callers, and existing HTML/string/copy regressions.

Supervisor independent property check (not this agent's own oracle): **5,032 cases passed**, exact helper/guard under Node24 against Python Decimal, seed11012, precision1–100, exponents ±1000. Breakdown: 2,516 faithful native outputs, 283 faithful encoded decodes, 2,233 atomic encoded fallbacks. See `supervisor-numeric-property-summary.json`; input/output datasets and script are preserved separately, not reproduced in this report.

Known non-failing output: existing Vite configLoader `__dirname`, large chunks and `use no memo` bundler warnings; one jsdom navigation notice in full tests. No backend/full unrelated E2E suite claimed. No new source validation failures remain.

## Fresh committed-source screenshots and proof

Gallery: **`index.html`**. Exactly **40 primary screenshots + 4 separate pointer-wheel supplements**, all newly captured. No dialog/playground/harness captures or imports.

| Side | Source | Tree | App port |
|---|---|---|---|
| BEFORE — WITHOUT PR | `d0dc9e5ff0a33a1962da6aae83e6f56ac72c444f` | `/home/user/phoenix-nested-json-base` | 6030 |
| AFTER — WITH PR | `7b53c86a2f2b80f124e49a433f3cb23a31d50c68` | `/home/user/phoenix` | 6031 |

Each side/theme contains actual seeded-app `annotation-{json,plain,malformed}.png`, `table-{json,plain,malformed}.png`, `document-{json,plain,malformed}.png`, and `table-collapsed.png`. `annotation-json-bottom.png` is explicitly **supplemental**, not a primary initial-state pair. Three production contexts: dataset-example evaluation tooltip, span Info → Annotations → Table, retriever document evaluations.

Both clean refs were rebuilt with `make build-frontend`. Artifact-only `serve.py` asserts the respective Phoenix Python source import; independent fresh processes serve their own build and SQLite copy. `verify-provenance.py` verifies manifest entries and every manifest asset's HTTP bytes against disk. `verify-captures.py` additionally verifies all browser-loaded app JS asset hashes, screenshot SHA/dimensions, source refs and clean worktrees. No source/harness/CSS injection or cached old PR asset reuse. Headless remote Playwright only; no host browser reclamation. Historical screenshots remain untouched and are not represented as current assets.

Frozen fixture SHA-256: **`d7eb4d3c7b75d7fc835041bcf0833586d4ba07ca0b1d352f942def7cc374343a`**. The existing frozen fixture and seed description were copied artifact-side; baseline/after DB files started byte-identical. All **13 relevant fixture tables** still match the frozen data after capture. No external LLM calls. See `fixtures/initial-equality.json`, `fixtures/equality.json`. Fixture contains multi-key encoded objects, nested encoded arrays/multiple layers, quotes/backslashes/newline, string score, and plain/malformed controls. No separate unsafe-number screenshot was added; literal rendering is covered by source tests and independent numeric oracle.

### Matched geometry and real controls

- 1440×1000, DPR 1, 100% zoom, en-US, UTC, fixed browser clock `2026-10-12T09:45:00.000Z`, both themes, fonts awaited.
- Same annotation example/trigger rectangles, document scroll anchors and focused elements in corresponding primary screenshots.
- Actual pointer-drag table resize from 400 to **320px**, horizontal scroll **404px**, vertical scroll 0, matching table width/pinned actions and name sort/row mode on both refs.
- JSON line bounds were measured: complete nested JSON is visible inside the explanation cell, clear of pinned actions and within the viewport. No textContent-only completeness claim.
- Actual collapse then re-expand verifies full original collapsed source, absent label when collapsed, unchanged sorted state/320px width/404px scroll, and complete restored decoded output.
- Document pages assert no horizontal page overflow. JSON/plain/malformed values and conditional labels match exact expected content; no compact editor chrome.
- Existing real document card copy action was clicked and the actual clipboard read matched the original document bytes. No tooltip copy control was added; original source data and annotation values remain identical in both DBs. Existing production-copy regression tests also passed.
- Capture pageerrors: **0** on each side/theme. Both sides retain the existing HydrateFallback console warning.

### Supplemental tooltip and limitations

Pointer wheel input was applied on both sides/themes. AFTER's existing 300px-high explanation region scrolls from 0 to **80px** (380px content height), exposing the closing nested structures. BEFORE's raw source has no vertical overflow in that region (80px content/height, scrollTop 0); its pre-existing horizontal overflow/placement is not hidden. Supplements are not asserted to have identical scroll offsets.

The existing RichTooltip keyboard-scroll limitation is **not fixed or claimed fixed**. No new focusable tooltip region/navigation is introduced. Primary expanded tooltip can require its existing scroll region; the supplement demonstrates pointer access only. Natural row/card heights and legitimate tooltip placement differ between main and PR. This is targeted seeded-app browser QA, not comprehensive routed E2E coverage; no orphan dialog claim remains.

`final-verification.log` passed: 40 primary +4 supplemental PNGs, matched theme/focus/anchors/table geometry, all loaded asset bytes correct, 13 tables equal, all four worktrees clean, playground diff empty. Artifact validator initialization ran before supplements finished and initially compared the full body class string instead of the theme class token; these automation-only checks were corrected/rerun successfully, with no source or screenshot alteration.

## Handoff

The supervising assistant reviewed the source/tests, independent numeric property check, capture provenance and representative screenshots, then published the source and current evidence separately. The coding agent performed no publication actions. Historical evidence and unrelated worktrees were preserved. No human visual approval is asserted.
