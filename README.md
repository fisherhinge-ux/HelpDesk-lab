# HelpDesk-lab

A documented log of Help Desk / IT Support troubleshooting tickets, worked end-to-end in a self-built Windows Server + Active Directory lab environment.

## Why this exists

I'm building toward a career in IT — starting in Help Desk / IT Support and moving toward System Administration and Cybersecurity over time. This repo is where I document real troubleshooting practice: I take a realistic Help Desk scenario, reproduce the problem in my own lab (a domain-joined Windows environment with a Windows Server 2022 Domain Controller), diagnose it the way a technician would, fix it, and write it up properly.

Each ticket includes the objective, environment, investigation steps, root cause, the actual fix, a security/detection angle, and an interview-ready explanation — not just "here's the command that fixed it."

## Approach

For every ticket:
1. **Reproduce** the problem for real in the lab — not theoretical
2. **Investigate** using the same tools a technician would have (GUI + CLI)
3. **Diagnose** the actual root cause, not just the first thing that looks related
4. **Fix and verify** — confirm the fix actually resolved the issue, don't just assume
5. **Document** — write it up so someone else (or an interviewer) can follow the reasoning

## Tickets

| # | Title | Category | Status |
|---|---|---|---|
| 001 | [HR Shared Folder Access Denied](tickets/solved/001-hr-shared-folder-access-denied) | NTFS / Share Permissions | ✅ Solved |

*(More tickets added as they're completed — DNS resolution issues, account lockouts, GPO problems, domain-join failures, printer/share access, and more.)*

## Skills demonstrated

- Windows file/folder permission diagnosis (NTFS vs Share permissions)
- Active Directory user/group troubleshooting
- Root-cause analysis and verification discipline
- Clear technical documentation and interview-ready explanations

## About me

Learning IT Support / Help Desk fundamentals alongside Windows/AD administration and networking, working toward System Administration and eventually Cybersecurity roles. See my [offensive security lab work](https://github.com/fisherhinge-ux/AD-Security-Lab) for the security side of what I'm building.
