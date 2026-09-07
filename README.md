# Discord Guild MCP

Declarative, guarded infrastructure-as-code for Discord roles and channels — driven from any MCP-compatible AI client (Claude Desktop, Claude Code, or your own).

---

## The problem this actually solves

If you run more than one Discord server, you already know the drift:

- Someone renames a role at 2am and every permission overwrite downstream is now wrong.
- A new channel category gets added "temporarily" and is still there eight months later, unmoderated.
- You want an AI assistant to help manage the server, but every existing option means handing it a bot token and hoping it doesn't nuke your `#mod-logs` channel.

Most Discord automation tools are built for moderation, XP, or tickets. Almost none of them treat your **guild structure itself** — roles, channels, categories, permission overwrites, ordering — as something you can describe, diff, and reconcile the way you'd manage infrastructure with Terraform.

This does that. Nothing else.

## How it works

You write your target server structure once, as YAML:

```yaml
roles:
  - name: Verified
    color: "#57F287"
    permissions: [SEND_MESSAGES, READ_MESSAGE_HISTORY]

channels:
  - name: general
    type: text
    category: Community
```

Then, from your MCP client:

1. `load_target_config` — read your YAML
2. `diff_state` — compare it against what's actually live on Discord right now
3. `apply_diff` with `dry_run: true` — see exactly what would change, nothing is written yet
4. `apply_diff` again with the returned confirmation token — and only then does anything touch your server

Every step is inspectable before it's committed. Nothing destructive happens without an explicit, single-use, time-limited confirmation.

## What it won't do

Worth being upfront about scope, because most people asking "can it also manage tickets/leveling/moderation bots" are asking the wrong tool:

- No third-party bot dashboards, XP systems, payments, or moderation workflows
- No reaction roles, no ticket systems, no event scheduling
- It manages **native Discord roles and channels**, and does that one job carefully

## Built for a bot token you don't have to fully trust

This was designed under the assumption that giving an AI assistant write access to your server's permission structure is a genuinely risky thing to do carelessly, and treated accordingly:

- **Protected entities** — configure role/channel names that can never be deleted, reordered, or have permissions changed, regardless of what the diff says
- **Dry-run is not optional** — every destructive apply requires a prior preview and a confirmation token that expires in 10 minutes and can only be used once
- **Full audit trail** — every write is logged with before/after state, actor, and timestamp to an append-only JSONL file
- **Minimum viable permissions** — the setup guide walks you through inviting the bot with only `Manage Roles` and `Manage Channels`, explicitly not `Administrator`
- **Discord's own role hierarchy stays authoritative** — the bot cannot act above its own highest role, and this software doesn't try to work around that

If you've read source code for MCP servers before, you know how rare it is for one to treat guardrails as a first-class feature rather than an afterthought. That's the actual point of this project.

## Connect it the way that fits your setup

- **stdio** — for local MCP clients (Claude Desktop, Claude Code) as a subprocess
- **HTTP + bearer token** — for a self-hosted remote deployment behind your own reverse proxy
- **OAuth 2.1** — built-in authorization server with PKCE, for clients like claude.ai's custom connectors that only support OAuth, not manual tokens

One codebase, pick the transport that matches where you're running it.

## Tools exposed

`get_live_state` · `load_target_config` · `diff_state` · `apply_diff` · `manage_role` · `manage_channel` · `set_member_roles` · `list_member_roles` · `query_audit_log`

## Who this is for

Realistically:

- People or small teams who administer **more than one** Discord server and are tired of manual drift
- Agencies or community consultants managing guild structure on behalf of clients
- Anyone comfortable running a Node.js process who wants their AI assistant to have *safe*, *auditable* write access to their server structure — not raw bot-token access

If you administer a single small server by hand and enjoy doing it, you probably don't need this.

## Getting it

The source in this repo (and the docs above) reflect the actual v1.0 implementation. The full package — source, tests, and setup support — is distributed under a paid license rather than published as free/open source, because the ongoing guardrail and security work here isn't a side project.

| | |
|---|---|
| **Individual license** | Unlimited servers you personally own or administer |
| **Agency / team license** | Unlimited servers, including client work and hosted/managed deployments |

Both are one-time purchases — no subscription, no per-server metering, no telemetry phoning home.

