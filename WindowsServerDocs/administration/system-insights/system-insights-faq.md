---
title: System Insights FAQ
description: "System Insights FAQ"
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
# System Insights FAQ

>Applies To: Windows Server 2019

## How does System Insights relate to Azure Operations Management Suite (OMS)

[Azure Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/) is a cloud-based IT management solution, providing operational information across your deployments to help you manage your on-premises and cloud infrastructure. System Insights, in contrast, is a Windows Server feature that introduces local predictive analytics capabilities. Together, these two technologies can help surface predictive analysis across a population of devices:

OMS can key off the events created by System Insights, as System Insights outputs the result of each prediction to the event log. OMS can surface these machine-specific predictions across a fleet of Windows Servers, enabling Azure OMS subscribers to have a unified, intuitive view of these predictions across a group of server instances.


## How does System Insights relate to Windows ML?

Windows ML is a platform that enables developers to import and score pre-trained ML models on Windows devices. These models benefit from hardware acceleration, and they can be scored locally. 

System Insights is a feature in Windows Server 2019 that offers local predictive capabilities together with a complete management experience, including PowerShell and Windows Admin Center integration. **The models behind these System Insights capabilities can leverage Windows ML** to benefit from hardware acceleration and the ability to import models in the [Open Neural Network Exchange (ONNX)](https://onnx.ai/) format. 

In Windows Server 2019, the default capabilities don't currently use Windows ML because these capabilities require local training to adapt to the specific datasets of each node. 


## Can I use System Insights for my cluster? 

System Insights runs on each individual Windows Server instance, and the default capabilities forecast local storage, volume, CPU, and networking usage for each node in a cluster. 

The default behavior of System Insights only forecasts for local volumes and disks, excluding cluster resources. In an upcoming release, however, System Insights will introduce functionality that allows you to start collecting data for CSVs, PDRs, and clustered disks. System Insights will continue to run on each server instance, so forecasts for cluster volumes will be duplicated across each Windows Server instance running System Insights. Stay tuned for this new functionality. 

## How expensive is it to run the default capabilities?

Each default capability is relatively inexpensive to run. Each capability will take longer to run as you collect more data, but they typically should complete in a just a few seconds. 

## See also

- [System Insights overview](system-insights-overview.md)
- [System Insights management](system-insights-management.md)
- [System Insights capabilities](system-insights-capabilities.md)