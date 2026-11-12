# Tileflow agent skills

Agent Skills for building maps with [Tileflow](https://tileflow.dev).

| Skill | What it does |
| --- | --- |
| [`tileflow-map-builder`](skills/tileflow-map-builder/SKILL.md) | Adds, restyles, and verifies Tileflow maps in a repository. |
| [`tileflow-spatial-analysis`](skills/tileflow-spatial-analysis/SKILL.md) | Answers spatial questions about your data with Turf or DuckDB and shows the result on a Tileflow map. |
| [`tileflow-mapbox-migration`](skills/tileflow-mapbox-migration/SKILL.md) | Migrates a Mapbox GL JS, MapLibre GL JS, or MapTiler SDK JS map to Tileflow. |
| [`tileflow-google-maps-migration`](skills/tileflow-google-maps-migration/SKILL.md) | Migrates a Google Maps JavaScript app to Tileflow, including its coordinates, overlays, and services. |

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

Or copy a folder from `skills/` into your agent's skills directory.

## What the skills need

Node.js 22 or newer, a package manager, and network access to `tileflow.dev`, `api.tileflow.dev`, and
the npm registry. Local map work needs no Tileflow account or API key.

The skills do not log in, create keys, upload data, publish, or deploy unless you ask for that
specific action.

## License

[Apache-2.0](LICENSE)
