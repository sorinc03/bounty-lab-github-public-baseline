# Experiments

## GH-PUBLIC-ACTIONS-BASELINE-001

Status: completed; no finding.

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

Observed on 2026-05-20:
- Run URL: https://github.com/sorinc03/bounty-lab-github-public-baseline/actions/runs/26154793037
- Runner: `ubuntu-24.04`.
- Token permissions: `Actions: read`, `Contents: read`, `Metadata: read`.
- Repository metadata read succeeded.
- README contents read succeeded.
- Attempted write failed with HTTP 403: `Resource not accessible by integration`.

Conclusion:
- Baseline matches expected GitHub behavior.

## VERCEL-SKILLS-PROC-ENV-POC-001

Status: ready to run.

Goal:
- Verify on a Linux runner whether `vercel-labs/skills` copying a malicious symlink to `/proc/self/environ` captures the install process environment.

Workflow:
- `.github/workflows/vercel-skills-proc-environ-poc.yml`

Safety:
- The install command is run with `env -i` and only a fake `CONTROLLED_SKILLS_SECRET_*` canary plus minimal `HOME`, `PATH`, and `CI`.
- The workflow checks for the canary and file type but does not print the full copied environment file.

Expected if vulnerable:
- Local path install copies `/proc/self/environ` into `.agents/skills/proc-env-local-poc/copied-proc-environ.bin` as a regular file.
- Git-backed install copies `/proc/self/environ` into `.agents/skills/proc-env-git-poc/copied-proc-environ.bin` as a regular file.
- Both copied files contain the canary env var set for the `node src/cli.ts add ...` process.
