# Implementation notes

## 1. Project objective

The lab models the internal Windows domain of **Net-Secur**, a fictional company that provides outsourced IT and information-security services.

The project establishes a maintainable Active Directory structure and demonstrates domain-client integration, centralized workstation security, department-specific policy, auditing, and staged validation.

## 2. Virtual environment

| Component | Configuration |
|---|---|
| Hypervisor | VMware Workstation |
| Domain controller | `DC-1.net-secur.com` |
| Server operating system | Windows Server 2022 |
| Server role | Active Directory Domain Services |
| Forest and domain | `net-secur.com` |
| Client | `PC-1-WIN11`, Windows 11 |
| Parent organizational unit | `net-secur` |

## 3. Active Directory objects

The following child organizational units were created under `OU=net-secur`:

| OU | Security group | Template pattern |
|---|---|---|
| `B2B` | `RDS-B2B-Users` | `Template B2B` |
| `Bugh` | `RDS-Bugh-Users` | `Template Bugh` |
| `Elfi` | `RDS-Elfi-Users` | `Template Elfi` |
| `IT-Dep` | `RDS-IT-dep-Users` | `Template IT-Dep` |
| `SEO` | `RDS-SEO-Users` | `Template SEO` |

Each OU contains four lab user accounts in addition to its template account.

## 4. User provisioning method

User accounts were created by copying a template account in **Active Directory Users and Computers**. This method provides consistent account properties and group membership.

The template account is intentionally disabled. After copying a template, the new employee account must be reviewed to ensure that:

- the first name, last name, display name, and UPN are correct;
- the account is enabled when it is ready for use;
- the user is required to change the initial password at first sign-in where appropriate;
- group membership matches the employee's role;
- the template itself is not included in an operational access group.

## 5. Client integration

The Windows 11 computer `PC-1-WIN11` was joined to `net-secur.com`. Its computer object was moved from the default `Computers` container to `OU=IT-Dep,OU=net-secur,DC=net-secur,DC=com`.

This move is required for Computer Configuration settings linked to `IT-Dep` to apply to the client. A computer left in the default `Computers` container would not receive GPOs linked only to the departmental OU.

## 6. Group Policy deployment

Before editing links and settings, the existing GPOs were backed up.

The common `Global_Security_Baseline` was created as a workstation pilot and linked to:

- `B2B`;
- `Bugh`;
- `Elfi`;
- `IT-Dep`;
- `SEO`.

It was deliberately not linked to the domain root or the `Domain Controllers` OU. Each departmental OU also has a separate security GPO for role-specific requirements:

| OU | Department GPO |
|---|---|
| B2B | `40--B2B-Security` |
| Bugh | `40-Bugh-Security` |
| Elfi | `40--Elfi-Security` |
| IT-Dep | `40--ITdep-Security` |
| SEO | `40--SEO-Security` |

The department GPO has higher link priority than `Global_Security_Baseline`; links are enabled and not enforced. Existing `GPO for ITdep` and `GPO for SEO` objects remain present.

The common baseline now covers:

- removable-media read/write access with direct execution blocked;
- AutoPlay and AutoRun protection;
- Microsoft Defender and real-time protection;
- removable-drive, archive, and packed-executable scanning;
- an ASR rule that blocks untrusted and unsigned processes launched from USB;
- UAC prompting on the secure desktop;
- logon, account-management, process-creation, command-line, and removable-storage auditing.

Department policy adds RDP restrictions, automatic inactivity locking, Controlled Folder Access in Audit Mode, PowerShell logging, Network Protection, or strengthened administrative RDP depending on the OU.

See [GPO security baseline and deployment](gpo-security-baseline.md) for exact policy values and validation steps.

## 7. Verification checklist

### Active Directory and client

- [x] The `net-secur.com` domain is visible in Active Directory Users and Computers.
- [x] The parent and child OUs are present.
- [x] Departmental security groups exist.
- [x] Lab user objects have been created.
- [x] Users are visible in the appropriate group membership lists.
- [x] `PC-1-WIN11` has joined the domain.
- [x] The client computer object has been moved to `IT-Dep`.
- [ ] All regular user accounts have been checked for enabled/disabled state.
- [ ] Template accounts have been removed from operational groups.
- [ ] DNS name resolution and domain sign-in have been documented on the client.

### Group Policy

- [x] Existing GPOs were backed up before changes.
- [x] `Global_Security_Baseline` is linked to all five department OUs.
- [x] Department-specific GPOs were created and linked.
- [x] The domain root and `Domain Controllers` OU were excluded from the pilot baseline.
- [ ] Effective policy has been documented with `gpresult /h` for `PC-1-WIN11`.
- [ ] At least one computer in every department OU has been tested.
- [ ] Security and PowerShell audit events have been captured as evidence.
- [ ] Restore of a GPO backup has been tested.

## 8. Evidence files

| File | Description |
|---|---|
| `01-user-template-password-options.png` | Password and account-state options during template copying |
| `02-copied-user-summary.png` | Summary of the copied user object before creation |
| `03-user-logon-name.png` | User identity and UPN configuration |
| `04-ou-and-group-overview.png` | OU structure and group membership overview |

Future evidence should avoid usernames, passwords, recovery keys, or command lines containing secrets.
