---
title: System Insights capabilities
description: "System Insights Capabilities"
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
# System Insights capabilities

>Applies To: Windows Server 2019

This topic defines the concept of capabilities in System Insights and introduces the default capabilities available to you in Windows Server 2019. 

This topic also describes the data sources, prediction timelines, and prediction statuses used for the default capabilities. 

## Capability overview
System Insights allows you to manage individual capabilities, which can be defined as a machine learning or statistics model that analyzes system data to help give you increased insight into the functioning of your deployments. System Insights introduces an initial set of default capabilities, as well as exposes functionality to add new capabilities dynamically, without any modifications to the operating system. Detailed documentation explaining how to create and add new capabilities will be available soon, and the [System Insights management](system-insights-management.md) provides more high-level information about this functionality. 

### Capability outputs
Every time a capability is invoked, it provides an output to help explain the result of the analysis or prediction. Each output must contain a **Status** and a **Status Description** to describe the prediction, and each result can optionally contain capability-specific data associated with the prediction. The **Status Description** is used to explain the **Status** reported by the capability, and the capability reports one of the following statuses:

- **Ok**
- **Warning**
- **Critical**

Additionally, a capability can output the following statuses if no prediction was made: 

- **Error** - An unknown problem caused the capability to fail. 
- **None** - No prediction was made. This could be due to a lack of data or any other capability-specific reason for not making a prediction. 

Any capability-specific data contained in the result will be serialized to a JSON file, which is [accessible through PowerShell](system-insights-management.md). 

## Default capabilities
In Windows Server 2019, System Insights introduces four default capabilities focused on capacity forecasting:

- **Volume consumption forecasting** - Forecastings storage consumption for each volume.
- **Total storage consumption forecasting** - Forecasts total storage consumption across all local drives. 
- **CPU capacity forecasting** - Forecasts CPU usage. 
- **Networking capacity forecasting** - Forecasts network usage for each network adapter. 

Each capability analyzes past historical data to predict future usage, and **all of the forecasting capabilities are designed to address long-term trends rather than short-term behavior**, helping administrators correct provision hardware and tune their workloads to avoid future resource contention. Because these capabilities focus on long-term trends, these capabilities analyze daily data rather than analyze smaller granularities. 

### Forecasting model and prediction timelines
Each System Insights default capability uses a forecasting model to predict future usage, and for each prediction, the model is trained locally on your machine's data. This model is designed to help detect longer term patterns, and retraining on each Windows Server instance enables the capaiblity to adapt to the specific behavior and nuances of each machine's usage.

>[!NOTE]
>Determining what type of model to use required testing a handful of different models using a dataset containing many thousands of machines. After analyzing many models and optimizing the behavior of each one, we decided to use an auto-regressive model, as it produces highly-accruate and visually intuitive predictions while not requiring too much time to train. This model, however, requires three weeks of training data, so each capability uses a basic linear trend until three weeks of data are available.]

Each capability forecasts a certain number of days into the future based on the number of days for which data has been collected. The following table shows the prediction behavior of these capabilities:

| Input data size | Forecast length |
| --------------- | --------------- |
| 0-5 days | No prediction is made. |
| 6-180 days | 1/3 * size of input data |
| 180-365 days | 60 days | 

### Forecasting data
Each capability analyzes daily data to forecast future usage. CPU, networking, and even storage usage, however, can frequently change throughout the day, dynamically adjusting to the workloads on the machine. Because usage isn't constant throughout the day, it's important to properly represent daily usage in a single data point. The table below details the specific data points and how the data is processed:


| Capability name | Data source(s) | Processing logic |
| --------------- | -------------- | ---------------- |
 Volume consumption forecasting          | Volume size                    | Maximum daily usage              
 Total storage consumption forecasting   | Sum of volume sizes, sum of disk sizes              | Maximum daily usage             
 CPU capacity forecasting                | % Processor Time  | Maximum 2-hour average per day   
 Networking capacity forecasting         | Bytes Total/sec         | Maximum 2-hour average per day  

To help explain the processing logic, it’s important to note that each capability seeks to inform administrators when future usage will meaningfully exceed the available capacity – even though CPU momentarily hit 100% utilization, CPU usage may not have caused meaningful performance degradation or resource contention. For CPU and networking, then, there should be sustained high usage rather than momentary spikes. Averaging CPU and networking usage throughout the whole day, however, would lose important usage information, as a few hours of high CPU or networking usage could meaningfully impact the performance of your critical workloads. The maximum 2-hour average during each day is a compromise between each extreme, allowing each capability to forecast on high-fidelity but not overly sensitive data. 

For volume and total storage usage, however, storage usage cannot exceed the available capacity, even momentarily, so the maximum daily usage is used for these capabilities. 

### Forecasting statuses
Each System Insights capability must output a status associated with each prediction. Each forecasting capability uses the following statuses to describe the prediction:
- **OK**: The forecast does not exceed the available capacity.
- **Warning**: The forecast exceeds the available capacity in the next 30 days. 
- **Critical**: The forecast exceeds the available capacity in the next 7 days. 
- **Error**: The capability ran into an unexpected error. 
- **None**: There isn’t enough data to make a prediction. This could be due to a lack of data or because no data has been reported recently.

>[!NOTE]
>[If a capability forecasts across multiple instances - such as multiple volumes or network adapters - the status reflects the most severe status across all instances. Individual statuses for each volume or network adapter are visible within Windows Admin Center or the data contained within the output of each capability.] 



## See also
- [System Insights overview](system-insights-overview.md)
- [System Insights management](system-insights-management.md)
- [System Insights FAQ](system-insights-faq.md)
