# GPO security baseline and deployment

> **Evidence review update, 28 August 2026:** the original screenshots include intermediate and incorrect states. The later `GBS-1` through `GBS-10` set confirms that the identified removable-storage, Defender, PowerShell logging, and IT-Dep link settings were corrected in the GPO editor. Effective application on `PC-1-WIN11` still requires `gpresult`, RSoP, functional USB testing, and Defender-status evidence. See the [screenshot evidence index](../net-secur_com_GPO/README.md).

## 1. Scope and design

The Group Policy rollout was performed as a staged workstation pilot. The common baseline is linked to the five department OUs below, not to the domain root and not to the `Domain Controllers` OU.

| Organizational unit | Common GPO | Department GPO |
|---|---|---|
| `B2B` | `Global_Security_Baseline` | `40--B2B-Security` |
| `Bugh` | `Global_Security_Baseline` | `40-Bugh-Security` |
| `Elfi` | `Global_Security_Baseline` | `40--Elfi-Security` |
| `IT-Dep` | `Global_Security_Baseline` | `40--ITdep-Security` |
| `SEO` | `Global_Security_Baseline` | `40--SEO-Security` |

The departmental GPO is placed above the common baseline in link order. Links are enabled and `Enforced` remains `No`. Existing `GPO for ITdep` and `GPO for SEO` objects were retained.

A Computer Configuration policy applies only when the target computer object is located in the linked OU. At the time of this update, `PC-1-WIN11` is the confirmed pilot computer in `OU=IT-Dep`; the other OU links are ready for departmental clients and still require per-client validation.

## 2. Preparation

1. Open **Group Policy Management** on the management server.
2. Expand **Forest: net-secur.com → Domains → net-secur.com**.
3. Back up the existing GPOs before editing or changing links.
4. Confirm the client computer object is in the intended departmental OU.
5. Create or select `Global_Security_Baseline`.
6. Link the same baseline to `B2B`, `Bugh`, `Elfi`, `IT-Dep`, and `SEO`.
7. Keep the baseline unlinked from the domain root and `Domain Controllers` during the pilot.
8. Link the matching department GPO to each OU and give it higher link priority than the baseline.
9. Leave `Enforced` disabled unless a documented conflict requires a different design.

## 3. Common baseline settings

### 3.1 Removable storage: allow data, block program execution

Policy path:

```text
Computer Configuration
→ Policies
→ Administrative Templates
→ System
→ Removable Storage Access
```

| Policy | State | Purpose |
|---|---|---|
| All Removable Storage classes: Deny all access | Disabled | Prevents the blanket block so approved users can read and write data |
| Removable Disks: Deny read access | Disabled | Allows files on removable disks to be viewed and copied |
| Removable Disks: Deny write access | Disabled | Allows files to be copied to removable disks |
| Removable Disks: Deny execute access | Enabled | Blocks direct execution of programs from removable disks |

This configuration is a usability/security compromise. It does not make files on USB media trustworthy; documents and copied executables still require Defender and application-control coverage.

The original `GPO-5.png` shows an incorrect blanket denial. It is superseded by `final/GBS-1.png` through `final/GBS-4.png`, which confirm read and write denial Disabled, blanket denial Disabled, and execute denial Enabled.

### 3.2 AutoPlay and AutoRun protection

Policy path:

```text
Computer Configuration
→ Policies
→ Administrative Templates
→ Windows Components
→ AutoPlay Policies
```

| Policy | State |
|---|---|
| Turn off AutoPlay | Enabled — All drives |
| Set the default behavior for AutoRun | Enabled — Do not execute any AutoRun commands |
| Disallow AutoPlay for non-volume devices | Enabled |

These settings prevent automatic launch of content when removable media or another AutoPlay-capable device is connected.

### 3.3 Microsoft Defender protection

The target baseline must prevent users or local software from disabling Microsoft Defender Antivirus and real-time protection. It also enables scanning of:

- removable drives;
- archive files;
- packed executables;
- content after the latest security-intelligence check where supported by the configured policy.

The Attack Surface Reduction rule below is configured in Block mode:

| ASR rule ID | Effect |
|---|---|
| `b2b3f03d-6a65-4f7b-a9c7-1c7ef74a9ba4` | Block untrusted and unsigned processes that run from USB |

The original evidence shows both Defender turn-off policies Enabled. The final `GBS-5.png` and `GBS-6.png` screenshots supersede that state and confirm both policies as Disabled. The effective client state must still be verified with `Get-MpComputerStatus` and `Get-MpPreference`.

### 3.4 UAC secure desktop

The User Account Control credential or consent prompt is moved to the secure desktop. This reduces the risk that an ordinary desktop process can imitate or interfere with the elevation prompt.

### 3.5 Advanced Audit Policy

