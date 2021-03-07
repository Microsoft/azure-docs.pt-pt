---
title: Gatilho de ônibus de serviço Azure para funções Azure
description: Aprenda a executar uma Função Azure quando as mensagens de autocarro do Serviço Azure forem criadas.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4b95c25400317b2baac694f4ba2b1b1dc1eae098
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435159"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Gatilho de ônibus de serviço Azure para funções Azure

Utilize o gatilho do Service Bus para responder a mensagens de uma fila ou tópico do Service Bus. Começando pela versão de extensão 3.1.0, pode ativar uma fila ou tópico ativado por sessão.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-service-bus.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê [metadados de mensagens](#message-metadata) e regista uma mensagem de fila do Service Bus:

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação do gatilho do Service Bus numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função lê [metadados de mensagens](#message-metadata) e regista uma mensagem de fila do Service Bus.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

Aqui está o código do guião C:

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

# <a name="java"></a>[Java](#tab/java)

A seguinte função Java utiliza a `@ServiceBusQueueTrigger` anotação da biblioteca de [tempo de execução](/java/api/overview/azure/functions/runtime) das funções Java para descrever a configuração de um gatilho de fila de ônibus de serviço. A função agarra a mensagem colocada na fila e adiciona-a aos registos.

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

As funções de Java também podem ser ativadas quando uma mensagem é adicionada a um tópico de Service Bus. O exemplo a seguir utiliza a `@ServiceBusTopicTrigger` anotação para descrever a configuração do gatilho.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho do Service Bus numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função lê [metadados de mensagens](#message-metadata) e regista uma mensagem de fila do Service Bus.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

Aqui está o código de script JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra uma ligação do gatilho do Service Bus numa *function.jsno* ficheiro e numa [função PowerShell](functions-reference-powershell.md) que utiliza a ligação. 

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
  "bindings": [
    {
      "name": "mySbMsg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "topicName": "mytopic",
      "subscriptionName": "mysubscription",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Aqui está a função que funciona quando uma mensagem de Autocarro de Serviço é enviada.

```powershell
param([string] $mySbMsg, $TriggerMetadata)

Write-Host "PowerShell ServiceBus queue trigger function processed message: $mySbMsg"
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem de fila do Service Bus através de um gatilho.

Uma ligação de autocarro de serviço é definida em *function.jsno* local onde *o tipo* está definido para `serviceBusTrigger` .

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

O código *_\_ no .py_ \_* declara um parâmetro como , que lhe permite ler a `func.ServiceBusMessage` mensagem de fila na sua função.

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
        'metadata' : msg.metadata
    })

    logging.info(result)
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar um gatilho de ônibus de serviço:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  O construtor do atributo tem o nome da fila ou do tópico e subscrição. Na versão 1.x do Azure Functions, também pode especificar os direitos de acesso da ligação. Se não especificar os direitos de acesso, o padrão é `Manage` . Para obter mais informações, consulte a secção [de configuração Trigger](#configuration) - .

  Aqui está um exemplo que mostra o atributo usado com um parâmetro de corda:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Uma vez que a `Connection` propriedade não está definida, as Funções procuram uma definição de app chamada `AzureWebJobsServiceBus` , que é o nome padrão para a cadeia de conexão Service Bus. Também pode definir a `Connection` propriedade para especificar o nome de uma definição de aplicação que contém a cadeia de ligação Service Bus para usar, como mostra o seguinte exemplo:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Para um exemplo completo, consulte [Trigger - exemplo](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Fornece outra forma de especificar a conta service bus a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação Service Bus. O atributo pode ser aplicado ao nível do parâmetro, método ou classe. O exemplo a seguir mostra o nível de classe e o nível do método:

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

A conta service bus a utilizar é determinada na seguinte ordem:

* Propriedade `ServiceBusTrigger` do `Connection` atributo.
* O `ServiceBusAccount` atributo aplicado ao mesmo parâmetro que o `ServiceBusTrigger` atributo.
* O `ServiceBusAccount` atributo aplicado à função.
* O `ServiceBusAccount` atributo aplicado à classe.
* A definição da aplicação "AzureWebJobsServiceBus".

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueTrigger` anotação permite-lhe criar uma função que funciona quando uma mensagem de fila do Service Bus é criada. As opções de configuração disponíveis incluem nome de fila e nome de cadeia de ligação.

A `ServiceBusTopicTrigger` anotação permite-lhe designar um tópico e subscrição para direcionar os dados que desencadeiam a função.

Consulte o [exemplo](#example) do gatilho para obter mais detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `ServiceBusTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido como "serviceBusTrigger". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/a | Deve ser definido para "dentro". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/a | O nome da variável que representa a fila ou mensagem de tópico no código de função. |
|**nome de fila**|**Nome da fila**|Nome da fila para monitorar.  Definir apenas se monitorizar uma fila, não para um tópico.
|**temaName**|**Nome tópico**|Nome do tópico para monitorizar. Definir apenas se monitorizar um tópico, não para uma fila.|
|**subscriptionName**|**Nome de assinatura**|Nome da subscrição para monitorar. Definir apenas se monitorizar um tópico, não para uma fila.|
|**conexão**|**Ligação**|O nome de uma definição de aplicação que contém a cadeia de ligação Service Bus para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se definir `connection` para "MyServiceBus", o tempo de execução de Funções procura uma definição de aplicação que se chama "AzureWebJobsMyServiceBus". Se deixar `connection` vazio, o tempo de execução de Funções utiliza a cadeia de ligação padrão do Service Bus na definição da aplicação que é denominada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos indicados na [Get as credenciais de gestão](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de ligação deve ser para um espaço de nomes do Service Bus, não se limitando a uma fila ou tópico específico. |
|**acessosDes**|**Acesso**|Direitos de acesso para a cadeia de ligação. Os valores disponíveis são `manage` `listen` e. O padrão é `manage` , o que indica que tem a `connection` permissão **'Gerir'.** Se utilizar uma cadeia de ligação que não tenha a permissão **'Gerir',** desemaça `accessRights` "ouvir". Caso contrário, o tempo de execução das Funções pode falhar ao tentar fazer operações que exijam direitos de gestão. Na versão 2.x e superior do Azure Functions, esta propriedade não está disponível porque a versão mais recente do Service Bus SDK não suporta operações de gestão.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`se ligar a uma fila ou subscrição [conscientes da sessão.](../service-bus-messaging/message-sessions.md) `false` caso contrário, que é o valor padrão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Os seguintes tipos de parâmetros estão disponíveis para a fila ou mensagem de tópico:

* `string` - Se a mensagem for sms.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, a Azure Functions tenta deserizar os dados JSON.
* `BrokeredMessage`- Dá-lhe a mensagem desercializada com o método [BrokeredMessage.GetBody \<T> ().](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)
* [`MessageReceiver`](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) - Utilizado para receber e reconhecer mensagens do recipiente de mensagens (necessário quando [`autoComplete`](functions-bindings-service-bus-output.md#hostjson-settings) definido para `false` )

Estes tipos de parâmetros são para Azure Functions versão 1.x; para 2.x e superior, use [`Message`](/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage` .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os seguintes tipos de parâmetros estão disponíveis para a fila ou mensagem de tópico:

* `string` - Se a mensagem for sms.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, a Azure Functions tenta deserizar os dados JSON.
* `BrokeredMessage`- Dá-lhe a mensagem desercializada com o método [BrokeredMessage.GetBody \<T> ().](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Estes parâmetros são para Azure Functions versão 1.x; para 2.x e superior, use [`Message`](/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage` .

# <a name="java"></a>[Java](#tab/java)

A mensagem de ônibus de serviço recebida está disponível através de um `ServiceBusQueueMessage` ou `ServiceBusTopicMessage` parâmetro.

[Consulte o exemplo para mais detalhes.](#example)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda à fila ou mensagem de tópico utilizando `context.bindings.<name from function.json>` . A mensagem 'Service Bus' é transmitida para a função como um objeto string ou JSON.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A instância do Service Bus está disponível através do parâmetro configurado no *function.jsna* propriedade do nome do ficheiro.

# <a name="python"></a>[Python](#tab/python)

A mensagem de fila está disponível para a função através de um parâmetro dactilografado como `func.ServiceBusMessage` . A mensagem 'Service Bus' é transmitida para a função como um objeto string ou JSON.

---

## <a name="poison-messages"></a>Mensagens venenosas

O manuseamento de mensagens venenosas não pode ser controlado ou configurado em Funções Azure. O autocarro da service lida com mensagens venenosas.

## <a name="peeklock-behavior"></a>Comportamento do PeekLock

O tempo de execução das funções recebe uma mensagem no [modo PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Solicita `Complete` a mensagem se a função terminar com sucesso ou se `Abandon` ligar se a função falhar. Se a função for mais longa do que o `PeekLock` tempo limite, o bloqueio é automaticamente renovado enquanto a função estiver em funcionamento.

O `maxAutoRenewDuration` é configurável em *host.jsem*, que mapeia para [OnMessageOptions.MaxAutoRenewDuration](/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration). O máximo permitido para esta regulação é de 5 minutos de acordo com a documentação do Service Bus, enquanto que pode aumentar o prazo de tempo de Funções do padrão de 5 minutos para 10 minutos. Para funções de Service Bus, não gostaria de fazer isso, pois ultrapassaria o limite de renovação do Service Bus.

## <a name="message-metadata"></a>Metadados de mensagens

O gatilho do Service Bus fornece várias [propriedades de metadados.](./functions-bindings-expressions-patterns.md#trigger-metadata) Estas propriedades podem ser usadas como parte de expressões de ligação em outras encadernações ou como parâmetros no seu código. Estas propriedades são membros da classe [Message.](/dotnet/api/microsoft.azure.servicebus.message)

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`ContentType`|`string`|Um identificador de tipo de conteúdo utilizado pelo remetente e pelo recetor para lógica específica da aplicação.|
|`CorrelationId`|`string`|A identificação da correlação.|
|`DeadLetterSource`|`string`|A fonte da carta morta.|
|`DeliveryCount`|`Int32`|O número de entregas.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo encadeado na UTC.|
|`ExpiresAtUtc`|`DateTime`|O tempo de validade na UTC.|
|`Label`|`string`|O rótulo específico da aplicação.|
|`MessageId`|`string`|Um valor definido pelo utilizador que o Service Bus pode utilizar para identificar mensagens duplicadas, se ativadas.|
|`MessageReceiver`|`MessageReceiver`|Recetor de mensagem de ônibus de serviço. Pode ser usado para abandonar, completar ou matar a mensagem.|
|`MessageSession`|`MessageSession`|Um recetor de mensagens especificamente para filas e tópicos ativados por sessão.|
|`ReplyTo`|`string`|A resposta ao endereço da fila.|
|`SequenceNumber`|`long`|O número único atribuído a uma mensagem pelo Service Bus.|
|`To`|`string`|O envio para o endereço.|
|`UserProperties`|`IDictionary<string, object>`|Propriedades definidas pelo remetente.|

Consulte [exemplos de código](#example) que utilizam estas propriedades anteriormente neste artigo.

## <a name="next-steps"></a>Passos seguintes

- [Enviar mensagens de autocarro da Azure Service de Azure Functions (ligação de saída)](./functions-bindings-service-bus-output.md)
