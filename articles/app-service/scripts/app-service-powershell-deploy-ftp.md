---
title: Azure PowerShell 脚本示例 - 使用 FTP 将文件上传到 Web 应用 | Microsoft 文档
description: Azure PowerShell 脚本示例 - 使用 FTP 将文件上传到 Web 应用
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 5d9cf2cf491afdfeadf620cc32dc08ae29cfe448
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324454"
---
# <a name="upload-files-to-a-web-app-using-ftp"></a>使用 FTP 将文件上传到 Web 应用

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，并使用 FTP（通过 [WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx)）部署 Web 应用代码。

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzureRmAccount` 创建与 Azure 的连接。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Upload files to a web app using FTP")]

## 根据脚本，如果文件夹中包含子目录，需要先创建子文件夹。添加代码如下：
          
    $xml = [xml](Get-AzureRmWebAppPublishingProfile -Name mydeployLocal30201 -ResourceGroupName efworkcasegroup -OutputFile null)
    $username = $xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@userName").value
    $password = $xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@userPWD").value
    $url = $xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
    $appdirectory ="C:\LuBuFolder\Azure\Project\mydeployLocal\mydeployLocal\bin\Release\netcoreapp2.1\publish"

    # Create FTP Directory/SubDirectory If Needed - Start
    $Srcfolders = Get-ChildItem -Path $appdirectory -Recurse | Where-Object{$_.PSIsContainer}
    foreach($folder in $Srcfolders)
    {
        $SrcFolderPath = $appdirectory  -replace "\\","\\" -replace "\:","\:"   
        $DesFolder = $folder.Fullname -replace $SrcFolderPath,$url
        $DesFolder = $DesFolder -replace "\\", "/"

        "Create folder in FTP:  " + $DesFolder

  	    try
            {
                $makeDirectory = [System.Net.WebRequest]::Create($DesFolder);
                $makeDirectory.Credentials = New-Object System.Net.NetworkCredential($username,$password);
                $makeDirectory.Method = [System.Net.WebRequestMethods+FTP]::MakeDirectory;
                $makeDirectory.GetResponse();
                #folder created successfully
            }
   	     catch [Net.WebException]
            {
                try {
                    #if there was an error returned, check if folder already existed on server
                    $checkDirectory = [System.Net.WebRequest]::Create($DesFolder);
                    $checkDirectory.Credentials = New-Object System.Net.NetworkCredential($username,$password);
                    $checkDirectory.Method = [System.Net.WebRequestMethods+FTP]::PrintWorkingDirectory;
                    $response = $checkDirectory.GetResponse();
                    #folder already exists!
                }
                catch [Net.WebException] {
                    #if the folder didn't exist
                }
            }
    }
    # Create FTP Directory/SubDirectory If Needed - Stop

    # Upload Files - Start
    #Set-Location $appdirectory
    $webclient = New-Object -TypeName System.Net.WebClient
    $webclient.Credentials = New-Object System.Net.NetworkCredential($username,$password)
    $files = Get-ChildItem -Path $appdirectory -Recurse | Where-Object{!($_.PSIsContainer)}
    foreach ($file in $files)
    {
        $relativepath = (Resolve-Path -Path $file.FullName -Relative).Replace(".\", "").Replace('\', '/')
        $uri = New-Object System.Uri("$url/$relativepath")
        "Uploading to " + $uri.AbsoluteUri
        $webclient.UploadFile($uri, $file.FullName)
    }
    $webclient.Dispose()
    # Upload Files - End

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzureRmResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | 创建应用服务计划。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | 创建 Web 应用。 |
| [Get-AzureRmWebAppPublishingProfile](/powershell/module/azurerm.websites/get-azurermwebapppublishingprofile) | 获取 Web 应用的发布配置文件。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../app-service-powershell-samples.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
