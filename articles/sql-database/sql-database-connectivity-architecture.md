---
title: Azure SQL 数据库连接体系结构 | Microsoft Docs
description: 本文档从 Azure 内部或 Azure 外部说明 Azure SQL 数据库连接体系结构。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 01/24/2018
ms.openlocfilehash: ca1ef9c402b370a8d1228e13d7fe3e13fd225f79
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49986315"
---
# <a name="azure-sql-database-connectivity-architecture"></a>Azure SQL 数据库连接体系结构

本文介绍 Azure SQL 数据库连接体系结构，并说明如何使用不同的组件将流量定向到 Azure SQL 数据库实例。 借助这些 Azure SQL 数据库连接组件，可以通过连接自 Azure 内部的客户端和连接自 Azure 外部的客户端将网络流量定向到 Azure 数据库。 本文还提供脚本示例用于更改连接方式，以及提供与更改默认连接设置相关的注意事项。

## <a name="connectivity-architecture"></a>连接体系结构

下图提供了 Azure SQL Database 连接体系结构的高级别概述。

![体系结构概述](./media/sql-database-connectivity-architecture/architecture-overview.png)

以下步骤介绍如何通过 Azure SQL 数据库软件负载均衡器 (SLB) 和 Azure SQL 数据库网关建立到 Azure SQL 数据库的连接。

- Azure 内部或 Azure 外部的客户端连接到 SLB，后者具有公用 IP 地址并侦听端口 1433。
- SLB 将流量定向到 Azure SQL 数据库网关。
- 网关将流量重定向到相应的代理中间件。
- 代理中间件将流量重定向到相应的 Azure SQL 数据库。

> [!IMPORTANT]
> 其中每个组件都具有内置于网络和应用层的分布式拒绝服务 (DDoS) 保护。

## <a name="connectivity-from-within-azure"></a>从 Azure 内部连接

如果从 Azure 内部连接，则连接默认具有重定向连接策略。 重定向策略指建立到 Azure SQL 数据库的 TCP 会话后的连接，然后通过将 Azure SQL 数据库网关的目标虚拟 IP 更改为代理中间件的目标虚拟 IP，将客户端会话重定向到代理中间件。 此后，所有后续数据包绕过 Azure SQL 数据库网关，直接通过代理中间件传输。 下图演示了此流量流。

![体系结构概述](./media/sql-database-connectivity-architecture/connectivity-from-within-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>从 Azure 外部连接

如果从 Azure 外部连接，则连接默认具有代理连接策略。 代理策略指通过 Azure SQL 数据库网关建立 TCP 会话，并且所有后续数据包通过网关传输。 下图演示了此流量流。

![体系结构概述](./media/sql-database-connectivity-architecture/connectivity-from-outside-azure.png)

> [!IMPORTANT]
> 在 Azure SQL 数据库中使用服务终结点时，默认情况下策略为“代理”。 若要从 VNet 内启用连接，必须允许到下面列表中指定的 Azure SQL 数据库网关 IP 地址的出站连接。

使用服务终结点时，我们强烈建议将连接策略更改为“重定向”，以实现更好的性能。 如果将连接策略更改为“重定向”，允许根据 NSG 出站到下面列出的 Azure SQL 数据库网关 IP 将还不够，必须允许出站到所有 Azure SQL 数据库 IP。 这可以借助 NSG（网络安全组）服务标记实现。 有关详细信息，请参阅[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)。

## <a name="azure-sql-database-gateway-ip-addresses"></a>Azure SQL 数据库网关 IP 地址

若要从本地资源连接到 Azure SQL 数据库，需要允许到你的 Azure 区域的 Azure SQL 数据库网关的出站网络流量。 在代理模式下连接时，连接仅通过网关建立，从本地资源进行连接时这是默认设置。

下表列出了所有数据区域的 Azure SQL 数据库网关的主 IP 和次要 IP。 某些区域中存在两个 IP 地址。 在这些区域中，主 IP 地址是网关的当前 IP 地址，第二个 IP 地址是故障转移 IP 地址。 故障转移地址是我们可能会将服务器移动到该位置以保持服务的高可用性的地址。 对于这些区域，我们建议允许出站到这两个 IP 地址。 第二个 IP 地址由 Microsoft 拥有，并且不侦听任何服务，除非 Azure SQL 数据库激活该地址以接受连接。

> [!IMPORTANT]
> 如果从 Azure 中进行连接，则默认情况下连接策略将为**重定向**（除非使用的是服务终结点）。 仅允许以下 IP 是不够的。 必须允许所有 Azure SQL 数据库 IP。 如果从 VNet 内部进行连接，则可以借助 NSG（网络安全组）服务标记完成此操作。 有关详细信息，请参阅[服务标记](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)。

| 区域名称 | 主 IP 地址 | 次要 IP 地址 |
| --- | --- |--- |
| 澳大利亚东部 | 191.238.66.109 | 13.75.149.87 |
| 澳大利亚东南部 | 191.239.192.109 | 13.73.109.251 |
| 巴西南部 | 104.41.11.5 | |
| 加拿大中部 | 40.85.224.249 | |
| 加拿大东部 | 40.86.226.166 | |
| 美国中部 | 23.99.160.139 | 13.67.215.62 |
| 中国东部 1 | 139.219.130.35 | |
| 中国东部 2 | 40.73.82.1 | |
| 中国北部 1 | 139.219.15.17 | |
| 中国北部 2 | 40.73.50.0 | |
| 东亚 | 191.234.2.139 | 52.175.33.150 |
| 美国东部 1 | 191.238.6.43 | 40.121.158.30 |
| 美国东部 2 | 191.239.224.107 | 40.79.84.180 * |
| 法国中部 | 40.79.137.0 | 40.79.129.1 |
| 德国中部 | 51.4.144.100 | |
| 德国东北部 | 51.5.144.179 | |
| 印度中部 | 104.211.96.159 | |
| 印度南部 | 104.211.224.146 | |
| 印度西部 | 104.211.160.80 | |
| 日本东部 | 191.237.240.43 | 13.78.61.196 |
| 日本西部 | 191.238.68.11 | 104.214.148.156 |
| 韩国中部 | 52.231.32.42 | |
| 韩国南部 | 52.231.200.86 |  |
| 美国中北部 | 23.98.55.75 | 23.96.178.199 |
| 北欧 | 191.235.193.75 | 40.113.93.91 |
| 美国中南部 | 23.98.162.75 | 13.66.62.124 |
| 东南亚 | 23.100.117.95 | 104.43.15.0 |
| 英国北部 | 13.87.97.210 | |
| 英国南部 1 | 51.140.184.11 | |
| 英国南部 2 | 13.87.34.7 | |
| 英国西部 | 51.141.8.11 | |
| 美国中西部 | 13.78.145.25 | |
| 西欧 | 191.237.232.75 | 40.68.37.158 |
| 美国西部 1 | 23.99.34.75 | 104.42.238.205 |
| 美国西部 2 | 13.66.226.202 | |
||||

\* **注意:** *美国东部 2* 还有第三个 IP 地址 `52.167.104.0`。

## <a name="change-azure-sql-database-connection-policy"></a>更改 Azure SQL 数据库连接策略

若要更改 Azure SQL 数据库服务器的 Azure SQL 数据库连接策略，请使用 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy) 命令。

