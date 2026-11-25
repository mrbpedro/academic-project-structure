# Rules for agents

Keep `AGENTS.md` and `CLAUDE.md` identical.

## Project structure

- **Root:** README, these rules, the .Rproj and configuration only. No loose data, scripts or documents.
- **`data/raw/`:** read-only. Every file has a row in `data/raw/SOURCES.md`.
- **`data/intermediate/` and `data/final/`:** written by scripts. Each file has exactly one writer script.
- **`scripts/`:** numbered pipeline. Each script starts with a Purpose / Reads / Writes header and runs from the project root with `Rscript --vanilla`.
- **`exploratory/`:** tests and drafts, with their outputs.
- **`manuscript/`:** current version only; earlier versions in `manuscript/versions/`, dated.
- **`submissions/`:** one folder per submission; frozen after it is sent.
- **`archive/`:** kept material. Not garbage.
- **`trash/`:** deletion candidates. Agents only move items here, with a row in `trash/READ_BEFORE_DELETING.md`. **Only the user deletes.**

## Working rules

- Never delete files.
- Never write to `data/raw/` or to a frozen submission folder.
- Never load a dataset, change it and save it under the same name.
- Record every move in `archive/MOVE_LOG.md`, after confirming the file at its destination.
- When running R, never save the workspace, and turn off the graphics device unless producing a figure.
- Ask before touching files modified in the last 24 hours or anything in a submission in progress.
