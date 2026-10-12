# Phoenix PR #11012 browser evidence

Synthetic local QA data. Three touchpoints use the full seeded Phoenix app. The playground dialog uses a visibly labeled harness mounting the actual production component against the seeded backend; it is not end-to-end playground coverage.

See COVERAGE.md and screenshots/. Source fix: afcce817c45d3389e668546032bca81631e99871.

## Phoenix design-system revision

Source: 7e6f20db14a054ca64410228b00efdf4509c4766. See [design revision report](design-revision/REPORT.md) and [before/after screenshots](design-revision/). All validation uses Node 24.21.0.

## Correct baseline: without PR vs with PR

The authoritative comparison is now [no-pr-comparison/](no-pr-comparison/REPORT.md): pristine upstream main d0dc9e5ff0a33a1962da6aae83e6f56ac72c444f vs PR head 7e6f20db14a054ca64410228b00efdf4509c4766, matched fixtures/themes/1440x1000 viewport. The earlier design-revision/before images show an earlier PR iteration, NOT main without the PR, and are retained only as historical evidence. No source changes were made for this screenshot correction.
