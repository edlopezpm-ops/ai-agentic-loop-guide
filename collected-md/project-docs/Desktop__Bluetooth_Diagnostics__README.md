# Bluetooth_Diagnostics

Root-cause investigation and repair toolkit for the Bluetooth unresponsiveness and random-reboot issues on this Lenovo Legion Pro 5 16ADR10 (Type 83LT), Windows 11 25H2.

## Folder layout

```
Bluetooth_Diagnostics/
├── README.md                          <- you are here
├── Reports/
│   └── Bluetooth_Diagnostic_Report.md <- full findings, root cause analysis, confidence scores, repair/rollback plan
└── Scripts/
    ├── 01_SystemInfo.ps1              <- BIOS, Windows build, sleep states, Driver Verifier status (read-only)
    ├── 02_Drivers.ps1                 <- BT/Wi-Fi/GPU driver versions, driver-store contents, installed Lenovo software (read-only)
    ├── 03_Bluetooth.ps1                <- BTHUSB Event ID 32/10 frequency, paired device inventory (read-only)
    ├── 04_EventLogs.ps1                <- Kernel-Power 41/6008, WHEA, WER bugcheck record, Reliability Monitor (read-only)
    ├── 05_Minidump.ps1                 <- Runs !analyze -v against every dump in C:\Windows\Minidump (read-only, self-elevates once via UAC)
    ├── 06_LenovoDrivers.ps1             <- Compares installed vs. latest-known Lenovo driver/BIOS versions (read-only)
    ├── 07_Repair.ps1                    <- The ONLY script that changes anything. No-op by default; requires -Action <name> -Execute plus a typed "YES" per action
    ├── Master_Run.ps1                   <- Runs 01-06 in sequence, collects all logs into one timestamped run folder
    └── Logs/                            <- Timestamped output from every script run (git-ignored if this becomes a repo)
```

## Quick start

```powershell
cd Scripts
.\Master_Run.ps1          # full read-only diagnostic sweep, ~1 min
.\07_Repair.ps1            # dry-run preview of every available repair action, makes no changes
```

To apply a specific repair once you've reviewed the report:
```powershell
.\07_Repair.ps1 -Action CleanDriverStore -Execute
.\07_Repair.ps1 -Action RestartBluetoothStack -Execute
.\07_Repair.ps1 -Action OpenLenovoUpdatePages -Execute
```
Each `-Execute` run still requires typing `YES` at a confirmation prompt before it does anything.

## Status as of 2026-07-16

Two separate problems identified, tracked independently in the report:
1. **Bluetooth unresponsiveness** — MediaTek BT LE hardware offload/filter-table saturation (`BTHUSB` Event ID 32, thousands/day). Driver-store cleanup and a `bthserv` restart have been applied; a newer Bluetooth driver (26.30.3.62) is available via Lenovo Vantage but not yet installed (manual step, intentionally not automated).
2. **Silent reboots** — only 1 of 6 unexpected shutdowns produced a bugcheck; that dump implicates `nvlddmkm.sys` (NVIDIA GPU driver) + `LegionSpace.exe`, unrelated to Bluetooth. BIOS is one revision behind (RLCN31WW → RLCN32WW available). Not yet resolved — still open.

See `Reports/Bluetooth_Diagnostic_Report.md` for full evidence, WinDbg output, confidence scores, and the complete execution/rollback log.
