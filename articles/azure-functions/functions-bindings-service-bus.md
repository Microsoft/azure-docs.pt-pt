---
title: Enlaces do Service Bus do Azure para as funções do Azure
description: Compreenda como utilizar o Azure Service Bus acionadores e enlaces nas funções do Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: 424d797410c091dc53687284c2b32e2f1f0358e1
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549075"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Enlaces do Service Bus do Azure para as funções do Azure

Este artigo explica como trabalhar com ligações de Azure Service Bus nas funções do Azure. As Funções do Azure suportam associações de acionador e saída para filas e tópicos do Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

Os enlaces do Service Bus são fornecidos na [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) pacote NuGet, versão 2.x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

Os enlaces do Service Bus são fornecidos na [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) pacote NuGet, versão 3.x. O código-fonte do pacote está no repositório GitHub [Azure-Functions-ServiceBus-Extension](https://github.com/Azure/azure-functions-servicebus-extension) .

> [!NOTE]
> As versões 2. x e superiores não criam o tópico ou a assinatura configurada na instância de `ServiceBusTrigger`. Essas versões são baseadas em [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) , que não lida com o gerenciamento de filas.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Acionador

Utilize o acionador do Service Bus para responder às mensagens a partir de uma fila do Service Bus ou tópico. 

## <a name="trigger---example"></a>Acionador - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que lê [metadados de mensagem](#trigger---message-metadata) e regista uma mensagem de fila do Service Bus:

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo seguinte mostra um acionador de barramento de serviço de enlace num *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função lê [metadados de mensagem](#trigger---message-metadata) e regista uma mensagem de fila do Service Bus.

Eis a vinculação de dados a *Function* ficheiro:

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

Aqui está o código de script do c#:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra um acionador de barramento de serviço de enlace num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função lê [metadados de mensagem](#trigger---message-metadata) e regista uma mensagem de fila do Service Bus. 

Eis a vinculação de dados a *Function* ficheiro:

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

Eis o código de script do JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem da fila do barramento de serviço por meio de um gatilho.

Uma associação do barramento de serviço é definida em *Function. JSON* , em que *Type* é definido como `serviceBusTrigger`.

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

# <a name="javatabjava"></a>[Java](#tab/java)

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

---

## <a name="trigger---attributes-and-annotations"></a>Gatilho-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize os seguintes atributos para configurar um acionador do Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Construtor do atributo obtém o nome da fila ou tópico e subscrição. Na versão das funções do Azure 1.x, também pode especificar direitos de acesso da ligação. Se não especificar direitos de acesso, a predefinição é `Manage`. Para obter mais informações, consulte a [acionador - configuração](#trigger---configuration) secção.

  Eis um exemplo que mostra o atributo utilizado com um parâmetro de cadeia de caracteres:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Você pode definir a propriedade `Connection` para especificar o nome de uma configuração de aplicativo que contém a cadeia de conexão do barramento de serviço a ser usada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Para obter um exemplo completo, consulte [gatilho-exemplo](#trigger---example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Proporciona outra forma de especificar a conta de barramento de serviço a utilizar. O construtor obtém o nome de uma definição de aplicação que contém uma cadeia de ligação do Service Bus. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo seguinte mostra o nível de classe e método:

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

A conta de barramento de serviço a utilizar é determinada pela seguinte ordem:

* O `ServiceBusTrigger` do atributo `Connection` propriedade.
* O `ServiceBusAccount` atributo aplicado para o mesmo parâmetro como o `ServiceBusTrigger` atributo.
* O `ServiceBusAccount` atributo aplicado à função.
* O `ServiceBusAccount` aplicado à classe de atributo.
* A definição de aplicação "AzureWebJobsServiceBus".

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação `ServiceBusQueueTrigger` permite que você crie uma função que é executada quando uma mensagem de fila do barramento de serviço é criada. As opções de configuração disponíveis incluem nome da fila e nome da cadeia de conexão.

A anotação `ServiceBusTopicTrigger` permite designar um tópico e uma assinatura para direcionar quais dados disparam a função.

Consulte o [exemplo](#trigger---example) de gatilho para obter mais detalhes.

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `ServiceBusTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido para "serviceBusTrigger". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido para "in". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável que representa a mensagem de fila ou tópico no código de função. |
|**queueName**|**QueueName**|Nome da fila para monitorizar.  Defina apenas se a monitorização de uma fila, não para um tópico.
|**topicName**|**topicName**|Nome do tópico para monitorizar. Defina apenas se a monitorização de um tópico, não para uma fila.|
|**subscriptionName**|**subscriptionName**|Nome da subscrição a monitorizar. Defina apenas se a monitorização de um tópico, não para uma fila.|
|**ligação**|**ligação**|O nome de uma definição de aplicação que contém a cadeia de ligação do Service Bus para utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se definir `connection` para "MyServiceBus", o runtime das funções procura uma definição de aplicação com o nome "AzureWebJobsMyServiceBus." Se deixar `connection` vazio, o runtime das funções utiliza a cadeia de ligação do Service Bus de predefinição na definição da aplicação com o nome "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico. |
|**accessRights**|**Acesso**|Direitos de acesso da cadeia de ligação. Valores disponíveis são `manage` e `listen`. A predefinição é `manage`, que indica que o `connection` tem o **gerir** permissão. Se usar uma cadeia de ligação que não tenha as **gerir** conjunto de permissões, `accessRights` de "ouvir". Caso contrário, as funções de tempo de execução poderá falhar tentando fazer operações que exigem gerir direitos. No Azure Functions versão 2. x e superior, essa propriedade não está disponível porque a versão mais recente do SDK do barramento de serviço não dá suporte a operações de gerenciamento.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` se estiver se conectando a uma fila ou assinatura [com reconhecimento de sessão](../service-bus-messaging/message-sessions.md) . `false` caso contrário, que é o valor padrão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Os seguintes tipos de parâmetro estão disponíveis para a mensagem de fila ou tópico:

* `string` -Se a mensagem de texto.
* `byte[]` -Útil para dados binários.
* Um tipo personalizado - se a mensagem contém JSON, as funções do Azure tenta desserializar os dados JSON.
* `BrokeredMessage` – fornece a você a mensagem desserializada com o método [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Esses tipos de parâmetro são para Azure Functions versão 1. x; para 2. x e superior, use [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage`.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Os seguintes tipos de parâmetro estão disponíveis para a mensagem de fila ou tópico:

* `string` -Se a mensagem de texto.
* `byte[]` -Útil para dados binários.
* Um tipo personalizado - se a mensagem contém JSON, as funções do Azure tenta desserializar os dados JSON.
* `BrokeredMessage` – fornece a você a mensagem desserializada com o método [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Esses parâmetros são para Azure Functions versão 1. x; para 2. x e superior, use [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Acesse a mensagem da fila ou do tópico usando `context.bindings.<name from function.json>`. A mensagem do Service Bus é passada para a função como uma cadeia ou um objeto JSON.

# <a name="pythontabpython"></a>[Python](#tab/python)

A mensagem da fila está disponível para a função por meio de um parâmetro digitado como `func.ServiceBusMessage`. A mensagem do Service Bus é passada para a função como uma cadeia ou um objeto JSON.

# <a name="javatabjava"></a>[Java](#tab/java)

A mensagem do barramento de serviço de entrada está disponível por meio de um parâmetro `ServiceBusQueueMessage` ou `ServiceBusTopicMessage`.

[Consulte o exemplo para obter detalhes](#trigger).

---

## <a name="trigger---poison-messages"></a>Acionador - mensagens suspeitas

Processamento de mensagens não processáveis não pode ser controlado ou configurado nas funções do Azure. Do Service Bus processa mensagens suspeitas em si.

## <a name="trigger---peeklock-behavior"></a>Acionador - PeekLock comportamento

O runtime das funções recebe uma mensagem numa [PeekLock modo](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Ele chama `Complete` na mensagem se a função for concluída com êxito ou chamadas `Abandon` se a função falhar. Se a função é executada mais do que o `PeekLock` tempo limite, o bloqueio é renovado automaticamente, desde que a função está em execução. 

O `maxAutoRenewDuration` é configurável em *host. JSON*, que é mapeado para [onmessageoptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). O máximo permitido para esta definição é de 5 minutos, de acordo com a documentação do Service Bus, ao passo que pode aumentar o limite de tempo de funções da predefinição de 5 minutos para 10 minutos. Para as funções do Service Bus não iria querer fazer isso, em seguida, porque iria exceder o limite de renovação do Service Bus.

## <a name="trigger---message-metadata"></a>Acionador - metadados de mensagem

O acionador do Service Bus fornece vários [propriedades de metadados](./functions-bindings-expressions-patterns.md#trigger-metadata). Essas propriedades podem ser utilizadas como parte das expressões de associação nas outras associações, ou como parâmetros no seu código. Estas são as propriedades do [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) classe.

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|O número de entregas.|
|`DeadLetterSource`|`string`|A origem de entregues.|
|`ExpiresAtUtc`|`DateTime`|A hora de expiração em UTC.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo de colocados em fila em UTC.|
|`MessageId`|`string`|Um valor definido pelo utilizador que o Service Bus pode utilizar para identificar mensagens duplicadas, se estiver ativada.|
|`ContentType`|`string`|Um identificador de tipo de conteúdo utilizado pelo remetente e receptor para lógica específica de aplicativo.|
|`ReplyTo`|`string`|O endereço de resposta fila.|
|`SequenceNumber`|`Int64`|O número exclusivo atribuído a uma mensagem pelo Service Bus.|
|`To`|`string`|Enviar para endereço.|
|`Label`|`string`|O rótulo específico do aplicativo.|
|`CorrelationId`|`string`|O ID de correlação.|

> [!NOTE]
> Atualmente, o gatilho do barramento de serviço que funciona com filas e assinaturas habilitadas para sessão está em versão prévia. Acompanhe [este item](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) para obter atualizações adicionais sobre isso. 

Ver [exemplos de código](#trigger---example) que utilizam estas propriedades no início deste artigo.

## <a name="output"></a>Saída

Utilize o enlace de saída de Azure Service Bus para enviar mensagens de fila ou tópico.

### <a name="output---example"></a>Saída - exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que envia uma mensagem de fila do Service Bus:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo seguinte mostra uma saída de barramento de serviço de enlace num *Function* ficheiro e uma [função de script do c#](functions-reference-csharp.md) que utiliza o enlace. A função utiliza um acionador de temporizador para enviar uma mensagem de fila a cada 15 segundos.

Eis a vinculação de dados a *Function* ficheiro:

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

Aqui está o script código c# que cria uma única mensagem:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

O script código c# aqui está que cria várias mensagens:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma saída de barramento de serviço de enlace num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função utiliza um acionador de temporizador para enviar uma mensagem de fila a cada 15 segundos.

Eis a vinculação de dados a *Function* ficheiro:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir demonstra como gravar em uma fila do barramento de serviço em Python.

Uma definição de associação do barramento de serviço é definida em *Function. JSON* , em que *Type* é definido como `serviceBus`.

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

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo seguinte mostra uma função de Java que envia uma mensagem numa fila do Service Bus `myqueue` quando acionada por um pedido HTTP.

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

 Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `@QueueOutput` anotação nos parâmetros de função, seria escrito cujo valor a uma fila do Service Bus.  O tipo de parâmetro deve ser `OutputBinding<T>`, em que T é qualquer tipo de Java nativo de um POJO.

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

---

## <a name="output---attributes-and-annotations"></a>Saída-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Construtor do atributo obtém o nome da fila ou tópico e subscrição. Também pode especificar direitos de acesso da ligação. Como escolher os definição de direitos de acesso é explicado no [de saída - configuração](#output---configuration) secção. Eis um exemplo que mostra o atributo aplicado para o valor de retorno da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Você pode definir a propriedade `Connection` para especificar o nome de uma configuração de aplicativo que contém a cadeia de conexão do barramento de serviço a ser usada, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída-exemplo](#output---example).

Pode utilizar o `ServiceBusAccount` atributo para especificar a conta do Service Bus para utilizar no nível de classe, método ou parâmetro.  Para obter mais informações, consulte [acionador - atributos](#trigger---attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

As anotações `ServiceBusQueueOutput` e `ServiceBusTopicOutput` estão disponíveis para gravar uma mensagem como uma saída de função. O parâmetro decorado com essas anotações deve ser declarado como um `OutputBinding<T>` onde `T` é o tipo correspondente ao tipo da mensagem.

---

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `ServiceBus` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido para "serviceBus". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido para "Sair". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável que representa a mensagem de fila ou tópico no código de função. Definido como "$return" para referenciar o valor de retorno da função. |
|**queueName**|**QueueName**|Nome da fila.  Defina apenas se o envio de mensagens de fila, não para um tópico.
|**topicName**|**topicName**|Nome do tópico. Defina apenas se o envio de mensagens de tópico, não para uma fila.|
|**ligação**|**ligação**|O nome de uma definição de aplicação que contém a cadeia de ligação do Service Bus para utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se definir `connection` para "MyServiceBus", o runtime das funções procura uma definição de aplicação com o nome "AzureWebJobsMyServiceBus." Se deixar `connection` vazio, o runtime das funções utiliza a cadeia de ligação do Service Bus de predefinição na definição da aplicação com o nome "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de conexão, siga as etapas mostradas em [obter as credenciais de gerenciamento](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico.|
|**accessRights**|**Acesso**|Direitos de acesso da cadeia de ligação. Valores disponíveis são `manage` e `listen`. A predefinição é `manage`, que indica que o `connection` tem o **gerir** permissão. Se usar uma cadeia de ligação que não tenha as **gerir** conjunto de permissões, `accessRights` de "ouvir". Caso contrário, as funções de tempo de execução poderá falhar tentando fazer operações que exigem gerir direitos. No Azure Functions versão 2. x e superior, essa propriedade não está disponível porque a versão mais recente do SDK do barramento de serviço não dá suporte a operações de gerenciamento.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

Nas funções do Azure 1.x, o tempo de execução cria a fila se não existir e tiver definido `accessRights` para `manage`. Nas funções versão 2. x e superior, a fila ou o tópico já deve existir; Se você especificar uma fila ou um tópico que não existe, a função falhará. 

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Use os seguintes tipos de parâmetro para a associação de saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável JSON. Se o valor do parâmetro for nulo quando a função sai, as funções cria a mensagem com um objeto nulo.
* `out string` – Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.
* `out byte[]` – Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.
* `out BrokeredMessage`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 1. x)
* `out Message`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 2. x e superior)
* `ICollector<T>` ou `IAsyncCollector<T>` – para a criação de várias mensagens. Uma mensagem é criada quando chama o `Add` método.

Ao trabalhar com C# funções:

* As funções assíncronas precisam de um valor de retorno ou `IAsyncCollector` em vez de um parâmetro `out`.

* Para acessar a ID da sessão, associe a um tipo de [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) e use a propriedade `sessionId`.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Use os seguintes tipos de parâmetro para a associação de saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável JSON. Se o valor do parâmetro for nulo quando a função sai, as funções cria a mensagem com um objeto nulo.
* `out string` – Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.
* `out byte[]` – Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.
* `out BrokeredMessage`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 1. x)
* `out Message`-se o valor do parâmetro for nulo quando a função for encerrada, as funções não criarão uma mensagem (para as funções 2. x e superior)
* `ICollector<T>` ou `IAsyncCollector<T>` – para a criação de várias mensagens. Uma mensagem é criada quando chama o `Add` método.

Ao trabalhar com C# funções:

* As funções assíncronas precisam de um valor de retorno ou `IAsyncCollector` em vez de um parâmetro `out`.

* Para acessar a ID da sessão, associe a um tipo de [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) e use a propriedade `sessionId`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Acesse a fila ou o tópico usando `context.bindings.<name from function.json>`. Você pode atribuir uma cadeia de caracteres, uma matriz de bytes ou um objeto JavaScript (desserializado em JSON) para `context.binding.<name>`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Use o [SDK do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging) em vez da Associação de saída interna.

# <a name="javatabjava"></a>[Java](#tab/java)

Use o [SDK do barramento de serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging) em vez da Associação de saída interna.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Service Bus | [Códigos de erro do Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limites do barramento de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O arquivo host. JSON de exemplo abaixo contém apenas as configurações para essa associação. Para obter mais informações sobre definições de configuração global, consulte [referência de host. JSON para Azure Functions versão](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de Host. JSON nas funções 1.x, consulte [referência de Host. JSON para as funções do Azure 1.x](functions-host-json-v1.md).

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
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|A duração máxima em que o bloqueio da mensagem será renovado automaticamente.|
|Preenchimento automático|true|Se o gatilho deve marcar imediatamente a mensagem como concluída (AutoCompletar) ou aguardar até que a função seja encerrada com êxito para chamar a conclusão.|
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que deve iniciar o bombardeamento de mensagens. Por predefinição, o runtime das funções processa várias mensagens em simultâneo. Para direcionar o tempo de execução para processar apenas uma única fila ou uma mensagem de tópico ao mesmo tempo, defina `maxConcurrentCalls` como 1. |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
