# AD Learning Path 14 — Create and Manage a Domain User

> **Level:** Beginner  
> **Series:** Activity 14 of 64

## Objective
Create, modify, disable, enable, reset, and remove a test domain user while understanding key identity attributes and account lifecycle controls.

## Prerequisites
- Users OU exists
- Delegated user-management rights
- Password policy understood

## Procedure
1. Prompt securely for an initial password and create `Alice Johnson` as `ajohnson` in the Users OU.
2. Require password change at first sign-in and populate non-sensitive attributes.
3. Test disable/enable, unlock, password reset, and expiry controls.
4. Inspect `SID`, `ObjectGUID`, `Enabled`, `PasswordLastSet`, and memberships.
5. Disable before deletion and document the retention decision.

## Implementation
```powershell
$DomainDN = (Get-ADDomain).DistinguishedName
$Password = Read-Host 'Initial password for ajohnson' -AsSecureString
New-ADUser -Name 'Alice Johnson' -GivenName 'Alice' -Surname 'Johnson' `
    -SamAccountName 'ajohnson' -UserPrincipalName 'ajohnson@corp.lab' `
    -Path "OU=Users,$DomainDN" -Department 'IT' -Title 'Lab User' `
    -AccountPassword $Password -Enabled $true -ChangePasswordAtLogon $true

Get-ADUser ajohnson -Properties *
Disable-ADAccount ajohnson
Enable-ADAccount ajohnson
```

## Validation
```powershell
Get-ADUser ajohnson -Properties Enabled,PasswordLastSet,PasswordExpired,Department,Title,MemberOf |
    Format-List Name,SamAccountName,UserPrincipalName,Enabled,PasswordLastSet,PasswordExpired,Department,Title,MemberOf
```

## Evidence
Capture creation commands without passwords, user attributes, enable/disable transitions, password-reset procedure, memberships, lifecycle findings, errors/remediation, and final pass/fail status under `evidence/`.

## Troubleshooting
- Password rejected: inspect the effective password policy.
- UPN conflict: search existing users before creation.
- Membership not visible in the current token: sign out and back in.

## Security notes
Never embed passwords in scripts or CSV files. Use least-privilege operators. Disable departed identities promptly and preserve audit evidence before deletion.

## Cleanup
Disable the test identity, review it, then remove it deliberately with `Remove-ADUser`.

## References
- Microsoft Learn: `New-ADUser`
- Microsoft Learn: Active Directory account lifecycle

## Next activity
`AD-Learning-Path-15-Create-and-Manage-Security-Groups`
