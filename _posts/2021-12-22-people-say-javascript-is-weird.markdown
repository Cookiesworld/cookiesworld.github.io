---
layout: post
title: "People say javascript is weird"
date: 2021-12-22 14:34:55 +0000
categories: powershell
---

Today I had to dip into Powershell for some deployment schannanigens. We have a script that formats hostname. It is passed a comma separated list of hosts which formats them into deployment hostnames and calls docker. For some reason if it were passed a single hostname we see an error 
>"Cannot process arugment transformation on parameter 'HostNames' cannot convert the value of type System.String to type System.Collection.ArrayList".

Below is a simplified version which displays the error

```
function Get-HostNames {
    Param(
        [string]$instanceList
    )

    $instanceArray = $instanceList.split(",")

    [System.Collections.ArrayList]$hostNames = @()
    Foreach($id in $instanceArray) {
        $hostId = $id.PadLeft(2, '0')
        $hostName = "app$(hostId).example.org"
        [void]$hostNames.Add($hostName)
    }

    return $hostNames;
}

function Get-XX {
    $hostnames = Get-HostNames -instanceList = "devweu"
    Get-YY -hostNames $hostnames
}

function Get-YY {
    Param(
        [System.Collections.ArrayList]$hostNames
    )

    return $hostNames;
}

$names = Get-XX;
$names.GetType()
Write-Output($names)
```

I dont normally write powershell so I am far from an expert but after some digging I found [this](https://docs.microsoft.com/en-us/powershell/scripting/learn/deep-dives/everything-about-arrays?view=powershell-7.2#return-an-array). By default Powershell enumerates lists on return. Therefore a single entry ends up being recast to a string rather than an array, even though we explicitly set this inside the function. The fix is to add a , on the return to explicitly tell powershell not to enumerate e.g;

```
function Get-HostNames {
    Param(
        [string]$instanceList
    )

    $instanceArray = $instanceList.split(",")

    [System.Collections.ArrayList]$hostNames = @()
    Foreach($id in $instanceArray) {
        $hostId = $id.PadLeft(2, '0')
        $hostName = "app$(hostId).example.org"
        [void]$hostNames.Add($hostName)
    }

    return ,$hostNames;
}

function Get-XX {
    $hostnames = Get-HostNames -instanceList = "devweu"
    Get-YY -hostNames $hostnames
}

function Get-YY {
    Param(
        [System.Collections.ArrayList]$hostNames
    )

    return ,$hostNames;
}

$names = Get-XX;
$names.GetType()
Write-Output($names)
```
