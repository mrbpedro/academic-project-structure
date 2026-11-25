---
name: academic-project-structure
description: Use when starting a quantitative research project (R, Quarto, LaTeX) or auditing and reorganizing an existing one, to prevent loose files in the project root and loss of data provenance.
---

# Academic project structure

Goal: every file in the project has a place and a known role. Nobody should have to open a file to find out what it is, where it came from, or what uses it.

The skill has three modes. Identify which one applies before acting:

- **Mode A, new project:** create the structure and the seed files.
- **Mode B, existing project:** audit and reorganize without breaking replication.
- **Mode C, periodic check:** verify that the rules still hold.

Respond in the user's language. Keep answers concise and structured.

## Standard structure

```
project/
├── README.md              # what the project is, folder tree, how to run it
├── AGENTS.md              # rules for agents
├── CLAUDE.md              # identical copy of AGENTS.md
├── project.Rproj
├── data/
│   ├── raw/               # read-only; exactly as downloaded
│   │   └── SOURCES.md     # one row per raw file
│   ├── intermediate/      # written by scripts; can be rebuilt
│   └── final/             # the datasets the analysis reads
├── scripts/               # numbered pipeline: 01_, 02_, ...
├── exploratory/           # tests, drafts and their outputs
├── output/
│   ├── figures/
│   └── tables/
├── manuscript/            # current version only
│   └── versions/          # earlier versions, dated
├── presentations/
├── submissions/           # one folder per submission, frozen
├── source_documents/      # reports, methodological notes, source PDFs
├── references/
├── archive/               # kept material; not garbage
│   └── MOVE_LOG.md
└── trash/                 # deletion candidates; only the user deletes
    └── READ_BEFORE_DELETING.md
```

Adapt the names to the project but keep the roles. Do not assume git, Overleaf or Python: the default is R with Quarto or LaTeX, in a folder that may be synced to cloud storage. Templates for the seed files are in `templates/`.

## Rules

### Root
1. The root holds only README, AGENTS.md, CLAUDE.md, the .Rproj and configuration files. Any other loose file in the root is in the wrong place.

### Data
2. **Raw is read-only.** No script writes to `data/raw/`. Manual corrections become code in a script, never an edit to the file.
3. **Raw data enters with a record.** At download time, add a row to `SOURCES.md`: file | source | indicator or table | URL | download date | downloaded by | notes. No row in SOURCES.md, no file.
4. **One file, one writer.** Each derived file is written by exactly one script. Loading a dataset, changing it and saving it under the same name is forbidden: each stage writes a new file (`panel_01_clean`, `panel_02_weighted`).
5. **Object name equals file name.** An `.RData` file called `df_x.RData` holds an object called `df_x`. Prefer `.rds`, which holds a single object.
6. Data that no script reads does not stay in `data/`: it goes to `archive/`.

### Scripts
7. Pipeline scripts are numbered in execution order and start with a header:
   ```r
   # Purpose: ...
   # Reads:  data/raw/x.xlsx, data/intermediate/y.rds
   # Writes: data/intermediate/z.rds
   ```
8. Paths are always relative to the project root (working directory at the root, or `here::here()`). No absolute paths, no paths from another machine.
9. **No workspace.** In the .Rproj: `RestoreWorkspace: No` and `SaveWorkspace: No`. No script depends on an object it does not create or load itself. The test is running it with `Rscript --vanilla`.
10. Pipeline scripts contain no interactive calls (data editors, point-and-click plot builders). Those belong in `exploratory/`.
11. Exploratory work starts in `exploratory/` and writes its outputs there. It moves to `scripts/` only when adopted, and then follows rules 4 and 7.

### Text and versions
12. `manuscript/` holds only the current version. When a new version is saved, the previous one goes to `manuscript/versions/` with the date in its name (`YYYY-MM-DD_description`). No `_v2`, `_final`, `_final2`.
13. File names have no spaces or accented characters, and dates use YYYY-MM-DD.
14. Each submission or revision round has its own folder in `submissions/` (`journal_YYYY-MM-DD/`, `journal_R1/`). After it is sent, the folder is frozen: read-only.
15. On submission, freeze the final datasets too: copy them to `archive/frozen_data_YYYY-MM-DD/` with a `CHECKSUMS.md` (file | md5 | size | date).

### Keeping and discarding
16. `archive/` keeps what has historical value. `trash/` receives deletion candidates, with one row per item in `READ_BEFORE_DELETING.md` (name | origin | size | why it is safe to delete | where the surviving copy is, for duplicates).
17. **The agent never deletes.** It only moves items to `trash/`. The user deletes.
18. Every move is recorded in `archive/MOVE_LOG.md` (date | from | to | size | reason), and only after the file is confirmed at its destination.

