# Security review: what an open-source Kortix install can prove

Kortix is the open-source AI Operating System, and this repository runs it on infrastructure a team controls: your agents, their skills, your company memory, and every connector in one git repo you own. Owning the server feels like the security argument. It is only the precondition. A self-hosted agent system is safer than a rented one when the repository can show where credentials live, what an agent may touch, and who must approve a change, because each answer can be checked before the first session runs.

The review applies those questions to `kortix.yaml`, `agents/self-host-guide.md` and `docker-compose.yml`. Three findings need a fix before a sign-off.

## The three questions

| Question | Where the answer lives | What passes |
| --- | --- | --- |
| Where do credentials live? | `docker-compose.yml`, project secrets, host environment | No working default in git; values encrypted or brokered |
| What may an agent touch? | `kortix.yaml` grants, `permission` in the agent file | Only the connectors and commands the job needs |
| Who approves a change? | `kortix_permissions`, branch rules, `policy` | No agent can merge its own change request |

## Where credentials live

`docker-compose.yml` commits a working database password in two places: `DATABASE_URL: postgres://kortix:kortix@db:5432/kortix` and `POSTGRES_PASSWORD: kortix`. It also publishes the control plane with `"3000:3000"`, which binds every interface. That is fine on a laptop. On a VPS it is an open door with a default key.

Move the database URL and the model keys into an untracked `.env`. Generate a password, bind the port to `127.0.0.1`, and put a TLS proxy in front. Then run a grep that should return no usable value:

```bash
git grep -nE 'PASSWORD|SECRET|API_KEY|DATABASE_URL'
```

Kortix separates two kinds of credential. Project secrets are encrypted at rest with a key per project, and a value reaches a sandbox only through its exposure setting. The default exposure is `environment`. The agent's code can read the variable. Egress-enforced is the experimental option that substitutes the value outside the sandbox only on approved HTTPS hosts ([Secrets](https://kortix.com/docs/project/secrets)). Connector credentials are brokered server-side and never enter the machine. The GitHub token used by the connector does not sit in the agent's environment.

