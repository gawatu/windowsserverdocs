---
title: System Insights Management
description: "System Insights Management"
ms.custom: na
ms.prod: windows-server
ms.reviewer: na
ms.suite: na
ms.technology: system-insights
ms.tgt_pltfrm: na
ms.topic: article
ms.assetid: 
author: gawatu
ms.author: gawatu
manager: mallikarjun.chadalapaka
ms.date: 6/05/2018
---
# Managing System Insights

>Applies To: Windows Server 2019

In Windows Server 2019, System Insights exposes a variety of settings for each capability, and these settings can be tuned to address the specific needs of your deployment. This topic describes how to manage the various settings that can be configured for each capability through PowerShell or Windows Admin Center. 

## Using Windows Admin Center to manage System Insights
The following video describes how to manage System Insights through Windows Admin Center:

[Placeholder](https://microsoft.com)

## Using PowerShell to manage System Insights
This section describes the functionality in System Insights, providing basic PowerShell examples to demonstrate how to adjust these settings. 

Though this section provides PowerShell examples, you can use the [System Insights PowerShell documentation](https://aka.ms/systeminsightspowershell) to see all of the cmdlets, parameters, and parameter sets within System Insights. 

### Enumerate capabilities
To get started, you can list all of the available capabilities using the **Get-InsightsCapability** cmdlet: 

```PowerShell
Get-InsightsCapability
``` 

### Enabling and disabling capabilities
Each capability can be enabled or disabled. Disabling a capability prevents that capability from being invoked, and for non-default capabilities, disabling a capability stops all data collection for that capability. By default, all capabilities are enabled, and you can check the state of a capability using the **Get-InsightsCapability** cmdlet. 

To enable or disable a capability, use the **Enable-InsightsCapability** and the **Disable-InsightsCapability** cmdlets:

```PowerShell
Enable-InsightsCapability -Name "CPU capacity forecasting"
Disable-InsightsCapability -Name "Networking capacity forecasting"
``` 

### Invoking a capability
Invoking a capability immediately runs the capability to retrieve a prediction, and administrators can invoke a capability any time. Running a capability, however, can be an expensive operation, so it is recommended to schedule predictions during machine downtime. 

>[!NOTE]
>[The performance impact of invoking the default capabilities is relatively modest compared to other machine learning models - they should take just a couple of seconds to run.]


```PowerShell
Invoke-InsightsCapability -Name "CPU capacity forecasting"
```

### Retrieving prediction results
Once a capability has been invoked, the most recent results are visible using **Get-InsightsCapability** or **Get-InsightsCapabilityResult**. These cmdlets output the most recent **Status** and **Status Description** of each capability, which describe the result of each prediction. The **Status** and **Status Description** fields are further described in the [System Insights capability page](system-insights-capability.md). 

Additionally, you can use the **Get-InsightsCapabilityResult** cmdlet to view the last 30 prediction results and to retrieve the data associated with the prediction: 

```PowerShell
# Specify the History parameter to see the last 30 prediction results.
Get-InsightsCapabilityResult -Name "CPU capacity forecasting" -History

# Use the Output field to locate and then show the results of "CPU capacity forecasting."
$Output = Get-Content (Get-InsightsCapabilityResult -Name "CPU capacity forecasting").Output | ConvertFrom-Json
$Output.ForecastingResults
```

### Setting a schedule
In addition to on-demand predictions, you can configure periodic predictions for each capability, so that the specified capability is automatically invoked on a predefined schedule. Use the **Get-InsightsCapabilitySchedule** cmdlet to see capability schedules: 

>[!TIP]
>[Use the pipe in PowerShell to see information for all capabilities returned by the **Get-InsightsCapability** cmdlet.]

```PowerShell
Get-InsightsCapability | Get-InsightsCapabilitySchedule
```

Periodic predictions are enabled by default though they can be disabled at any time using the **Enable-InsightsCapabilitySchedule** and **Disable-InsightsCapabilitySchedule** cmdlets:

```PowerShell
Enable-InsightsCapabilitySchedule -Name "Total storage consumption forecasting"
Disable-InsightsCapabilitySchedule -Name "Volume consumption forecasting"
```

Each default capability is scheduled to run every day at 3am. You can, however, create custom schedules for each capability, and System Insights supports a variety of schedule types, which can be configured using the **Set-InsightsCapabilitySchedule** cmdlet: 

```PowerShell
Set-InsightsCapabilitySchedule -Name "CPU capacity forecasting" -Daily -DaysInterval 2 -At 4:00PM
Set-InsightsCapabilitySchedule -Name "Networking capacity forecasting" -Daily -DaysOfWeek Saturday, Sunday -At 2:30AM
Set-InsightsCapabilitySchedule -Name "Total storage consumption forecasting" -Hourly -HoursInterval 2 -DaysOfWeek Monday, Wednesday, Friday
Set-InsightsCapabilitySchedule -Name "Volume consumption forecasting" -Minute -MinutesInterval 30 
```
>[!TIP]
>[Because the default capabilities analyze daily data, it's recommended to use daily schedules for these capabilities. Learn more about the default capabilities [here](system-insights-capabilities.md).]

### Setting remediation actions
System Insights enables you to configure custom remediation scripts to automatically react to and remediate any issue detected by each capability. For each capability, you can configure a custom PowerShell script for each prediction status. Once a capability returns a prediction status, System Insights automatically invokes the associated script to help address the issue reported by the capability, allowing administrators to take corrective action automatically, rather than requiring manual intervention. 

The diagram below provides examples of sample remediation scripts for the total storage consumption forecasting capability, which helps you understand when your data will exceed your available disk space:

<img src="media/remediation-actions.png" width=640; alt="Sample remediation actions for total storage consumption forecasting">

You can see the actions for each capability using the **Get-InsightsCapabilityAction** cmdlet:

```PowerShell
Get-InsightsCapability | Get-InsightsCapabilityAction
```

You can create new actions or delete existing actions using the **Set-InsightsCapabilityAction** and **Remove-InsightsCapabilityAction** cmdlets. Each action runs using credentials that are specified in the **ActionCredential** parameter.

>[!NOTE]
>[In the initial System Insights release, you must specify remediation scripts outside of user directories. This will be fixed in an upcoming release.]

```PowerShell
$Cred = Get-Credential
Set-InsightsCapabilityAction -Name "CPU capacity forecasting" -Type Warning -Action "C:\Users\Public\WarningScript.ps1" -ActionCredential $Cred
Set-InsightsCapabilityAction -Name "CPU capacity forecasting" -Type Critical -Action "C:\Users\Public\CriticalScript.ps1" -ActionCredential $Cred

Remove-InsightsCapabilityAction -Name "CPU capacity forecasting" -Type Warning
```

### Adding new capabilities
In Windows Server 2019, System Insights has four default capabilities, but System Insights also enables you to add new capabilities dynamically using the **Add-InsightsCapability** cmdlet. 

Creating the default capabilities and the associated management interfaces required implementing new, valuable infrastructure in Windows Server, including local data collection and the capability management functionality described above. Rather than require others to recreate this functionality, new capabilities can directly use the System Insights, which means new capabilities can:

- **Specify custom performance counters to collect and analyze.** This data can be any performance counter or ETW event, which will be collected, persisted locally, and returned to the capability for analysis when the capability is invoked. 
- **Leverage the existing System Insights PowerShell and Windows Admin Center management planes.** This includes using the custom scheduling and remediation actions functionality described above.

By exposing this extensibility, System Insights allows you to add new capabilities designed to address your own scenarios and benefit from the System Insights data collection and management infrastructure.

>[!NOTE]
>[Detailed developer documentation and sample capabilities will be available soon.]

## See also
- [System Insights overview](system-insights-overview.md)
- [System Insights management](system-insights-management.md)
- [System Insights FAQ](system-insights-faq.md)