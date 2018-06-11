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


In Windows Server 2019, you can run and manage each capability available on System Insights. System Insights exposes a variety of settings you can configure for each capability, which can be tuned to address the specific needs of your deployment. 

This topic describes how to manage the various settings that can be configured for each capability through PowerShell or Windows Admin Center. 

## Using Windows Admin Center to manage System Insights
The following video describes how to manage System Insights through Windows Admin Center. 

## Using PowerShell to manage System Insights
This section describes the configuration options available to you within System Insights, providing basic PowerShell examples to demonstrate how you can adjust these settings. 

Though this section provides PowerShell examples, you can use the PowerShell documentation to see all of the cmdlets, parameters, and parameter sets within System Insights. 

### Enumerate capabilities
To get started, you can list all of the capabilities available to you in System Insights using the **Get-InsightsCapability**: 

```PowerShell
Get-InsightsCapability
``` 

The capabilities listed should enumerate 

### Enabling and disabling capabilities
Disabling a capability prevents that capability from being invoked, and for non-default capabilities, disabling a capability stops all custom data collection for that capability. By default, all capabilities are enabled, and you can check the state of a capability using the **Get-InsightsCapability** cmdlet. 

To enable or disable a capability, you can use the **Enable-InsightsCapability** and the **Disable-InsightsCapability** cmdlets:

```PowerShell
Enable-InsightsCapability -Name "CPU capacity forecasting"
Disable-InsightsCapability -Name "Networking capacity forecasting"
``` 


## Invoking a capability
Invoking a capability immediately runs a capability to retrieve a prediction, and administrators can invoke a capability at any time. Running a capability can be an expensive operation, so it is recommended to schedule predictions during machine downtime.  

```PowerShell
Invoke-InsightsCapability -Name "CPU capacity forecasting"
```

>[!NOTE]
>[Invoking the default capabilities are relatively inexpensive compared to other machine learning models, as they should take just a couple of seconds to run.]

### Setting a schedule
In addition to on-demand predictions, you can also configure periodic predictions for each capability, so that the specified capability is automatically invoked on a predefined schedule. Each default capability is scheduled to run every day at 3am, and you can see capability schedules using the **Get-InsightsCapabilitySchedule** cmdlet. 

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

Each default capability is scheduled to run every day at 3am. Administrators, however, can create custom schedules for each capability, and System Insights supports a variety of schedule types.

```PowerShell
Set-InsightsCapabilitySchedule -Name "CPU capacity forecasting" -Daily -DaysInterval 2 -At 4:00PM
Set-InsightsCapabilitySchedule -Name "Networking capacity forecasting" -Daily -DaysOfWeek Saturday, Sunday -At 2:30AM
Set-InsightsCapabilitySchedule -Name "Total storage consumption forecasting" -Hourly -HoursInterval 2 -DaysOfWeek Monday, Wednesday, Friday
Set-InsightsCapabilitySchedule -Name "Volume consumption forecasting" -Minute -MinutesInterval 30 
```
>[!TIP]
>[Because the default capabilities analyze daily data, it's recommended to use daily schedules for these capabilities. [Learn more about the default capabilities.](system-insights-capabilities.md)]

### Setting remediation actions
System Insights enables you to use custom remediation scripts to automatically react to and remediate any issue detected by each capability:
- Every capability outputs a prediction status, and each status can have an associated remediation script. If the capability returns the associated status.



to specify custom remediation scripts for each capability, allowing administrators to automatically mitigate the issues they detect. Optional remediation actions can be linked to the prediction statuses of a capability

System Insights supports administrators creating an optional remediation script for each status that’s returned by a capability. Once a capability returns a prediction status, System Insights checks whether there’s an action associated with the status. If so, the remediation script will be automatically invoked to help address the issue reported by the capability, allowing administrators to take corrective action automatically, rather than requiring manual intervention. 

### Adding new capabilities
Additionally, System Insights enables IT administrators to add new predictive capabilities. Though System Insights has four default capabilities, System Insights enables administrators to add new capabilities dynamically. This extensibility allows others to leverage the rich infrastructure of System Insights. New capabilities can use the data collection functionality and the model management functionality directly:
- A new capability can specify custom data it wishes to collect and analyze. This data can be any performance counter or ETW event, which will be collected, persisted locally, and returned to the capability for analysis when the capability is invoked. 
- Additionally, new capabilities will be managed using the same System Insights PowerShell module and Windows Admin Center extension. This means that new capabilities benefit from configurable schedules and optional remediation actions, and administrators can manage them within a familiar management interface. 
By exposing this functionality, System Insights allows IT administrators to add new capabilities that address their own scenarios and directly benefit from the System Insights infrastructure.

>[!IMPORTANT]
>[For the intial System Insights release, you must use scripts that are stored outside of user directories. This will be fixed in an upcoming release.]

### Seeing prediction output


## See also

- [System Insights overview](system-insights-overview.md)
- [System Insights management](system-insights-management.md)
- [System Insights FAQ](system-insights-faq.md)