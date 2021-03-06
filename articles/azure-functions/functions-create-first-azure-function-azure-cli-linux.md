---
title: 通过 Azure CLI 在 Linux 上创建第一个函数（预览版）| Microsoft Docs
description: 了解如何使用 Azure CLI 创建第一个默认在 Linux 映像中运行的 Azure 函数。
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/15/2017
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 1cf20a4a93ef1b5bfb9c7818f35be5e75e45a3d2
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901084"
---
# <a name="create-your-first-function-running-on-linux-using-the-azure-cli-preview"></a>使用 Azure CLI 创建第一个在 Linux 上运行的函数（预览版）

使用 Azure Functions 可将函数托管在 Linux 上的默认 Azure 应用服务容器中。 还可以[自带自定义的容器](functions-create-function-linux-custom-image.md)。 此功能目前为预览版，并且需要 [Functions 2.0 运行时](functions-versions.md)。

本快速入门主题逐步讲解如何配合使用 Azure Functions 和 Azure CLI，在 Linux 上创建第一个托管在默认应用服务容器中的函数应用。 函数代码本身将部署到 GitHub 示例存储库中的映像。    

支持在 Mac、Windows 或 Linux 计算机上执行以下步骤。 

## <a name="prerequisites"></a>先决条件 

若要完成本快速入门，你需要：

+ 一个有效的 Azure 订阅。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求使用 Azure CLI 2.0.21 版或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI]( /cli/azure/install-azure-cli)。 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>创建 Linux 应用服务计划

目前仅支持通过应用服务计划在 Linux 上托管 Functions。 尚不支持消耗计划托管。 若要了解有关托管的详细信息，请参阅 [Azure Functions 托管计划比较](functions-scale.md)。 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>在 Linux 上创建函数应用

必须使用函数应用在 Linux 上托管函数的执行。 函数应用提供一个用于执行函数代码的环境。 它可让你将函数分组为一个逻辑单元，以便更轻松地管理、部署和共享资源。 使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 命令在 Linux 应用服务计划中创建一个函数应用。 

在以下命令中，请将 `<app_name>` 占位符替换成唯一函数应用名称，将 `<storage_name>` 替换为存储帐户名。 `<app_name>` 将用作 Function App 的默认 DNS 域，因此，该名称需要在 Azure 中的所有应用之间保持唯一。 _deployment-source-url_ 参数是 GitHub 中包含“Hello World”HTTP 触发函数的示例存储库。

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart-linux
```
创建并部署函数应用后，Azure CLI 会显示类似于以下示例的信息：

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

由于 `myAppServicePlan` 是 Linux 计划，因此使用了内置的 Docker 映像来创建在 Linux 上运行函数应用的容器。 

>[!NOTE]  
>示例存储库目前包含两个脚本文件：[deploy.sh](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/deploy.sh) 和 [.deployment](https://github.com/Azure-Samples/functions-quickstart-linux/blob/master/.deployment)。 .deployment 文件告知部署过程要使用 deploy.sh 作为[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。 在当前预览版中，必须使用脚本在 Linux 映像中部署函数应用。  

## <a name="configure-the-function-app"></a>配置函数应用

GitHub 存储库中的项目需要 Functions 运行时版本 1.x。 将 `FUNCTIONS_WORKER_RUNTIME` 应用程序设置为 `~1` 会将函数应用固定到最新的 1.x 版本。 使用 [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) 命令设置应用程序设置。

在以下 Azure CLI 命令中，<app_name> 是函数应用的名称。

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~1
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
