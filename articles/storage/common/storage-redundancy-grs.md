---
title: Azure 存储中用于实现跨区域持续性的异地冗余存储 (GRS) | Microsoft Docs
description: 异地冗余存储 (GRS) 在相距数百英里的两个区域之间复制数据。 GRS 针对数据中心内的硬件故障以及区域性灾难提供保护。
services: storage
author: tolandmike
ms.service: storage
ms.topic: article
ms.date: 03/20/2018
ms.author: jeking
ms.component: common
ms.openlocfilehash: eee9998280f2105fee5144b520bc8000b086f941
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603917"
---
# <a name="geo-redundant-storage-grs-cross-regional-replication-for-azure-storage"></a>异地冗余存储 (GRS)：Azure 存储的跨区域复制
[!INCLUDE [storage-common-redundancy-GRS](../../../includes/storage-common-redundancy-grs.md)]

## <a name="read-access-geo-redundant-storage"></a>读取访问异地冗余存储
读取访问异地冗余存储 (RA-GRS) 可最大程度提高存储帐户的可用性。 除了跨两个区域的异地复制之外，RA-GRS 提供对次要位置中的数据的只读访问。

当启用对次要区域中的数据的只读访问权限时，可以在存储帐户的辅助终结点以及主终结点上获取数据。 辅助终结点与主终结点类似，但会在帐户名称后面追加后缀 `–secondary`。 例如，如果 Blob 服务的主终结点是 `myaccount.blob.core.windows.net`，辅助终结点则是 `myaccount-secondary.blob.core.windows.net`。 存储帐户的访问密钥对于主终结点和辅助终结点是相同的。

使用 RA-GRS 时需要注意一些注意事项：

* 应用程序必须管理在使用 RA-GRS 时与之交互的终结点。
* 由于异步复制涉及延迟，因此如果无法将数据从主要区域中恢复（例如在遇到区域性灾难时），则尚未复制到次要区域的更改可能会丢失。
* 可以检查存储帐户的上次同步时间。 上次同步时间是 GMT 日期/时间值。 在上次同步时间之前主要位置的写入内容已成功写入次要位置，这意味着可以从次要位置读取这些内容。 在上次同步时间之后主要位置的写入内容不一定可供读取。 可以使用 [Azure 门户](https://portal.azure.com/)、[Azure PowerShell](storage-powershell-guide-full.md) 或通过 Azure 存储客户端库之一查询此值。
* 如果 Microsoft 启动了故障转移到次要区域，那么在故障转移完成后，便可以读取和编写该数据。 有关详细信息，请参阅[灾难恢复指南](storage-disaster-recovery-guidance.md)。
* 有关如何切换到次要区域的信息，请参阅[在 Azure 存储中断时该怎么办](storage-disaster-recovery-guidance.md)。
* RA-GRS 的用途是实现高可用性。 有关可伸缩性的指南，请查看[性能清单](storage-performance-checklist.md)。
* 有关如何使用 RA-GRS 进行高可用性设计的建议，请参阅[使用 RA-GRS 存储设计高可用性应用程序](storage-designing-ha-apps-with-ragrs.md)。

## <a name="what-is-the-rpo-and-rto-with-grs"></a>GRS 的 RPO 和 RTO 是什么意思？
**恢复点目标 (RPO)：** 在 GRS 和 RA-GRS 中，存储服务以异步方式将数据从主要位置异地复制到次要位置。 主要区域中发生重大区域灾难时，Microsoft 会故障转移到次要区域。 如果进行故障转移，则尚未进行异地复制的最近更改可能会丢失。 潜在的数据丢失分钟数称为 RPO，它表示可将数据恢复到的时间点。 Azure 存储的 RPO 通常小于 15 分钟，但目前没有 SLA 规定异地复制所用时长。

**恢复时间目标 (RTO)：** RTO 用于度量执行故障转移以及将存储帐户恢复联机所花费的时间。 执行故障转移的时间包括以下操作：

   * Microsoft 确定是否可以在主要位置恢复数据或是否需要故障转移所需的时间。
   * 通过将主要 DNS 条目更改为指向次要位置来执行存储帐户故障转移的时间。

   Microsoft 会认真负责保留数据。 如果有任何机会在主要区域中恢复数据，则 Microsoft 会延迟故障转移并专注于恢复数据。 

## <a name="paired-regions"></a>配对区域 
创建存储帐户时，可以为帐户选择主要区域。 配对的次要区域是根据主要区域确定的且无法更改。 有关 Azure 支持的区域的最新信息，请参阅[业务连续性和灾难恢复 (BCDR)：Azure 配对区域](../../best-practices-availability-paired-regions.md)。

## <a name="see-also"></a>另请参阅
- [Azure 存储复制](storage-redundancy.md)
- [本地冗余存储 (LRS)：Azure 存储的低成本数据冗余](storage-redundancy-lrs.md)
- [区域冗余存储 (ZRS)：具有高可用性的 Azure 存储应用程序](storage-redundancy-zrs.md)