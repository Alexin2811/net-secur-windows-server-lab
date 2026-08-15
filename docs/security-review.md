# Security review

## Current findings

### 1. Copied accounts may inherit the disabled state

The user-creation screenshot shows **Account is disabled** selected. This is correct for a reusable template, but a real employee account must be enabled deliberately after its properties and group memberships have been reviewed.

**Action:** inspect every non-template account and enable only the accounts that are ready for use.

### 2. Template accounts appear in operational groups

The group-membership overview includes the `Template ...` accounts. Templates should not receive operational access because they are provisioning objects rather than users.

**Action:** remove templates from operational groups and keep every template disabled.

### 3. Initial-password workflow needs confirmation

The template-copying screen shows **User cannot change password**. This setting may be unsuitable for normal employees and can prevent a secure first-sign-in workflow.

**Action:** for regular users, normally require a password change at the first sign-in unless the lab scenario explicitly requires a managed non-interactive account.

### 4. Group naming should match group purpose

The `RDS-` prefix normally suggests Remote Desktop Services access. If these groups are intended only to represent departmental membership, a purpose-based prefix such as `GG-` may be clearer.

**Action:** document whether the groups grant RDS access or simply identify department membership, then standardize their names.

## Planned hardening

- Create separate standard and administrative accounts.
- Delegate administration through groups instead of individual users.
- Apply password, account-lockout, Windows Defender, firewall, and audit policies through GPO.
- Apply least privilege to file shares and administrative tools.
- Protect privileged accounts from interactive use on ordinary workstations.
- Configure event logging and review failed sign-in attempts.
- Back up Active Directory system state and test restoration.
- Document recovery procedures without storing secrets in Git.

## Repository safety

Do not commit:

- passwords or password screenshots;
- `.env` files;
- private keys or certificates;
- unattended-installation files containing credentials;
- exported Group Policy backups before checking them for sensitive paths or scripts;
- VMware disks, memory snapshots, or saved-state files.
