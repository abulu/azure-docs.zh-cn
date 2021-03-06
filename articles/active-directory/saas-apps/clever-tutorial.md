---
title: 教程：Azure Active Directory 与 Clever 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Clever 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 483d03fcc72e0a93111d10b0221164459de27d12
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431855"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>教程：Azure Active Directory 与 Clever 的集成

本教程介绍了如何将 Azure Active Directory (Azure AD) 与 Clever 进行集成。

将 Clever 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Clever。
- 可以让用户使用其 Azure AD 帐户自动登录到 Clever（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Clever 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了单一登录的 Clever 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Clever
1. 配置和测试 Azure AD 单一登录

## <a name="adding-clever-from-the-gallery"></a>从库中添加 Clever
若要配置 Clever 与 Azure AD 的集成，需要从库中将 Clever 添加到托管 SaaS 应用列表。

**若要从库中添加 Clever，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“Clever”，在结果面板中选择“Clever”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Clever](./media/clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Clever 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Clever 用户。 换句话说，需要在 Azure AD 用户与 Clever 中的相关用户之间建立链接关系。

在 Clever 中，通过将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置并测试 Clever 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Clever 测试用户](#create-a-clever-test-user)** - 在 Clever 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Clever 应用程序中配置单一登录。

**若要配置 Clever 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Clever** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/clever-tutorial/tutorial_clever_samlbase.png)

1. 在“Clever 域和 URL”部分中，执行以下步骤：

    ![Clever 域和 URL 单一登录信息](./media/clever-tutorial/tutorial_clever_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://clever.com/in/<companyname>`

    b. 在“标识符”文本框中，键入 URL：`https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > 登录 URL 值不是真实值。 使用实际登录 URL 更新此值。 请联系 [Clever 客户端支持团队](https://clever.com/about/contact/)来获取此值。

1. 在“SAML 签名证书”部分中，单击”复制”按钮来复制“应用联合元数据 URL”，并将其粘贴到记事本。
    
    ![配置单一登录](./media/clever-tutorial/tutorial_metadataurl.png)

1. Clever 应用程序需要特定格式的 SAML 断言，这要求向 **SAML 令牌属性**配置添加自定义属性映射。

    以下屏幕截图显示一个示例。

    ![配置单一登录](./media/clever-tutorial/tutorial_clever_07.png)

1. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称  | 属性值 |
    | --------------- | -------------------- |
    | clever.teacher.credentials.district_username|user.userprincipalname|
    | clever.student.credentials.district_username| user.userprincipalname |
    | 名  | user.givenname |
    | Lastname  | user.surname |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/clever-tutorial/tutorial_attribute_04.png)
    
    ![配置单一登录](./media/clever-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”文本框保留为空。
    
    d. 单击“确定” 。
    
1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/clever-tutorial/tutorial_general_400.png)

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Clever 公司站点。

1. 在工具栏中，单击“即时登录”。

    ![即时登录](./media/clever-tutorial/ic798984.png "Instant Login")

    > [!NOTE]
    > 在可以测试单一登录之前，你必须联系 [Clever 客户支持团队](https://clever.com/about/contact/)，以在后端启用 Office 365 SSO。

1. 在“即时登录”页上，执行以下步骤：
    
      ![即时登录](./media/clever-tutorial/ic798985.png "Instant Login")
    
      a. 键入“登录 URL”。
    
      >[!NOTE]
      >“登录 URL”是一个自定义值。 请联系 [Clever 客户端支持团队](https://clever.com/about/contact/)来获取此值。
    
      b. 对于“标识系统”，选择“ADFS”。

      c. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的**应用联合元数据 URL**值。
    
      d. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/clever-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/clever-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/clever-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/clever-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="create-a-clever-test-user"></a>创建 Clever 测试用户

若要使 Azure AD 用户能够登录到 Clever，必须将其预配到 Clever 中。

对于 Clever，请与 [Clever 客户端支持团队](https://clever.com/about/contact/)协作来在 Clever 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

>[!NOTE]
>可以使用 Clever 提供的任何其他 Clever 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Clever 的权限，允许其使用 Azure 单一登录。

![分配用户角色][200]

**若要将 Britta Simon 分配到 Clever，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201]

1. 在应用程序列表中，选择“Clever”。

    ![应用程序列表中的 Clever 链接](./media/clever-tutorial/tutorial_clever_app.png)

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Clever 磁贴时，应当会自动登录到 Clever 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/clever-tutorial/tutorial_general_01.png
[2]: ./media/clever-tutorial/tutorial_general_02.png
[3]: ./media/clever-tutorial/tutorial_general_03.png
[4]: ./media/clever-tutorial/tutorial_general_04.png

[100]: ./media/clever-tutorial/tutorial_general_100.png

[200]: ./media/clever-tutorial/tutorial_general_200.png
[201]: ./media/clever-tutorial/tutorial_general_201.png
[202]: ./media/clever-tutorial/tutorial_general_202.png
[203]: ./media/clever-tutorial/tutorial_general_203.png

