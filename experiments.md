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

Status: completed; candidate Vercel finding remains low-confidence for bounty value.

Goal:
- Verify on a Linux runner whether `vercel-labs/skills` copying malicious symlinks captures out-of-tree readable files and whether `/proc/self/environ` captures install process environment.

Workflow:
- `.github/workflows/vercel-skills-proc-environ-poc.yml`

Safety:
- The install command is run with `env -i` and only a fake `CONTROLLED_SKILLS_SECRET_*` canary plus minimal `HOME`, `PATH`, and `CI`.
- The workflow checks for the canary and file type but does not print the full copied environment file.

Observed:
- Successful run URL: https://github.com/sorinc03/bounty-lab-github-public-baseline/actions/runs/26158730562
- Local path install copied `CONTROLLED_OUTSIDE_FILE_LOCAL_20260520` from an out-of-tree symlink target into `.agents/skills/proc-env-local-poc/copied-outside-file.txt` as a regular file.
- Git-backed install copied `CONTROLLED_OUTSIDE_FILE_GIT_20260520` from an out-of-tree symlink target into `.agents/skills/proc-env-git-poc/copied-outside-file.txt` as a regular file.
- `/proc/self/environ` copied as a regular empty file and did not contain the process env canary in either local-path or git-backed mode.

Conclusion:
- Out-of-tree file-copy primitive confirmed on Linux.
- The stronger `/proc/self/environ` token-capture angle did not reproduce.
