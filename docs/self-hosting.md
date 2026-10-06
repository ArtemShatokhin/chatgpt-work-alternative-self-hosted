# Self-hosting the open-source ChatGPT Work alternative

ChatGPT Work runs inside OpenAI's cloud — there is no self-host edition, and the agent instructions, connectors and schedules stay inside OpenAI's product. This guide runs the open source Kortix AI Management System on infrastructure you control: a laptop, a VPS, your own VPC or an on-prem network. Pick the path that matches where the work should live.

## Path A — a laptop (evaluate in ten minutes)

```bash
curl -fsSL https://kortix.com/install | bash
kortix init                 # scaffolds kortix.yaml, agents/ and skills/
kortix self-host start      # one Docker Compose stack on this machine
```

`kortix init` writes the manifest this repository already contains. `kortix self-host start` brings up the control plane and the session runtime locally. Open `http://localhost:3000` and start a session:

```bash
kortix sessions new --prompt "Summarize today's commits and open a change request"
```

Everything runs on the one machine. This is the fastest way to confirm the platform does what it says before you commit any infrastructure.

## Path B — a VPS (a shared box for a small team)

Same stack, different host. On a fresh Ubuntu 24.04 VPS:

```bash
# 1. Docker
curl -fsSL https://get.docker.com | sh
# 2. Kortix
curl -fsSL https://kortix.com/install | bash
kortix init
kortix self-host start
```

Then put a reverse proxy (Caddy or nginx) in front of port `3000`, point a DNS record at the box, and terminate TLS there. The connector credentials are brokered server-side, so the worker machine never holds them; keep the database volume on an encrypted disk.

## Path C — your VPC or on-prem (governed, air-gapped if needed)

For a security team that needs the work to stay inside the network:

1. **Images.** Mirror `kortix/opencode:latest` into your own registry and set `machine.image` in `kortix.yaml` to that reference. The stack then pulls nothing from outside.
2. **Database.** Point `DATABASE_URL` at your managed Postgres. Secrets are encrypted at rest with a key per project.
3. **Model access.** Kortix is model-agnostic. Either let agents reach a provider through an egress proxy, or run your own OpenAI-compatible endpoint behind your own URL and set it per agent, per session or per message.
4. **Identity.** Wire SAML 2.0 single sign-on and SCIM 2.0 so people and agents are provisioned from your directory.
5. **Permissions.** Set every tool call to allow, ask or block — down to the arguments of a single call. An `ask` holds the call until a person approves it, then the agent resumes.

## What to verify

Run the `verify-self-host` skill after any path. It checks the control plane, the manifest, an isolated session, the change-request gate and model choice. A self-hosted open source system is only worth it if it really runs on your own infrastructure.

## Where the work lands

Every session runs on its own isolated Linux machine on its own branch. The agent commits there; work reaches `main` only through a change request a person reads as a diff. That is the pitch of an owned system: the whole company is one git repo you can grep, diff and roll back.

Continue with the [manifest reference](kortix-yaml-reference.md), the [ownership comparison](chatgpt-work-vs-kortix.md), or the [FAQ](faq.md). Full walkthrough on the [self-hosting page](https://chatgptworkalternative.com/self-hosting.html); project docs at [kortix.com/docs](https://kortix.com/docs).
