# Three-surface PrettyText rollout — verified scope reduction

## Status

- Branch: `fix-10969`.
- Reviewed additive source commit: `0f690f0de444d9398a87abcb085515dc84484a6d`.
- Parent / reviewed restoration comparison: `69d3486d316027d1d5028ee54f2c62d44adcaee7`.
- Pristine upstream PR base: `d0dc9e5ff0a33a1962da6aae83e6f56ac72c444f`.
- Commit message: `fix(ui): remove playground dialog changes from PrettyText rollout`.
- Author/committer identity: `rajan-personal <39906424+rajan-personal@users.noreply.github.com>` (per-command config).
- **Source working tree clean. The coding agent made no push, PR/comment write, or merge.** After reviewing the exact restoration and passing validation, the supervising assistant pushed this additive commit to the existing PR branch without force or merge. No app-docs, backup, unrelated-control or historical-evidence changes. No browser or orphan harness launched; ended host browser task space 337 was not accessed.
- The supervising assistant approved this exact two-path restoration. This is not a claim of human-user artifact review.

## Exact two-path change

1. `js/app/src/pages/playground/PlaygroundExperimentRunDetailsDialog.tsx` — restored byte-for-byte from `d0dc9e5ff0a33a1962da6aae83e6f56ac72c444f`. The upstream component itself is retained; no other playground code changed.
2. `js/app/src/pages/playground/__tests__/PlaygroundExperimentRunDetailsDialog.test.tsx` — removed because that exact path does not exist at the upstream base.

The entire base-to-final **`js/app/src/pages/playground` diff is empty**, including the component and dedicated test. The additive commit is 2 paths, 98 insertions / 245 deletions. `restoration.patch` and `diff-stat.txt` contain its exact diff.

Component Git blob, baseline and restored: **`b9ed83d87a4c0c8d11119debf7314f8f1222d7d1`**.

Component SHA-256: **`9323de9794b7ffe6b783d81f41b954398f44b41c15a2ae73eff1e503bf4d21df`**.

All **14 remaining rollout source/test files** are byte-for-byte identical to `69d3486d316027d1d5028ee54f2c62d44adcaee7`. The compact PrettyText variant, bounded nested display-preview helper, annotation/tooltip, table, document implementations and their remaining regressions were not modified. `source-proof.json` contains per-path SHA-256 hashes and baseline/parent/final identity checks.

## Validation — Node v24.21.0

| Command/check | Result | Log |
|---|---|---|
| `make test-frontend` | **2,647 passed, 12 skipped / 2,659; 266 files passed, 0 failed** | `full-tests.log` |
| Focused remaining regressions, 7 colocated files | **88 passed / 88**, 0 failed | `focused-tests.log` |
| `make lint-frontend` | Passed, exit 0 | `lint.log` |
| `make typecheck-frontend` | Passed, exit 0 | `typecheck.log` |
| Project oxfmt `--check` on restored component | Passed, exit 0; no formatting write | `format.log` |
| `make build-frontend` | Passed, exit 0 | `build.log` |
| `git diff --check` | Passed | final scope verification |
| React Doctor `--verbose --scope changed --base 69d3486d316027d1d5028ee54f2c62d44adcaee7 --no-score --no-supply-chain --no-parallel` | **No issues found**, 1 extant changed file scanned, exit 0 | `react-doctor.log` |

The deleted file accounts for exactly **10 fewer tests and one fewer file** than the previous 2,657-passed full / 98-passed focused totals. No remaining test was edited, removed or skipped to obtain these results. The 12 existing skipped tests remain skipped.

React Doctor flagged no restored-code finding, so no legacy cleanup was necessary. Its score service was disabled; no numeric score is claimed. Regardless of design conventions in current guidance, this task intentionally restores the exact upstream dialog rather than redesigning its existing error/title/scroll behavior. Existing Vite module-directive/config-loader warnings and jsdom navigation notices are not source regressions and were left alone.