The compose file passes `OPENAI_API_KEY` and `ANTHROPIC_API_KEY` from the host into `kortix-api`. Anyone with `docker inspect`, `.env`, or the Docker socket can read those keys. Limit that group and rotate on departure. The supported self-host stack runs an updater that mounts the Docker socket, so treat that socket as root ([self-hosting docs](https://kortix.com/docs/host)).

## What an agent may touch

The manifest gives `self-host-guide` two connectors. They are `shell` and `github`, with `repo` and `issues` scopes. The manifest declares no `secrets` grant, and an omitted grant means no project secret. That grant is right. The GitHub scopes are broader than a deployment guide needs if it only reads files and opens change requests.

The file also carries a block that looks like a shell control:

```yaml
permissions:
  shell:
    - match: "rm -rf *"
      action: block
    - match: "docker compose *"
      action: ask
```

In a version 2 manifest, tool rules belong in the agent file's `permission` frontmatter. `permissions` is not a top-level key, and Kortix ignores unknown top-level keys, so the block has no effect. The agent file has no frontmatter, and a tool with no rule is allowed, so `self-host-guide` can run any shell command, including the one the manifest appears to block.

The manifest header is worse: it declares `version: 2` where the schema requires `kortix_version: 2`. Validation stops there, which makes `kortix validate` the cheapest check.

The corrected shape puts the rules in the agent file, where the harness reads them:

```yaml
---
description: Deployment guide for a self-hosted Kortix install.
mode: primary
permission:
  bash:
    "*": ask
    "rm -rf *": deny
    "docker compose *": ask
---
```

An `ask` rule pauses the turn for a person's approval. That works for an interactive guide and stalls the `nightly-healthcheck` trigger, which runs with nobody present, so unattended agents need a narrow allow list and `deny` on destructive commands. `seo-weekly` also points at the same `agents/self-host-guide.md` file, so a permission change for one job changes both.

## Who must approve a change

This is the strongest part of the repository. Neither agent declares `kortix_permissions`, and an agent with no `kortix_permissions` holds no project permission, so neither agent can merge. Session work reaches `main` through a change request. Merge is default-deny for agents. Those two facts are the control; the line in the agent prompt that says "never change `kortix.yaml` on main directly" is guidance to the model, not enforcement.

A session works on its own branch, pushes it, and opens a change request. A person with `project.gitops.merge` reviews the diff and merges it. A session cannot merge the change request it opened unless its agent's grant in `kortix.yaml` includes `project.gitops.merge`; without it the merge returns `CR_SELF_MERGE_REFUSED` ([Git and change requests](https://kortix.com/docs/git)). Confirm that no one later adds `kortix_permissions: all` to a block for convenience, and read the Review page permissions: merging needs `project.write` and `project.gitops.merge`.

The second approval surface is the connector call. `policy.default_mode` defaults to `allow_all`, so calls run unapproved until a team sets it. The repository has no `policy:` block, which leaves the `shell` and `github` calls governed only by the ignored tool rules. Set the project policy, then use the agent's `permission` rules for command-level detail ([manifest reference](https://kortix.com/docs/project/manifest)).

On a GitHub origin, Kortix does not block a force-push, so add a ruleset that blocks force-pushes and deletions on the default branch; change-request merges still work.

## The stack you actually run

`docker-compose.yml` runs `kortix/opencode:latest` as `kortix-api` and `postgres:16-alpine` as the database. The self-hosting docs name a larger stack: `kortix/kortix-frontend`, `kortix/kortix-api`, `kortix/kortix-gateway`, Supabase and Caddy, the stack that `kortix self-host` installs ([self-hosting docs](https://kortix.com/docs/host)). Confirm which stack is running and that the image you call the API is the API.

Both tags float. Pin a digest and mirror the images if the registry matters. Mirroring does not move the agent's computer: sessions run on Daytona by default, or Platinum or E2B, and the instance must be reachable so those sandboxes can call back. Drop the word air-gapped unless the sandbox provider and the model endpoint are local too.

## What this repository gets right

Neither agent receives a project secret or can merge. Proposed changes go through a change request, and the agent's instructions say not to paste secrets and not to edit `main` directly. The `verify-self-host` skill checks the control plane, the manifest, a session branch, the change-request queue and the model provider.

## A checklist to run

1. `kortix validate` returns no errors.
2. `kortix validate --scopes` lists only grants the team intends.
3. `git grep -nE 'PASSWORD|SECRET|API_KEY|DATABASE_URL'` returns no usable value.
4. `docker-compose.yml` binds the control plane to loopback and terminates TLS in front.
5. Every agent file has a `permission` block with `deny` on destructive commands.
6. No agent block lists `project.gitops.merge` unless a person is meant to be bypassed.
7. `policy:` sets allow, ask or block for `shell` and `github`.
8. `kortix cr ls` shows the work waiting for review, and `kortix self-host doctor` passes.

## FAQ

**Is a self-hosted agent system automatically more secure?**

No. Self-hosting moves the control plane onto infrastructure the team owns, and it adds patching, key custody, network exposure and backup. The security comes from the controls in the repository, not from the address of the server.

**Does Kortix encrypt secrets in a self-hosted install?**

Yes. Project secrets are encrypted at rest with a key per project. The exposure setting still decides whether the agent's code can read a value: `environment` is the default, and egress-enforced is experimental. Connector credentials are brokered server-side.

**Can an agent merge its own change request?**

Only when its grant in `kortix.yaml` includes `project.gitops.merge`. Otherwise Kortix refuses the merge with `CR_SELF_MERGE_REFUSED`, and a person reviews the diff. In this repository neither agent declares any project permission, so neither can merge.

Read more in the [self-hosting guide](self-hosting.md) and the [Kortix docs](https://kortix.com/docs).
