---
title: how to make powershell remoting to windows work on linux
date: 2020-05-21 15:12:20
tags:
---

Microsoft disabled Basic Auth over HTTP with PSRP on Linux. See [here](https://github.com/PowerShell/PowerShell/issues/6779)

We have to use Negotiate / Kerberos to remote to windows. 

But using WinRM is very very unstable on linux. Microsoft is suggesting to install ssh on windows box.

## With official powershell docker image

`docker run -it --rm mcr.microsoft.com/powershell`

We have to run `apt-get update && apt-get install netbase` to make it work. The related issue is [here](https://github.com/PowerShell/PowerShell/issues/7342#issuecomment-585808026)

> The microsoft/powershell image is based on Ubuntu, which does not bundle /etc/services, so you are likely being bit by this issue: microsoft/omi#623.

## Known dependencies on linux

Many issues targets to [](https://github.com/microsoft/omi). It has some documents about setting up [ntlm](https://github.com/microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md) and [kerberos](https://github.com/microsoft/omi/blob/master/Unix/doc/setup-kerberos-omi.md).

The mentioned special packages are `krb5` and `gss-ntlmssp`.

I am not quite sure about this part yet. Just record here for now.
