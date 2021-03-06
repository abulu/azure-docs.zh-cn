---
title: Azure Functions 的 Azure 事件中心绑定
description: 了解如何在 Azure Functions 中使用 Azure 事件中心绑定。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions，函数，事件处理，动态计算，无服务体系结构
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: d79a57db6f56264d4070debbca83de4192f7f503
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987080"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Functions 的 Azure 事件中心绑定

本文介绍如何使用 Azure Functions 的 [Azure 事件中心](../event-hubs/event-hubs-what-is-event-hubs.md)绑定。 Azure Functions 支持事件中心的触发器和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>包 - Functions 1.x

对于 Azure Functions 版本 1.x，[Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet 包 2.x 版中提供了事件中心绑定。
[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) GitHub 存储库中提供了此包的源代码。


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>包 - Functions 2.x

对于 Functions 2.x，请使用 [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) 包 3.x 版。
[azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) GitHub 存储库中提供了此包的源代码。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>触发器

使用事件中心触发器来响应发送到事件中心事件流的事件。 若要设置触发器，必须具有事件中心的读取访问权限。

触发事件中心触发器函数时，进行触发的消息将作为字符串传递到该函数中。

## <a name="trigger---scaling"></a>触发器 - 缩放

事件中心触发的函数的每个实例都仅由一个 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 实例提供支持。 事件中心确保只有一个 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 实例能够在给定分区上获得租约。

例如，考虑如下所述的一个事件中心：

* 10 个分区。
* 在所有分区之间平均分配 1000 个事件，每个分区中有 100 条消息。

首次启用函数时，只有一个函数实例。 我们将此函数实例命名为 `Function_0`。 `Function_0` 具有单个 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 实例，此实例在所有十个分区上都有租约。 此实例从分区 0-9 读取事件。 从此时开始，将发生下列情况之一：

* **不需要新的函数实例**：在 Functions 的缩放逻辑介入之前，`Function_0` 能够处理所有 1000 个事件。 在这种情况下，所有 1000 条消息都由 `Function_0` 进行处理。

* **添加另一个函数实例**：Functions 缩放逻辑确定 `Function_0` 收到的消息数超出了它的处理能力。 在这种情况下，会创建一个新的函数应用实例 (`Function_1`) 以及一个新的 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 实例。 事件中心检测到一个新的主机实例正在尝试读取消息。 事件中心在其主机实例之间对分区进行负载均衡。 例如，可以将分区 0-4 分配给 `Function_0`，将分区 5-9 分配给 `Function_1`。 

* **额外添加 N 个函数实例**：Functions 缩放逻辑确定 `Function_0` 和 `Function_1` 收到的消息数超出了它们的处理能力。 创建新的函数应用实例 `Function_2`...`Functions_N`，其中，`N` 大于事件中心分区数。 在我们的示例中，事件中心再次对分区进行负载均衡，在本例中是在实例 `Function_0`...`Functions_9` 之间进行的。 

请注意，当 Functions 缩放到 `N` 个实例时，这是一个大于事件中心分区数的数字。 这样做是为了确保始终有 [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) 实例可供用来在其他实例释放锁时在分区上获取锁。 你只需为执行函数实例时使用的资源付费，不需要为过度预配的资源付费。

当所有函数执行都完成时（不管是否有错误），则会将检查点添加到关联的存储帐户。 检查点设置成功后，永远不会再次检索所有 1000 条消息。

## <a name="trigger---example"></a>触发器 - 示例

参阅语言特定的示例：

* [C#](#trigger---c-example)
* [C# 脚本 (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>触发器 - C# 示例

以下示例演示用于记录事件中心触发器消息正文的 [C# 函数](functions-dotnet-class-library.md)。

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

若要在函数代码中访问[事件元数据](#trigger---event-metadata)，请绑定到 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 对象（需要对 `Microsoft.Azure.EventHubs` 使用 using 语句）。 此外，还可以通过在方法签名中使用绑定表达式来访问相同的属性。  以下示例演示了获取相同数据的两种方法：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

若要成批接收事件，请将 `string` 或 `EventData` 设为数组。  

> [!NOTE]
> 成批接收事件时，不能像上述示例那样使用 `DateTime enqueuedTimeUtc` 绑定到方法参数，且必须从每个 `EventData` 对象中接收这些事件  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {Encoding.UTF8.GetString(message.Body)}");
        log.Info($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

### <a name="trigger---c-script-example"></a>触发器 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数记录事件中心触发器的消息正文。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x，第二个示例适用于 Functions 1.x。 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

C# 脚本代码如下所示：

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

若要在函数代码中访问[事件元数据](#trigger---event-metadata)，请绑定到 [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) 对象（需要对 `Microsoft.Azure.EventHubs` 使用 using 语句）。 此外，还可以通过在方法签名中使用绑定表达式来访问相同的属性。  以下示例演示了获取相同数据的两种方法：

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

若要成批接收事件，请将 `string` 或 `EventData` 设为数组：

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>触发器 - F# 示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数记录事件中心触发器的消息正文。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x，第二个示例适用于 Functions 1.x。 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

F# 代码如下所示：

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>触发器 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 此函数将读取[事件元数据](#trigger---event-metadata)并记录消息。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x，第二个示例适用于 Functions 1.x。 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

若要批量接收事件，请将 function.json 文件中的 `cardinality` 设为 `many`，如以下示例所示。 第一个示例适用于 Functions 2.x，第二个示例适用于 Functions 1.x。 

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

JavaScript 代码如下所示：

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---java-example"></a>触发器 - Java 示例

以下示例演示 function.json 文件中的一个事件中心触发器绑定以及使用该绑定的 [Java 函数](functions-reference-java.md)。 该函数记录事件中心触发器的消息正文。

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context ) 
       {
          context.getLogger().info(message);
 }
 ```

 在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值来自事件中心的参数使用 `EventHubTrigger` 注释。 带有这些注释的参数会导致函数在事件到达时运行。  可以将此注释与本机 Java 类型、POJO 或使用了 Optional<T> 的可为 null 的值一起使用。 

## <a name="trigger---attributes"></a>触发器 - 特性

在 [C# 类库](functions-dotnet-class-library.md)中，使用 [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) 特性。

该特性的构造函数使用事件中心的名称、使用者组的名称和包含连接字符串的应用设置的名称。 有关这些设置的详细信息，请参阅[触发器配置部分](#trigger---configuration)。 下面是 `EventHubTriggerAttribute` 特性的示例：

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

有关完整示例，请参阅[触发器 - C# 示例](#trigger---c-example)。

## <a name="trigger---configuration"></a>触发器 - 配置

下表解释了在 function.json 文件和 `EventHubTrigger` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|类型 | 不适用 | 必须设置为 `eventHubTrigger`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|direction | 不适用 | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|name | 不适用 | 在函数代码中表示事件项的变量的名称。 | 
|**路径** |**EventHubName** | 仅适用于 Functions 1.x。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 | 
|**eventHubName** |**EventHubName** | 仅适用于 Functions 2.x。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|**consumerGroup** |**ConsumerGroup** | 一个可选属性，用于设置[使用者组](../event-hubs/event-hubs-features.md#event-consumers)，该组用于订阅事件中心中的事件。 如果将其省略，则会使用 `$Default` 使用者组。 | 
|**基数** | 不适用 | 适用于 JavaScript。 设为 `many` 以启用批处理。  如果省略或设为 `one`，将向函数传递一条消息。 | 
|**连接** |**Connection** | 应用设置的名称，该名称中包含事件中心命名空间的连接字符串。 单击 [命名空间](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace) （而不是事件中心本身）的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须至少具有读取权限才可激活触发器。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>触发器 - 事件元数据

事件中心触发器提供了几个[元数据属性](functions-triggers-bindings.md#binding-expressions---trigger-metadata)。 这些属性可在其他绑定中用作绑定表达式的一部分，或者用作代码中的参数。 以下是 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) 类的属性。

|属性|类型|Description|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|`PartitionContext` 实例。|
|`EnqueuedTimeUtc`|`DateTime`|排队时间 (UTC)。|
|`Offset`|`string`|数据相对于事件中心分区流的偏移量。 偏移量是事件中心流中的事件的标记或标识符。 该标识符在事件中心流的分区中是惟一的。|
|`PartitionKey`|`string`|事件数据应该发送到的分区。|
|`Properties`|`IDictionary<String,Object>`|事件数据的用户属性。|
|`SequenceNumber`|`Int64`|事件的逻辑序列号。|
|`SystemProperties`|`IDictionary<String,Object>`|系统属性，包括事件数据。|

请参阅在本文的前面部分使用这些属性的[代码示例](#trigger---example)。

## <a name="trigger---hostjson-properties"></a>触发器 - host.json 属性

[host.json](functions-host-json.md#eventhub) 文件包含控制事件中心触发器行为的设置。

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>输出

使用事件中心输出绑定将事件写入到事件流。 必须具有事件中心的发送权限才可将事件写入到其中。

确保已设置所需的包引用：[Functions 1.x](#packages---functions-1.x) 或 [Functions 2.x](#packages---functions-2.x) 

## <a name="output---example"></a>输出 - 示例

参阅语言特定的示例：

* [C#](#output---c-example)
* [C# 脚本 (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>输出 - C# 示例

以下示例演示使用方法返回值作为输出将消息写入到事件中心的 [C# 函数](functions-dotnet-class-library.md)：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>输出 - C# 脚本示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [C# 脚本函数](functions-reference-csharp.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x，第二个示例适用于 Functions 1.x。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

下面是可创建一条消息的 C# 脚本代码：

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

下面是可创建多条消息的 C# 脚本代码：

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>输出 - F# 示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [F# 函数](functions-reference-fsharp.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x，第二个示例适用于 Functions 1.x。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

F# 代码如下所示：

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>输出 - JavaScript 示例

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [JavaScript 函数](functions-reference-node.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x，第二个示例适用于 Functions 1.x。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

下面是可发送一条消息的 JavaScript 代码：

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

下面是可发送多条消息的 JavaScript 代码：

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---java-example"></a>输出 - Java 示例

以下示例演示一个将包含当前时间的消息写入到事件中心的 Java 函数。

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
 ```

在 [Java 函数运行时库](/java/api/overview/azure/functions/runtime)中，对其值将被发布到事件中心的参数使用 `@EventHubOutput` 注释。  此参数应为 `OutputBinding<T>` 类型，其中 T 是 POJO 或任何本机 Java 类型。 

## <a name="output---attributes"></a>输出 - 特性

对于 [C# 类库](functions-dotnet-class-library.md)，使用 [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) 特性。

该特性的构造函数使用事件中心的名称和包含连接字符串的应用设置的名称。 有关这些设置的详细信息，请参阅[输出 - 配置](#output---configuration)。 下面是 `EventHub` 特性的示例：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

有关完整示例，请参阅[输出 - C# 示例](#output---c-example)。

## <a name="output---configuration"></a>输出 - 配置

下表解释了在 function.json 文件和 `EventHub` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|类型 | 不适用 | 必须设置为“eventHub”。 |
|direction | 不适用 | 必须设置为“out”。 在 Azure 门户中创建绑定时，会自动设置该参数。 |
|name | 不适用 | 函数代码中使用的表示事件的变量名称。 | 
|**路径** |**EventHubName** | 仅适用于 Functions 1.x。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 | 
|**eventHubName** |**EventHubName** | 仅适用于 Functions 2.x。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|**连接** |**Connection** | 应用设置的名称，该名称中包含事件中心命名空间的连接字符串。 单击 *命名空间* （而不是事件中心本身）的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须具有发送权限才可将消息发送到事件流。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>输出 - 用法

在 C# 和 C# 脚本中，可以使用 `out string paramName` 等方法参数发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 代替 `out string`。

在 JavaScript 中，通过使用 `context.bindings.<name>` 访问输出事件。 `<name>` 是在 *function.json* 的 `name` 属性中指定的值。

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 引用 |
|---|---|
| 事件中心 | [操作指南](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
