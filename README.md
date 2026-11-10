# Tileflow agent skills

Agent Skills for building maps with [Tileflow](https://tileflow.dev).

| Skill | What it does |
| --- | --- |
| [`tileflow-map-builder`](skills/tileflow-map-builder/SKILL.md) | Adds, restyles, and verifies Tileflow maps in a repository. |

## Install

With the [Skills CLI](https://github.com/vercel-labs/skills), which detects your agent:

```bash
npx skills add tileflow/skills
```

In Claude Code, as a plugin:

```text
/plugin marketplace add tileflow/skills
/plugin install tileflow@tileflow-skills
```

Or copy `skills/tileflow-map-builder` into your agent's skills directory.

## What the skill needs

Node.js 22 or newer, a package manager, and network access to `tileflow.dev`, `api.tileflow.dev`, and
the npm registry. Local map work needs no Tileflow account or API key.

The skill does not log in, create keys, upload data, publish, or deploy unless you ask for that
specific action.

## License

[Apache-2.0](LICENSE). See [NOTICE](NOTICE).
