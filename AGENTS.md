# AGENTS.md — pod-agentteams-controller

Standalone candy repo for the `agentteams-controller` candy — the AgentTeams
control plane (kine-backed kube-apiserver + reconciler + `agt` REST client) the
full AgentTeams stack composes. The entire candy lives in `charly.yml` at the
repo root: the `agentteams-controller:` entity with its `plan:` of build-time
`check:` steps and its service declaration. There is no Go source tree in this
repo — the controller is built from the pinned upstream AgentTeams source at
image build time.

Canonical files:

- `charly.yml` — the `agentteams-controller:` candy entity (description,
  `require`, `env_accept`, volume, port, `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-agentteams:agentteams` — the owning skill for the AgentTeams stack
  (Manager–Worker model, the five-service composition, volumes, ports, both
  deploy substrates). Load before editing, building, deploying, or
  troubleshooting this candy.
- `/charly-agentteams:agentteams-cli` — the compiled-in `charly agentteams` REST
  CLI the controller's `agt` client and the snapshot bed drive.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, package sections, service declarations).
  Load before editing any entity field or plan step.
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own; the family skill
`/charly-agentteams:agentteams` covers the surface. The gap is routed to the named skill-authoring
batch [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The live R10 witness is the stack's `check-agentteams-pod` bed
  (`charly check run check-agentteams-pod`), which exercises the controller
  together with matrix / minio / higress. There is no controller-only bed.

## Modify this repo

- Edit the `agentteams-controller:` candy entity in `charly.yml`. A service,
  port, `env_accept`, or credential-resolution change must stay consistent with
  the `agentteams-matrix` / `agentteams-minio` / `agentteams-higress` candies it
  shares credentials with (the same `AGENTTEAMS_*` vars are read by all of
  them).
- The `description:` and the `plan:` `check:` steps are the verifiable contract;
  every claim should be a file/binary on disk or a live HTTP/service response.
- Keep the pinned AgentTeams source tag and the `version:` schema stamp in step.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
