<a href="https://github.com/NVIDIA/NemoClaw">
     <img width="1500" height="801" alt="nemoclaw" src="https://github.com/user-attachments/assets/38f0625e-b1a1-4c4e-88d9-663ea940a305" />
</a>

<br/>
<br/>

<div align="center">
    <strong>A list of practical resources for NemoClaw, the sandboxed OpenClaw runtime built on NVIDIA OpenShell.
    </strong>
    <br />
    <br />
</div>

<div align="center">

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
![Last Update](https://img.shields.io/github/last-commit/VoltAgent/awesome-nemoclaw?label=Last%20update&style=flat-square)
[![Discord](https://img.shields.io/discord/1361559153780195478.svg?label=&logo=discord&logoColor=ffffff&color=7389D8&labelColor=6A7EC2)](https://s.voltagent.dev/discord)

</div>

# Awesome NemoClaw

**NemoClaw** is an open-source runtime layer for running OpenClaw inside a controlled NVIDIA OpenShell sandbox. OpenClaw is the agent framework itself; NemoClaw adds sandboxing, policy-based network control, inference routing, and operational tooling around it. 

This open-source list collects practical presets, recipes, deployment patterns, and operational references to help you run NemoClaw in real-world setups.

## Start Here

Install NemoClaw and onboard an OpenClaw agent:

```console
$ curl -fsSL https://nvidia.com/nemoclaw.sh | bash
```

Connect to the sandbox and start chatting:

```console
$ nemoclaw my-assistant connect
```

References:

- **[Quickstart](https://docs.nvidia.com/nemoclaw/latest/get-started/quickstart.html)**

## Plugin Layout

High-level plugin structure used by `openclaw nemoclaw`:

```text
nemoclaw/
├── src/
│   ├── index.ts                    Plugin entry -- registers all commands
│   ├── cli.ts                      Commander.js subcommand wiring
│   ├── commands/
│   │   ├── launch.ts               Fresh install into OpenShell
│   │   ├── connect.ts              Interactive shell into sandbox
│   │   ├── status.ts               Blueprint run state + sandbox health
│   │   ├── logs.ts                 Stream blueprint and sandbox logs
│   │   └── slash.ts                /nemoclaw chat command handler
│   └── blueprint/
│       ├── resolve.ts              Version resolution, cache management
│       ├── fetch.ts                Download blueprint from OCI registry
│       ├── verify.ts               Digest verification, compatibility checks
│       ├── exec.ts                 Subprocess execution of blueprint runner
│       └── state.ts                Persistent state (run IDs)
├── openclaw.plugin.json            Plugin manifest
└── package.json                    Commands declared under openclaw.extensions
```

This is why NemoClaw feels split into two layers: a thin TypeScript plugin for UX/CLI and a blueprint execution layer for sandbox orchestration.

### Policy Presets

Ready-made network policy bundles for common services.

Important:

- Community presets in this repository are example baselines.
- Review and customize host, path, method, and binary constraints before production use.
- Replace placeholders like `your-subdomain` and `your-bucket`.

#### Official Presets (NVIDIA)

- **[`discord`](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/presets/discord.yaml)** - Discord API, gateway, CDN.
- **[`docker`](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/presets/docker.yaml)** - Docker Hub and NVIDIA registry.
- **[`huggingface`](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/presets/huggingface.yaml)** - Hugging Face Hub and inference.
- **[`jira`](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/presets/jira.yaml)** - Atlassian Cloud.
- **[`npm`](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/presets/npm.yaml)** - npm and Yarn registries.
- **[`outlook`](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/presets/outlook.yaml)** - Microsoft Graph and Outlook.
- **[`pypi`](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/presets/pypi.yaml)** - Python package endpoints.
- **[`slack`](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/presets/slack.yaml)** - Slack API and webhooks.
- **[`telegram`](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/presets/telegram.yaml)** - Telegram Bot API.

#### Community Presets (This Repo)

- **[`gitlab`](./presets/gitlab.yaml)** - GitLab API access via `/api/v4/**`.
- **[`notion`](./presets/notion.yaml)** - Notion API access via `/v1/**`.
- **[`linear`](./presets/linear.yaml)** - Linear GraphQL access via `/graphql`.
- **[`confluence`](./presets/confluence.yaml)** - Confluence and Atlassian API access with tenant scoping.
- **[`teams`](./presets/teams.yaml)** - Microsoft Teams and Graph API access.
- **[`zendesk`](./presets/zendesk.yaml)** - Zendesk API access with tenant placeholders.
- **[`sentry`](./presets/sentry.yaml)** - Sentry API and ingestion endpoints.
- **[`stripe`](./presets/stripe.yaml)** - Stripe API access via `/v1/**`.
- **[`cloudflare`](./presets/cloudflare.yaml)** - Cloudflare API access via `/client/v4/**`.
- **[`google-workspace`](./presets/google-workspace.yaml)** - OAuth, Gmail, Drive, and Calendar APIs.
- **[`aws`](./presets/aws.yaml)** - STS, S3, and Bedrock API access.
- **[`gcp`](./presets/gcp.yaml)** - OAuth, Cloud Storage, and Vertex AI APIs.
- **[`vercel`](./presets/vercel.yaml)** - Vercel deployment API access.
- **[`supabase`](./presets/supabase.yaml)** - Supabase REST, Auth, and Storage APIs.
- **[`neon`](./presets/neon.yaml)** - Neon API access via `/api/v2/**`.
- **[`algolia`](./presets/algolia.yaml)** - Algolia indexing and search API endpoints.
- **[`airtable`](./presets/airtable.yaml)** - Airtable API access via `/v0/**`.
- **[`hubspot`](./presets/hubspot.yaml)** - HubSpot CRM and OAuth API access.

Policy authoring references:

- **[Community preset guide](./presets/README.md)** - Hardening checklist and usage.
- **[Customize Network Policy](https://docs.nvidia.com/nemoclaw/latest/network-policy/customize-network-policy.html)** - Static and dynamic policy updates.
- **[`policy-add` and `policy-list` commands](https://docs.nvidia.com/nemoclaw/latest/reference/commands.html)** - Apply and inspect presets.
- **[Baseline policy template](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/openclaw-sandbox.yaml)** - Format reference.


### Agent Recipes

Task-oriented setups combining policy, routing, and operations.

- **[Approval-first web agent](https://docs.nvidia.com/nemoclaw/latest/network-policy/approve-network-requests.html)** - Unknown hosts require operator approval.
- **[Sandbox monitoring workflow](https://docs.nvidia.com/nemoclaw/latest/monitoring/monitor-sandbox-activity.html)** - Status, logs, TUI loop.
- **[Remote GPU assistant recipe](https://docs.nvidia.com/nemoclaw/latest/deployment/deploy-to-remote-gpu.html)** - Persistent remote sandbox.
- **[Telegram support bot recipe](https://docs.nvidia.com/nemoclaw/latest/deployment/set-up-telegram-bridge.html)** - Bot bridge into sandboxed agent.
- **[Runtime model-switching workflow](https://docs.nvidia.com/nemoclaw/latest/inference/switch-inference-providers.html)** - Switch model without restart.


### Templates

- **[Baseline sandbox policy](https://github.com/NVIDIA/NemoClaw/blob/main/nemoclaw-blueprint/policies/openclaw-sandbox.yaml)** - Main policy template.
- **[Official preset examples](https://github.com/NVIDIA/NemoClaw/tree/main/nemoclaw-blueprint/policies/presets)** - Upstream preset references.
- **[Community preset examples](./presets/README.md)** - This repo preset catalog.
- **[Sandbox image template](https://github.com/NVIDIA/NemoClaw/blob/main/Dockerfile)** - Container build structure.
- **[Service bootstrap script](https://github.com/NVIDIA/NemoClaw/blob/main/scripts/start-services.sh)** - Telegram and tunnel services.
- **[Remote bootstrap script](https://github.com/NVIDIA/NemoClaw/blob/main/scripts/brev-setup.sh)** - Remote host bootstrap.

### Example Projects

- **[NVIDIA/NemoClaw](https://github.com/NVIDIA/NemoClaw)** - Canonical implementation.
- **[Add your project](https://github.com/necatiozmen/awesome-nemoclaw/pulls)** - Share production-ready setups.


## Official Resources

- **[NemoClaw GitHub](https://github.com/NVIDIA/NemoClaw)** - Main source repository.
- **[Overview](https://docs.nvidia.com/nemoclaw/latest/about/overview.html)** - Product scope and value.
- **[How It Works](https://docs.nvidia.com/nemoclaw/latest/about/how-it-works.html)** - Plugin, blueprint, and sandbox lifecycle.
- **[Architecture](https://docs.nvidia.com/nemoclaw/latest/reference/architecture.html)** - Component layout and flow.
- **[Commands](https://docs.nvidia.com/nemoclaw/latest/reference/commands.html)** - CLI reference.
- **[Network Policies](https://docs.nvidia.com/nemoclaw/latest/reference/network-policies.html)** - Baseline egress model.
- **[Inference Profiles](https://docs.nvidia.com/nemoclaw/latest/reference/inference-profiles.html)** - Provider and model configuration.

## 🤝 Contributing

Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md).

Preferred submissions:

- practical preset files
- tested deployment recipes
- clear security and ops playbooks
- real example projects

Avoid low-signal link dumps. Curate for real usage.


## License

MIT License - see [LICENSE](LICENSE)

This is a curated list. Resources listed here reference projects created and maintained by their respective authors and teams, not by us. They are not security-audited and should be reviewed before production use.

If you find an issue with a listed resource or want your entry removed, please [open an issue](https://github.com/VoltAgent/awesome-nemoclaw/issues) and we'll take care of it promptly.
