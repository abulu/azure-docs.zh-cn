---
title: 使用 Azure Site Recovery 对 Azure 本地计算机运行灾难恢复演练 | Microsoft 文档
description: 了解使用 Azure Site Recovery 对从 Azure 本地计算机运行灾难恢复演练的相关信息
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 3be3631d8d917fe9ff85e8471a35ac2ddece80b7
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078148"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>运行 Azure 灾难恢复演练

本文演示如何使用测试故障转移对 Azure 本地计算机运行灾难恢复演练。 演练在不丢失数据的情况下验证你的复制策略。

这是本系列的第四个教程，演示如何为本地 VMware VM 或 Hyper-V VM 设置到 Azure 的灾难恢复。

本教程假定你已完成头三个教程：
    - 在[第一个教程](tutorial-prepare-azure.md)中，我们设置了 VMware 灾难恢复所需的 Azure 组件。
    - 在[第二个教程](vmware-azure-tutorial-prepare-on-premises.md)中，我们准备了用于灾难恢复的本地组件，并查看了先决条件。
    - 在[第三个教程](vmware-azure-tutorial.md)中，我们为本地 VMware VM 设置并启用了复制。
    - 教程旨在介绍**方案的最简单部署路径**。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 如果想要更详细地了解测试故障转移步骤，请阅读[操作方法指南](site-recovery-test-failover-to-azure.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置隔离式网络以用于测试故障转移
> * 准备在故障转移后连接到 Azure VM
> * 为单一计算机运行测试故障转移



## <a name="verify-vm-properties"></a>验证 VM 属性

在运行测试故障转移前，请验证 VM 属性，确保 [Hyper-V VM](hyper-v-azure-support-matrix.md#replicated-vms) 或 [VMware VM](vmware-physical-azure-support-matrix.md#replicated-machines) 符合 Azure 要求。

1. 在“受保护的项”中，单击“复制的项”，然后单击 VM。
2. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。
3. 在“计算和网络”中，可修改 Azure 名称、资源组、目标大小、可用性集和托管磁盘设置。
4. 可查看和修改网络设置，包括在运行故障转移后 Azure VM 所在的网络/子网，以及将分配给它的 IP 地址。
5. 在“磁盘”中，可以看到关于 VM 上的操作系统和数据磁盘的信息。

## <a name="create-a-network-for-test-failover"></a>创建用于测试故障转移的网络

对于测试故障转移，我们建议选择与每个 VM 的“计算和网络”设置中指定的生产恢复站点网络相互独立的网络。 默认情况下，创建 Azure 虚拟网络时，该网络独立于其他网络。 测试网络应模拟生产网络：

- 测试网络中的子网数目应与生产网络中的子网数目相同。 这些子网的名称应该相同。
- 测试网络应使用相同的 IP 地址范围。
- 使用“计算和网络”设置中为 DNS VM 指定的 IP 地址更新测试网络的 DNS。 有关更多详细信息，请参阅 [Active Directory 的测试性故障转移注意事项](site-recovery-active-directory.md#test-failover-considerations)。

## <a name="run-a-test-failover-for-a-single-vm"></a>为单个 VM 运行测试故障转移

运行测试故障转移时需执行下列操作：

1. 运行必备项检查，确保故障转移所需的所有条件都已就绪。
2. 故障转移处理数据，以便创建 Azure VM。 如果选择最新恢复点，则根据该数据创建恢复点。
3. 使用上一步中处理的数据创建 Azure VM。

按如下所述运行测试故障转移：

1. 在“设置” > “复制的项”中，单击“VM”>“+测试故障转移”。
2. 为本教程选择最近处理的恢复点。 这会将 VM 故障转移到最近的可用时间点上。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO（恢复时间目标）会较低。
3. 在“测试故障转移”中，选择 Azure VM 在故障转移之后要连接到的目标 Azure 网络。
4. 单击“确定”开始故障转移。 可以通过单击 VM 打开其属性来跟踪进度。 或者，**可以在保管库名称** > **设置** > **作业** >
   **Site Recovery 作业** 中，单击“测试故障转移” 作业。
5. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 请确保虚拟机的大小适当、已连接到正确的网络，并且正在运行。
6. 现在应该能够连接到 Azure 中复制的 VM。
7. 若要删除在测试故障转移期间创建的 Azure VM，请在 VM 上单击“清理测试故障转移”。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。

在某些情况下，故障转移需要大约八到十分钟的时间完成其他进程。 你可能注意到，VMware Linux 计算机、未启用 DHCP 服务的 VMware VM，以及未安装启动驱动程序（storvsc、vmbus、storflt、intelide、atapi）的 VMware VM 需要更长的测试故障转移时间。

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>准备在故障转移后连接到 Azure VM

如果想要在故障转移后使用 RDP/SSH 连接到 Azure VM，请遵照[此处](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)表格中汇总的要求。

按照[此处](site-recovery-failover-to-azure-troubleshoot.md)所述步骤对故障转移后的任何连接问题进行故障排除。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为本地 VMware VM 运行故障转移和故障回复](vmware-azure-tutorial-failover-failback.md)。
> [为本地 Hyper-V VM 运行故障转移和故障回复](hyper-v-azure-failover-failback-tutorial.md)。
