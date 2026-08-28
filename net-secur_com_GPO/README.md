# Group Policy screenshot evidence

This folder contains screenshots captured during the Net-Secur Group Policy rollout on 27–28 August 2026. The filenames are preserved to retain the original chronology and avoid a noisy rename-only commit.

A screenshot proves only the state visible in the editor at capture time. It does not prove that the GPO applied successfully to a workstation; effective policy still requires `gpresult`, RSoP, or Group Policy Results.

## Evidence status

- **Confirmed** — the visible setting matches the documented target.
- **Intermediate** — the screenshot records an earlier deployment step, not the final state.
- **Correction required** — the visible state conflicts with the intended security baseline.
- **Not confirmed** — the screenshot does not show enough information to support the claim.

## Architecture and linking

| File | Status | What it shows |
|---|---|---|
| [`GPO-2.png`](GPO-2.png) | Intermediate | `PC-1-WIN11` and `PC-WIN10X64` in the default `Computers` container before departmental placement |
| [`GPO-3.png`](GPO-3.png) | Confirmed | Existing `GPO for ITdep` linked to `IT-Dep`; link enabled, `Authenticated Users` filtering |
| [`GPO-4.png`](GPO-4.png) | Confirmed | Initial `Global_Security_Baseline` link to `IT-Dep`; link enabled and not enforced |
| [`28-1.png`](28-1.png) | Intermediate | Selection of the existing `Global_Security_Baseline` while adding another OU link |
| [`28-2.png`](28-2.png) | Confirmed with limitation | Common baseline and department links visible across the OU tree; Bugh link order shows baseline above `40-Bugh-Security` |
| [`28-3.png`](28-3.png) | Confirmed | Interactive logon inactivity limit set to 300 seconds |

### Limitation in `28-2.png`

At the time of this screenshot, the `IT-Dep` tree shows `Global_Security_Baseline` and the existing `GPO for ITdep`, but does not visibly show `40--ITdep-Security`. This limitation is superseded by `final/GBS-10.png`.

## Common baseline

| File | Status | What it shows |
|---|---|---|
| [`GPO-5.png`](GPO-5.png) | **Correction required** | `All Removable Storage classes: Deny all access = Enabled`; this blocks all removable-storage read and write access and conflicts with the requirement to allow file copying |
| [`GPO-6.png`](GPO-6.png) | **Correction required** | `Turn off Microsoft Defender Antivirus = Enabled`; despite the misleading double-negative name, this disables Defender Antivirus |
| [`GPO-7.png`](GPO-7.png) | **Correction required** | `Turn off real-time protection = Enabled`; this disables Defender real-time protection |
| [`GPO-8.png`](GPO-8.png) | Confirmed | UAC switches to the secure desktop when prompting for elevation |
| [`GPO-9.png`](GPO-9.png) | Confirmed | Standard users are prompted for credentials on the secure desktop |
| [`GPO-10.png`](GPO-10.png) | Confirmed | Audit Logon configured for Success and Failure |
| [`GPO-11.png`](GPO-11.png) | Confirmed | Audit User Account Management configured for Success and Failure |
| [`GPO-12.png`](GPO-12.png) | Confirmed with note | Audit Process Creation shows Success and Failure selected; Success supplies process-creation event 4688, while Failure is not required for the stated objective |
| [`GPO-13.png`](GPO-13.png) | Confirmed | Command-line data enabled for process-creation audit events |

## Department-specific policy

### Bugh

| File | Status | What it shows |
|---|---|---|
| [`Bugh-1.png`](Bugh-1.png) | Confirmed | Controlled Folder Access enabled in Audit Mode |
| [`Bugh-2.png`](Bugh-2.png) | Confirmed | Incoming Remote Desktop connections disabled |
| [`Bugh-3.png`](Bugh-3.png) | Confirmed | Machine inactivity limit set to 300 seconds |

### Elfi

