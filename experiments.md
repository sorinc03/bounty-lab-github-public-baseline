# Experiments

## GH-PUBLIC-ACTIONS-BASELINE-001

Status: ready, not yet run.

Goal:
- Establish a free public-repo baseline for what a low-permission `GITHUB_TOKEN` can and cannot do.

Workflow:
- `.github/workflows/manual-token-boundary.yml`

Expected:
- `GET /repos/{owner}/{repo}` succeeds.
- `GET /repos/{owner}/{repo}/contents/README.md` succeeds.
- Attempting to write repository contents with `contents: read` fails.

Evidence to capture:
- Workflow run URL.
- Job log excerpt showing allowed reads.
- Job log excerpt showing denied write attempt.

Report potential:
- None by itself. This is baseline plumbing.