- 如果将连接策略设置为“代理”，则所有网络数据包均通过 Azure SQL 数据库网关进行传输。 对于此设置，需要允许仅出站到 Azure SQL 数据库网关 IP。 使用“代理”设置比使用“重定向”设置的延迟时间更长。
- 如果连接策略设置为“重定向”，则所有网络数据包直接向中间件代理传输。 对于此设置，需要允许出站到多个 IP。

## <a name="script-to-change-connection-settings-via-powershell"></a>通过 PowerShell 编写脚本以更改连接设置

> [!IMPORTANT]
> 此脚本需要 [Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。
>

以下 PowerShell 脚本演示如何更改连接策略。

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <Subscription Name>

# Azure Active Directory ID
$tenantId = "<Azure Active Directory GUID>"
$authUrl = "https://login.microsoftonline.com/$tenantId"

# Subscription ID
$subscriptionId = "<Subscription GUID>"

# Create an App Registration in Azure Active Directory.  Ensure the application type is set to NATIVE
# Under Required Permissions, add the API:  Windows Azure Service Management API

# Specify the redirect URL for the app registration
$uri = "<NATIVE APP - REDIRECT URI>"

# Specify the application id for the app registration
$clientId = "<NATIVE APP - APPLICATION ID>"

# Logical SQL Server Name
$serverName = "<LOGICAL DATABASE SERVER - NAME>"

# Resource Group where the SQL Server is located
$resourceGroupName= "<LOGICAL DATABASE SERVER - RESOURCE GROUP NAME>"


# Login and acquire a bearer token
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$result = $AuthContext.AcquireToken(
"https://management.core.windows.net/",
$clientId,
[Uri]$uri,
[Microsoft.IdentityModel.Clients.ActiveDirectory.PromptBehavior]::Auto
)

$authHeader = @{
'Content-Type'='application\json; '
'Authorization'=$result.CreateAuthorizationHeader()
}

#Get current connection Policy
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method GET -Headers $authHeader

#Set connection policy to Proxy
$connectionType="Proxy" <#Redirect / Default are other options#>
$body = @{properties=@{connectionType=$connectionType}} | ConvertTo-Json

# Apply Changes
Invoke-RestMethod -Uri "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/servers/$serverName/connectionPolicies/Default?api-version=2014-04-01-preview" -Method PUT -Headers $authHeader -Body $body -ContentType "application/json"
```

## <a name="script-to-change-connection-settings-via-azure-cli"></a>通过 Azure CLI 编写脚本以更改连接设置

> [!IMPORTANT]
> 此脚本需要 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

以下 CLI 脚本演示如何更改连接策略。

```azurecli-interactive
<pre>
# Get SQL Server ID
sqlserverid=$(az sql server show -n <b>sql-server-name</b> -g <b>sql-server-group</b> --query 'id' -o tsv)

# Set URI
id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $id

# Update connection policy
az resource update --ids $id --set properties.connectionType=Proxy

</pre>
```

## <a name="next-steps"></a>后续步骤

- 有关如何更改 Azure SQL 数据库服务器的 Azure SQL 数据库连接策略的信息，请参阅 [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)。
- 有关使用 ADO.NET 4.5 或更高版本的客户端的 Azure SQL 数据库连接行为的信息，请参阅[用于 ADO.NET 4.5 的非 1433 端口](sql-database-develop-direct-route-ports-adonet-v12.md)。
- 有关常规应用程序开发的概述信息，请参阅[SQL 数据库应用程序开发概述](sql-database-develop-overview.md)。
