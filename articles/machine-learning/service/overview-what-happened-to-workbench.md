---
title: Azure Machine Learning workbench 发生了什么情况？ | Microsoft Docs
description: 了解 Workbench 应用程序发生的情况、Azure 机器学习有何变化，以及支持时间线是什么。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: j-martens
ms.author: jmartens
ms.date: 09/24/2018
ms.openlocfilehash: 88e7dad15a7080c4132a6983d949f9451ad5ce69
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239247"
---
# <a name="what-is-happening-to-workbench-in-azure-machine-learning-preview"></a>Azure 机器学习（预览版）中的 Workbench 发生了什么情况？

为了给改进后的[体系结构](concept-azure-machine-learning-architecture.md)让路，2018 年 9 月版本替换了 Workbench 应用程序和其他一些早期功能。 此版本包含许多重大更新，这些更新是由有助于改善体验的客户反馈促成。 核心功能（从试验运行到模型部署）并无变化，但现在可以使用可靠的 <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> 和 [CLI](reference-azure-machine-learning-cli.md) 完成机器学习任务和管道。  

本文介绍了具体变化及其对使用 Azure 机器学习服务构建的已有内容的影响。

## <a name="what-changed"></a>有何变化？

最新版 Azure 机器学习服务包括：
+ [简化的 Azure 资源模型](concept-azure-machine-learning-architecture.md)
+ [全新门户 UI](how-to-track-experiments.md)，用于管理试验和计算目标
+ 更全面的全新 Python <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>
+ 已扩充的全新 [Azure CLI 扩展](reference-azure-machine-learning-cli.md)，用于机器学习

