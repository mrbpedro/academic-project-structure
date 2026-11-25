# academic-project-structure

An agent skill for Claude and Codex that keeps academic research projects organized: it scaffolds new projects, audits and reorganizes existing ones without breaking replication, and runs periodic checks.

It is designed especially for quantitative research built on data and scripts, with tooling details written for R with Quarto or LaTeX. The folder structure, versioning, and archive rules also work for qualitative projects. It works in a plain folder, including one synced to cloud storage. Git, Overleaf, and Python are not required.

## The problem it addresses

Research folders tend to decay in the same ways:

- raw downloads with no record of where they came from;
- several scripts that load, change and overwrite the same dataset, so it can no longer be rebuilt;
- scripts that silently depend on a saved R workspace;
- manuscript versions, slides and exploratory outputs piling up in the project root;
- nobody remembering which files the analysis actually uses.

The skill's rules come from reorganizing a real project with these problems, not from a generic template.

## What it does

| Mode | When | What happens |
|---|---|---|
| **A. New project** | Starting a project | Asks a few questions, creates the folder structure and seed files |
| **B. Existing project** | A folder that has grown messy | Read-only inventory and lineage, then phased moves from safest to riskiest, each approved by you |
| **C. Periodic check** | Every few weeks | Read-only report of what has drifted from the rules |

Core rules:

1. The project root holds only README, agent rules, the `.Rproj` and configuration.
2. Raw data is read-only and enters with a row in `SOURCES.md`.
3. Each derived file is written by exactly one script. No load-modify-save under the same name.
4. No script depends on a saved workspace.
5. `archive/` keeps things; `trash/` holds deletion candidates. The agent never deletes.
6. Every move is logged.

## Structure it creates

```
project/
├── README.md
├── AGENTS.md
├── CLAUDE.md
├── project.Rproj
├── data/
│   ├── raw/               # read-only, with SOURCES.md
│   ├── intermediate/
│   └── final/
├── scripts/               # numbered pipeline
├── exploratory/
├── output/
│   ├── figures/
│   └── tables/
├── manuscript/
│   └── versions/
├── presentations/
├── submissions/
├── source_documents/
├── references/
├── archive/               # with MOVE_LOG.md
└── trash/                 # with READ_BEFORE_DELETING.md
```

## Install

Clone the repository, then copy the skill folder into your agent's skills directory.

```bash
git clone https://github.com/mrbpedro/academic-project-structure.git
```

**Claude**, for all your projects:

```bash
mkdir -p ~/.claude/skills
cp -r academic-project-structure/skills/academic-project-structure ~/.claude/skills/
```

For a single project, copy it to `.claude/skills/` inside that project instead.

**Codex**, for all your projects:

```bash
mkdir -p ~/.agents/skills
cp -r academic-project-structure/skills/academic-project-structure ~/.agents/skills/
```

For a single repository, copy it to `.agents/skills/` inside that repository instead. Codex picks up new skills automatically; restart it if the skill does not appear.

## Use

Ask in plain language, for example:

- "Set up a new research project for a paper on X."
- "Audit this project folder and propose a reorganization. Do not move anything yet."
- "Run the periodic check on this project."

In Mode B the skill never moves or edits anything without showing you the exact list first.

## Repository layout

```
skills/academic-project-structure/
├── SKILL.md
└── templates/
    ├── AGENTS.md
    ├── SOURCES.md
    ├── MOVE_LOG.md
    ├── READ_BEFORE_DELETING.md
    ├── script_header.R
    └── project.Rproj
```

## Related work

The structure draws on established conventions for reproducible research:

- [TIER Protocol](https://www.projecttier.org/tier-protocol/) for documenting empirical research in the social sciences.
- [rrtools](https://github.com/benmarwick/rrtools) and the research compendium idea for R projects.

## License

MIT. See [LICENSE](LICENSE).
