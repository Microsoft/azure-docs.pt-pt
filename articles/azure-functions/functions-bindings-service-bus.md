---
title: Associações do barramento de serviço do Azure para Azure Functions
description: Entenda como usar gatilhos e associações do barramento de serviço do Azure no Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: b261594076857b841ba288dfaba8b5b8e9250065
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987937"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Associações do barramento de serviço do Azure para Azure Functions

Este artigo explica como trabalhar com associações do barramento de serviço do Azure no Azure Functions. O Azure Functions dá suporte a associações de gatilho e saída para filas e tópicos do barramento de serviço.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes – funções 1. x

As associações do barramento de serviço são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) , versão 2. x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacotes – funções 2. x

As associações do barramento de serviço são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) , versão 3. x. O código-fonte do pacote está no repositório GitHub [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/) .

> [!NOTE]
> A versão 2. x não cria o tópico ou a assinatura configurada na instância de `ServiceBusTrigger`. A versão 2. x é baseada em [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) e não lida com o gerenciamento de filas.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Acionador

Use o gatilho do barramento de serviço para responder às mensagens de uma fila ou tópico do barramento de serviço. 

## <a name="trigger---example"></a>Gatilho-exemplo

Consulte o exemplo específico de linguagem:

* [C#](#trigger---c-example)
* [C#script (. CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Gatilho- C# exemplo

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que lê [metadados de mensagem](#trigger---message-metadata) e registra uma mensagem de fila do barramento de serviço:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

### <a name="trigger---c-script-example"></a>Gatilho- C# exemplo de script

O exemplo a seguir mostra uma associação de gatilho do barramento de serviço em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função lê [metadados de mensagem](#trigger---message-metadata) e registra uma mensagem de fila do barramento de serviço.

Aqui estão os dados de associação no arquivo *Function. JSON* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Este é o C# código de script:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>Gatilho- F# exemplo

O exemplo a seguir mostra uma associação de gatilho do barramento de serviço em um arquivo *Function. JSON* e uma [ F# função](functions-reference-fsharp.md) que usa a associação. A função registra uma mensagem de fila do barramento de serviço. 

Aqui estão os dados de associação no arquivo *Function. JSON* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Este é o F# código de script:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Gatilho-exemplo de Java

A função Java a seguir usa a anotação `@ServiceBusQueueTrigger` da [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime) para descrever a configuração de um gatilho de fila do barramento de serviço. A função captura a mensagem colocada na fila e a adiciona aos logs.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

As funções Java também podem ser disparadas quando uma mensagem é adicionada a um tópico do barramento de serviço. O exemplo a seguir usa a anotação `@ServiceBusTopicTrigger` para descrever a configuração do gatilho.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>Gatilho-exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho do barramento de serviço em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função lê [metadados de mensagem](#trigger---message-metadata) e registra uma mensagem de fila do barramento de serviço. 

Aqui estão os dados de associação no arquivo *Function. JSON* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Este é o código do script JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

### <a name="trigger---python-example"></a>Gatilho-exemplo de Python

O exemplo a seguir demonstra como ler uma mensagem da fila do ServiceBus por meio de um gatilho.

Uma associação de ServiceBus é definida em *Function. JSON* , em que *Type* é definido como `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

O código em  *_\_init_\_. py* declara um parâmetro como `func.ServiceBusMessage`, o que permite que você leia a mensagem da fila em sua função.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Gatilho-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho do barramento de serviço:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  O construtor do atributo usa o nome da fila ou do tópico e da assinatura. No Azure Functions versão 1. x, você também pode especificar os direitos de acesso da conexão. Se você não especificar direitos de acesso, o padrão será `Manage`. Para obter mais informações, consulte a seção [gatilho-configuração](#trigger---configuration) .

  Aqui está um exemplo que mostra o atributo usado com um parâmetro de cadeia de caracteres:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Você pode definir a propriedade `Connection` para especificar a conta do barramento de serviço a ser usada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Para obter um exemplo completo, consulte [gatilho C# -exemplo](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Fornece outra maneira de especificar a conta do barramento de serviço a ser usada. O construtor usa o nome de uma configuração de aplicativo que contém uma cadeia de conexão do barramento de serviço. O atributo pode ser aplicado ao parâmetro, método ou nível de classe. O exemplo a seguir mostra o nível de classe e método:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

A conta do barramento de serviço a ser usada é determinada na seguinte ordem:

* A propriedade `Connection` do atributo de `ServiceBusTrigger`.
* O atributo `ServiceBusAccount` aplicado ao mesmo parâmetro que o atributo `ServiceBusTrigger`.
* O atributo `ServiceBusAccount` aplicado à função.
* O atributo `ServiceBusAccount` aplicado à classe.
* A configuração do aplicativo "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Gatilho-configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `ServiceBusTrigger`.

|Propriedade function. JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "serviceBusTrigger". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direção** | n/d | Deve ser definido como "in". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**nomes** | n/d | O nome da variável que representa a mensagem de fila ou de tópico no código de função. Defina como "$return" para referenciar o valor de retorno da função. |
|**queueName**|**QueueName**|Nome da fila a ser monitorada.  Defina somente se estiver monitorando uma fila, não para um tópico.
|**topicname**|**Topicname**|Nome do tópico a ser monitorado. Defina somente se estiver monitorando um tópico, não para uma fila.|
|**subscriptionName**|**SubscriptionName**|Nome da assinatura a ser monitorada. Defina somente se estiver monitorando um tópico, não para uma fila.|
|**conexão**|**Conexão**|O nome de uma configuração de aplicativo que contém a cadeia de conexão do barramento de serviço a ser usada para esta associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome. Por exemplo, se você definir `connection` como "MyServiceBus", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyServiceBus". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão padrão do barramento de serviço na configuração do aplicativo chamada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de conexão deve ser para um namespace do barramento de serviço, não limitado a uma fila ou tópico específico. |
|**accessRights**|**Acesso**|Direitos de acesso para a cadeia de conexão. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **gerenciar** . Se você usar uma cadeia de conexão que não tenha a permissão **gerenciar** , defina `accessRights` como "escutar". Caso contrário, o tempo de execução do Functions pode falhar ao tentar realizar operações que exigem direitos de gerenciamento. No Azure Functions versão 2. x, essa propriedade não está disponível porque a versão mais recente do SDK de armazenamento não dá suporte a operações de gerenciamento.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Gatilho-uso

Em C# e C# script, você pode usar os seguintes tipos de parâmetro para a mensagem de fila ou tópico:

* `string` – se a mensagem for texto.
* `byte[]`-útil para dados binários.
* Um tipo personalizado – se a mensagem contiver JSON, Azure Functions tentará desserializar os dados JSON.
* `BrokeredMessage` – fornece a você a mensagem desserializada com o método [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Esses parâmetros são para Azure Functions versão 1. x; para 2. x, use [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage`.

No JavaScript, acesse a mensagem da fila ou do tópico usando `context.bindings.<name from function.json>`. A mensagem do barramento de serviço é passada para a função como uma cadeia de caracteres ou um objeto JSON.

## <a name="trigger---poison-messages"></a>Gatilho – mensagens suspeitas

A manipulação de mensagens suspeitas não pode ser controlada ou configurada no Azure Functions. O barramento de serviço manipula mensagens suspeitas em si.

## <a name="trigger---peeklock-behavior"></a>Gatilho-comportamento de PeekLock

O tempo de execução do Functions recebe uma mensagem no [modo Peeklock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Ele chamará `Complete` na mensagem se a função for concluída com êxito ou chamará `Abandon` se a função falhar. Se a função for executada mais tempo do que o tempo limite de `PeekLock`, o bloqueio será renovado automaticamente, desde que a função esteja em execução. 

O `maxAutoRenewDuration` é configurável em *host. JSON*, que é mapeado para [onmessageoptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). O máximo permitido para essa configuração é de 5 minutos, de acordo com a documentação do barramento de serviço, enquanto você pode aumentar o limite de tempo do Functions do padrão de 5 minutos para 10 minutos. Para as funções do barramento de serviço, não convém fazer isso, porque você excederia o limite de renovação do barramento de serviço.

## <a name="trigger---message-metadata"></a>Gatilho-metadados de mensagem

O gatilho do barramento de serviço fornece várias [Propriedades de metadados](./functions-bindings-expressions-patterns.md#trigger-metadata). Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Essas são propriedades da classe [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|O número de entregas.|
|`DeadLetterSource`|`string`|A origem da carta de inatividade.|
|`ExpiresAtUtc`|`DateTime`|O tempo de expiração em UTC.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo enfileirado em UTC.|
|`MessageId`|`string`|Um valor definido pelo usuário que o barramento de serviço pode usar para identificar mensagens duplicadas, se habilitadas.|
|`ContentType`|`string`|Um identificador de tipo de conteúdo utilizado pelo remetente e pelo destinatário para lógica específica do aplicativo.|
|`ReplyTo`|`string`|O endereço da fila de resposta para.|
|`SequenceNumber`|`Int64`|O número exclusivo atribuído a uma mensagem pelo barramento de serviço.|
|`To`|`string`|O endereço de envio para.|
|`Label`|`string`|O rótulo específico do aplicativo.|
|`CorrelationId`|`string`|A ID de correlação.|

> [!NOTE]
> Atualmente, o gatilho do barramento de serviço que funciona com filas e assinaturas habilitadas para sessão está em versão prévia. Acompanhe [este item](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) para obter atualizações adicionais sobre isso. 

Consulte os [exemplos de código](#trigger---example) que usam essas propriedades anteriormente neste artigo.

## <a name="trigger---hostjson-properties"></a>Trigger-Propriedades de host. JSON

O arquivo [host. JSON](functions-host-json.md#servicebus) contém configurações que controlam o comportamento do gatilho do barramento de serviço.

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "maxAutoRenewDuration": "00:05:00"
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que a bomba de mensagem deve iniciar. Por padrão, o tempo de execução do Functions processa várias mensagens simultaneamente. Para direcionar o tempo de execução para processar apenas uma única fila ou mensagem de tópico de cada vez, defina `maxConcurrentCalls` como 1. |
|prefetchCount|n/d|O PrefetchCount padrão que será usado pelo MessageReceiver subjacente.|
|maxAutoRenewDuration|00:05:00|A duração máxima na qual o bloqueio de mensagem será renovado automaticamente.|

## <a name="output"></a>Saída

Use a associação de saída do barramento de serviço do Azure para enviar mensagens de tópico ou de fila.

## <a name="output---example"></a>Saída-exemplo

Consulte o exemplo específico de linguagem:

* [C#](#output---c-example)
* [C#script (. CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Saída- C# exemplo

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que envia uma mensagem da fila do barramento de serviço:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Exemplo de C# script de saída

O exemplo a seguir mostra uma associação de saída do barramento de serviço em um arquivo *Function. JSON* e uma [ C# função de script](functions-reference-csharp.md) que usa a associação. A função usa um gatilho de temporizador para enviar uma mensagem da fila a cada 15 segundos.

Aqui estão os dados de associação no arquivo *Function. JSON* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Aqui está C# o código de script que cria uma única mensagem:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Aqui está C# o código de script que cria várias mensagens:

```cs
public static void Run(TimerInfo myTimer, ILogger log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Saída- F# exemplo

O exemplo a seguir mostra uma associação de saída do barramento de serviço em um arquivo *Function. JSON* e uma [ F# função de script](functions-reference-fsharp.md) que usa a associação. A função usa um gatilho de temporizador para enviar uma mensagem da fila a cada 15 segundos.

Aqui estão os dados de associação no arquivo *Function. JSON* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Aqui está F# o código de script que cria uma única mensagem:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Saída-exemplo de Java

O exemplo a seguir mostra uma função Java que envia uma mensagem para uma fila do barramento de serviço `myqueue` quando disparada por uma solicitação HTTP.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@QueueOutput` em parâmetros de função cujo valor seria gravado em uma fila do barramento de serviço.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde T é qualquer tipo Java nativo de um POJO.

As funções Java também podem gravar em um tópico do barramento de serviço. O exemplo a seguir usa a anotação `@ServiceBusTopicOutput` para descrever a configuração da Associação de saída. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

### <a name="output---javascript-example"></a>Saída-exemplo de JavaScript

O exemplo a seguir mostra uma associação de saída do barramento de serviço em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função usa um gatilho de temporizador para enviar uma mensagem da fila a cada 15 segundos.

Aqui estão os dados de associação no arquivo *Function. JSON* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Aqui está o código de script JavaScript que cria uma única mensagem:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Aqui está o código de script JavaScript que cria várias mensagens:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Saída-exemplo de Python

O exemplo a seguir demonstra como gravar em uma fila do ServiceBus em Python.

Uma definição de associação ServiceBue é definida em *Function. JSON* , em que *Type* é definido como `serviceBus`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

Em  *_\_init_\_. py*, você pode gravar uma mensagem na fila passando um valor para o método `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

## <a name="output---attributes"></a>Saída-atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o [atributo servicebusattribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

O construtor do atributo usa o nome da fila ou do tópico e da assinatura. Você também pode especificar os direitos de acesso da conexão. Como escolher a configuração de direitos de acesso é explicada na seção [saída-configuração](#output---configuration) . Aqui está um exemplo que mostra o atributo aplicado ao valor de retorno da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Você pode definir a propriedade `Connection` para especificar a conta do barramento de serviço a ser usada, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída C# -exemplo](#output---c-example).

Você pode usar o atributo `ServiceBusAccount` para especificar a conta do barramento de serviço a ser usada no nível de classe, método ou parâmetro.  Para obter mais informações, consulte [Trigger-Attributes](#trigger---attributes).

## <a name="output---configuration"></a>Saída-configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `ServiceBus`.

|Propriedade function. JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "serviceBus". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direção** | n/d | Deve ser definido como "out". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**nomes** | n/d | O nome da variável que representa a fila ou o tópico no código de função. Defina como "$return" para referenciar o valor de retorno da função. |
|**queueName**|**QueueName**|Nome da fila.  Defina somente se enviar mensagens da fila, não para um tópico.
|**topicname**|**Topicname**|Nome do tópico a ser monitorado. Defina somente se enviar mensagens de tópico, não para uma fila.|
|**conexão**|**Conexão**|O nome de uma configuração de aplicativo que contém a cadeia de conexão do barramento de serviço a ser usada para esta associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome. Por exemplo, se você definir `connection` como "MyServiceBus", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyServiceBus". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão padrão do barramento de serviço na configuração do aplicativo chamada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de conexão deve ser para um namespace do barramento de serviço, não limitado a uma fila ou tópico específico.|
|**accessRights**|**Acesso**|Direitos de acesso para a cadeia de conexão. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, que indica que o `connection` tem a permissão **gerenciar** . Se você usar uma cadeia de conexão que não tenha a permissão **gerenciar** , defina `accessRights` como "escutar". Caso contrário, o tempo de execução do Functions pode falhar ao tentar realizar operações que exigem direitos de gerenciamento. No Azure Functions versão 2. x, essa propriedade não está disponível porque a versão mais recente do SDK de armazenamento não dá suporte a operações de gerenciamento.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída-uso

No Azure Functions 1. x, o tempo de execução cria a fila, caso ela não exista, e você definiu `accessRights` como `manage`. Nas funções versão 2. x, a fila ou o tópico já deve existir; Se você especificar uma fila ou um tópico que não existe, a função falhará. 

Em C# e C# script, você pode usar os seguintes tipos de parâmetro para a associação de saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável por JSON. Se o valor do parâmetro for nulo quando a função for encerrada, o Functions criará a mensagem com um objeto nulo.
* `out string`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem.
* `out byte[]`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem.
* `out BrokeredMessage`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 1. x)
* `out Message`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 2. x)
* `ICollector<T>` ou `IAsyncCollector<T>`-para criar várias mensagens. Uma mensagem é criada quando você chama o método `Add`.

Ao trabalhar com C# funções:

* As funções assíncronas precisam de um valor de retorno ou `IAsyncCollector` em vez de um parâmetro `out`.

* Para acessar a ID da sessão, associe a um tipo de [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) e use a propriedade `sessionId`.

No JavaScript, acesse a fila ou o tópico usando `context.bindings.<name from function.json>`. Você pode atribuir uma cadeia de caracteres, uma matriz de bytes ou um objeto JavaScript (desserializado em JSON) para `context.binding.<name>`.

Para enviar uma mensagem para uma fila habilitada para sessão em outrosC# idiomas, use o [SDK do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging) em vez da Associação de saída interna.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vinculação | Referência |
|---|---|
| Service Bus | [Códigos de erro do barramento de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limites do barramento de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host. JSON

Esta seção descreve as definições de configuração global disponíveis para essa associação na versão 2. x. O arquivo host. JSON de exemplo abaixo contém apenas as configurações de versão 2. x para essa associação. Para obter mais informações sobre definições de configuração global na versão 2. x, consulte [referência de host. JSON para Azure Functions versão 2. x](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host. JSON nas funções 1. x, consulte [referência de host. JSON para Azure Functions 1. x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            }
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|A duração máxima na qual o bloqueio de mensagem será renovado automaticamente.|
|Preenchimento automático|true|Se o gatilho deve marcar imediatamente como concluído (preenchimento automático) ou aguardar o processamento ser concluído.|
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que a bomba de mensagem deve iniciar. Por padrão, o tempo de execução do Functions processa várias mensagens simultaneamente. Para direcionar o tempo de execução para processar apenas uma única fila ou mensagem de tópico de cada vez, defina `maxConcurrentCalls` como 1. |
|prefetchCount|n/d|O PrefetchCount padrão que será usado pelo MessageReceiver subjacente.|


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os gatilhos e associações do Azure Functions](functions-triggers-bindings.md)
