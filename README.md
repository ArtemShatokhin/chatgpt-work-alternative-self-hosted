# Self-host the open-source ChatGPT Work alternative

Kortix is the open source ChatGPT Work alternative: run the agents, skills, memory and connectors on your own machine instead of renting them inside OpenAI's cloud. This repository is the working self-hosting guide — the `kortix.yaml`, the Docker Compose stack, and the step-by-step docs to bring the open source Kortix platform up on a laptop, a VPS or your own VPC.

ChatGPT Work is OpenAI's agent for longer, multi-step work. It is capable, but the runtime stays under OpenAI's control and there is no self-host edition. Kortix is the open source AI Operating System: agents, skills, company memory and every connector live in one git repo you own, and each session boots its own isolated Linux machine. This repo shows you exactly how to run that system yourself.

- Full guide: [the open-source ChatGPT Work alternative](https://chatgptworkalternative.com/open-source-chatgpt-work-alternative.html)
- Run it on your own box: [self-hosting Kortix](https://chatgptworkalternative.com/self-hosting.html)
- Side-by-side: [ChatGPT Work vs Kortix](https://chatgptworkalternative.com/chatgpt-work-vs-kortix.html)
- Ranked options: [ChatGPT Work alternatives](https://chatgptworkalternative.com/chatgpt-work-alternatives.html)
- Short answers: [FAQ](https://chatgptworkalternative.com/faq.html)

## What you get

| Property | ChatGPT Work | Kortix |
|---|---|---|
| Source | Closed | Open source |
| Where it runs | OpenAI-managed cloud, no self-host | Your laptop, VPS, VPC, on-prem, or managed cloud |
| Where config lives | In OpenAI's workspace | Files in one git repo you own |
| Models | OpenAI's models only | Any provider, your own keys |
| Source and hosting | Proprietary | Open source — self-host, read and modify |

## Quick start (managed cloud, 3 commands)

```bash
curl -fsSL https://kortix.com/install | bash
kortix init
kortix ship
```

`kortix init` scaffolds `kortix.yaml` with your agents, skills and runtime config. `kortix ship` pushes the repo and brings the whole thing live. To run everything yourself instead, follow [`docs/self-hosting.md`](docs/self-hosting.md).

## What is in this repository

| Path | What it is |
|---|---|
| `kortix.yaml` | The project manifest — machine image, connectors, triggers, agents |
| `docker-compose.yml` | A self-hosted stack for your own machine or VPS |
| `agents/self-host-guide.md` | A Kortix agent that walks a teammate through deployment |
| `skills/verify-self-host.md` | A skill that checks a running install end to end |
| `docs/self-hosting.md` | Step-by-step: laptop, VPS, VPC |
| `docs/kortix-yaml-reference.md` | Every manifest key used here |
| `docs/chatgpt-work-vs-kortix.md` | The ownership comparison in prose |
| `docs/faq.md` | Deployment, licensing and model-choice answers |
| `docs/security-review.md` | What a self-hosted install can prove before sign-off |

## Read the source

Kortix is open source: [Kortix on GitHub](https://github.com/kortix-ai/suna). Docs are at [kortix.com/docs](https://kortix.com/docs).

## Open source

Kortix is open source — self-host it, read it and modify it. See the [project page](https://kortix.com).

## What the companion site now covers

Five operational pages sit alongside the pages linked above: [pricing and what you actually pay for](https://chatgptworkalternative.com/pricing.html), a step-by-step [migration from ChatGPT Work](https://chatgptworkalternative.com/migrate-from-chatgpt-work.html), [running any model with your own keys](https://chatgptworkalternative.com/models-and-your-keys.html), [connectors and automation](https://chatgptworkalternative.com/connectors-and-automation.html), and [governance and permissions](https://chatgptworkalternative.com/governance-and-permissions.html) for teams that need per-tool approval gates.


New pages cover the questions teams ask before they move: [ChatGPT Work for teams](https://chatgptworkalternative.com/chatgpt-work-for-teams) on a shared setup the team owns and reviews, [ChatGPT Work data and privacy](https://chatgptworkalternative.com/chatgpt-work-data-and-privacy) on where the work and the configuration sit, [ChatGPT Work limitations](https://chatgptworkalternative.com/chatgpt-work-limitations) on the real limits of the hosted product, and a head-to-head [ChatGPT Work vs Gemini](https://chatgptworkalternative.com/chatgpt-work-vs-gemini). Two more comparisons sit alongside them: [ChatGPT Work vs Claude Cowork](https://chatgptworkalternative.com/chatgpt-work-vs-claude-cowork) and [ChatGPT Work vs Microsoft Copilot](https://chatgptworkalternative.com/chatgpt-work-vs-copilot), with a short answer to [is ChatGPT Work open source](https://chatgptworkalternative.com/is-chatgpt-work-open-source).
