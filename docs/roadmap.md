# Project roadmap

## Phase 1 — Active Directory foundation

- [x] Deploy Windows Server in VMware.
- [x] Install Active Directory Domain Services.
- [x] Promote the server to a domain controller.
- [x] Create the domain and OU structure.
- [x] Create templates, users, and security groups.
- [ ] Correct the account-state and template-membership findings.

## Phase 2 — Client integration

- [ ] Add a Windows client virtual machine.
- [ ] Configure the client to use the domain DNS server.
- [ ] Join the client to `net-secur.com`.
- [ ] Verify domain sign-in for one user from each OU.
- [ ] Document DNS, authentication, and connectivity tests.

## Phase 3 — Centralized policy

- [ ] Define password and account-lockout policy.
- [ ] Configure desktop and security settings with GPO.
- [ ] Configure Windows Defender and firewall policies.
- [ ] Test GPO application with `gpresult` and Resultant Set of Policy.

## Phase 4 — File and access services

- [ ] Create departmental shared folders.
- [ ] Apply share and NTFS permissions through security groups.
- [ ] Validate permitted and denied access with test users.
- [ ] Document the access-control model.

## Phase 5 — Operations and security

- [ ] Separate privileged and standard accounts.
- [ ] Configure administrative delegation.
- [ ] Enable security auditing and centralized log review.
- [ ] Configure backup and test restore procedures.
- [ ] Add PowerShell scripts for repeatable deployment and verification.
