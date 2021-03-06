---
title: Azure 安全中心支持的功能和平台 | Microsoft Docs
description: 本文档列出了 Azure 安全中心支持的功能和平台。
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 279818e6b43e53206deb9e33591f75ef381a8962
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319976"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure 安全中心支持的平台和功能

使用经典部署模式和资源管理器部署模式创建的虚拟机 (VM) 和计算机支持安全状态监视和建议。

> [!NOTE]
> 了解 Azure 资源的[经典部署模型和 Resource Manager 部署模型](../azure-classic-rm.md)。
>
>

## <a name="supported-platforms"></a>支持的平台 

本部分列出了 Azure 安全中心代理可在其上运行并从中收集数据的平台。

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows 计算机和 VM 支持的平台
支持的 Windows 操作系统：

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016


### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux 计算机和 VM 支持的平台
支持的 Linux 操作系统：

* Ubuntu 版本 12.04 LTS、14.04 LTS、16.04 LTS
* Debian 版本 6、7、8、9
* CentOS 版本 5、6、7
* Red Hat Enterprise Linux (RHEL) 版本 5、6、7
* SUSE Linux Enterprise Server (SLES) 版本 11、12
* Oracle Linux 版本 5、6、7
* Amazon Linux 2012.09 - 2017
* 仅 x86_64 平台（64 位）支持 Openssl 1.1.0

> [!NOTE]
> Linux 操作系统暂不支持虚拟机行为分析。
>
>

## <a name="vms-and-cloud-services"></a>VM 和云服务
此外还支持云服务中运行的 VM。 仅监视云服务 web 和在生产槽运行的辅助角色。 若要了解有关云服务的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)。


## <a name="supported-iaas-features"></a>支持的 IaaS 功能

> [!div class="mx-tableFixed"]
> 

|服务器|Windows||Linux||
|----|----|----|----|----|
|环境|Azure|非 Azure|Azure|非 Azure|
|VMBA 威胁检测警报|✔|✔|✔（在支持的版本上）|✔|
|基于网络的威胁检测警报|✔|X|✔|X|
|Windows Defender ATP 集成*|✔（在支持的版本上）|✔|X|X|
|允许缺失修补程序|✔|✔|✔|✔|
|安全配置|✔|✔|✔|✔|
|反恶意软件|✔|✔|X|X|
|JIT VM 访问|✔|X|✔|X|
|自适应应用程序控制|✔（仅限 Azure）|X|X|X|
|FIM|✔|✔|✔|✔|
|磁盘加密|✔|X|✔|X|
|第三方部署|✔|X|✔|X|
|NSG|✔|X|✔|X|
|Filess V1|✔|✔|X|X|
|网络映射|✔|X|✔|X|
|自适应网络强化|✔|X|✔|X|

* 公共预览版目前支持这些功能。


## <a name="supported-paas-features"></a>支持的 PaaS 功能


|服务|建议|威胁检测|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Blob 存储帐户*|✔| ✔|
|应用程序服务|✔| ✔|
|云服务|✔| X|
|Redis 缓存|✔| X|
|Service Fabric|✔| X|
|Azure 自动化|✔| X|
|Data Lake |✔| X|
|密钥保管库|✔| X|
|服务总线|✔| X|
|流分析|✔| X|
|Batch|✔| X|
|逻辑应用|✔| X|
|Vnet|✔| NA|
|子网|✔| NA|
|NIC|✔| ✔|
|NSG|✔| NA|
|订阅|✔| ✔|

* 公共预览版目前支持这些功能。

## <a name="next-steps"></a>后续步骤

- [Azure 安全中心规划和操作指南](security-center-planning-and-operations-guide.md) - 了解如何规划和了解设计注意事项，以适应 Azure 安全中心
- [Azure 安全中心按类型划分的安全警报](security-center-alerts-type.md#virtual-machine-behavioral-analysis) - 了解关于安全中心中虚拟机行为分析和故障转储内存分析的详细信息
- [Azure 安全中心常见问题解答](security-center-faq.md) - 查找有关使用服务的常见问题
- [Azure 安全性博客](http://blogs.msdn.com/b/azuresecurity/) - 查找关于 Azure 安全性及合规性的博客文章
