# 3D plugin review remediation validation (2026-10-14)

## Code and deployment

- Production-based gateway patch: `codex/3d-mcp-review-annotations`, commit `7a247f2` (base main `260d683`). Pushed; not merged or deployed to production.
- Test integration: `codex/3d-mcp-review-test`, commit `b18a699`, based on existing test `4b47574`; preserves all prior test changes.
- Yunxiao pipeline 5030907 run 248: SUCCESS, source branch and commit verified as `codex/3d-mcp-review-test` / `b18a69922c5b692e8bf5be18b1ad58a2f1155cea`.
- Existing local Alibaba Cloud CLI credentials were sufficient. No new Yunxiao token required.

## Completed checks

- Main-based full regression: 887 tests, 0 failures, 0 errors, 0 skipped. Explicitly enabled tests with `-DskipTests=false -Dmaven.test.skip=false`; Docker API 1.44 compatibility setting used for local Testcontainers.
- Test integration regression: 903 tests, 3 failures, 1 error. All four occur in unchanged Canvas resource-list tests. A fresh archive of the unmodified test baseline reproduces the exact same four failures. The dedicated 3D tests pass. This is not recorded as an all-green test-branch suite.
- Local CLI doctor: version 0.1.6, renderer/assets/Playwright/Chromium available after installation.
- New isolated local project: `/private/tmp/topview-3d-review-scene`.
- Built a mannequin and box, applied built-in arms-down static pose, added front/low-angle/bird-eye cameras, validated the document and rendered three views (low-angle contact sheet includes frames 0 and 48).
- `inspect views`: `checksComplete=true`, no blocking checks. All three output images were viewed; both subjects are visible and separated. Frames 0 and 48 are intentionally static, not an animation claim.
- Studio acceptance initially found missing Draco public assets: `studio open` directly launches Next and skips its `predev` hook. Both installers now run the existing copy-draco script after building. The copied decoder returns HTTP 200, and the native in-app browser displays the mannequin, box and camera preview with no load error. macOS shell syntax checked; PowerShell change reviewed but not executed on Windows.
- Static compilation of `scripts/verify-mcp-3d-review.py` passes. The script has not yet been run against an authenticated endpoint.

## Still required before production / resubmission

- Dedicated account login and action-time OAuth approval for the new test verification client.
- Live 3D MCP tool list, exact booleans and denied-call checks across Legacy/Modern protocols and host headers.
- Real create Canvas → prepare upload → PUT local render → add image node → readback → link round trip.
- Production deployment and repeat acceptance; refresh Portal tool discovery and review cases; submit only after checks complete.

No review-account passwords or bearer tokens are stored in this document, the source tree or upload archive.