**[→ Get the license on Gumroad](https://xxen.gumroad.com/l/mcp)** — current pricing, instant download, setup docs included.

## Before you buy: verify it yourself

Don't take a README's word for a security model. The license includes full source access specifically so you (or someone you trust) can read `src/domain/guardrails.ts` and `SECURITY.md` before it ever touches a real server. Test it against a throwaway guild first. That's what the dry-run mode is for.

---

*Requires Node.js 20+, a Discord bot application with `Manage Roles` / `Manage Channels`, and a server you administer.*

This does that. Nothing else.

## How it works

You write your target server structure once, as YAML:

```yaml
roles:
  - name: Verified
    color: "#57F287"
    permissions: [SEND_MESSAGES, READ_MESSAGE_HISTORY]

channels:
  - name: general
    type: text
    category: Community
```

Then, from your MCP client:

1. `load_target_config` — read your YAML
2. `diff_state` — compare it against what's actually live on Discord right now
3. `apply_diff` with `dry_run: true` — see exactly what would change, nothing is written yet
4. `apply_diff` again with the returned confirmation token — and only then does anything touch your server

Every step is inspectable before it's committed. Nothing destructive happens without an explicit, single-use, time-limited confirmation.

## What it won't do

Worth being upfront about scope, because most people asking "can it also manage tickets/leveling/moderation bots" are asking the wrong tool:

- No third-party bot dashboards, XP systems, payments, or moderation workflows
- No reaction roles, no ticket systems, no event scheduling
- It manages **native Discord roles and channels**, and does that one job carefully

## Built for a bot token you don't have to fully trust

This was designed under the assumption that giving an AI assistant write access to your server's permission structure is a genuinely risky thing to do carelessly, and treated accordingly:

- **Protected entities** — configure role/channel names that can never be deleted, reordered, or have permissions changed, regardless of what the diff says
- **Dry-run is not optional** — every destructive apply requires a prior preview and a confirmation token that expires in 10 minutes and can only be used once
- **Full audit trail** — every write is logged with before/after state, actor, and timestamp to an append-only JSONL file
- **Minimum viable permissions** — the setup guide walks you through inviting the bot with only `Manage Roles` and `Manage Channels`, explicitly not `Administrator`
- **Discord's own role hierarchy stays authoritative** — the bot cannot act above its own highest role, and this software doesn't try to work around that

If you've read source code for MCP servers before, you know how rare it is for one to treat guardrails as a first-class feature rather than an afterthought. That's the actual point of this project.

## Connect it the way that fits your setup

- **stdio** — for local MCP clients (Claude Desktop, Claude Code) as a subprocess
- **HTTP + bearer token** — for a self-hosted remote deployment behind your own reverse proxy
- **OAuth 2.1** — built-in authorization server with PKCE, for clients like claude.ai's custom connectors that only support OAuth, not manual tokens

One codebase, pick the transport that matches where you're running it.

## Tools exposed

`get_live_state` · `load_target_config` · `diff_state` · `apply_diff` · `manage_role` · `manage_channel` · `set_member_roles` · `list_member_roles` · `query_audit_log`

## Who this is for

Realistically:

- People or small teams who administer **more than one** Discord server and are tired of manual drift
- Agencies or community consultants managing guild structure on behalf of clients
- Anyone comfortable running a Node.js process who wants their AI assistant to have *safe*, *auditable* write access to their server structure — not raw bot-token access

If you administer a single small server by hand and enjoy doing it, you probably don't need this.

## Getting it

The source in this repo (and the docs above) reflect the actual v1.0 implementation. The full package — source, tests, and setup support — is distributed under a paid license rather than published as free/open source, because the ongoing guardrail and security work here isn't a side project.

| | |
|---|---|
| **Individual license** | Unlimited servers you personally own or administer |
| **Agency / team license** | Unlimited servers, including client work and hosted/managed deployments |

Both are one-time purchases — no subscription, no per-server metering, no telemetry phoning home. Reach out via the repo's contact link for current pricing and to get set up.

## Before you buy: verify it yourself

Don't take a README's word for a security model. The license includes full source access specifically so you (or someone you trust) can read `src/domain/guardrails.ts` and `SECURITY.md` before it ever touches a real server. Test it against a throwaway guild first. That's what the dry-run mode is for.

---

*Requires Node.js 20+, a Discord bot application with `Manage Roles` / `Manage Channels`, and a server you administer.*
