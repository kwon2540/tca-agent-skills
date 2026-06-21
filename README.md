# TCA Agent Skills

Reusable Agent Skills for building, reviewing, testing, and migrating Swift features that use Point-Free's Composable Architecture (TCA).

The `tca` skill is compatible with tools that implement the Agent Skills standard, including Gemini CLI, Codex, Claude Code, and Google Antigravity. It uses progressive disclosure: the main skill defines current practices and routes the agent to focused references only when needed.

## Contents

```text
skills/
└── tca/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
```

The references cover reducers, observation, dependencies, effects, cancellation, bindings, navigation, shared state, recursion, and reusable components.

## Install with Gemini CLI

Install for your user account so the skill is available in every project:

```bash
gemini skills install \
  https://github.com/kwon2540/tca-agent-skills.git \
  --path skills/tca \
  --scope user
```

Use `--scope workspace` instead when a project should own the installation.

## Install with Codex

Invoke `$skill-installer` and ask:

```text
Install the tca skill from
https://github.com/kwon2540/tca-agent-skills/tree/main/skills/tca
```

Codex installs user skills under `~/.agents/skills`, making the skill available across repositories.

## Install with Claude Code

Install for your user account so the skill is available in every project:

```bash
npx skills add kwon2540/tca-agent-skills \
  --skill tca \
  --agent claude-code \
  --global
```

Claude Code discovers personal skills under `~/.claude/skills`. Remove `--global` for a project-only installation under `.claude/skills`.

After installation, invoke the skill with `/tca` or ask Claude for a task that matches the skill description. Claude Code detects skill changes live, but you should restart it if the top-level skills directory did not exist when the session started. See the [Claude Code skills documentation](https://code.claude.com/docs/en/skills) for discovery and invocation details.

## Install with Google Antigravity

Install globally for both the Antigravity app and Antigravity CLI:

```bash
npx skills add kwon2540/tca-agent-skills \
  --skill tca \
  --agent antigravity \
  --agent antigravity-cli \
  --global
```

Remove `--global` for a project-only installation. Project installations use the interoperable `.agents/skills` directory.

Restart Antigravity after installation and run `/skills` to verify that `tca` is available. Antigravity CLI does not currently provide its own terminal command for installing skills, so this repository uses the cross-agent [`skills` CLI](https://github.com/vercel-labs/skills). See the [Antigravity skills documentation](https://antigravity.google/docs/skills) for discovery behavior.

## Use

Invoke the skill using your agent's syntax, such as `$tca` in Codex or `/tca` in Claude Code and Antigravity, or ask for a task that matches its description. For example:

```text
Use $tca to review this reducer for cancellation and Swift 6 concurrency issues.
```

```text
Use $tca to migrate this navigation flow to current TCA presentation APIs.
```

## Version policy

The current references were audited against TCA 1.26.0. The skill requires agents to inspect the target project's resolved TCA version and verify official release and migration documentation for version-sensitive work.

## Content and copyright policy

This repository was independently assembled from publicly available, MIT-licensed source code in Point-Free's [`swift-composable-architecture`](https://github.com/pointfreeco/swift-composable-architecture) repository and independently written guidance based on public documentation.

This repository does **not** contain, reproduce, or distribute “The Point-Free Way” skill documents, member-only videos or transcripts, paid downloads, or any other subscription-only Point-Free material.

Point-Free, The Composable Architecture, and related names belong to their respective owners. Their names are used only to identify the open-source project this skill supports. This repository is not affiliated with, sponsored by, or endorsed by Point-Free.

If you are a rights holder and believe any material in this repository has been included improperly, please [open a copyright concern](https://github.com/kwon2540/tca-agent-skills/issues/new) identifying the specific file and the basis of the claim. The maintainer will promptly review a credible notice and remove, replace, or correct the material when appropriate.

## License and attribution

The repository is licensed under the MIT License. Reference examples are adapted from Point-Free's `swift-composable-architecture` case studies and retain their original MIT attribution in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
