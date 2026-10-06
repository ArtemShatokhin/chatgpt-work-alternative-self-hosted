# FAQ — self-hosting the open source ChatGPT Work alternative

Short, concrete answers. For the long form, see the [self-hosting walkthrough](self-hosting.md) and the [ownership comparison](chatgpt-work-vs-kortix.md).

## Can I self-host Kortix?

Yes. Kortix is open source and self-hostable: install the CLI, run `kortix self-host start`, and the control plane and session runtime come up as one Docker Compose stack. The same manifest runs on a laptop, a VPS, your VPC or an on-prem network. Steps: [self-hosting](self-hosting.md).

## Is Kortix really open source?

Yes. Kortix is open source under the Elastic License 2.0 — self-host it, read the code and modify it. The source is public at [Kortix on GitHub](https://github.com/kortix-ai/suna).

## Can I run it air-gapped?

Yes. Mirror `kortix/opencode:latest` into your own registry, set `machine.image` to that reference, point `DATABASE_URL` at your own Postgres, and run your own OpenAI-compatible model endpoint behind your own URL. Nothing then leaves the network.

## Do I have to use OpenAI's models?

No. Kortix is model-agnostic. Pick Claude, OpenAI, Gemini or your own OpenAI-compatible endpoint — per agent, per session or per message — and bring your own API key.

## Where does the configuration live?

In one git repo you own. Agents, skills, memory, connector configuration and triggers are markdown and YAML files. You can grep the whole company, diff any change and roll back any part. That is the core difference from ChatGPT Work, whose configuration lives inside OpenAI's product.

## How does work reach production?

Every session runs on its own isolated Linux machine on its own branch. The agent commits there, and the work reaches `main` only through a change request a person reads as a diff. Approval gates are off until you set them.

## What does it cost?

Self-hosting Kortix is free. Managed Kortix Cloud is $40 per seat per month plus usage; Enterprise is custom. You bring your own model keys either way. Compare with ChatGPT Work's paid plans on the [alternatives page](https://chatgptworkalternative.com/chatgpt-work-alternatives.html).

## How do I verify an install?

Run the `verify-self-host` skill in this repository: it checks the control plane, the manifest, an isolated session, the change-request gate and model choice.

## Where do I start?

```bash
curl -fsSL https://kortix.com/install | bash
kortix init
kortix ship
```

Docs: [kortix.com/docs](https://kortix.com/docs). More answers on the [FAQ page](https://chatgptworkalternative.com/faq.html).
