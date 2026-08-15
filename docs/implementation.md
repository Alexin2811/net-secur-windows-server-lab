# Implementation notes

## 1. Project objective

The lab models the internal Windows domain of **Net-Secur**, a fictional company that provides outsourced IT and information-security services.

The first project milestone is to establish a maintainable Active Directory structure that can later support domain-joined clients, centralized policies, file services, delegation, auditing, and backup.

## 2. Virtual environment

- Hypervisor: VMware
- Server operating system: Windows Server
- Server role: Active Directory Domain Services
- Forest and domain name: `net-secur.com`
- Parent organizational unit: `net-secur`

Exact Windows Server and VMware versions will be recorded in a later inventory update.

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

## 5. Verification checklist

- [x] The `net-secur.com` domain is visible in Active Directory Users and Computers.
- [x] The parent and child OUs are present.
- [x] Departmental security groups exist.
- [x] Lab user objects have been created.
- [x] Users are visible in the appropriate group membership lists.
- [ ] All regular user accounts have been checked for enabled/disabled state.
- [ ] Template accounts have been removed from operational groups.
- [ ] A client computer has successfully joined the domain.
- [ ] DNS name resolution has been tested from a client.
- [ ] User sign-in has been tested from a domain-joined client.

## 6. Evidence files

| File | Description |
|---|---|
| `01-user-template-password-options.png` | Password and account-state options during template copying |
| `02-copied-user-summary.png` | Summary of the copied user object before creation |
| `03-user-logon-name.png` | User identity and UPN configuration |
| `04-ou-and-group-overview.png` | OU structure and group membership overview |
