---
title: 如何使用 Azure 数字孪生启用多租户应用程序 | Microsoft Docs
description: 了解如何使用 Azure 数字孪生注册客户的 Azure Active Directory 租户
author: mavoge
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: mavoge
ms.openlocfilehash: e465ab95b670268f8ef31472259783fce8f24b36
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323766"
---
# <a name="how-to-enable-multitenant-applications-with-azure-digital-twins"></a>如何使用 Azure 数字孪生启用多租户应用程序

使用 Azure 数字孪生平台的开发人员可能希望构建多租户应用程序。 多租户应用程序是一个单一预配实例，用于支持多位客户，每位客户都拥有自己独立的数据和权限。

本文档详细介绍了如何创建支持多个 Azure Active Directory (AD) 租户和客户的 Azure 数字孪生多租户应用。

## <a name="scenario-summary"></a>方案摘要

在本方案中，假设存在开发人员 D 和客户 C：

- 开发人员 D 拥有包含 Azure AD 租户的 Azure 订阅。
- 开发人员 D 已将数字孪生实例部署到其 Azure 订阅中。
- 开发人员 D 的 Azure AD 租户中的用户可以获得针对数字孪生服务的令牌，因为 Azure AD 已在开发人员 D的 Azure AD 租户中创建了服务主体。
- 现在，开发人员 D 创建一个可以与数字孪生的管理 API 直接集成的移动应用。
- 然后开发人员 D 允许客户 C 使用该移动应用程序。
- 现在，客户 C 需要被授予权限，以便能够使用开发人员 D 的应用程序中的数字孪生管理 API。

  > [!IMPORTANT]
  > - 当客户 C 登录到开发人员 D 的应用程序时，该应用将无法获取客户 C 的用户用以与管理 API 对话的令牌。
  > - 然后，Azure AD 将引发一个错误，指示无法在客户 C 的目录中识别数字孪生。

## <a name="solution"></a>解决方案

要解决上述情况，需要按照以下操作在客户 C 的 Azure AD 租户中创建数字孪生服务主体：

- 如果客户 C 尚不具有包含 Azure AD 租户的 Azure 订阅，则：

  - 客户 C 的 Azure AD 租户管理员需要获取[即用即付 Azure 订阅](https://azure.microsoft.com/offers/ms-azr-0003p/)。
  - 然后客户 C 的 Azure AD 租户管理员必须[将其租户与新订阅进行链接](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。

- 然后，在 [Azure 门户](https://portal.azure.com)中，客户 C 的 Azure AD 租户管理员应执行以下操作：
  1. 打开“订阅”。
  1. 选择一个订阅，该订阅需具有要在开发人员 D 的订阅中使用的 Azure AD 租户。
  1. 选择“资源提供程序”。
  1. 搜索 Microsoft.IoTSpaces。
  1. 单击“注册”。
  
## <a name="next-steps"></a>后续步骤

若要了解有关将用户定义的函数与 Azure 数字孪生结合使用的详细信息，请阅读 [Azure 数字孪生 UDF](how-to-user-defined-functions.md)。

若要了解如何使用基于角色的访问控制进一步保护具有角色分配的应用程序，请阅读[数字孪生基于角色的访问控制](security-create-manage-role-assignments.md)。