Policy path:

```text
Computer Configuration
→ Policies
→ Windows Settings
→ Security Settings
→ Advanced Audit Policy Configuration
→ Audit Policies
```

| Subcategory | Setting | Purpose |
|---|---|---|
| Audit Logon | Success and Failure | Records successful and failed logons |
| Audit User Account Management | Success and Failure | Records account creation, deletion, and modification |
| Audit Process Creation | Success | Records process starts, normally as event 4688; the screenshot also has Failure selected, although it is not required for this objective |
| Audit Removable Storage | Success and Failure | Records permitted and denied access to removable storage |

Command-line capture is also enabled:

```text
Computer Configuration
→ Policies
→ Administrative Templates
→ System
→ Audit Process Creation
→ Include command line in process creation events = Enabled
```

Command lines can contain usernames, paths, arguments, and occasionally sensitive values. Access to Security logs and exported evidence must therefore be restricted.

## 4. Department-specific GPOs

### 4.1 B2B — `40--B2B-Security`

- Remote Desktop inbound connections are disabled.
- `Interactive logon: Machine inactivity limit` is set to `300` seconds.

Effect: reduces unnecessary remote-entry exposure and locks an unattended session after five minutes.

### 4.2 Bugh — `40-Bugh-Security`

- Controlled Folder Access is enabled in **Audit Mode**.
- Remote Desktop inbound connections are disabled.
- The inactivity limit is `300` seconds.

Effect: records applications that would be blocked from protected folders without immediately disrupting the department. Audit data must be reviewed before switching to Block mode.

### 4.3 Elfi — `40--Elfi-Security`

- PowerShell Script Block Logging is enabled in the final `GBS-7.png` evidence.
- PowerShell Module Logging is enabled for module name `*`.
- PowerShell execution remains available.

Effect: improves investigation visibility through events such as 4104 and 4103 while preserving legitimate automation.

### 4.4 IT-Dep — `40--ITdep-Security`

- Require Network Level Authentication for remote connections = Enabled.
- Always prompt for password upon connection = Enabled.
- PowerShell Script Block Logging is enabled in the final `GBS-9.png` evidence.
- PowerShell Module Logging for `*` = Enabled.

Effect: keeps the administrative department's remote-management capability while strengthening authentication and recording PowerShell activity.

### 4.5 SEO — `40--SEO-Security`

- Microsoft Defender Network Protection is enabled in **Block** mode.
- Remote Desktop inbound connections are disabled.
- The inactivity limit is `300` seconds.

Effect: blocks access to known malicious or suspicious network destinations and reduces workstation remote-entry exposure.

## 5. Validation

Run the following from an elevated command prompt on each test workstation:

```powershell
gpupdate /force
gpresult /r /scope computer
gpresult /h C:\Temp\gpresult.html
Get-MpComputerStatus
Get-MpPreference
```

Confirm that:

1. `Global_Security_Baseline` and the correct department GPO appear under applied computer policies.
2. No unexpected GPO is denied because of security filtering or an inaccessible path.
3. `Applications and Services Logs/Microsoft/Windows/PowerShell/Operational` contains expected 4103/4104 events where PowerShell logging applies.
4. The Security log contains expected 4624/4625, account-management, 4688, and removable-storage events.
5. Programs cannot run directly from removable disks, while ordinary files can still be read and written.
6. AutoRun and AutoPlay do not launch removable-media content automatically.
7. RDP and inactivity behavior match the department profile.
8. Defender remains healthy after the policy refresh.

The GPO-editor corrections are complete. Do not mark the baseline as fully enforced until the effective client configuration and functional tests match the target.

Use **Group Policy Results** or RSoP when the effective configuration differs from the documented link order.

## 6. Deliberately deferred controls

### BitLocker To Go

Not enabled yet because the current lab requirement allows normal read/write use of removable media and no complete corporate-media lifecycle has been established. Before enforcement, define:

- which removable drives are corporate assets;
- where recovery keys are escrowed;
- who can recover data;
- how exceptions and lost media are handled;
- how non-Windows compatibility is managed.

### USB device allowlist

Not enabled yet because an approved VID/PID or device-instance inventory has not been completed. Premature allowlisting can block keyboards, smart-card readers, storage devices, and other required peripherals. Build and test the inventory in Audit/Pilot scope before enforcement.

## 7. Remaining work

- Validate the baseline on at least one computer in every department OU.
- Review Controlled Folder Access audit events before considering Block mode.
- Confirm effective ASR and Network Protection states with Defender tooling.
- Standardize the double-hyphen naming inconsistency only through a controlled rename.
- Introduce Windows LAPS and privileged-account separation.
- Evaluate Windows Event Forwarding or another centralized log-review method.
- Test GPO restore from the backup and document rollback criteria.
