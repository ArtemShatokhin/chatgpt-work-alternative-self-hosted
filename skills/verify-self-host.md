# Skill: verify-self-host

Check that a self-hosted open source Kortix install is actually running end to
end. Use this after `docker compose up` or `kortix self-host start`.

## Steps

1. **Control plane answers.** Request `http://localhost:3000/health` (or the
   host you deployed to). A `200` with a JSON body is the pass. Record the
   version string it returns.

2. **Manifest loads.** Run `kortix project info` in the repository root. It
   must echo the name from `kortix.yaml` (`chatgpt-work-alternative-self-hosted`)
   and list the `github` and `shell` connectors and the `self-host-guide` agent.

3. **A session boots.** Run:

   ```bash
   kortix sessions new --prompt "Print the working directory and the Kortix version, then stop."
   ```

   The session must report an isolated working directory (`/workspace`) and a
   branch name that matches the session id. That confirms one sandbox per
   session.

4. **Work lands through a gate.** Run `kortix cr ls`. The session from step 3
   should be listed, and any change it proposes must appear as a change request
   a human reviews — never applied to main directly.

5. **Model choice is yours.** Confirm `kortix.yaml` reads a provider key from
   the environment (for example `OPENAI_API_KEY` or `ANTHROPIC_API_KEY`) and
   that the same install can switch providers per agent. Record which provider
   answered step 3.

## Pass condition

All five steps pass. If any step fails, capture the exact error and open a
change request with the manifest or doc fix — do not edit main.

## Why it matters

A self-hosted open source system is only worth it if it truly runs on your own
infrastructure. This skill is the difference between a claim and a verified
install.

Links: [self-hosting guide](https://chatgptworkalternative.com/self-hosting.html),
[docs](https://kortix.com/docs).
