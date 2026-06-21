# TCA Agent Skills

Reusable Agent Skills for building, reviewing, testing, and migrating Swift features that use Point-Free's Composable Architecture (TCA).

The `tca` skill is compatible with tools that implement the Agent Skills standard, including Gemini CLI and Codex. It uses progressive disclosure: the main skill defines current practices and routes the agent to focused references only when needed.

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

Invoke the skill explicitly as `$tca`, or ask for a task that matches its description, for example:

```text
Use $tca to review this reducer for cancellation and Swift 6 concurrency issues.
```

```text
Use $tca to migrate this navigation flow to current TCA presentation APIs.
```

## Version policy

The current references were audited against TCA 1.26.0. The skill requires agents to inspect the target project's resolved TCA version and verify official release and migration documentation for version-sensitive work.

## License and attribution

The repository is licensed under the MIT License. Reference examples are adapted from Point-Free's `swift-composable-architecture` case studies and retain their original MIT attribution in [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).

This project is not affiliated with or endorsed by Point-Free.
