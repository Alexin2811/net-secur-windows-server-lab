# Security review

## Current findings

### 1. Copied accounts may inherit the disabled state

The user-creation screenshot shows **Account is disabled** selected. This is correct for a reusable template, but a real employee account must be enabled deliberately after its properties and group memberships have been reviewed.

**Action:** inspect every non-template account and enable only accounts that are ready for use.

### 2. Template accounts appear in operational groups

The group-membership overview includes the `Template ...` accounts. Templates should not receive operational access because they are provisioning objects rather than users.

**Action:** remove templates from operational groups and keep every template disabled.

### 3. Initial-password workflow needs confirmation

The template-copying screen shows **User cannot change password**. This setting may be unsuitable for normal employees and can prevent a secure first-sign-in workflow.

**Action:** for regular users, normally require a password change at first sign-in unless the lab scenario explicitly requires a managed non-interactive account.

### 4. Group naming should match group purpose

The `RDS-` prefix normally suggests Remote Desktop Services access. If these groups are intended only to represent departmental membership, a purpose-based prefix such as `GG-` may be clearer.

**Action:** document whether the groups grant RDS access or identify department membership, then standardize their names.

### 5. GPO naming is inconsistent

The implemented department policy names use both single and double hyphens, for example `40-Bugh-Security` and `40--B2B-Security`.

**Action:** retain the working names during the pilot, then perform a controlled rename after evidence and references have been updated.

## Control status after screenshot review

### Staged scope

`Global_Security_Baseline` is linked only to the five department OUs. It is not linked to the domain root or `Domain Controllers`, limiting the blast radius of an incorrect workstation policy.

Department GPOs are linked separately with higher priority and without `Enforced`. This design reduces duplication and keeps exceptions visible.

### Removable media and automatic execution — corrected in GPO editor

The target design allows users to read and write ordinary files on removable disks while denying direct program execution. The original `GPO-5.png` shows an incorrect blanket denial; final screenshots `GBS-1` through `GBS-4` confirm that read, write, and blanket denial are Disabled while execute denial is Enabled. AutoPlay/AutoRun, Defender removable scanning, USB ASR, and effective client behavior still require separate evidence.

**Benefit:** lowers the probability of automatic or direct malware execution while preserving required file-transfer workflows.

**Residual risk:** files can still be copied and opened locally. Documents, scripts, signed malware, and user-assisted execution remain possible, so Defender, ASR, user awareness, and future application control are still required.

### Microsoft Defender — corrected in GPO editor

The original captured configuration shows both Defender turn-off policies Enabled. Final screenshots `GBS-5.png` and `GBS-6.png` confirm that both are now Disabled. SEO Network Protection in Block mode is separately confirmed by `SEO-1.png`. Effective Defender health must still be verified on the client.

**Benefit:** raises resistance to common malware delivery and malicious network destinations.

**Residual risk:** enforcement state must be verified on the client. Exclusions, signature currency, cloud-delivered protection, and tamper protection still need explicit review.

### UAC

Elevation prompts use the secure desktop.

**Benefit:** makes spoofing or interfering with the elevation prompt more difficult.

**Residual risk:** UAC is not an administrative-account separation mechanism. Users with local administrator rights still have broad authority after consent.

### Auditing — editor configuration confirmed, effective events unverified

The baseline records successful and failed logons, account management, process creation, process command lines, and removable-storage access.

**Benefit:** improves troubleshooting and investigation and provides evidence of security-relevant activity.

**Residual risk:** command lines can contain sensitive arguments, logs may roll over, and local logs can be altered by administrators. Configure retention and centralized collection.

### Department controls

- B2B: RDP disabled and five-minute inactivity lock.
- Bugh: Controlled Folder Access in Audit Mode, RDP disabled, and five-minute inactivity lock.
- Elfi: Script Block Logging and Module Logging for `*` are enabled in the final evidence.
- IT-Dep: RDP requires NLA and a password prompt; Script Block Logging and Module Logging for `*` are enabled.
- SEO: Defender Network Protection in Block mode, RDP disabled, and five-minute inactivity lock.

Controlled Folder Access remains in Audit Mode so legitimate applications can be identified before enforcement.

## Deferred controls

### BitLocker To Go

BitLocker To Go was not enabled because the lab still permits normal read/write use of removable media and does not yet have a documented corporate USB-media lifecycle or tested recovery-key escrow.

Enabling it prematurely can deny access to legitimate data, create recovery dependencies, and complicate use on systems that do not support the encryption workflow.

**Prerequisites:** approved corporate drives, recovery-key storage, ownership and exception procedures, recovery tests, and compatibility rules.

### USB device allowlist

A USB device allowlist was not enabled because no complete VID/PID or device-instance inventory exists. An incomplete allowlist can block storage devices, keyboards, smart-card readers, and other required peripherals.

**Prerequisites:** approved hardware inventory, test OU, emergency recovery path, and documented process for new devices.

### Restricted Groups / local administrators

Local administrator enforcement was not included in the first stage. An incorrect `Restricted Groups` configuration can replace existing local group membership and remove required administrative access.

**Prerequisites:** separate administrative groups, tested break-glass access, and staged deployment.

## Priority follow-up

1. Generate `gpresult /h` evidence on `PC-1-WIN11`.
2. Verify effective Defender health and perform the removable-disk read/write/execute test.
3. Verify expected Security and PowerShell events.
4. Test each department policy on at least one computer in its OU.
5. Review Controlled Folder Access audit findings.
6. Configure Windows LAPS and separate standard from privileged accounts.
7. Implement Windows Event Forwarding or another protected central log destination.
8. Test GPO backup restoration and document rollback criteria.

## Repository safety

Do not commit:

- passwords or password screenshots;
- environment files;
- private keys or certificates;
- recovery keys;
- unattended-installation files containing credentials;
- raw audit exports containing sensitive command-line arguments;
- exported Group Policy backups before checking them for sensitive paths or scripts;
- VMware disks, memory snapshots, or saved-state files.
