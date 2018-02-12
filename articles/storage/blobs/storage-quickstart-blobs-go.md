---
title: "Azure 快速入门 - 使用 Go 将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象 | Microsoft Docs"
description: "快速了解如何使用 Go 语言将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象"
services: storage
author: seguler
manager: jahogg
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: go
ms.topic: quickstart
ms.date: 01/29/2018
ms.author: seguler
ms.openlocfilehash: 4ba9721dc12bc50b20ad85019b1df51a56b52ebc
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2018
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-go"></a>使用 Go 将对象转移到 Azure Blob 存储或从 Azure Blob 存储转移对象
本快速入门介绍如何使用 Go 编程语言上传、下载和列出 Azure Blob 存储的容器中的块 Blob。 

## <a name="prerequisites"></a>先决条件

完成本快速入门教程： 
* 安装 [Go 1.8 或更高版本](https://golang.org/dl/)
* 使用 `go get -u github.com/azure/azure-storage-blob-go/2016-05-31/azblob` 下载和安装[用于 Go 的 Azure 存储 Blob SDK](https://github.com/azure/azure-storage-blob-go/)。 

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>下载示例应用程序
本快速入门中使用的[示例应用程序](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git)是基本的 Go 应用程序。  

使用 [git](https://git-scm.com/) 可将应用程序的副本下载到开发环境。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

此命令会将存储库克隆到本地 git 文件夹。 若要打开用于 Blob 存储的 Go 示例，请查找 storage-quickstart.go 文件。  

## <a name="configure-your-storage-connection-string"></a>配置存储连接字符串
此解决方案要求将存储帐户名称和密钥安全地存储在运行示例的计算机的本地环境变量中。 根据操作系统按照下面的一个示例创建环境变量。

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>运行示例
此示例在当前文件夹中创建一个测试文件，将该测试文件上传到 Blob 存储，在容器中列出 Blob，然后将文件下载到缓冲区中。 

若要运行此示例，请发出以下命令： 

```go run storage-quickstart.go```

以下输出是运行应用程序时返回的输出的示例：
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
按键继续时，示例程序会删除存储容器和文件。 

> [!TIP]
> 还可以使用工具（如 [Azure 存储资源管理器](http://storageexplorer.com)）查看 Blob 存储中的文件。 Azure 存储资源管理器是免费的跨平台工具，可用于访问存储帐户信息。 
>

## <a name="understand-the-sample-code"></a>了解示例代码

接下来逐步介绍示例代码，以便展示其工作方式。

### <a name="create-containerurl-and-bloburl-objects"></a>创建 ContainerURL 和 BlobURL 对象
首先要做的是创建 ContainerURL 和 BlobURL 对象的引用，此类对象用于访问和管理 Blob 存储。 此类对象提供 Create、PutBlob 和 GetBlob 之类的低级 API，用于发出 REST API。

* 使用 **SharedKeyCredential** 结构来存储凭据。 

* 使用凭据和选项创建一个**管道**。 该管道用于指定重试策略、日志记录、HTTP 响应有效负载的反序列化等事项。  

* 实例化一个新的 ContainerURL 和一个新的 BlobURL 对象，以便在容器上运行操作 (Create) 和在 Blob 上运行操作（PutBlob 和 GetBlob）。


有了 ContainerURL 之后，即可实例化指向 Blob 的 **BlobURL** 对象，并执行上传、下载、复制之类的操作。

> [!IMPORTANT]
> 容器名称必须为小写。 有关容器名称和 blob 名称的详细信息，请参阅[命名和引用容器、Blob 和元数据](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)。

本部分创建一个新的容器。 容器名为 **quickstartblobs-[随机字符串]**。 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential := azblob.NewSharedKeyCredential(accountName, accountKey)
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err := containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>将 blob 上传到容器

Blob 存储支持块 blob、追加 blob 和页 blob。 块 blob 是最常用的 blob，此快速入门中使用的便是它。  

若要将文件上传到 Blob，请使用 **os.Open** 打开该文件。 然后即可使用一个 REST API（PutBlob、PutBlock 或 PutBlockList）将该文件上传到指定的路径。 

另外，也可使用 SDK 提供的[高级 API](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go)，这是在低级 REST API 的基础上生成的。 例如，***UploadFileToBlockBlob*** 函数使用 PutBlock 操作以区块方式同时上传一个文件，以便优化吞吐量。 如果文件小于 256 MB，则会改用 PutBlob 通过单个事务完成传输。

以下示例将文件上传到名为 **quickstartblobs-[randomstring]** 的容器。

```go
// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level PutBlob API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that PutBlob can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/en-us/rest/api/storageservices/put-blob
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.PutBlob(ctx, file, azblob.BlobHTTPHeaders{}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls PutBlock/PutBlockList for files larger 256 MBs, and calls PutBlob for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>列出容器中的 Blob

可以使用基于 **ContainerURL** 的 **ListBlobs** 方法获取容器中文件的列表。 ListBlobs 返回单个 Blob 段（最多 5000 个 Blob，从指定的**标记**开始计算）。 使用空标记从头开始枚举。 Blob 名称按字典中的顺序返回。 获取一个段以后，对其进行处理，然后再次调用 ListBlobs，传递以前返回的标记。  

```go
// List the blobs in the container
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobs(ctx, marker, azblob.ListBlobsOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Blobs.Blob {
        fmt.Print("Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>下载 Blob

使用基于 BlobURL 的 **GetBlob** 低级方法下载 Blob。 也可创建一个流，然后使用 [highlevel.go](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go) 中提供的 **NewDownloadStream** 高级 API 从流中读取范围。 NewDownloadStream 函数在出现连接故障时重试，而 Get Blob API 则仅在出现特定的 HTTP 状态代码（例如“503 (服务器忙)”）时重试。 以下代码使用 **NewDownloadStream** 函数下载 Blob。 Blob 的内容写入到缓冲区中，显示在控制台上。

```go
// Here's how to download the blob. NOTE: This method automatically retries if the connection fails
// during download (the low-level GetBlob function does NOT retry errors when reading from its stream).
stream := azblob.NewDownloadStream(ctx, blobURL.GetBlob, azblob.DownloadStreamOptions{})
downloadedData := &bytes.Buffer{}
_, err = downloadedData.ReadFrom(stream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>清理资源
如果不再需要本快速入门中上传的 Blob，可使用 **Delete** 方法删除整个容器。 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press the enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="next-steps"></a>后续步骤
 
本快速入门介绍了如何使用 Go 在本地磁盘和 Azure Blob 存储之间转移文件。 有关 Azure 存储 Blob SDK 的详细信息，请查看[源代码](https://github.com/Azure/azure-storage-blob-go/)和 [API 参考](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob)。