The Makefile was used for the full suite, lint, typecheck and build. Focused Vitest and check-only oxfmt used the app's existing pnpm scripts/tools. Root AGENTS and relevant Phoenix design references were reread. No new UI API or feature was introduced.

## Existing three-surface evidence reuse — no recapture claim

Remaining production touchpoints are **annotation explanations, span feedback table explanations, and document evaluation explanations only**. No current playground-dialog rollout or dedicated test/harness coverage is claimed.

Historical screenshots remain physically untouched under `/home/user/phoenix-artifacts/nested-json-preview/` and earlier evidence directories. Their actual AFTER capture source remains **`69d3486d316027d1d5028ee54f2c62d44adcaee7`**; they are **not** relabeled as newly captured at `0f690f0de444d9398a87abcb085515dc84484a6d`. Reuse should select only `annotation-*`, `table-*`, and `document-*` images in the existing light/dark before/after folders. Historical dialog images/reports remain archives, not current three-surface evidence. The current evidence folder selects only these three surfaces: 40 primary matched screenshots plus four supplemental annotation-scroll images. The active PR description removes the dialog section and its coverage claims; historical dialog evidence is not part of the current presentation.

Evidence supporting reuse:

- All 14 remaining displayed-source/helper/test hashes match the actual capture-source commit `69d3486d316027d1d5028ee54f2c62d44adcaee7`.
- The freshly rebuilt main-app manifest is **byte-identical to the pre-restoration manifest**.
- Fresh main-app entry: `assets/index-CKFKk2ab.js` (5,157,482 bytes).
- Fresh entry SHA-256: **`bba75bc5d8c7b57957a5b3e663ddaa2ef936dedc59b7bf554b54ffea8fed7cd1`**.
- That exact entry filename and SHA-256 match `nested-json-preview/served-provenance.json` from the `69d3486d316027d1d5028ee54f2c62d44adcaee7` captures. This verifies identical main-app entry bytes, not merely matching filenames; it does not invent historical per-chunk byte hashes that were not recorded.
- `asset-proof.json` records this comparison and explicitly sets `new_browser_capture_performed: false`.

The orphan dialog was not a routed app caller, and its removal from this PR did not alter the built main-app entry. No new screenshots or orphan harness runs were needed or performed. Existing bounded-tooltip behavior/limitations in reused annotation images remain unchanged; this task makes no new accessibility claim.

## Unchanged remaining PR paths

```text
js/app/src/components/annotation/AnnotationDetailsContent.tsx
js/app/src/components/annotation/AnnotationTooltip.tsx
js/app/src/components/annotation/__tests__/AnnotationDetailsContent.test.tsx
js/app/src/components/table/TextCell.tsx
js/app/src/components/table/__tests__/PrettyTextCell.test.tsx
js/app/src/components/utility/PrettyText.tsx
js/app/src/components/utility/__tests__/PrettyText.test.tsx
js/app/src/pages/trace/DocumentAnnotationItem.tsx
js/app/src/pages/trace/SpanAnnotationsTable.tsx
js/app/src/pages/trace/__tests__/DocumentAnnotationItem.test.tsx
js/app/src/pages/trace/__tests__/DocumentItem.test.tsx
js/app/src/pages/trace/__tests__/SpanAnnotationsTable.test.tsx
js/app/src/utils/__tests__/getJSONDisplayPreview.test.ts
js/app/src/utils/getJSONDisplayPreview.ts
```

## Artifact index and handoff

Directory: `/home/user/phoenix-artifacts/remove-playground-dialog/`.

- `REPORT.md` — this report.
- `source-proof.json` — exact upstream component identity, absent baseline test, final clean commit and unchanged remaining-file hashes.
- `asset-proof.json` — fresh asset/manifest comparisons with historical evidence.
- `restoration.patch`, `diff-stat.txt` — exact additive local diff.
- `final-status.txt` — empty porcelain status after the local commit.
- `node-version.log`, check logs — actual commands/results and exit codes.

No remaining validation failures or restoration blockers. The supervising assistant published the reviewed source; the coding agent performed no publication actions.
