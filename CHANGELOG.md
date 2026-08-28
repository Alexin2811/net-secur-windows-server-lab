# Changelog

All notable changes to this training project will be documented in this file.

## [0.2.1] — 2026-08-28

### Added

- Twenty-five GPO rollout screenshots in `net-secur_com_GPO`.
- Evidence index with per-image descriptions and verification status.

### Security review

- Identified three unsafe captured states: blanket removable-storage denial, Defender Antivirus disabled, and real-time protection disabled.
- Identified PowerShell Script Block Logging as Not Configured in the captured Elfi and IT-Dep policies.
- Marked the final `40--ITdep-Security` link and effective client policy as requiring additional evidence.
- Updated the README, implementation notes, security review, baseline guide, and roadmap so documented claims match the available evidence.

## [0.2.0] — 2026-08-28

### Added

- Windows 11 client `PC-1-WIN11` to the domain lab and placed its computer object in `IT-Dep`.
- GPO backup before security-policy changes.
- Common `Global_Security_Baseline` linked to the five department OUs.
- Department-specific security GPOs for B2B, Bugh, Elfi, IT-Dep, and SEO.
- Removable-storage policy that allows file read/write but denies direct execution.
- AutoRun and AutoPlay protection for all drives and non-volume devices.
- Microsoft Defender removable-media scanning and USB ASR protection.
- UAC secure-desktop prompting.
- Advanced auditing for logons, account management, process creation, command lines, and removable storage.
- Department-specific RDP, inactivity, Controlled Folder Access, PowerShell logging, and Network Protection settings.
- Detailed deployment, validation, limitations, and rollback guidance in `docs/gpo-security-baseline.md`.

### Changed

- Project status, implementation notes, security review, and roadmap now reflect the completed GPO stage.
- Existing `GPO for ITdep` and `GPO for SEO` were retained alongside the new department security policies.

### Deferred

- BitLocker To Go until corporate-media ownership and recovery-key handling are defined.
- USB device allowlisting until an approved hardware inventory and test plan exist.
- Local administrator enforcement until privileged groups and break-glass access are tested.

## [0.1.0] — 2026-08-15

### Added

- Initial Net-Secur company and domain scenario.
- Windows Server domain-controller deployment documentation.
- Active Directory OU, user-template, user, and security-group structure.
- Screenshots documenting user creation and group membership.
- Initial security review and development roadmap.
