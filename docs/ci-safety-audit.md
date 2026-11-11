# CI safety audit — OpenFlux

Audited 2026-10-13 at source `edcf77a1848a72370cfba9f346d3b897b61e7e7d`; default branch `main`.
GitHub API confirmed repository Actions **disabled** before preparation.
Scope: inherited workflow entry points and referenced build, packaging, installer,
artifact verification and release scripts. This is a targeted CI audit, not a full
application or dependency security audit. No application code or license changed.

## Inherited workflows

All originals are preserved byte-for-byte in `.github/workflows-disabled/`.
GitHub only discovers workflow YAML under `.github/workflows/`; the archived files
are reference material, not runnable entry points at this revision.

| Workflow | Triggers / token | Effects | Decision |
| --- | --- | --- | --- |
| `ci.yml` | Every push / PR; contents:read | Go tests/race/vet, cross-builds; sudo network namespace raw UDP/MTU integration | No publication, but privileged integration and full builds are outside this small stage. Archive; later split unit checks from privileged tests. |
| `nightly.yml` | nightly branch push, daily schedule, manual; contents:write | Upload binaries; create/update prereleases; clobber assets; delete old releases **and tags** | Archive. Automatic publication and destructive cleanup must not run in this fork. |
| `node-install.yml` | main push / PR with path filters, manual; permissions implicit | Ten distro images; privileged Docker, writable host cgroups, systemd/SSH installer tests; failure logs include shadow prefixes | Archive. Explicit read-only permissions, runner isolation and log review needed before any future use. |
| `node-release.yml` | `node-v*` tag push; contents:write | Cross-build exitnode and create release with binaries | Archive; not needed. |
| `release.yml` | `v*` tag push / manual; contents:write | Upload binaries even with publish=false; create/update releases, clobber assets | Archive. Manual version is interpolated into shell; future redesign must validate inputs and pass them through environment variables. |

No inherited `pull_request_target` or `workflow_run` entry point was found.
Third-party actions/toolchains and container tags need a separate pinning review
before restoring inherited pipelines. No such broad modernization is attempted here.
Called scripts are retained unchanged; archiving a workflow does not make those
scripts safe to run manually. In qThrone, build/pack scripts write deployment
directories and upgrade tests execute downloaded programs; in OpenFlux the
provision integration is opt-in via OPENFLUX_TEST_SSH and the distro Dockerfile
sets synthetic SSH passwords. Neither is executed by the new CI.

## Minimal replacement

`source-safety.yml` is JSON-form YAML (valid GitHub workflow syntax), allowing
validation with Python standard library only. It runs on PRs to `main`
and pushes to `main` / `experimental/keenetic-arm64`, without path filters.
One hosted Ubuntu job, five-minute limit, contents:read only; SHA-pinned checkout,
no persisted credentials, no submodules/LFS, no secrets, cache, artifact uploads,
release, package, Pages, deployment, sudo or repository-code execution.

It checks whitespace errors in the changed source diff and parses changed Python
and JSON files without importing/executing them. Other source languages receive
the whitespace check only. This is **not** Go/C++/Rust compilation, unit testing,
runtime acceptance or Keenetic compatibility evidence. Checkout uses full history
to resolve comparison commits; application dependencies are not downloaded.
For a new branch with an all-zero before SHA, compare against the empty tree.

## Safe future activation — owner action required

1. Keep repository Actions disabled while these Draft PRs are reviewed. Changes
   on the experimental branch do **not** neutralize workflows on the default branch
   or old tags/branches. Do not enable Actions merely to test this PR.
2. Separately approve and merge the safety changes through the existing protected
   default-branch PR process. This preparation does not perform that merge.
3. Before enabling, inventory all historical refs and workflow IDs. Disable all
   inherited workflow IDs at repository level (including archived/old-ref versions).
   Old tag workflows can otherwise remain a publication route. Do not dispatch or
   push old tags. If this cannot be verified, leave repository Actions disabled.
4. Set the default GITHUB_TOKEN to read-only and PR write/approval permission off;
   allow only reviewed actions pinned to full SHAs. Keep publication credentials,
   signing keys, deployment secrets and external publishing integrations absent.
   Repository action allowlists govern actions, not arbitrary shell commands; they
   do not replace disabling historical workflows. Review every workflow change.
5. Enable only the new static workflow, then verify its actual PR/push runs, token
   permissions and absence of artifacts/releases/deployments. Audit any queued or
   unexpected runs before proceeding. Add no required status checks until the
   workflow is in the default branch and its real check name/runs are verified.
   Preserve the solo-maintainer zero-review policy; no Rulesets changed here.
6. Restore individual test workflows only after a separate scoped review. Release,
   nightly cleanup, package publication and deployment remain disabled until
   separately authorized and designed with explicit owner admission.

## Verification and limits

Local validation: parse the replacement workflow as JSON, compile its embedded
Python without execution, run the source checker against the prepared diff, verify
archived workflow hashes below and ensure only workflow paths/documentation change.
GitHub CI is intentionally **NOT RUN** because Actions remain disabled. A local
static check is not a successful GitHub Actions run. Re-read Actions permissions,
default SHA and effective branch rules after opening the Draft PR.

Original Git blob SHA256 (archive integrity; before checkout newline conversion):

```text
f8e0c67c6a64bfa949987988d55a99833b5fbcd4c5daaf1c80b77a2a9f161127  ci.yml
4c8c4e4ace001f57a00db3e4c4247e95fa96d195e52d9cac3d7f40b1c5e3bbcc  nightly.yml
d30dfba44a87afd0340b6a6330bd44b11eee5cd6bfea6ffa0ade5ca1366638e4  node-install.yml
be9c95b5f3dfc6b132ba719bf403112f536e67b7ac087aad52dd814bff476147  node-release.yml
61dbc2aa7210e44a51dad254cde7578bc311bd20c1a611f875507f1b3b97755a  release.yml
```