| File | Status | What it shows |
|---|---|---|
| [`Elfi-1.png`](Elfi-1.png) | **Correction required** | PowerShell Script Block Logging is visibly `Not Configured`, not Enabled |
| [`Elfi-2.png`](Elfi-2.png) | Partially confirmed | PowerShell Module Logging is enabled, but the configured module list is not open in this screenshot |

### IT-Dep

| File | Status | What it shows |
|---|---|---|
| [`Itdep-1.png`](Itdep-1.png) | Confirmed | Network Level Authentication required for remote connections |
| [`Itdep-2.png`](Itdep-2.png) | Confirmed | Password prompt required upon RDP connection |
| [`Itdep-3.png`](Itdep-3.png) | **Correction required** | PowerShell Script Block Logging is visibly `Not Configured`, not Enabled |
| [`Itdep-4.png`](Itdep-4.png) | Confirmed | PowerShell Module Logging enabled with wildcard module value `*` |

### SEO

| File | Status | What it shows |
|---|---|---|
| [`SEO-1.png`](SEO-1.png) | Confirmed | Microsoft Defender Network Protection enabled in Block mode |

## Final correction evidence

| File | Final state confirmed |
|---|---|
| [`final/GBS-1.png`](final/GBS-1.png) | Removable Disks: Deny read access = Disabled |
| [`final/GBS-2.png`](final/GBS-2.png) | All Removable Storage classes: Deny all access = Disabled |
| [`final/GBS-3.png`](final/GBS-3.png) | Removable Disks: Deny write access = Disabled |
| [`final/GBS-4.png`](final/GBS-4.png) | Removable Disks: Deny execute access = Enabled |
| [`final/GBS-5.png`](final/GBS-5.png) | Turn off Microsoft Defender Antivirus = Disabled |
| [`final/GBS-6.png`](final/GBS-6.png) | Turn off real-time protection = Disabled |
| [`final/GBS-7.png`](final/GBS-7.png) | Elfi PowerShell Script Block Logging = Enabled |
| [`final/GBS-8.png`](final/GBS-8.png) | Elfi PowerShell Module Logging = Enabled, module value `*` |
| [`final/GBS-9.png`](final/GBS-9.png) | IT-Dep PowerShell Script Block Logging = Enabled; Module Logging `*` was also confirmed during the correction session |
| [`final/GBS-10.png`](final/GBS-10.png) | `40--ITdep-Security` linked at priority 1; all three IT-Dep links enabled and not enforced |

The original correction-required screenshots remain in the repository as chronological evidence and are superseded by the final set above.

## Client-side validation

| File | Client result confirmed |
|---|---|
| [`client-validation/cmd-1.png`](client-validation/cmd-1.png) | `40--ITdep-Security`, `Global_Security_Baseline`, and `Default Domain Policy` applied to `PC-1-WIN11` |
| [`client-validation/cmd-2.png`](client-validation/cmd-2.png) | Defender service, antivirus, real-time protection, behavior monitoring, and IOAV protection all report `True` |
| [`client-validation/cmd-3.png`](client-validation/cmd-3.png) | Defender health reconfirmed and `DisableRealtimeMonitoring = False` |

`GPO for ITdep` does not appear in the Computer Scope output, which is consistent with a GPO that has no applicable Computer Configuration settings. This does not affect validation of the security baseline.

## Optional future validation

1. Test removable-disk read, write, and execute behavior.
2. Verify PowerShell events 4103/4104 and Security audit events.
3. Add screenshots or reports for AutoRun/AutoPlay and ASR effective state.

## Recommended validation evidence

Run on the target workstation from an elevated terminal:

```powershell
gpupdate /force
gpresult /r /scope computer
gpresult /h C:\Temp\gpresult.html
Get-MpComputerStatus
Get-MpPreference
```

Review the generated report before publishing it because paths, usernames, security filtering, and command-line information may be sensitive.
