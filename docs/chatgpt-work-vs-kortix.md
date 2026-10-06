# ChatGPT Work vs Kortix: what you actually own

Both are agents that do longer, multi-step work across your apps and files. The difference is where the system lives and who can change it. Below is the comparison in the terms a team evaluating a self-hosted open source platform actually cares about.

## The short version

OpenAI's ChatGPT Work is a strong agent. It runs inside OpenAI's cloud, on OpenAI's models, with the configuration inside OpenAI's product. Kortix is the open source AI Management System: the same category of system, delivered as files in one git repo you own and can run on your own infrastructure.

## Side by side

| Dimension | Kortix | ChatGPT Work |
|---|---|---|
| Source | Open source (Elastic License 2.0) | Closed |
| Models | Any provider, your own keys | OpenAI's models only |
| Where it runs | Your laptop, VPS, VPC, on-prem, or managed cloud | OpenAI-managed cloud; no self-host |
| Where config lives | Files in one git repo you own | In OpenAI's workspace |
| Agent instructions | Markdown in `agents/` | Inside the product |
| Skills the company builds | Markdown in `skills/` | Inside the product |
| Memory | Files that accumulate, greppable | Inside the product |
| Connectors | 3,000+ apps, plus MCP / OpenAPI / GraphQL / HTTP | OpenAI's connected apps |
| Permission model | Allow / ask / block per tool call | Product controls |
| How work lands | A change request a person reviews | In the product |

## Why "one git repo" matters

With Kortix, the agent instructions, the skills the company has built, the memory it has accumulated, the connector configuration and the triggers are all text files. That means you can:

- **grep the whole company** to find where a rule lives;
- **diff any change** an agent proposes;
- **roll back** one part without touching the rest;
- **move the whole system** to another host, because nothing important is trapped in a database you do not have.

ChatGPT Work gives you a capable agent inside a product you do not control. That is a fair trade for some teams. For a team that wants the AI operations to be an asset it owns — auditable, portable, self-hosted — it is not.

## The runtime

Each Kortix session boots its own isolated Linux machine on its own branch. Thousands run in parallel on the same config. The agent can install, run and break anything; only what it commits survives, and work reaches `main` only through a change request a person reads first.

## Model choice

Kortix is model-agnostic. Pick Claude, OpenAI, Gemini or your own OpenAI-compatible endpoint per agent, per session or per message, and bring your own API key. When a better model lands, you switch a line — you do not wait for a vendor.

## Licence

Kortix is open source (Elastic License 2.0) — self-host it, read the code and modify it. The source is public: [Kortix on GitHub](https://github.com/kortix-ai/suna).

## Start

```bash
curl -fsSL https://kortix.com/install | bash
kortix init
kortix ship
```

Then follow the [self-hosting walkthrough](self-hosting.md) if you want it on your own box. Read more on the [ChatGPT Work vs Kortix page](https://chatgptworkalternative.com/chatgpt-work-vs-kortix.html) and the [alternatives ranking](https://chatgptworkalternative.com/chatgpt-work-alternatives.html).
