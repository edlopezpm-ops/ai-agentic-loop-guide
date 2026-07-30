# Manifest — where each collected file came from

`collected-md/` bundles every `.md` file found under `Documents`, `Desktop`,
`Downloads` (profile + OneDrive) on this machine. 30 source files were
reduced to **9 files** through two consolidation passes:

1. **Exact-duplicate removal** (byte-identical after normalizing line
   endings): 30 → 19 unique files.
2. **Version consolidation**: some files weren't byte-identical but were
   different revisions of the *same* governance document, carried forward
   across projects at different times. For those, only the most recent
   revision was kept:
   - `CODEX.md` existed in 3 revisions across projects (`FreeCAD Hull`,
     `FreeCAD_WHRackDesign`, `SQL-PY JOIN` all shared one older, shorter
     "V1"-style revision from 2026-06-27; `SF Demo Emulator` had a newer,
     far more developed "V2" revision from 2026-07-02, restructured to
     mirror `CLAUDE.md`'s own V2 format). The V2 revision was kept; the two
     older copies were dropped.
   - `CLAUDE.md` and `agents.md` had no revision conflicts — every instance
     found was already byte-identical, so each collapsed to one file in
     pass 1.

`collected-md/governance/` now holds exactly one canonical copy of each:
`CLAUDE.md`, `CODEX.md`, `agents.md` — no per-project name prefix, since
there's nothing left to disambiguate.

## Redactions applied before publishing

- One local PostgreSQL dev password (`postgres-json-demo/README.md`) →
  `<REDACTED>`. Local-only dev credential, never used in a real environment.
- One personal email address, embedded in `CODEX.md`'s proprietary-use
  notice → `<REDACTED_EMAIL>`.
- One hardware BIOS serial number (`Bluetooth_Diagnostic_Report.md`) →
  `<REDACTED_SERIAL>`.

## Final file list

| File | Origin |
|---|---|
| `collected-md/governance/CLAUDE.md` | Identical across every project checked; canonical copy from `MD files/CLAUDE.md` |
| `collected-md/governance/CODEX.md` | Newest ("V2") revision, from `SF Demo Emulator`; supersedes the older revision previously duplicated in `FreeCAD Hull`, `FreeCAD_WHRackDesign`, and `SQL-PY JOIN` |
| `collected-md/governance/agents.md` | Identical across every project checked; canonical copy from `_migration_logs/Madness-untracked-backup-before-rebase` |
| `collected-md/project-docs/Desktop__Bluetooth_Diagnostics__README.md` | `Desktop\Bluetooth_Diagnostics\README.md` |
| `collected-md/project-docs/Desktop__Bluetooth_Diagnostics__Reports__Bluetooth_Diagnostic_Report.md` | `Desktop\Bluetooth_Diagnostics\Reports\Bluetooth_Diagnostic_Report.md` (BIOS serial redacted) |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__cv-local-generator__README.md` | `VS Code\cv-local-generator\README.md` |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__cv-local-generator__docs__ai-json-prompt.md` | `VS Code\cv-local-generator\docs\ai-json-prompt.md` |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__edlopezpm-ops--Madness__README.md` | `VS Code\edlopezpm-ops--Madness\README.md` (also covers the identical `Madness\README.md`) |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__FreeCAD_Hull__README.md` | `VS Code\FreeCAD Hull\README.md` |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__FreeCAD_WHRackDesign__README.md` | `VS Code\FreeCAD_WHRackDesign\README.md` |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__postgres-json-demo__README.md` | `VS Code\postgres-json-demo\README.md` (password redacted) |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__SF_Demo_Emulator__README.md` | `VS Code\SF Demo Emulator\README.md` |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__SF_Demo_Emulator__docs__context-prompt.md` | `VS Code\SF Demo Emulator\docs\context-prompt.md` |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__SF_Demo_Emulator__docs__editing-guide.md` | `VS Code\SF Demo Emulator\docs\editing-guide.md` |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__SF_Demo_Emulator__docs__research-notes.md` | `VS Code\SF Demo Emulator\docs\research-notes.md` |
| `collected-md/project-docs/OneDrive__Documents__VS_Code__SQL-PY_JOIN__README.md` | `VS Code\SQL-PY JOIN\README.md` |

`project-docs/` keeps its full path-based names because those files are
genuinely different projects, not versions of one document — collisions
(multiple `README.md`) are real there and the prefix is load-bearing.

## MANIFEST.csv

A full machine-readable source→destination mapping is kept locally at
`MANIFEST.csv` but is **not published** (`.gitignore`d) — it's redundant
with this file and added no value to a reader beyond what's above.
