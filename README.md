# agentteams-controller

The AgentTeams control plane as an OpenCharly candy.

AgentTeams is a multi-agent runtime built on a Manager–Workers model: a Manager
plans and delegates, Workers execute, and they converse in shared Rooms (Matrix
chat spaces). This candy is the **controller** — a Go binary that embeds a
kine-backed kube-apiserver, reconciles the Manager / Worker / Team / Human
custom resources, and serves a REST API on `:8090`. It also ships the `agt` REST
client used to drive that API.

The controller spawns Manager and Worker containers through the rootless podman
socket and mints the admin CLI token to `/var/run/agentteams/cli-token`. The
service runs rootless as the image user (uid 1000).

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `agentteams-controller` |
| Binary | the controller + `agt`, built from the pinned AgentTeams v1.2.2 source |
| Service / port | `agentteams-controller` on `8090` |
| Volume | `~/.agentteams/controller` (admin password, CLI token) |
| Requires | `layer-golang` |

Deploy-overridable `env_accept` vars cover the admin credentials, the
Manager/Worker image refs the controller spawns, the LLM provider/model, and the
shared MinIO / Matrix credentials (self-provisioned on their own candies'
volumes when unset). The controller reads the Matrix URL, AI-gateway URLs, and
MinIO endpoint from its service environment.

## How to use it

Compose the candy into a box (or the full AgentTeams stack, whose top
composition already includes it):

```yaml
my-agentteams:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/pod-agentteams-controller:<tag>'
```

Then build and deploy with the charly CLI:

```bash
charly box build my-agentteams
charly start my-agentteams
```

The controller is normally deployed as part of the full stack, not on its own.
See the owning skill for the composition, volumes, ports, and both deploy
substrates.

## Layout

- `charly.yml` — the `agentteams-controller:` candy entity: description, the
  `layer-golang` require, the Arch package list, `env_accept`, the volume, the
  port, the `agentteams-controller` service, and the build/runtime `plan:`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-agentteams:agentteams` — the full stack composition.
- CLI: `/charly-agentteams:agentteams-cli` (`charly agentteams`).
- Verb-level deploy: `/charly-core:deploy`.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
