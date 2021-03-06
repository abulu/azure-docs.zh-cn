---
title: 使用 REST API 将 Azure 资源的自定义指标发送到 Azure Monitor 指标存储
description: 使用 REST API 将 Azure 资源的自定义指标发送到 Azure Monitor 指标存储
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: d36697e6b5765ecf35ed9b3add45cff6c33823a5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958208"
---
# <a name="send-custom-metrics-for-an-azure-resource-to-the-azure-monitor-metric-store-using-a-rest-api"></a>使用 REST API 将 Azure 资源的自定义指标发送到 Azure Monitor 指标存储

本文展示了如何通过 REST API 将 Azure 资源的自定义指标发送到 Azure Monitor 指标存储。  当自定义指标位于 Azure Monitor 中之后，你可以像对标准指标一样对其执行所有操作，例如绘制图表、生成警报，将其路由到其他外部工具，等等。  

>[!NOTE] 
>REST API 仅允许为 Azure 资源发送自定义指标。 若要为其他环境中或本地的资源发送自定义指标，可以使用 [Application Insights](../application-insights/app-insights-api-custom-events-metrics.md)。    


## <a name="create-and-authorize-a-service-principal-to-emit-metrics"></a>创建服务主体并授权其发布指标 

使用[创建服务主体](../active-directory/develop/howto-create-service-principal-portal.md)中的说明在 Azure Active Directory 租户中创建一个服务主体。 

在完成此过程时记下以下内容： 

- 对于单一登录 URL，可以放入任何 URL。  
- 为此应用创建新的客户端机密  
- 保存密钥和客户端 ID，以便在后面的步骤中使用。  

对于在步骤 1 中创建的应用，为其授予你希望发布其指标的资源的“监视指标发布者”权限。 如果你计划使用此应用为许多资源发布自定义指标，则可以在资源组或订阅级别授予这些权限。 

## <a name="get-an-authorization-token"></a>获取授权令牌
打开一个命令提示符并运行以下命令
```shell
curl -X POST https://login.microsoftonline.com/<yourtenantid>/oauth2/token -F "grant_type=client_credentials" -F "client_id=<insert clientId from earlier step> " -F "client_secret=<insert client secret from earlier step>" -F "resource=https://monitoring.azure.com/"
```
保存响应中的访问令牌

![访问令牌](./media/metrics-store-custom-rest-api/accesstoken.png)

## <a name="emit-the-metric-via-the-rest-api"></a>通过 REST API 发布指标 

1. 将以下 JSON 粘贴到一个文件中，并将其作为 custommetric.json 保存在本地计算机上。 更新 JSON 文件中的时间参数。 
    
    ```json
    { 
        "time": "2018-09-13T16:34:20”, 
        "data": { 
            "baseData": { 
                "metric": "QueueDepth", 
                "namespace": "QueueProcessing", 
                "dimNames": [ 
                  "QueueName", 
                  "MessageType" 
                ], 
                "series": [ 
                  { 
                    "dimValues": [ 
                      "ImagesToProcess", 
                      "JPEG" 
                    ], 
                    "min": 3, 
                    "max": 20, 
                    "sum": 28, 
                    "count": 3 
                  } 
                ] 
            } 
        } 
    } 
    ``` 

1. 在命令提示符窗口中，发布指标数据 
    - Azure 区域 - 必须与你要为其发布指标的资源的部署区域相匹配。 
    - 资源 ID - 你要跟踪其指标的 Azure 资源的资源 ID。  
    - 访问令牌 - 粘贴前面获取的令牌

    ```Shell 
    curl -X POST curl -X POST https://<azureRegion>.monitoring.azure.com/<resourceId> /metrics -H "Content-Type: application/json" -H "Authorization: Bearer <AccessToken>" -d @custommetric.json 
    ```
1. 更改 JSON 文件中的时间戳和值。 
1. 多次重复前两个步骤，以便获得几分钟的数据。

## <a name="troubleshooting"></a>故障排除 
如果在过程的某个部分中收到错误，请注意以下事项：

1. 无法针对作为 Azure 资源的订阅或资源组发布指标。 
1. 无法将已过去了 20 分钟的指标放入到存储中。 指标存储针对警报和实时图表绘制进行了优化。 
2. 维度名称的数量应当与值数量匹配，反之亦然。 检查值。 
2. 你可能在针对不支持自定义指标的区域发布指标。 请参阅[支持的自定义指标（预览版）区域](metrics-custom-overview.md#supported-regions) 



## <a name="view-your-metrics"></a>查看指标 

1. 登录到 Azure 门户 

1. 在左侧菜单中，单击“监视” 

1. 在“监视”页上单击“指标”。 

   ![访问令牌](./media/metrics-store-custom-rest-api/metrics.png) 

1. 将聚合时限更改为“过去 30 分钟”。  

1. 在“资源”下拉列表中，选择你针对其发布了指标的资源。  

1. 在“命名空间”下拉列表中，选择“QueueProcessing” 

1. 在“指标”下拉列表中，选择“QueueDepth”。  

 
## <a name="next-steps"></a>后续步骤
- 详细了解[自定义指标](metrics-custom-overview.md)。