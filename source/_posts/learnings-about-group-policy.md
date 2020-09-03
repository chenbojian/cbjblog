---
title: Learnings about windows group policies
date: 2020-09-03 17:02:48
tags: windows
---

## Group Policy Precedence

GPO in AD Domain Controller will override the local GPO
https://docs.microsoft.com/en-us/previous-versions/windows/desktop/policy/group-policy-hierarchy


## Command line to export GPO

`secedit /export /areas USER_RIGHTS /cfg OldUserRights.inf`
https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/secedit


## User Rights Assignment 

https://docs.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/user-rights-assignment

We usually need SeBatchLogonRight and SeServiceLogonRight

IIS full installation will require IIS_IUSRS to have SeImpersonatePrivilege. Will get 500 error if the right is missing.
https://serverfault.com/questions/50160/500-error-when-using-custom-account-for-application-pool-in-iis-7


## Splunk required rights

- Bypass traverse checking
- Log on as a batch job
- Log on as a service
- Replace a process-level token
https://docs.splunk.com/Documentation/Splunk/8.0.5/Installation/PrepareyourWindowsnetworkforaSplunkinstallation


