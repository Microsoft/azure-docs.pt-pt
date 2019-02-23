---
title: Enlaces do Service Bus do Azure para as funções do Azure
description: Compreenda como utilizar o Azure Service Bus acionadores e enlaces nas funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: ac71a88d6c2cbd3a7159ac7382872ea28e079bed
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728153"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Enlaces do Service Bus do Azure para as funções do Azure

Este artigo explica como trabalhar com ligações de Azure Service Bus nas funções do Azure. Funções do Azure suporta acionaram e ligações para tópicos e filas do Service Bus de saída.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

Os enlaces do Service Bus são fornecidos na [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) pacote NuGet, versão 2.x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Os enlaces do Service Bus são fornecidos na [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) pacote NuGet, versão 3.x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Acionador

Utilize o acionador do Service Bus para responder às mensagens a partir de uma fila do Service Bus ou tópico. 

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de idioma específico:

* [C#](#trigger---c-example)
* [Script do c# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Acionador - exemplo do c#

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que lê [metadados de mensagem](#trigger---message-metadata) e regista uma mensagem de fila do Service Bus:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", AccessRights.Manage, Connection = "ServiceBusConnection")] 
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

Este exemplo é para a versão das funções do Azure 1.x. Para fazer esse código funcionar para 2.x:

- [omita o parâmetro de direitos de acesso](#trigger---configuration)
- alterar o tipo do parâmetro de registo de `TraceWriter` para `ILogger`
- Alteração `log.Info` para `log.LogInformation`
 
### <a name="trigger---c-script-example"></a>Acionador - exemplo de script do c#

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

### <a name="trigger---f-example"></a>Acionador - F# exemplo

O exemplo seguinte mostra um acionador de barramento de serviço de enlace num *Function* ficheiro e uma [ F# função](functions-reference-fsharp.md) que utiliza o enlace. A função regista uma mensagem de fila do Service Bus. 

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

Aqui está o F# código de script:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---javascript-example"></a>Acionador - exemplo de JavaScript

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

### <a name="trigger---java-example"></a>Acionador - exemplo de Java

O exemplo seguinte mostra um acionador de barramento de serviço de enlace num *Function* ficheiro e uma [função Java](functions-reference-java.md) que utiliza o enlace. A função é acionada por uma mensagem colocada numa fila do Service Bus e a função regista a mensagem de fila.

Eis a vinculação de dados a *Function* ficheiro:

```json
{
"bindings": [
    {
    "queueName": "myqueuename",
    "connection": "MyServiceBusConnection",
    "name": "msg",
    "type": "ServiceBusQueueTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Eis o código Java:

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

## <a name="trigger---attributes"></a>Acionador - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize os seguintes atributos para configurar um acionador do Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

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

  Pode definir o `Connection` propriedade para especificar a conta de barramento de serviço a utilizar, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Para obter um exemplo completo, consulte [acionador - exemplo do c#](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

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

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `ServiceBusTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido para "serviceBusTrigger". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido para "in". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável que representa a mensagem de fila ou tópico no código de função. Definido como "$return" para referenciar o valor de retorno da função. | 
|**queueName**|**QueueName**|Nome da fila para monitorizar.  Defina apenas se a monitorização de uma fila, não para um tópico.
|**topicName**|**topicName**|Nome do tópico para monitorizar. Defina apenas se a monitorização de um tópico, não para uma fila.|
|**subscriptionName**|**subscriptionName**|Nome da subscrição a monitorizar. Defina apenas se a monitorização de um tópico, não para uma fila.|
|**ligação**|**ligação**|O nome de uma definição de aplicação que contém a cadeia de ligação do Service Bus para utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se definir `connection` para "MyServiceBus", o runtime das funções procura uma definição de aplicação com o nome "AzureWebJobsMyServiceBus." Se deixar `connection` vazio, o runtime das funções utiliza a cadeia de ligação do Service Bus de predefinição na definição da aplicação com o nome "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos apresentados na [obter as credenciais de gestão](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#get-the-connection-string). A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico. |
|**accessRights**|**Acesso**|Direitos de acesso da cadeia de ligação. Valores disponíveis são `manage` e `listen`. A predefinição é `manage`, que indica que o `connection` tem o **gerir** permissão. Se usar uma cadeia de ligação que não tenha as **gerir** conjunto de permissões, `accessRights` de "ouvir". Caso contrário, as funções de tempo de execução poderá falhar tentando fazer operações que exigem gerir direitos. Na versão das funções do Azure 2.x, esta propriedade não está disponível porque a versão mais recente do SDK do armazenamento não suporta operações de gerir.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

No c# e no c# script, pode utilizar os seguintes tipos de parâmetro para a mensagem de fila ou tópico:

* `string` -Se a mensagem de texto.
* `byte[]` -Útil para dados binários.
* Um tipo personalizado - se a mensagem contém JSON, as funções do Azure tenta desserializar os dados JSON.
* `BrokeredMessage` -Dá-lhe a mensagem de serialização anulada com o [BrokeredMessage.GetBody<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) método.

Esses parâmetros são para a versão das funções do Azure 1.x; para 2.x, utilize [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage`.

No JavaScript, aceder a mensagem de fila ou tópico ao utilizar `context.bindings.<name from function.json>`. A mensagem do Service Bus é passada para a função como uma cadeia ou um objeto JSON.

## <a name="trigger---poison-messages"></a>Acionador - mensagens suspeitas

Processamento de mensagens não processáveis não pode ser controlado ou configurado nas funções do Azure. Do Service Bus processa mensagens suspeitas em si.

## <a name="trigger---peeklock-behavior"></a>Acionador - PeekLock comportamento

O runtime das funções recebe uma mensagem numa [PeekLock modo](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Ele chama `Complete` na mensagem se a função for concluída com êxito ou chamadas `Abandon` se a função falhar. Se a função é executada mais do que o `PeekLock` tempo limite, o bloqueio é renovado automaticamente, desde que a função está em execução. 

O `maxAutoRenewDuration` pode ser configurada no *Host. JSON*, que é mapeado para [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). O máximo permitido para esta definição é de 5 minutos, de acordo com a documentação do Service Bus, ao passo que pode aumentar o limite de tempo de funções da predefinição de 5 minutos para 10 minutos. Para as funções do Service Bus não iria querer fazer isso, em seguida, porque iria exceder o limite de renovação do Service Bus.

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
|`Label`|`string`|A etiqueta específica da aplicação.|
|`CorrelationId`|`string`|O ID de correlação.|
|`UserProperties`|`IDictionary<String,Object>`|As propriedades de mensagem específica da aplicação.|

> [!NOTE]
> Atualmente, acionador só funciona com filas e subscrições que não as utilizam. Volte a controlar [este item de funcionalidade](https://github.com/Azure/azure-functions-host/issues/563) para todas as atualizações mais sobre esta funcionalidade. 

Ver [exemplos de código](#trigger---example) que utilizam estas propriedades no início deste artigo.

## <a name="trigger---hostjson-properties"></a>Acionador - propriedades de Host. JSON

O [Host. JSON](functions-host-json.md#servicebus) ficheiro contém as definições que controlam o comportamento de Acionador do Service Bus.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-service-bus.md)]

## <a name="output"></a>Saída

Utilize o enlace de saída de Azure Service Bus para enviar mensagens de fila ou tópico.

## <a name="output---example"></a>Saída - exemplo

Veja o exemplo de idioma específico:

* [C#](#output---c-example)
* [Script do c# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* Java

### <a name="output---c-example"></a>Saída - exemplo do c#

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

### <a name="output---c-script-example"></a>Saída - exemplo de script do c#

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
public static void Run(TimerInfo myTimer, ILogger log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Saída - F# exemplo

O exemplo seguinte mostra uma saída de barramento de serviço de enlace num *Function* ficheiro e uma [ F# função do script](functions-reference-fsharp.md) que utiliza o enlace. A função utiliza um acionador de temporizador para enviar uma mensagem de fila a cada 15 segundos.

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

Segue-se F# código que cria uma única mensagem do script:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---javascript-example"></a>Saída - exemplo de JavaScript

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


### <a name="output---java-example"></a>Saída - exemplo de Java

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

## <a name="output---attributes"></a>Saída - atributos

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [ServiceBusAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Construtor do atributo obtém o nome da fila ou tópico e subscrição. Também pode especificar direitos de acesso da ligação. Como escolher os definição de direitos de acesso é explicado no [de saída - configuração](#output---configuration) secção. Eis um exemplo que mostra o atributo aplicado para o valor de retorno da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Pode definir o `Connection` propriedade para especificar a conta de barramento de serviço a utilizar, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída - exemplo do c#](#output---c-example).

Pode utilizar o `ServiceBusAccount` atributo para especificar a conta do Service Bus para utilizar no nível de classe, método ou parâmetro.  Para obter mais informações, consulte [acionador - atributos](#trigger---attributes).

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `ServiceBus` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido para "serviceBus". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido para "Sair". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila ou tópico no código de função. Definido como "$return" para referenciar o valor de retorno da função. | 
|**queueName**|**QueueName**|Nome da fila.  Defina apenas se o envio de mensagens de fila, não para um tópico.
|**topicName**|**topicName**|Nome do tópico para monitorizar. Defina apenas se o envio de mensagens de tópico, não para uma fila.|
|**ligação**|**ligação**|O nome de uma definição de aplicação que contém a cadeia de ligação do Service Bus para utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se definir `connection` para "MyServiceBus", o runtime das funções procura uma definição de aplicação com o nome "AzureWebJobsMyServiceBus." Se deixar `connection` vazio, o runtime das funções utiliza a cadeia de ligação do Service Bus de predefinição na definição da aplicação com o nome "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos apresentados na [obter as credenciais de gestão](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md#get-the-connection-string). A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico.|
|**accessRights**|**Acesso**|Direitos de acesso da cadeia de ligação. Valores disponíveis são `manage` e `listen`. A predefinição é `manage`, que indica que o `connection` tem o **gerir** permissão. Se usar uma cadeia de ligação que não tenha as **gerir** conjunto de permissões, `accessRights` de "ouvir". Caso contrário, as funções de tempo de execução poderá falhar tentando fazer operações que exigem gerir direitos. Na versão das funções do Azure 2.x, esta propriedade não está disponível porque a versão mais recente do SDK do armazenamento não suporta operações de gerir.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

Nas funções do Azure 1.x, o tempo de execução cria a fila se não existir e tiver definido `accessRights` para `manage`. Na versão de funções 2.x, a fila ou tópico já deve existir; Se especificar uma fila ou tópico que não existe, a função irá falhar. 

No c# e no c# script, pode utilizar os seguintes tipos de parâmetro para o enlace de saída:

* `out T paramName` - `T` pode ser qualquer tipo serializável JSON. Se o valor do parâmetro for nulo quando a função sai, as funções cria a mensagem com um objeto nulo.
* `out string` – Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.
* `out byte[]` – Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.
* `out BrokeredMessage` – Se o valor do parâmetro é nulo quando a função sai, as funções não cria uma mensagem.
* `ICollector<T>` ou `IAsyncCollector<T>` – para a criação de várias mensagens. Uma mensagem é criada quando chama o `Add` método.

Nas funções assíncronas, utilize o valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

Esses parâmetros são para a versão das funções do Azure 1.x; para 2.x, utilize [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage`.

No JavaScript, aceder a fila ou tópico ao utilizar `context.bindings.<name from function.json>`. Pode atribuir uma cadeia de caracteres, uma matriz de bytes ou um objeto Javascript (anular a serialização em JSON) para `context.binding.<name>`.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Service Bus | [Códigos de erro do Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limites do barramento de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta secção descreve as definições de configuração global disponíveis para essa ligação na versão 2.x. O ficheiro de Host. JSON de exemplo abaixo contém apenas as versão 2.x as definições para este enlace. Para obter mais informações sobre as definições de configuração global na versão 2.x, consulte [referência de Host. JSON para a versão das funções do Azure 2.x](functions-host-json.md).

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
            }
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------| 
|maxAutoRenewDuration|00:05:00|A duração máxima em que o bloqueio da mensagem será renovado automaticamente.| 
|Preenchimento automático|true|Se o acionador imediatamente deve marcar como concluída (conclusão automática) ou aguardar o processamento para chamar completa.| 
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que deve iniciar o bombardeamento de mensagens. Por predefinição, o runtime das funções processa várias mensagens em simultâneo. Para direcionar o tempo de execução para processar apenas uma única fila ou uma mensagem de tópico ao mesmo tempo, defina `maxConcurrentCalls` como 1. | 
|prefetchCount|n/d|A predefinição PrefetchCount que será utilizada pelo MessageReceiver subjacente.| 


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
