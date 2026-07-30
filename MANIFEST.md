# Manifest — where each collected file came from

`collected-md/` bundles every `.md` file found under `Documents`, `Desktop`,
`Downloads` (profile + OneDrive) on this machine, **deduplicated by content**
(line-ending differences ignored) and with security-relevant values redacted.
30 source files collapsed to 18 unique files — 12 were duplicates (mostly
`CLAUDE.MD`/`agents.md` copies shared across several local project folders;
one pair differed only by CRLF vs LF line endings).

Redacted before publishing:
- One local PostgreSQL dev password (`postgres-json-demo/README.md`) →
  `<REDACTED>`. Local-only dev credential, never used in a real environment,
  but no reason to exist in a public repo either.
- One personal email address, embedded in three `CODEX.MD` files as part of
  a proprietary-use notice → `<REDACTED_EMAIL>`.
- One hardware BIOS serial number (`Bluetooth_Diagnostic_Report.md`) →
  `<REDACTED_SERIAL>`.

| Original path (relative to `C:\Users\<user>\`) | Stored as | Duplicate of an earlier row? |
|---|---|---|
| `Desktop\Bluetooth_Diagnostics\README.md` | `project-docs/Desktop__Bluetooth_Diagnostics__README.md` | No |
| `Desktop\Bluetooth_Diagnostics\Reports\Bluetooth_Diagnostic_Report.md` | `project-docs/Desktop__Bluetooth_Diagnostics__Reports__Bluetooth_Diagnostic_Report.md` | No |
| `OneDrive\Documents\MD files\CLAUDE.md` | `governance/OneDrive__Documents__MD_files__CLAUDE.md` | No |
| `OneDrive\Documents\VS Code\_migration_logs\...\agents.md` | `governance/..._migration_logs__Madness-untracked-backup-before-rebase__agents.md` | No |
| `OneDrive\Documents\VS Code\cv-local-generator\CLAUDE.MD` | *(same as MD files/CLAUDE.md)* | Yes |
| `OneDrive\Documents\VS Code\cv-local-generator\docs\ai-json-prompt.md` | `project-docs/...cv-local-generator__docs__ai-json-prompt.md` | No |
| `OneDrive\Documents\VS Code\cv-local-generator\README.md` | `project-docs/...cv-local-generator__README.md` | No |
| `OneDrive\Documents\VS Code\DataAnalyticsPy\CLAUDE.MD` | *(same as MD files/CLAUDE.md)* | Yes |
| `OneDrive\Documents\VS Code\edlopezpm-ops--Madness\agents.md` | *(same as _migration_logs/agents.md — identical content, different line endings)* | Yes |
| `OneDrive\Documents\VS Code\edlopezpm-ops--Madness\CLAUDE.MD` | *(same as MD files/CLAUDE.md)* | Yes |
| `OneDrive\Documents\VS Code\edlopezpm-ops--Madness\README.md` | `project-docs/...edlopezpm-ops--Madness__README.md` | No |
| `OneDrive\Documents\VS Code\FreeCAD Hull\CLAUDE.MD` | *(same as MD files/CLAUDE.md)* | Yes |
| `OneDrive\Documents\VS Code\FreeCAD Hull\CODEX.MD` | `governance/...FreeCAD_Hull__CODEX.MD` | No |
| `OneDrive\Documents\VS Code\FreeCAD Hull\README.md` | `project-docs/...FreeCAD_Hull__README.md` | No |
| `OneDrive\Documents\VS Code\FreeCAD_WHRackDesign\CLAUDE.MD` | *(same as MD files/CLAUDE.md)* | Yes |
| `OneDrive\Documents\VS Code\FreeCAD_WHRackDesign\CODEX.MD` | *(same as FreeCAD Hull/CODEX.MD)* | Yes |
| `OneDrive\Documents\VS Code\FreeCAD_WHRackDesign\README.md` | `project-docs/...FreeCAD_WHRackDesign__README.md` | No |
| `OneDrive\Documents\VS Code\Madness\agents.md` | *(same as _migration_logs/agents.md)* | Yes |
| `OneDrive\Documents\VS Code\Madness\CLAUDE.MD` | *(same as MD files/CLAUDE.md)* | Yes |
| `OneDrive\Documents\VS Code\Madness\README.md` | *(same as edlopezpm-ops--Madness/README.md)* | Yes |
| `OneDrive\Documents\VS Code\postgres-json-demo\README.md` | `project-docs/...postgres-json-demo__README.md` (password redacted) | No |
| `OneDrive\Documents\VS Code\SF Demo Emulator\CLAUDE.MD` | *(same as MD files/CLAUDE.md)* | Yes |
| `OneDrive\Documents\VS Code\SF Demo Emulator\CODEX.MD` | `governance/...SF_Demo_Emulator__CODEX.MD` | No |
| `OneDrive\Documents\VS Code\SF Demo Emulator\docs\context-prompt.md` | `project-docs/...SF_Demo_Emulator__docs__context-prompt.md` | No |
| `OneDrive\Documents\VS Code\SF Demo Emulator\docs\editing-guide.md` | `project-docs/...SF_Demo_Emulator__docs__editing-guide.md` | No |
| `OneDrive\Documents\VS Code\SF Demo Emulator\docs\research-notes.md` | `project-docs/...SF_Demo_Emulator__docs__research-notes.md` | No |
| `OneDrive\Documents\VS Code\SF Demo Emulator\README.md` | `project-docs/...SF_Demo_Emulator__README.md` | No |
| `OneDrive\Documents\VS Code\SQL-PY JOIN\CLAUDE.MD` | *(same as MD files/CLAUDE.md)* | Yes |
| `OneDrive\Documents\VS Code\SQL-PY JOIN\CODEX.MD` | `governance/...SQL-PY_JOIN__CODEX.MD` | No |
| `OneDrive\Documents\VS Code\SQL-PY JOIN\README.md` | `project-docs/...SQL-PY_JOIN__README.md` | No |

Full machine-readable version: [`MANIFEST.csv`](MANIFEST.csv).
