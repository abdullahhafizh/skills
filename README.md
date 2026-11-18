# skills

One GitHub repository, `abdullahhafizh/skills`. Each skill is a directory `skills/<name>/`.

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

## About

Install a skill from this repository into an agent, or add a directory when you have another skill to publish.

`skills/<name>/` is one skill. `SKILL.md` in that directory is the procedure the agent loads. `LICENSE` states the terms for that skill. `README.md` in the same directory is the guide for people.

The [catalog](#catalog) lists every skill in the repository.

## Features

- Each skill is the directory `skills/<name>/`, with `SKILL.md`, `README.md`, and `LICENSE`.
- The install command takes that directory name, so you add one skill from this repository.

## Installation

Install any skill in this repository when you want an agent to load that skill.

### Requirements

- Node.js and npm, so `npx` can run
- An agent that loads a skill after the `skills` CLI installs it

1. Install Node.js if `npx` is not on your PATH.
2. Choose `<name>` from the [catalog](#catalog).
3. Run:

```bash
npx skills add abdullahhafizh/skills --skill <name>
```

Example, using a name from the [catalog](#catalog):

```bash
npx skills add abdullahhafizh/skills --skill correcting-bahasa-spelling
```

4. Open a new agent session so it loads the skill you installed.

The command finished when the CLI exits. In the new session, ask for the task described in that skill's README.

If `npx` is missing, install Node.js and run the command again. If the agent session was already open, open a new one. If the CLI reports an unknown skill, use a directory name that exists under `skills/`.

## Usage

Follow the guide linked from the [catalog](#catalog) table. The agent follows the `SKILL.md` you installed.

### More Examples

Examples for a skill stay in that skill's directory. The guide column of the [catalog](#catalog) table links to that directory.

## Catalog

| Skill | Directory | Guide | Procedure | License |
|---|---|---|---|---|
| `code-precheck` | `skills/code-precheck/` | [skills/code-precheck/README.md](skills/code-precheck/README.md) | [skills/code-precheck/SKILL.md](skills/code-precheck/SKILL.md) | [skills/code-precheck/LICENSE](skills/code-precheck/LICENSE) |
| `code-review` | `skills/code-review/` | [skills/code-review/README.md](skills/code-review/README.md) | [skills/code-review/SKILL.md](skills/code-review/SKILL.md) | [skills/code-review/LICENSE](skills/code-review/LICENSE) |
| `correcting-bahasa-spelling` | `skills/correcting-bahasa-spelling/` | [skills/correcting-bahasa-spelling/README.md](skills/correcting-bahasa-spelling/README.md) | [skills/correcting-bahasa-spelling/SKILL.md](skills/correcting-bahasa-spelling/SKILL.md) | [skills/correcting-bahasa-spelling/LICENSE](skills/correcting-bahasa-spelling/LICENSE) |
| `judging-skill` | `skills/judging-skill/` | [skills/judging-skill/README.md](skills/judging-skill/README.md) | [skills/judging-skill/SKILL.md](skills/judging-skill/SKILL.md) | [skills/judging-skill/LICENSE](skills/judging-skill/LICENSE) |

## Documentation

This file lists the skills. `skills/<name>/README.md` is the guide for that skill. `skills/<name>/SKILL.md` is the procedure the agent loads.

## Architecture

The repository root holds the catalog. Each skill is one directory under `skills/`.

- `README.md` lists the skills
- `LICENSE` is the license for this repository
- `skills/<name>/README.md` is the guide for that skill
- `skills/<name>/SKILL.md` is the procedure the agent loads
- `skills/<name>/LICENSE` is the license for that skill

## Contributing

Contributions are welcome. This repository has no separate contributing guide.

To add another skill, create a new directory `skills/<name>/` and add `SKILL.md` there. Add `README.md` and `LICENSE` in that directory, then add a row to the [catalog](#catalog) table.

Open an issue or a pull request on the `abdullahhafizh/skills` repository.

### Development Setup

Clone the repository and edit the Markdown. Development has no package install and no build.

```bash
git clone https://github.com/abdullahhafizh/skills.git
cd skills
```

### Running Tests

This repository has no automated test command.

## Roadmap

- [x] List `correcting-bahasa-spelling` at `skills/correcting-bahasa-spelling/`
- [x] List `judging-skill` at `skills/judging-skill/`

## Acknowledgments

- abdullahhafizh, copyright 2026. See [`LICENSE`](LICENSE).

## License

skills is licensed under the MIT license. See the [`LICENSE`](LICENSE) file for more information.

Each skill directory has its own license file. Open the license link in the [catalog](#catalog) table.