### Agents
19. When running R: never save the workspace (`--no-save` or `--vanilla`) and turn off the graphics device when no figure is being produced, so that no `Rplots.pdf` is created.
20. If more than one agent works in the project, each states what it is doing and in which folder. None moves files while another is running code.

## Qualitative projects

The structure and the rules on the root, versions, archive and trash apply unchanged. Adapt the data rules:

- `data/raw/` holds recordings, transcripts, field notes, documents and images, exactly as collected. Still read-only.
- `SOURCES.md` records, per item: what it is, who or where it came from, collection date, collected by, consent or access terms.
- `data/intermediate/` holds anonymized transcripts, coded excerpts and exports from analysis software (NVivo, Atlas.ti, MAXQDA).
- Rules 4, 7, 9 and 10 (single writer, script headers, workspace, interactive calls) apply only where scripts exist.
- Never move, copy or rename files that identify participants without asking the user first.

## Mode A: new project

1. Ask, in a single round: title and topic; co-authors; expected data sources; destination (journal, conference, report); manuscript format (Quarto, LaTeX or Word); where the folder will live.
2. Create the standard structure, adjusted to the answers.
3. Create the seed files from `templates/`:
   - `README.md` with the folder tree and the role of each folder;
   - `AGENTS.md` and `CLAUDE.md`, identical, with the rules above summarized and a "Project structure" section;
   - `data/raw/SOURCES.md` with the table header;
   - `archive/MOVE_LOG.md` and `trash/READ_BEFORE_DELETING.md` with their headers;
   - the `.Rproj` with the workspace turned off;
   - `scripts/00_run_all.R`, which calls the scripts in order.
4. Show the tree that was created and stop. Do not write analysis scripts unless asked.

## Mode B: existing project

Principle: understand first, then propose, and only then move. Each phase requires the user's explicit approval before the next one starts.

1. **Inventory, read-only.** Count files by type and by folder. Read any existing rules (AGENTS.md, CLAUDE.md, including those in subfolders) and follow them.
2. **Who uses what.** For each data file, find what reads it and what writes it (script:line). The search must cover:
   - every kind of script and document (.R, .qmd, .Rmd, .tex, .md);
   - the literal name, the stem without extension, and names built with `paste0`, `sprintf`, `file.path`, `list.files`, `Sys.glob`;
   - helper functions that read files;
   - matching anchored on the opening quote, to avoid substring false positives.
3. **Lineage.** Trace each final dataset back to the raw files. Flag: datasets with no originating script, files with more than one writer, inputs that live outside the project, scripts that depend on a hidden workspace, scripts that fail `parse()`.
4. **"Read by a script" is not "useful to the project".** Check whether the variable each file feeds reaches the analysis, the text or the supplementary material. When in doubt, the file stays.
5. **Protect before moving.** Copy the final datasets to `archive/frozen_data_YYYY-MM-DD/` with md5 checksums. Verify those checksums at the end of every phase.
6. **Move in phases, from safest to riskiest:**
   1. documents and old versions that no script reads;
   2. data that no script reads;
   3. md5-identical duplicates, to `trash/`, keeping the copy the scripts point to;
   4. raw files that are only read, editing the read lines;
   5. intermediate files with a single writer, editing reads and writes in the same pass and using the same path in both;
   6. last, and only outside delivery periods, datasets with several writers and anything the current submission reads.
7. **In every phase:** an exact file-by-file list for approval; `mv -n`, one item at a time; md5 verification; an entry in the log.
8. **When editing scripts:** back up the scripts first, with md5; get the diff approved before applying it; validate (the new path resolves to the same md5, `identical()` between the old and new read, no old name left, `parse()` compared against the backup, only the planned lines changed); write an undo script guarded by md5.
9. **Never run scripts that overwrite datasets in order to validate.** Validation is static and by equivalent reads.
10. **Leave active material alone.** Folders of a submission in progress and files modified in the last 24 hours are touched only after asking the user.
11. At the end, write `DATA_LINEAGE.md` and `DATA_INDEX.md` in the root, update AGENTS.md and CLAUDE.md, and leave a written plan for whatever was postponed.

Content findings that surface along the way (a missing source, a mislabeled variable, a mismatch between text and data) are reported separately, without changing anything, and with what is confirmed kept apart from what is inferred.

## Mode C: periodic check

Read-only. Report, in a short table:

- loose files in the root that should not be there;
- raw files with no row in SOURCES.md;
- derived files with more than one writer;
- scripts with no Reads/Writes header, with an absolute path, or that depend on the workspace;
- more than one version of the same document outside `versions/`;
- a hidden `.RData` workspace, `Rplots.pdf`, `~$` lock files and compilation leftovers;
- md5 duplicates.

Propose a destination for each item and wait for approval.
