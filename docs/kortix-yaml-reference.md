# kortix.yaml reference — the manifest keys this repo uses

`kortix.yaml` is the manifest for an open source Kortix project. It declares the machine a session boots, the connectors it may reach, the agents and skills, the triggers that start work, and the per-tool permissions. It is versioned beside your code, so any change is a diff a person can review.

## `machine`

The runtime image each session boots.

```yaml
machine:
  image: kortix/opencode:latest
  workdir: /workspace
```

Swap `image` for a mirror in your own registry to run air-gapped.

## `connectors`

Declared once, scoped per agent. Credentials are brokered server-side and never enter the machine.

```yaml
connectors:
  github: { kind: mcp, scopes: [repo, issues] }
  shell:  { kind: builtin }
```

`kind: mcp` takes any MCP server; OpenAPI, Postman, GraphQL and raw HTTP are also supported, alongside 3,000+ app integrations.

## `agents`

Markdown files beside the manifest. Each names the connectors it may use.

```yaml
agents:
  self-host-guide:
    file: agents/self-host-guide.md
    connectors: [shell, github]
```

The markdown file is the agent's instructions in plain language. One agent can serve many jobs through different prompts.

## `skills`

Procedures the agents load. Keep them short and concrete.

```yaml
skills:
  - skills/verify-self-host.md
```

## `triggers`

Start a session with nobody present.

```yaml
triggers:
  - slug: nightly-healthcheck
    type: cron
    cron: "0 3 * * *"
    prompt: Run the verify-self-host skill and open a change request with the result.
    agent: self-host-guide
```

`type: webhook` starts a session from a signed webhook instead.

## `permissions`

Per-tool rules, down to the arguments of a call.

```yaml
permissions:
  shell:
    - match: "rm -rf *"
      action: block
    - match: "docker compose *"
      action: ask
```

`action` is `allow`, `ask` or `block`. An `ask` holds the call until a person approves it, then the agent resumes.

## Applying a change

Edit the file on a session branch, run the `verify-self-host` skill, and open a change request. A human reviews the diff before it reaches `main` — the same gate applies to the configuration as to the code, because here they are the same thing.

Next: the [self-hosting walkthrough](self-hosting.md) and the [ownership comparison](chatgpt-work-vs-kortix.md). Manifest docs: [kortix.com/docs](https://kortix.com/docs).
