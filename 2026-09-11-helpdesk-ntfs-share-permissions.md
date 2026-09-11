# Help Desk Ticket #1 — HR Shared Folder Access Denied

## Objective
Understand the difference between NTFS and Share permissions in Windows, how they combine when a user accesses a file over the network, how an explicit Deny overrides any Allow, and diagnose + resolve a real permission-denied ticket end-to-end in a lab AD environment.

## Environment
- **File server:** Windows Server 2022 Domain Controller — hosts the shared folder and the `HR-Team` security group
- **Client:** Windows 10, domain-joined — the affected user logs in here and accesses the share remotely
- **Tools used:** Windows Server GUI (Security tab for NTFS permissions, Advanced Sharing → Permissions for Share-level permissions), `icacls` (CLI cross-verification)

## Initial Situation
- User reports domain authentication is working normally — no login issues
- Network connectivity confirmed good — can browse other shared folders without problem
- Specific issue: attempting to open the HR-Team shared folder returns "Access is denied"
- User states the folder was accessible the previous week — implying a change occurred since then

## Investigation
1. Verified the user's report was accurate — confirmed authentication worked and connectivity was fine, ruling both out as the cause
2. Checked the NTFS Security tab on the file — found an explicit **Deny** entry covering all permission types (Full Control, Modify, Read & Execute, List Folder Contents, Read, Write)
3. Checked Share-level permissions separately (Advanced Sharing → Permissions) — found **Allow** across the board (Full Control, Change, Read), creating an apparent contradiction with the NTFS finding
4. Cross-verified the NTFS finding using `icacls` from the command line
5. Applied the governing rules to resolve the contradiction (see Findings)

## Findings
Root cause: an explicit **NTFS Deny** entry on the user's account, covering all permission types, on the HR-Team shared folder. Share permissions were wide open, but NTFS overrode them, because:
- **Rule 1:** when Share and NTFS permissions differ, the more restrictive of the two governs network access
- **Rule 2:** within NTFS itself, an explicit Deny overrides any Allow, regardless of whether the Allow comes from a direct entry or an inherited group membership

## Commands / Tools Used
| Tool | Purpose |
|---|---|
| GUI — Properties → Security tab | Inspect/edit NTFS permissions |
| GUI — Properties → Sharing → Advanced Sharing → Permissions | Inspect/edit Share-level permissions |
| `icacls <path>` | Cross-verify NTFS permissions from the command line |

## Problem Encountered
Initially confused Share-permission terminology ("Change") with NTFS-permission terminology while reporting findings, leading to inconsistent data across two separate checks before separating the two layers cleanly. Also initially defaulted toward escalating the issue rather than investigating further, despite having sufficient access to diagnose directly — corrected once prompted to gather full evidence first.

## Troubleshooting
See Investigation above — verified symptom accuracy, checked both permission layers independently, cross-verified with CLI, then applied the Share-vs-NTFS and Deny-vs-Allow rules to identify the actual blocking entry.

## Solution
Removed the explicit NTFS Deny entry on the affected user's permissions for the HR-Team folder and set to Allow (Full Control). Verified the fix by logging in as the affected user on the client machine and successfully opening **and editing** the file — a live write test, not just an open test — confirming the fix actually resolved the issue rather than assuming it had.

## Security Perspective
- **Offensive angle:** an attacker operating from a compromised low-privilege account who unexpectedly hits a Deny on a resource previously accessible should treat that as a possible signal of active defensive response (permissions being tightened in reaction to detected activity), rather than assuming it's an unrelated config issue.
- **Detection:** Windows can log permission changes via Event ID 4670 ("Permissions on an object were changed") if auditing is enabled on the object — not on by default, and worth enabling for sensitive shares.
- **Mitigation:** avoid stacking conflicting Allow/Deny entries across the Share and NTFS layers where possible. Inconsistent permissions across the two layers are a common source of both accidental lockouts (as in this ticket) and, in the reverse case, accidental over-exposure of sensitive data.

## Key Lessons
- NTFS permissions apply regardless of access method (local or network); Share permissions only ever apply over the network
- The effective permission a user gets is always the more restrictive of the Share and NTFS results
- Within NTFS, explicit Deny overrides Allow from any source, direct or inherited
- Diagnosing "Access Denied" symptoms requires checking both permission layers separately and precisely — conflating the two leads to wasted troubleshooting cycles
- Elevated access doesn't replace diagnosis — gather real evidence before concluding or escalating
- Verification means testing that the fix actually works end-to-end, not just applying a change and assuming success

## Interview Explanation
A user reported Access Denied on a shared HR folder despite successful domain login and normal network connectivity. I diagnosed it by checking both Windows permission layers separately — Share permissions, which only apply over the network, and NTFS permissions, which apply regardless of access method. I found Share permissions were fully open, but NTFS had an explicit Deny set on the user's account, which overrides any Allow and was also the stricter of the two layers in this case. I corrected the NTFS Deny to Allow and verified the fix by logging in as the affected user and successfully reading and writing the file, rather than assuming the permission change alone had resolved it.
