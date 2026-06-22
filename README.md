# TCA Agent Skills

Reusable Agent Skills for building, reviewing, testing, and migrating Swift features that use Point-Free's Composable Architecture (TCA).

The `tca` skill is compatible with tools that implement the Agent Skills standard, including Google Antigravity, GitHub Copilot in Visual Studio Code, Codex, Claude Code, and supported Gemini CLI environments. It uses progressive disclosure: the main skill defines current practices and routes the agent to focused references only when needed.

Each installation section provides both a user/global option for all projects and a project-only option for a single repository.

## Contents

```text
skills/
└── tca/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
```

The references cover reducers, observation, view actions, dependencies, effects, cancellation, bindings, navigation, shared state, recursion, and reusable components.

## Install with Google Antigravity

Google is transitioning consumer Gemini CLI users to Antigravity CLI.

### User/global installation

Install for both the Antigravity app and Antigravity CLI:

```bash
npx skills add kwon2540/tca-agent-skills \
  --skill tca \
  --agent antigravity \
  --agent antigravity-cli \
  --global
```

### Project-only installation

Run from the repository root:

```bash
npx skills add kwon2540/tca-agent-skills \
  --skill tca \
  --agent antigravity \
  --agent antigravity-cli
```

Project installations use the interoperable `.agents/skills` directory.

Restart Antigravity after installation and run `/skills` to verify that `tca` is available. Antigravity CLI does not currently provide its own terminal command for installing skills, so this repository uses the cross-agent [`skills` CLI](https://github.com/vercel-labs/skills). See the [Antigravity skills documentation](https://antigravity.google/docs/skills) for discovery behavior.

## Install with Gemini CLI (limited legacy support)

> [!IMPORTANT]
> On June 18, 2026, Gemini CLI stopped serving requests for free individual users and Google AI Pro and Ultra consumer accounts. Google directs those users to Antigravity CLI. The instructions below are retained only for environments where Gemini CLI remains available. See [Google's transition announcement](https://developers.googleblog.com/an-important-update-transitioning-gemini-cli-to-antigravity-cli/).

### User/global installation

Install for your user account so the skill is available in every project:

```bash
gemini skills install \
  https://github.com/kwon2540/tca-agent-skills.git \
  --path skills/tca \
  --scope user
```

### Project-only installation

Run from the repository root:

```bash
gemini skills install \
  https://github.com/kwon2540/tca-agent-skills.git \
  --path skills/tca \
  --scope workspace
```

## Install with Codex

The following commands require GitHub CLI 2.90.0 or later.

### User/global installation

Install for your user account so the skill is available in every project:

```bash
gh skill install kwon2540/tca-agent-skills tca \
  --agent codex \
  --scope user
```

### Project-only installation

Run from the repository root:

```bash
gh skill install kwon2540/tca-agent-skills tca \
  --agent codex \
  --scope project
```

User installations use `~/.agents/skills`; project installations use `.agents/skills`. As an alternative for a user installation, invoke `$skill-installer` in Codex and ask it to install `https://github.com/kwon2540/tca-agent-skills/tree/main/skills/tca`. See the [Codex Agent Skills documentation](https://developers.openai.com/codex/skills) for discovery behavior.

## Install with GitHub Copilot in Visual Studio Code

This method requires GitHub CLI 2.90.0 or later because `gh skill` is currently in public preview. Update GitHub CLI first if `gh skill` is not available.

### User/global installation

Install for your user account so the skill is available in every VS Code workspace:

```bash
gh skill install kwon2540/tca-agent-skills tca \
  --agent github-copilot \
  --scope user
```

### Project-only installation

For a project-only installation, run the following from the repository root:

```bash
gh skill install kwon2540/tca-agent-skills tca \
  --agent github-copilot \
  --scope project
```

User installations use `~/.copilot/skills`; project installations use the interoperable `.agents/skills` directory.

In Copilot Chat, type `/skills` to verify that `tca` is available. Invoke it with `/tca`, or ask Copilot for a task that matches the skill description. See the [VS Code Agent Skills documentation](https://code.visualstudio.com/docs/agent-customization/agent-skills) and [`gh skill install` manual](https://cli.github.com/manual/gh_skill_install) for current discovery and installation behavior.

## Install with Claude Code

### User/global installation

Install for your user account so the skill is available in every project:

```bash
npx skills add kwon2540/tca-agent-skills \
  --skill tca \
  --agent claude-code \
  --global
```

### Project-only installation

Run from the repository root:

```bash
npx skills add kwon2540/tca-agent-skills \
  --skill tca \
  --agent claude-code
```

Claude Code discovers personal skills under `~/.claude/skills` and project skills under `.claude/skills`.

After installation, invoke the skill with `/tca` or ask Claude for a task that matches the skill description. Claude Code detects skill changes live, but you should restart it if the top-level skills directory did not exist when the session started. See the [Claude Code skills documentation](https://code.claude.com/docs/en/skills) for discovery and invocation details.

## Use

Invoke the skill using your agent's syntax, such as `$tca` in Codex or `/tca` in GitHub Copilot, Claude Code, and Antigravity, or ask for a task that matches its description. For example:

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
