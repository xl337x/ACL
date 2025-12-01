# ACL Chain Enumerator

Advanced Active Directory ACL enumeration tool for discovering privilege escalation paths through nested permissions and group memberships.

## What It Does

- **Deep Nested Enumeration** - Recursively follows permission chains through groups and users
- **Attack Path Discovery** - Maps complete attack chains from start user to privileged targets
- **DCSync Detection** - Identifies DCSync capabilities across all group memberships
- **Group Nesting Analysis** - Tracks permissions inherited through nested group memberships
- **Extended Rights Resolution** - Translates GUIDs to human-readable permission names
- **Smart Caching** - Optimized performance for large AD environments

## Installation

```powershell
# Download the script
iwr -Uri "https://raw.githubusercontent.com/xl337x/wew/main/acl.ps1" -OutFile "acl.ps1"

# Load the script
. .\acl.ps1
```

## Usage

```powershell
# Basic scan (auto-detects domain)
acl -StartUser "wley"

# Quick scan (users, groups, computers, OUs only)
acl -StartUser "wley" -QuickScan

# Deep scan with custom depth
Invoke-ACLChain -Domain "CORP" -StartUser "wley" -MaxDepth 7

# Include computers and export results
Invoke-ACLChain -StartUser "wley" -IncludeComputers -ExportJSON -OutputFile "results.json"
```

## Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `-StartUser` | Username to start enumeration from | Required |
| `-Domain` | Target domain (auto-detected if omitted) | Auto |
| `-MaxDepth` | Maximum recursion depth | 5 |
| `-QuickScan` | Scan only critical object types | False |
| `-IncludeComputers` | Include computer objects | False |
| `-ExportJSON` | Export results to JSON file | False |
| `-OutputFile` | JSON output filename | acl_chain_results.json |

## Example Output

```
[*] Depth 0 | Enumerating: wley
  [+] Member of: IT Support
  [!] Group: IT Support -> GenericAll -> damundsen [user]
    [→] Can compromise user, recursing...
[*] Depth 1 | Enumerating: damundsen
  [+] Member of: Help Desk
  [!] Group: Help Desk -> WriteMembers -> Domain Admins [group]
      [PRIVILEGED GROUP]

[!] DCSYNC POSSIBLE!
    Via: INLANEFREIGHT\Domain Admins
    Right: DS-Replication-Get-Changes-All

========== SUMMARY ==========
[+] Total findings: 47
[+] Max depth reached: 3
[!] Critical findings: 12
```

## Output Color Coding

- **Red** - Critical rights (GenericAll, WriteDacl, WriteOwner)
- **Yellow** - High-impact rights (GenericWrite, WriteProperty)
- **Green** - Medium-impact rights (ExtendedRight, etc.)
- **Gray** - Informational (group memberships, members)

## Requirements

- Active Directory PowerShell module
- Domain user credentials
- PowerShell 5.1 or higher