出于易用性考虑，[体系结构](concept-azure-machine-learning-architecture.md)已经过重新设计。 无需使用多个 Azure 资源和帐户，只需使用 [Azure 机器学习服务工作区](concept-azure-machine-learning-architecture.md#workspace)即可。  可以在 [Azure 门户](quickstart-get-started.md)中快速创建工作区。  多个用户可使用工作区，以存储定型和部署计算目标、模型试验、Docker 映像、已部署模型等。

当前版本推出了改进后的全新 CLI 和 SDK 客户端，但桌面 Workbench 应用程序本身已遭弃用。 现在可以在 [Azure Web 门户的工作区仪表板](how-to-track-experiments.md#view-the-experiment-in-the-azure-portal)中监视试验。 借助仪表板，可以获取试验历史记录、管理附加到工作区的计算目标、管理模型和 Docker 映像，甚至还能部署 Web 服务。

## <a name="how-do-i-migrate"></a>如何迁移？

在旧版 Azure 机器学习服务中创建的大多数项目，都存储在你自己的本地存储或云存储中。 这些项目永远不会消失。 若要进行迁移，必须再次向更新后的 Azure 机器学习服务注册项目。 这篇[迁移文章](how-to-migrate.md)介绍了可以迁移哪些项目以及迁移方法。

<a name="timeline"></a>

## <a name="support-timeline"></a>支持时间线

在 2018 年 9 月后的一段时间内，可继续使用试验和模型管理帐户以及 Workbench 应用程序。 在此版本发布后的 3-4 个月内，对以下资源的支持将逐渐停止提供。 有关旧功能的文档，仍可访问目录底部的[“资源”部分](../desktop-workbench/tutorial-classifying-iris-part-1.md)。

|阶段|早期功能的支持详细信息|
|:---:|----------------|
|1|在 Azure 门户中和使用 CLI 创建 Azure 机器学习试验帐户和模型管理帐户的功能支持将停止提供。 使用 CLI 创建机器学习计算环境的功能支持也将停止提供。 若有现有帐户，可以在此阶段继续使用 CLI 和桌面 Workbench。|
|2|用于在桌面 Workbench 中和使用 CLI 创建旧工作区和项目的基础 API 的支持将停止提供。 在这一阶段，仍可打开现有项目、向项目添加其他脚本、在现有项目中运行脚本，并能将 Web 服务部署到现有机器学习计算环境。|
|3|在这一阶段，对其他所有功能的支持（包括剩余的 API 和桌面 Workbench）都将停止提供。|

立即[开始迁移](how-to-migrate.md)。 使用全新 <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>、[CLI](reference-azure-machine-learning-cli.md) 和[门户](quickstart-get-started.md)即可使用所有最新功能。

## <a name="what-about-run-histories"></a>运行历史记录又如何？

在一段时间内仍可继续访问运行历史记录。 当准备好迁移到更新版 Azure 机器学习服务时，若要保留副本，可导出这些运行历史记录。

在当前版本中，运行历史记录现在称为“试验”。 可使用 SDK、CLI 或 Web 门户收集并探索模型的试验。

门户的工作区仪表板仅在 Edge、Chrome 和 Firefox 浏览器上受支持。

[ ![联机门户](./media/overview-what-happened-to-workbench/image001.png) ] (./media/overview-what-happened-to-workbench/image001.png#lightbox)


## <a name="can-i-still-prep-data"></a>我能否仍准备数据？

已有的数据准备文件不可移植到最新版本中，因为 Workbench 已遭弃用。 不过，仍可以准备数据用于建模。  

使用较小的数据集，可以在建模前使用 <a href="http://aka.ms/aml-sdk" target="_blank">Azure 机器学习数据准备 SDK</a> 快速准备数据。 

可以对较大数据集使用这一相同 <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>，也可以使用 Azure Databricks 准备大数据集。 

## <a name="will-projects-persist"></a>项目是否继续存在？

不会丢失任何代码或工作。 在旧版本中，项目是包含本地目录的云实体。 在最新版本中，可使用本地配置文件将本地目录附加到 Azure 机器学习服务工作区。 [请参阅最新体系结构的关系图](concept-azure-machine-learning-architecture.md)。

由于很多项目内容已存在于本地计算机上，因此只需在相应目录中创建配置文件，并在代码中引用它，即可连接到工作区。 [了解如何迁移现有项目](how-to-migrate.md#projects)。

了解如何开始[在 Python 中使用主 SDK](quickstart-get-started.md)。

## <a name="what-about-my-registers-models-and-images"></a>我注册的模型和映像又如何？
 
若要继续使用旧模型注册表中注册的模型，必须将它们迁移到新工作区。 为此，请在新工作区中[下载模型并重新注册它们](how-to-migrate.md)。 

若要继续使用在旧映像注册表中创建的映像，必须在新工作区中重新创建它们。 为此，请按照[创建 docker 映像](how-to-deploy-to-aci.md#configure-an-image)部分中的说明操作。 

## <a name="what-about-deployed-web-services"></a>已部署 Web 服务又如何？

只要 Azure 容器服务 (ACS) 受支持，使用模型管理帐户部署为 Web 服务的模型就可以继续运行。 甚至在模型管理帐户不再受支持后，这些 Web 服务也仍会运行。 不过，在旧 CLI 不再受支持后，管理这些 Web 服务的功能支持也不再提供。

在更高版本中，模型作为 Web 服务部署到 [Azure 容器实例](how-to-deploy-to-aci.md) (ACI) 或 [Azure Kubernetes 服务](how-to-deploy-to-aks.md) (AKS) 群集中。 还可以[部署到 FPGA 和 IoT Edge](how-to-deploy-and-where.md)。 无需更改任何计分文件、依赖项和架构，即可使用全新 SDK 或 CLI 重新部署模型。 

## <a name="what-about-the-old-sdk--cli"></a>旧 SDK 和 CLI 又如何？

在一段时间内仍可继续使用它们（请参阅上面的[时间线](#timeline)）。 建议开始使用最新 SDK 和/或 CLI 新建试验和模型。

在最新版本中，使用全新 Python SDK，可以在任何 Python 环境中与 Azure 机器学习服务进行交互。 了解如何安装最新版 <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>。  还可以结合使用[更新后的 Azure CLI 机器学习扩展](reference-azure-machine-learning-cli.md)和丰富的 `az ml` 命令集，在任何命令行环境（包括 Azure 门户 Cloud Shell）中与服务进行交互。

## <a name="what-about-vs-code-tools-for-ai"></a>VS Code Tools for AI 又如何？

在此最新版本中，Visual Studio (VS) Code Tools for AI 扩展已获扩充和改进，以支持上述新功能。

[ ![Visual Studio Code Tools for AI](./media/overview-what-happened-to-workbench/vscode.png) ] (./media/overview-what-happened-to-workbench/vscode-big.png#lightbox)

## <a name="what-about-domain-packages"></a>域包又如何？

[计算机视觉、文本分析和预测](../desktop-workbench/reference-python-package-overview.md)的域包无法与最新版 Azure 机器学习结合使用。 不过，仍可以使用最新版 Azure 机器学习 Python <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a>，生成并定型计算机视觉、文本分析和预测模型。 若要了解如何迁移使用计算机视觉、文本分析和预测包构建的已有模型，请联系我们：[AML-Packages@microsoft.com](mailto:AML-Packages@microsoft.com)。

## <a name="next-steps"></a>后续步骤

了解 [Azure 机器学习服务的最新体系结构](concept-azure-machine-learning-architecture.md)，并尝试学习快速入门或教程之一：

* [什么是 Azure 机器学习服务](overview-what-is-azure-ml.md)
* [快速入门：使用 Python 创建工作区](quickstart-get-started.md)
* [教程：定型模型](tutorial-train-models-with-aml.md)
