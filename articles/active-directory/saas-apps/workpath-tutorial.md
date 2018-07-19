---
title: 教程：Azure Active Directory 与 Workpath 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workpath 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 320b0daf-14be-4813-b59b-25a6a5070690
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 925d4b0d8c116887c14d22f821500273daefba40
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218588"
---
# <a name="tutorial-azure-active-directory-integration-with-workpath"></a>教程：Azure Active Directory 与 Workpath 的集成

在本教程中，了解如何将 Workpath 与 Azure Active Directory (Azure AD) 集成。

将 Workpath 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Workpath
- 可让用户使用其 Azure AD 帐户自动登录到 Workpath（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Workpath 的集成，需要以下项目：

- Azure AD 订阅
- 已启用 Workpath 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Workpath
2. 配置和测试 Azure AD 单一登录

## <a name="adding-workpath-from-the-gallery"></a>从库中添加 Workpath
若要配置 Workpath 与 Azure AD 的集成，需要从库中将 Workpath 添加到托管 SaaS 应用列表。

若要从库中添加 Workpath，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Workpath”。

    ![创建 Azure AD 测试用户](./media/workpath-tutorial/tutorial_workpath_search.png)

5. 在结果窗格中，选择“Workpath”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/workpath-tutorial/tutorial_workpath_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Workpath 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Workpath 用户。 换句话说，需要建立 Azure AD 用户与 Workpath 中相关用户之间的链接关系。

在 Workpath 中，将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置和测试 Workpath 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Workpath 测试用户](#creating-a-workpath-test-user) - 在 Workpath 中创建 Britta Simon 的对应用户，将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Workpath 应用程序中配置单一登录。

若要配置 Workpath 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 Workpath 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/workpath-tutorial/tutorial_workpath_samlbase.png)

3. 在“Workpath 域和 URL”部分，如果要在“IDP 发起的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/workpath-tutorial/tutorial_workpath_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://api.workpath.com/v1/saml/metadata/<instancename>`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://api.workpath.com/v1/saml/assert/<instancename>`

4. 选中“显示高级 URL 设置”。 如果要在“SP”发起的模式下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/workpath-tutorial/tutorial_workpath_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.workpath.com/ `

    > [!NOTE] 
    > 这些不是实际值。 使用实际登录 URL、标识符和回复 URL 更新这些值。 若要获取这些值，请联系 [Workpath 支持团队](https://help.workpath.com)。

5. Workpath 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示了此配置的示例。 

    ![配置单一登录](./media/workpath-tutorial/tutorial_workpath_attributes.png)
    
6. 在“单一登录”对话框的“用户属性”部分，按图中所示配置 SAML 令牌属性，然后执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ------------------- | -------------------- |    
    | first_name | user.givenname |
    | last_name | user.surname |
    
    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/workpath-tutorial/tutorial_attribute_04.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    ![配置单一登录](./media/workpath-tutorial/tutorial_attribute_05.png)

    c. 在“值”列表中，选择为该行显示的属性值。

    d. 将“命名空间”文本框保留为空。
    
    e. 单击“确定” 。
    

7. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/workpath-tutorial/tutorial_workpath_certificate.png) 

8. 单击“保存”按钮。

    ![配置单一登录](./media/workpath-tutorial/tutorial_general_400.png)

9. 在“Workpath 配置”部分上，单击“配置 Workpath”，打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/workpath-tutorial/tutorial_workpath_configure.png) 

10. 若要在 Workpath 端配置单一登录，需要将下载的元数据 XML、注销 URL、SAML 实体 ID 和 SAML 单一登录服务 URL 发送给 [Workpath 支持团队](https://help.workpath.com)。 

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/workpath-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/workpath-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/workpath-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/workpath-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-workpath-test-user"></a>创建 Workpath 测试用户

Workpath 支持实时用户预配。 进行身份验证后，会自动在应用程序中创建用户。 


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Workpath 的权限，允许其使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Workpath，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Workpath”。

    ![配置单一登录](./media/workpath-tutorial/tutorial_workpath_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Workpath”磁贴，即可自动登录到 Workpath 应用程序。
有关访问面板的详细信息，请参阅 [introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workpath-tutorial/tutorial_general_01.png
[2]: ./media/workpath-tutorial/tutorial_general_02.png
[3]: ./media/workpath-tutorial/tutorial_general_03.png
[4]: ./media/workpath-tutorial/tutorial_general_04.png

[100]: ./media/workpath-tutorial/tutorial_general_100.png

[200]: ./media/workpath-tutorial/tutorial_general_200.png
[201]: ./media/workpath-tutorial/tutorial_general_201.png
[202]: ./media/workpath-tutorial/tutorial_general_202.png
[203]: ./media/workpath-tutorial/tutorial_general_203.png
