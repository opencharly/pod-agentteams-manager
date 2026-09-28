# agentteams-manager

The AgentTeams Manager agent image, as an OpenCharly candy.

In the AgentTeams Manager–Workers model the Manager plans and delegates. This
candy is the **Manager runtime image**: the controller spawns one container from
it per Manager custom resource. It composes the shared openclaw gateway runtime
and the shared `agt` / `mc` CLI, then installs the Manager agent trees and
configs from the pinned AgentTeams source.

A charly-owned start script waits for the controller's in-container services,
syncs the manager workspace from MinIO, renders `openclaw.json` from the
template, and execs the openclaw gateway. The service runs rootless as the image
user (uid 1000).

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `agentteams-manager` |
| Runtime | openclaw gateway + `agt` REST client + `mc` |
| Service | `agentteams-manager` (spawned by the controller) |
| Requires | `layer-agentteams-openclaw`, `layer-agentteams-cli` |

## How to use it

This candy is consumed **indirectly**: the controller spawns Manager containers
from the image built on top of it. Point the controller at that image with
`AGENTTEAMS_MANAGER_IMAGE`. The full stack composes it for you:

```yaml
my-agentteams:
  candy:
    base: cachyos.cachyos
    candy:
      - '@github.com/opencharly/pod-agentteams-manager:<tag>'
```

Build the image with the charly CLI:

```bash
charly box build my-agentteams
```

See the owning skill for the composition, the Manager–Worker model, and both
deploy substrates.

## Layout

- `charly.yml` — the `agentteams-manager:` candy entity: the two shared
  requires, the `agentteams-manager` service, and the plan that installs the
  agent trees, pre-creates the rootless runtime directories, and writes the
  start script.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-agentteams:agentteams` — the full stack composition.
- CLI: `/charly-agentteams:agentteams-cli` (`charly agentteams`).
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
