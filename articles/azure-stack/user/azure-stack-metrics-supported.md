---
title: 支持在 Azure Stack 上的 Azure Monitor 指标 |Microsoft Docs
description: 了解 Azure Stack 上的 Azure Monitor 支持的指标。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: mabrigg
ms.openlocfilehash: 0cd8d309cfbf72a05c83c2a536d754e9cbc6e008
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022653"
---
# <a name="supported-metrics-with-azure-monitor-on-azure-stack"></a>Azure Stack 上的 Azure Monitor 支持的指标

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以从 Azure 检索你的度量值基于 Azure Stack 中与全球 Azure 相同的监视器。 你可以在度量值在门户中，从 REST API 获取它们，或使用 PowerShell 或 CLI 查询它们。

下表列出了适用于 Azure Stack 上的 Azure Monitor 指标管道的度量值。 若要查询和访问这些指标，你将需要**2018年-01-01** api 版本的 API 配置文件的版本。 有关 API 配置文件和 Azure Stack 的详细信息，请参阅[管理 Azure Stack 中的 API 版本配置文件](azure-stack-version-profiles.md)。

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| 指标 | 指标显示名称 | 单位 | 聚合类型 | 说明 | 维度 |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| CPU 百分比 | CPU 百分比 | 百分比 | 平均值 | 当前虚拟机正在使用的已分配计算单元百分比 | 无维度 |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| 指标 | 指标显示名称 | 单位 | 聚合类型 | 说明 | 维度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | 已用容量 | 字节 | 平均值 | 帐户使用的容量 | 无维度 |
| 事务 | 事务 | Count | 总计 | 向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。 | ResponseType、GeoType、ApiName |
| 流入量 | 流入量 | 字节 | 总计 | 流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。 | GeoType、ApiName |
| 流出量 | 流出量 | 字节 | 总计 | 流出的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。 | GeoType、ApiName |
| SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值 | 由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。 | GeoType、ApiName |
| SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值 | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 | GeoType、ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 | GeoType、ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| 指标 | 指标显示名称 | 单位 | 聚合类型 | 说明 | 维度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| BlobCapacity | Blob 容量 | 字节 | 总计 | 存储帐户的 Blob 服务使用的存储量（以字节为单位）。 | /BlobType |
| BlobCount | Blob 计数 | Count | 总计 | 存储帐户的 Blob 服务中的 Blob 数。 | /BlobType |
| ContainerCount | Blob 容器计数 | Count | 平均值 | 存储帐户的 Blob 服务中的容器数。 | 无维度 |
| 事务 | 事务 | Count | 总计 | 向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。 | ResponseType、GeoType、ApiName |
| 流入量 | 流入量 | 字节 | 总计 | 流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。 | GeoType、ApiName |
| 流出量 | 流出量 | 字节 | 总计 | 流出的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。 | GeoType、ApiName |
| SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值 | 由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。 | GeoType、ApiName |
| SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值 | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 | GeoType、ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 | GeoType、ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| 指标 | 指标显示名称 | 单位 | 聚合类型 | 说明 | 维度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | 表容量 | 字节 | 平均值 | 存储帐户的表服务使用的存储量（以字节为单位）。 | 无维度 |
| TableCount | 表计数 | Count | 平均值 | 存储帐户的表服务中的表数。 | 无维度 |
| TableEntityCount | 表实体计数 | Count | 平均值 | 存储帐户的表服务中的表实体数。 | 无维度 |
| 事务 | 事务 | Count | 总计 | 向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。 | ResponseType、GeoType、ApiName |
| 流入量 | 流入量 | 字节 | 总计 | 流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。 | GeoType、ApiName |
| 流出量 | 流出量 | 字节 | 总计 | 流出的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。 | GeoType、ApiName |
| SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值 | 由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。 | GeoType、ApiName |
| SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值 | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 | GeoType、ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 | GeoType、ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| 指标 | 指标显示名称 | 单位 | 聚合类型 | 说明 | 维度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | 队列容量 | 字节 | 平均值 | 存储帐户的队列服务使用的存储量（以字节为单位）。 | 无维度 |
| QueueCount | 队列计数 | Count | 平均值 | 存储帐户的队列服务中的队列数。 | 无维度 |
| QueueMessageCount | 队列消息计数 | Count | 平均值 | 存储帐户的队列服务中的队列消息的大致数目。 | 无维度 |
| 事务 | 事务 | Count | 总计 | 向存储服务或指定的 API 操作发出的请求数。 此数值包括成功和失败的请求数，以及引发错误的请求数。 针对不同类型的响应数使用 ResponseType 维度。 | ResponseType、GeoType、ApiName |
| 流入量 | 流入量 | 字节 | 总计 | 流入的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流入的数据量，以及流入 Azure 中的数据量。 | GeoType、ApiName |
| 流出量 | 流出量 | 字节 | 总计 | 流出的数据量（以字节为单位）。 此数值包括从外部客户端到 Azure 存储流出的数据量，以及流出 Azure 中的数据量。 因此，此数值不反映计费的流出量。 | GeoType、ApiName |
| SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值 | 由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。 | GeoType、ApiName |
| SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值 | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 | GeoType、ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 存储服务或指定的 API 操作的可用性百分比。 可用性通过由 TotalBillableRequests 值除以适用的请求数（其中包括引发意外错误的请求）计算得出。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 | GeoType、ApiName |

## <a name="next-steps"></a>后续步骤

详细了解如何[监视 Azure Stack 上的 Azure](azure-stack-metrics-azure-data.md)。