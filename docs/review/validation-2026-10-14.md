# 3D plugin review remediation validation (2026-10-14)

## Code and deployment

- Production-based gateway patch: `codex/3d-mcp-review-annotations`, commit `7a247f2` (base main `260d683`). Fast-forwarded to production main after live test acceptance. Production pipeline 5031277 run 80 completed SUCCESS; source main and exact commit 7a247f22a39437d1f260bdb46fb8af847643c4fc verified. Public OAuth metadata recovered to HTTP 200 after the deployment restart.
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
- Static compilation of `scripts/verify-mcp-3d-review.py` passes. Authenticated test endpoint verification passed all eight protocol/client-header combinations (Legacy 2025-03-26 and Modern 2026-07-28; portal/codex/claude/cursor). These are HTTP contract tests, not eight native-host UI runs. Each checks the exact six tools, explicit booleans, seven forbidden direct calls, and invalid upload arguments. Both protocol runs completed create Canvas → prepare upload → PUT PNG → create media node → state/list/link verification. Evidence: `dist/review-live-test.json`.

## Still required before production / resubmission

- Production deployment and repeat acceptance; refresh Portal tool discovery and review cases; submit only after checks complete.

No review-account passwords or bearer tokens are stored in this document, the source tree or upload archive.

## OAuth follow-up

The first loopback receiver used a single-threaded HTTPServer and stalled on a Chrome preconnection. It was replaced with a threaded receiver with connection timeouts; `/health` now returns `ready`. The test login page explicitly reports that the production review account email is not registered in the test environment. The user authorized the existing test account qbluo4914@163.com and completed login/authorization. No passwords or bearer tokens appear in the evidence. One interrupted verification attempt hit a transient remote disconnect; the resumed run passed. Test Canvas IDs: 53a896ac070c49f89be118918e7e458e (first harness assertion failed after successful write), 6039f44af688443b9572d420efe72886 (Legacy complete), 7153eb8d271f4a02b6aaa60c3e95b4dd (Modern complete). No existing content was removed. Native Chrome rendered the first Canvas with the actual local mannequin/box image; screenshot `dist/review-test-canvas-verified.png`.

Portal repair package 0.1.7 uploaded as a new draft; production verification and Portal rediscovery remain pending. CLI remains 0.1.6 because CLI code has not changed.

## Production and Portal follow-up

- Portal metadata/skills version 0.1.7 uploaded as a draft. Dedicated review account authorized the new Portal client. The latest MCP scan discovers exactly six tools and reports No findings. Each scanned definition was read through the Portal UI and its three boolean annotations matched the table. Evidence: `dist/review-portal-scanned-definitions.json` and `dist/review-portal-six-tools-no-findings.png`. Not submitted yet.
- Five positive cases, three negative cases, the supplied demo video URL and per-tool annotation explanations were saved to the new draft. Credentials were saved only in the Portal with explicit prior user approval.
- Existing production native connector completed create → upload HTTP 200 → media node → state/link for Canvas 95ce8770f8d846dd998f8d0a43f8e8f4, node node_1791959057896_3898e021ad35. However, the dedicated account browser showed Sharing is off. Its connector account/workspace identity is not independently confirmed; this is not accepted as dedicated-account end-to-end proof. A fresh production verifier OAuth flow is pending to resolve the discrepancy.
