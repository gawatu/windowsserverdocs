---
title: System Insights Overview
description: "System Insights Overview"
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
ms.date: 5/23/2018
---
# System Insights Overview

>Applies To: Windows Server 2019

System Insights is a new feature that introduces predictive analytics natively to Windows Server. These predictive capabilities run completely locally on each Windows Server instance, analyzing system data, such as performance counters and ETW events. The insights derived from these predictive capabilities help IT administrators proactively detect and address problematic behavior in their deployments, enabling IT administrators to reduce the operational expenses associated with monitoring their deployments. In Windows Server 2019, System Insights focuses on capacity forecasting - predicting future resource usage based on historical usage data. 

“System Insights” is a new feature in this Preview release that introduces predictive analytics capabilities natively in Windows Server. These predictive capabilities run locally on each Server instance, and they analyze Windows Server system data, such as performance counters and ETW events. System Insights enables IT admins to run and configure these predictive capabilities, which will help provide value to IT Pros and IT Decision Markers in the following ways:
-	Lower OPEX by turning reactive emergencies of the past into proactive management experiences. 
-	Help increase service uptime through advanced warning of system errors, problematic behavior, or failures. 
-	Lower CAPEX by helping IT decision makers plan their hardware purchasing plans.
-	Offload IT Pros from repetitive manual tasks.

In this Preview release, System Insights focuses on capacity forecasting – predicting future resource usage based on historical usage data. This Windows Server Preview Validation Guide provides you the necessary background information along with step-by-step instructions to interact with, manage, and evaluate System Insights using PowerShell. 


## Local functionality

System Insights
System Insights has been designed to run completely locally – all of your data is collected, persisted, and analyzed directly on your machine. Organizations can take advantage of predictive analytics features without any connection to the cloud, helping IT administrators both abide by any privacy and compliance boundaries and save on networking capacity. To enable this predictive functionality locally, Windows Server 2019 added new features and processes described below:

First, Windows Server 2019 introduces a new, local data collection feature: System Data Archiver. This feature collects and stores a default set of usage data, and it minimally impacts your system performance and negligibly increases your storage footprint. System Data Archiver is installed by default, and importantly, System Insights analyzes the data collected by System Data Archiver to make predictions.
Second, the predictive capabilities in System Insights require no cloud-connectivity. This is possible because they are either:
1.	Trained offline in the cloud, using thousands of examples, and then deployed locally. 
2.	Or, they are trained completely locally.

Determining which approach to use depends on the capability and the scenarios it targets.Thus, the data collection and training methodology enable Windows Server 2019 to offer completely local predictive capabilities. 



## Why did we make this

## Installing the feature

Inplace upgrade