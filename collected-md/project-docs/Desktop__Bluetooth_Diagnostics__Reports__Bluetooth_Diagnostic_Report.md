# Bluetooth / Random Reboot Diagnostic Report
Lenovo Legion Pro 5 16ADR10 (Type 83LT) — Windows 11 Home 25H2 (Build 26200.8875)

Report generated: 2026-07-16. This file is updated after every investigation step. Nothing below is removed once confirmed — only appended or corrected with a note.

---

## Executive Summary

Two **separate, unrelated** problems are present on this machine:

1. **Bluetooth unresponsiveness ("stays Connected but stops responding")** — almost certainly caused by the MediaTek Bluetooth controller hitting its **hardware offload / LE filter-pattern table limit**. Event ID 32 from `BTHUSB` was logged **thousands of times over the last 2 days** (query capped at 5000 — true count may be higher), not "dozens" as originally estimated. This is a saturation/overflow condition, not a driver crash.

2. **Silent reboots (black screen, no BSOD)** — only **1 of the 6** logged "unexpected shutdown" events actually produced a Windows bugcheck record. That one dump (6/10/2026) points to **`nvlddmkm.sys` (NVIDIA GPU driver)** crashing with a `purecall` exception while **`LegionSpace.exe`** (Lenovo's Legion Space app) was the active process — completely unrelated to Bluetooth/`BTHUSB`. The other 5 reboots left **zero forensic trace** (no dump, no WER bugcheck record, no WHEA record, no reliability-log entry in the preceding 10 minutes) — consistent with a true hard hang / power-domain reset rather than a normal Windows crash.

**Preliminary conclusion: the Bluetooth symptom and the reboot symptom do not currently share a confirmed common root cause.** They are being tracked and will be repaired separately. See confidence scores below.

---

## Hardware

| Item | Value |
|---|---|
| Model | Lenovo Legion Pro 5 16ADR10, Type 83LT |
| BIOS Vendor/Version | LENOVO / **RLCN31WW** |
| BIOS Release Date | 2025-09-23 |
| BIOS Serial | <REDACTED_SERIAL> |
| OS | Windows 11 Home, 25H2, Build 10.0.26200 (UBR/exact build confirmed via registry) |
| Bluetooth | MediaTek Bluetooth Adapter (USB\VID_0489&PID_E111) |
| Wi-Fi | MediaTek Wi-Fi 7 MT7925 Wireless LAN Card (PCI\VEN_14C3&DEV_7925) — combo module, shares silicon with BT |
| GPU (dGPU) | NVIDIA GeForce RTX 5060 Laptop GPU |
| GPU (iGPU) | AMD Radeon(TM) 610M |
| Sleep states supported | S3 Standby, Hibernate, Fast Startup (S0 Low Power Idle **not** supported by firmware) |

## Installed Drivers

| Device | Version | Driver Date | Provider | INF |
|---|---|---|---|---|
| MediaTek Bluetooth Adapter | **25.30.3.58** | 2025-11-19 | Mediatek Inc. | oem95.inf (mtkbtfilter.inf) |
| MediaTek Wi-Fi 7 MT7925 | **25.30.3.59** | 2025-12-10 | MediaTek, Inc. | oem58.inf (mtkwecx.inf) |
| NVIDIA GeForce RTX 5060 Laptop GPU | 32.0.15.9201 | 2026-02-14 | NVIDIA | — |
| AMD Radeon 610M | 32.0.21030.13004 | 2025-12-02 | AMD | — |

**Driver Store duplicates found** (stale versions left behind by prior updates, safe-to-clean candidates, not yet removed):
- `mtkbtfilter.inf` (Bluetooth): oem95 (active) + oem28 (stale duplicate)
- `mtkwecx.inf` (Wi-Fi): oem58 (active) + oem71, oem31 (stale duplicates)

**Installed Lenovo software:**
- Lenovo Now 4.7.0.48 — installed 2026-06-08
- Lenovo Vantage Service 5.1.2606.17 — installed 2026-06-30
- Legion Space 1.8.12.13 (path confirmed via crash/RestartManager log; not in Programs list snapshot yet)

**Device Manager:** No Bluetooth/Wi-Fi/GPU device shows a problem status code. All relevant devices report `Status: OK`. One BLE device instance (`BTHLEDEVICE\{A3FA5111-...}`) shows `Status: Unknown`, which simply means "not currently present/paired-but-out-of-range," not a driver error.

**Driver Verifier:** Not enabled on any driver (`verifier /query` → "No drivers are currently verified"). Historical crash was **not** induced by Driver Verifier stress testing.

## Event Viewer Findings

### BTHUSB Event ID 32 — "Hardware Offload limit is reached by the Bluetooth controller"
- Query capped at 5000 events and **hit the cap** within just 2 calendar days:
  - 2026-07-15: 2,057 occurrences
  - 2026-07-16: 2,943 occurrences
- Events fire in **bursts of one every 2–10 seconds** during active periods (confirmed from raw timestamps, e.g. 18:35:10–18:37:51 on 7/16 alone had 50 occurrences).
- Also observed: repeated "The remote adapter (xx:xx:xx:xx:xx:xx) is no longer a paired device. Its link key has been removed" (Event ID 10) — link keys being dropped and BLE devices un-pairing/re-pairing in rapid succession, at the exact same timestamps as the offload-limit warnings.
- This is consistent with the MediaTek controller's onboard LE advertisement-filter / pattern-match table (fixed hardware size) being continuously oversubscribed — new filter registrations are silently dropped once the limit is hit, and the repeated link-key churn suggests devices are being forced to re-negotiate.

### Kernel-Power 41 / EventLog 6008 — Unexpected shutdowns (6 total on record)
| Reboot detected at | Previous shutdown timestamp | Matching WER bugcheck record? |
|---|---|---|
| 2025-11-24 21:18 | 2025-11-24 21:03:54 | No |
| 2026-06-10 08:43 | 2026-06-10 08:43:01 | **Yes — 0x3B, see WinDbg section** |
| 2026-07-02 15:44 | 2026-07-02 15:34:59 | No |
| 2026-07-13 18:16 | 2026-07-13 18:02:22 | No |
| 2026-07-16 13:55 | 2026-07-16 13:33:17 | No |
| 2026-07-16 18:11 | 2026-07-16 17:55:41 | No |

Only 1 of 6 reboots left a Windows-level bugcheck trace. The other 5 have **no** preceding warning/error in Reliability Monitor in the 10 minutes prior, no WHEA record, and no WER record — i.e., Windows itself has no idea why it stopped. That pattern points toward a hard hang or power/EC-level reset rather than a software exception Windows could catch and log.

### WHEA-Logger (hardware machine-check errors)
**Zero events found.** No CPU, memory, or PCIe hardware-reported errors are on record. This argues against classic silicon-level hardware failure (CPU/RAM/PCIe) as the cause of the reboots, though it would not catch a true instantaneous power-cut/EC watchdog reset (Windows never gets a chance to log anything in that case).

### Application log — relevant non-Bluetooth findings
- **2026-07-14 10:08:20 AM** — `UDClientService.exe` (Lenovo, path `C:\Windows\System32\drivers\Lenovo\udc\Service\`) crashed with access violation (0xc0000005) in `UDCWindows.Shared.Native.dll`.
- **2026-07-13 22:49:55** and **2026-07-14 10:02:30** — `LegionSpace.exe` (v1.8.12.13) reported by RestartManager: "cannot be restarted - Application SID does not match Conductor SID" — a known Legion Space service/elevation-mismatch defect, not a full crash, but shows Legion Space has been unstable in the days surrounding the reboots.

## Reliability Monitor Findings
- Confirms the Windows Update history (BIOS-unrelated) and the two software faults above.
- **No reliability-log entries exist in the 10 minutes before any of the 6 reboots** — reinforces that these are abrupt, not preceded by escalating warnings.

## WinDbg Analysis

**Tooling note:** No classic Debugging Tools for Windows were preinstalled. The Microsoft.WinDbg Store package (v1.2606.22001.0) was found installed; its bundled `amd64\cdb.exe` was used instead of the GUI shell (the GUI shell did not process `-c` batch commands headlessly). Reading `C:\Windows\Minidump` requires admin rights — user explicitly approved a one-time elevated run (UAC-confirmed) to execute this analysis.

**Dump analyzed:** `C:\Windows\Minidump\061026-13375-01.dmp` (only minidump present on the system; `CrashDumpEnabled=3` i.e. Automatic Memory Dump is configured, so absence of other dumps means the other 5 reboots genuinely never reached the point of writing one).

```
BugCheck 3B — SYSTEM_SERVICE_EXCEPTION
Arg1: c0000002        (STATUS_NOT_IMPLEMENTED - purecall)
Arg2: fffff806da2b0e9f (faulting instruction address)
Arg3: fffffe8d5af97000 (context record)
Arg4: 0

PROCESS_NAME:  LegionSpace.ex[e]
FAULTING module (stack):  nvlddmkm.sys  (NVIDIA kernel-mode display driver)
STACK_TEXT (abridged):
  nt!RtlRaiseStatus+0x4f
  nt!purecall+0xe
  nvlddmkm+0x15e9600
FAILURE_BUCKET_ID: 0x3B_C0000002_nvlddmkm!unknown_function
Debug session time: 2026-06-10 08:43:06 UTC-4
System Uptime before crash: 15h 32m
```

**Interpretation:** This is a C++ "pure virtual function call" fault (`purecall`) inside NVIDIA's `nvlddmkm.sys`, triggered while Lenovo's `LegionSpace.exe` was the foreground process — almost certainly via an NVAPI/driver-escape call Legion Space makes for GPU control (overclocking, hybrid-graphics/refresh-rate switching, "Smart" performance modes, etc.). **This has no relationship to `BTHUSB`, `mtkbtfilter.inf`, or the Bluetooth stack.** No WHEA/TDR event accompanies it, meaning it's a driver logic bug, not a GPU hang or hardware fault.

The other 5 unexpected reboots have no dump to analyze. Their complete absence of any OS-level trace is itself diagnostic — see Root Cause Analysis.

## Lenovo Driver Comparison

**Caveat on sourcing:** Lenovo's official product page (`pcsupport.lenovo.com/.../legion-pro-5-16adr10/83lt/downloads`) is a JavaScript-rendered SPA that could not be fetched directly (timed out 3 times). The figures below come from a secondary driver-aggregator site (drvhub.net) cross-checked against an independent GitHub hardware-support thread for this exact model. **Treat as high-likelihood, not certain — verify directly in Lenovo Vantage or on the support page in a browser before acting.**

| Component | Installed | Latest found (secondary sources) | Behind? |
|---|---|---|---|
| BIOS | **RLCN31WW** (2025-09-23) | **RLCN32WW** (2025-11-25) — confirmed independently via a GitHub Legion-Linux hardware-support issue thread | **Yes — 1 revision behind** |
| MediaTek Bluetooth | **25.30.3.58** (2025-11-19) | **26.30.3.62** — found on drvhub.net's Legion Pro 5 16ADR10 driver page | **Yes — appears to be a newer driver generation (25.x → 26.x)** |
| MediaTek Wi-Fi 7 MT7925 | 25.30.3.59 (2025-12-10) | Not confirmed from an authoritative source | Unknown |
| NVIDIA GeForce RTX 5060 Laptop | 32.0.15.9201 (2026-02-14) | Not confirmed from an authoritative source | Unknown |

No official release notes could be retrieved for RLCN32WW or Bluetooth driver 26.30.3.62, so it cannot be confirmed from documentation alone that they specifically fix the offload-limit/LE-filter behavior seen here. However, the version gap itself — a full BIOS revision and what looks like a Bluetooth driver *generation* bump (25.x → 26.x) — is meaningful evidence on its own that Lenovo has shipped updates since this machine's drivers were installed.

## Root Cause Analysis

**These are two separate problems with two separate root causes. Do not treat them as one bug.**

**Hypothesis A — MediaTek BT LE offload/filter-table saturation (Bluetooth symptom)** — Confidence: **75%**
- Directly explains "shows Connected but stops responding, recovers after seconds/minutes."
- Explains why Wi-Fi is unaffected (shared silicon, but the Wi-Fi function block is independent of the BT LE filter table).
- Explains why the trackpad (not a Bluetooth device on this laptop) is unaffected.
- Corroborated by a real driver-generation gap: installed Bluetooth driver (25.30.3.58) is behind the version found for this model (26.30.3.62) — see Lenovo Driver Comparison.
- Root trigger candidate: a large number of paired/duplicate BLE bonds were observed (iPhone appears twice under two different addresses, JBL Xtreme 3 appears twice), plus continuous background LE scanning — together these are the most plausible way to keep re-filling a hardware filter table that has a fixed, small capacity.

**Hypothesis B — NVIDIA driver / Legion Space software fault (reboot symptom, ≥1 of 6 confirmed instances)** — Confidence: **55%** as the dominant cause of *all* 6 reboots, **95%** as the cause of the *one* reboot with a dump.
- The only forensic evidence available directly implicates nvlddmkm.sys + LegionSpace.exe, and Legion Space independently showed instability (RestartManager SID-mismatch errors) in the same week.
- The other 5 reboots produced no trace at all, which is unusual for a repeatable software bug (a `purecall` bugcheck should reliably dump every time) — suggesting those 5 may be a **more severe variant** of the same class (GPU/EC hang so complete that even the bugcheck handler cannot run), a **different cause entirely** (power delivery/EC watchdog, thermal), or that dump generation itself occasionally fails.
- NVIDIA driver version could not be confirmed against Lenovo's official latest — this remains an open thread.

**Hypothesis C — Hardware failure (GPU, power delivery, EC)** — Confidence: **20%**
- Zero WHEA entries argues against classical CPU/RAM/PCIe hardware faults.
- Cannot be ruled out for the 5 trace-less reboots specifically; a true EC/power-domain reset would not log anything by design.

**Hypothesis D — Windows 25H2 regression** — Confidence: **10%**
- No evidence points to a Windows component; both faulting modules identified so far (`nvlddmkm.sys`, `BTHUSB`/MediaTek stack) are OEM/IHV drivers, not Microsoft-authored code.

**Hypothesis E — Outdated BIOS/firmware** — Confidence: **40%** (as a contributing factor, not sole cause)
- Confirmed one revision behind (RLCN31WW installed vs RLCN32WW available, released 2025-11-25 — after the currently-installed BIOS date).
- BIOS/EC updates commonly address exactly the kind of trace-less hard-hang/reset behavior seen in 5 of the 6 reboots (power sequencing, EC watchdog tuning), which is why this is scored as a real contributing factor for Hypothesis B/C rather than for the Bluetooth symptom.

## Confidence Score Summary

| Hypothesis | Confidence |
|---|---|
| A. MediaTek BT LE filter-table saturation (Bluetooth symptom) | 75% |
| B. NVIDIA driver / Legion Space fault (reboot symptom) | 55% (all 6) / 95% (the 1 dumped instance) |
| C. Hardware failure (GPU/EC/power) | 20% |
| D. Windows 25H2 regression | 10% |
| E. Outdated BIOS/firmware (contributing factor) | 40% |

## Repair Steps

**Nothing has been changed on this system yet.** A PowerShell toolkit was built at `Desktop\Bluetooth_Diagnostics\` (01–06 read-only diagnostics + `Master_Run.ps1` + `07_Repair.ps1`). All eight scripts were syntax-validated and the read-only ones (01, 02, 03, 04, 06) were executed end-to-end successfully; `07_Repair.ps1`'s no-argument dry-run was also executed and confirmed it takes no action by default — it correctly identified the 3 stale driver-store packages (`oem28.inf`, `oem71.inf`, `oem31.inf`) without touching anything.

Proposed order, safest first, **awaiting your explicit go-ahead for each step**:

1. **Clean stale driver-store duplicates** (low risk, reversible) — `07_Repair.ps1 -Action CleanDriverStore -Execute`. Removes 3 orphaned INF packages; `pnputil` structurally cannot remove the in-use one.
2. **Disable Bluetooth adapter power-saving suspend** (low risk, reversible) — `07_Repair.ps1 -Action DisableBtPowerManagement -Execute`. Rules out/in power-management as a contributor to the "stops responding" symptom.
3. **Restart the Bluetooth stack** (trivial, reversible) — `07_Repair.ps1 -Action RestartBluetoothStack -Execute`. Quick way to clear the LE filter table right now without a reboot, and to see if the offload warnings stop firing afterward.
4. **Official Lenovo Bluetooth driver update** (25.30.3.58 → 26.30.3.62) — must be done via Lenovo Vantage or the official support page; this toolkit will only open those for you (`07_Repair.ps1 -Action OpenLenovoUpdatePages -Execute`), it will not silently install a driver.
5. **BIOS update** (RLCN31WW → RLCN32WW) — same: human-driven via Lenovo Vantage/BIOS Update utility, never scripted here. Recommended given it's confirmed one revision behind and BIOS/EC updates are the most common fix for trace-less hard-hang reboots like the 5 unexplained ones.
6. **NVIDIA/Legion Space investigation** — separate from the Bluetooth track. Given the one confirmed dump implicates `nvlddmkm.sys` + `LegionSpace.exe`, recommend checking for a Legion Space update/reinstall and confirming the NVIDIA driver is current, independent of the BIOS/Bluetooth work above.
7. **Registry modifications** — none identified as necessary. Not recommended unless a specific new finding requires one.

**No step above will be executed without your explicit confirmation, action by action.**

### Execution log (2026-07-16, user approved "proceed with the changes/fixes")

| Step | Result |
|---|---|
| 1. CleanDriverStore | **Success.** Removed 3 stale driver-store packages: `oem28.inf` (old `mtkbtfilter.inf`), `oem71.inf` and `oem31.inf` (old `mtkwecx.inf`). |
| 2. DisableBtPowerManagement | **No change made.** This adapter has no `MSPower_DeviceEnable` WMI entry at all. Root cause found during step 3: Windows flags this MediaTek combo radio as a **critical system device**, which is almost certainly why no independent power-management toggle exists for it. Not a script bug — a real platform constraint. If you want to check manually: Device Manager → MediaTek Bluetooth Adapter → Properties → Power Management tab (may not exist). |
| 3. RestartBluetoothStack | **Partial, then corrected.** First attempt (`Disable-PnpDevice`) failed with "Not supported." Retried via `pnputil /disable-device`, which returned an explicit refusal: *"Cannot disable critical system device."* This confirms the device is platform-protected from PnP-level disable. **Restarting the Bluetooth Support Service (`bthserv`) succeeded** and is the safe, effective substitute — the script was updated to do only this going forward, dropping the pnputil attempt so it no longer prints Windows' scary (but harmless) boot-risk warning on every future run. |
| 4. OpenLenovoUpdatePages | **Success.** Lenovo Vantage launched (confirmed running). The official support page URL was also issued to the default browser. **You still need to manually apply the Bluetooth driver (→ 26.30.3.62) and BIOS (→ RLCN32WW) updates through Vantage or the support page — this was intentionally not automated.** |

**Net effect right now:** driver store is clean, and the Bluetooth software stack (bthserv) has been freshly restarted. Re-ran `03_Bluetooth.ps1` immediately after (19:13) — no new Event ID 32 since 18:37:42, but that quiet period **started ~33 minutes before the restart**, so it can't yet be credited to the fix; the offload storm is bursty/intermittent rather than continuous, so this is inconclusive either way. Recommend monitoring over the next several hours (re-run `03_Bluetooth.ps1` periodically) to see whether bursts recur, before deciding whether the driver/BIOS update is still necessary or just a bonus fix.

## Commands Executed (evidence log, chronological)

1. `Get-CimInstance Win32_BIOS` — BIOS vendor/version/date/serial
2. `Get-CimInstance Win32_OperatingSystem` + registry `CurrentVersion` read — exact build/UBR
3. `Get-PnpDevice` filtered to Bluetooth/MediaTek — device inventory + status
4. `Get-PnpDevice | Where Status -ne 'OK'` — problem-code scan (none found on relevant hardware)
5. `powercfg /a` — supported sleep states
6. `verifier /query` — Driver Verifier state (inactive)
7. `Get-PnpDeviceProperty` (DriverVersion/DriverDate/DriverProvider/DriverInfPath) for BT + Wi-Fi adapters
8. `Get-WinEvent -FilterHashtable @{LogName='System';ProviderName='BTHUSB'}` — Event ID 32 pull
9. `Get-WinEvent -FilterHashtable @{LogName='System';Id=41,6008}` — Kernel-Power/unexpected shutdown history
10. `Get-WinEvent -FilterHashtable @{LogName='System';ProviderName='Microsoft-Windows-WHEA-Logger'}` — 0 results
11. `Get-ChildItem C:\Windows\Minidump` + `HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl` — dump inventory + config (CrashDumpEnabled=3)
12. `Get-WinEvent ... BTHUSB Id=32` (uncapped count) — 5000+ (capped) events across 2 days
13. `Get-CimInstance Win32_ReliabilityRecords` — general + BugCheck/Kernel-Power/Bluetooth filtered
14. `pnputil /enum-drivers` filtered for MediaTek/Bluetooth — driver store contents, duplicate INFs identified
15. Registry `Uninstall` key scan for Lenovo/MediaTek/Vantage — installed OEM software inventory
16. `Get-WinEvent ... Microsoft-Windows-WER-SystemErrorReporting` — confirmed only 1 of 6 reboots has a bugcheck record (0x3B)
17. Located `Microsoft.WinDbg` Store package and its bundled `amd64\cdb.exe`
18. **User-approved elevated run:** `cdb.exe -z <dump> -y srv*...msdl.microsoft.com... -c "!analyze -v; q"` → full analysis captured above
19. `Get-PnpDevice -Class Display` + properties — NVIDIA/AMD driver versions
20. `Get-WinEvent -FilterHashtable @{LogName='System';ProviderName='Display'}` — no TDR events found (only unrelated Auto-HDR toasts)
21. `Get-WinEvent -FilterHashtable @{LogName='Application'}` filtered for LegionSpace/nvlddmkm — RestartManager SID-mismatch entries found
22. `Get-CimInstance Win32_ReliabilityRecords` windowed ±10 min around each of the 6 reboot timestamps — no preceding entries found for any of them

## Rollback Procedure

No changes have been made to the system yet. Rollback for each proposed repair step, if applied:

1. **CleanDriverStore** — no rollback needed in normal operation (only orphaned, inactive packages are removed). If the active Bluetooth/Wi-Fi driver ever needs restoring from scratch, use Windows Update or reinstall from Lenovo's official package.
2. **DisableBtPowerManagement** — reverse by re-running `07_Repair.ps1` with the same action after flipping the script's `Enable` value back to `$true`, or manually via Device Manager → MediaTek Bluetooth Adapter → Properties → Power Management → re-check "Allow the computer to turn off this device to save power."
3. **RestartBluetoothStack** — self-resolving; the device is re-enabled by the same action within seconds.
4. **Bluetooth driver update (26.30.3.62)** — roll back via Device Manager → Update Driver → "Roll Back Driver" (available for 10 days post-update), or Device Manager → Uninstall → check "Delete the driver software" → let Windows reinstall the previous version from the driver store (not yet cleaned at that point).
5. **BIOS update (RLCN32WW)** — Lenovo Legion laptops support dual-BIOS/BIOS recovery; consult the official BIOS update package's included rollback instructions before flashing. This step will not be scripted or automated by Claude — it must be performed manually by the user following Lenovo's official instructions.
6. **NVIDIA/Legion Space changes** — standard driver rollback via Device Manager, or Legion Space reinstall from the Microsoft Store/Lenovo Vantage.

## Toolkit

This report now lives as part of a self-contained project folder at `Desktop\Bluetooth_Diagnostics\` (see `README.md` at that folder's root for the full layout):
- `Scripts\01_SystemInfo.ps1`, `02_Drivers.ps1`, `03_Bluetooth.ps1`, `04_EventLogs.ps1`, `06_LenovoDrivers.ps1` — read-only, safe to re-run anytime, each writes a timestamped log to `Scripts\Logs\`.
- `Scripts\05_Minidump.ps1` — read-only against dump contents; self-elevates (one UAC prompt) because `C:\Windows\Minidump` is admin-protected.
- `Scripts\Master_Run.ps1` — runs 01–06 in sequence and collects all logs into one timestamped run folder.
- `Scripts\07_Repair.ps1` — the only script capable of changing anything. Defaults to a no-op dry-run preview; requires both `-Action <name>` and `-Execute`, plus typing `YES` at a final prompt, before touching the system. Verified working via dry-run execution, and via the live execution log above.
- This report itself lives at `Reports\Bluetooth_Diagnostic_Report.md`.
