# Project roadmap

## Phase 1 — Active Directory foundation

- [x] Deploy Windows Server 2022 in VMware.
- [x] Install Active Directory Domain Services.
- [x] Promote `DC-1` to a domain controller.
- [x] Create the domain and OU structure.
- [x] Create templates, users, and security groups.
- [ ] Correct the account-state and template-membership findings.
- [ ] Separate privileged and standard administrative accounts.

## Phase 2 — Client integration

- [x] Add the `PC-1-WIN11` Windows 11 virtual machine.
- [x] Join the client to `net-secur.com`.
- [x] Move its computer object to `OU=IT-Dep`.
- [ ] Document DNS, authentication, and connectivity tests.
- [ ] Verify domain sign-in for one user from each OU.
- [ ] Add at least one pilot workstation to every department OU.

## Phase 3 — Centralized policy

- [x] Back up existing GPOs before policy changes.
- [x] Create `Global_Security_Baseline`.
- [x] Link the baseline to `B2B`, `Bugh`, `Elfi`, `IT-Dep`, and `SEO`.
- [x] Exclude the domain root and `Domain Controllers` OU from the workstation pilot.
- [x] Correct and document removable-media read/write/execute policy in the GPO editor.
- [ ] Verify AutoRun/AutoPlay protection and removable-media behavior on a client.
- [x] Configure UAC secure-desktop controls.
- [x] Correct and document Microsoft Defender Antivirus and real-time protection policy states.
- [x] Verify effective Defender Antivirus and real-time protection states on `PC-1-WIN11`.
- [ ] Verify selected ASR states during a future extended test.
- [x] Configure Advanced Audit Policy and process command-line logging.
- [x] Verify the IT-Dep security GPO link and enable the missing Script Block Logging settings.
- [x] Validate effective computer policy with `gpresult` on `PC-1-WIN11`.
- [ ] Capture safe evidence from one client in each department.
- [ ] Define domain password and account-lockout policy separately.
- [ ] Test restoration of a backed-up GPO.

## Phase 4 — Department policy maturation

- [ ] Review Bugh Controlled Folder Access audit events and define allowed applications.
- [ ] Verify SEO Network Protection in Block mode.
- [ ] Verify Elfi and IT-Dep PowerShell 4103/4104 logging.
- [ ] Confirm RDP denial in B2B, Bugh, and SEO.
- [ ] Confirm IT-Dep RDP requires NLA and a new password prompt.
- [ ] Standardize the single/double-hyphen GPO naming convention.

## Phase 5 — File and access services

- [ ] Create departmental shared folders.
- [ ] Apply share and NTFS permissions through security groups.
- [ ] Validate permitted and denied access with test users.
- [ ] Document the access-control model.

## Phase 6 — Operations and security

- [ ] Configure Windows LAPS.
- [ ] Enforce local administrator membership only after a tested break-glass design exists.
- [ ] Configure Windows Event Forwarding or another centralized log-review method.
- [ ] Define Security-log size and retention.
- [ ] Evaluate BitLocker To Go after creating a recovery-key and corporate-media process.
- [ ] Evaluate a USB device allowlist after completing the approved hardware inventory.
- [ ] Configure backup and test restore procedures.
- [ ] Add PowerShell scripts for repeatable validation and evidence collection.
