---
title: Enlaces do Service Bus do Azure para as funções do Azure
description: Aprenda a executar uma Função Azure quando forem criadas mensagens de ônibus de serviço Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273571"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Gatilho de ônibus de serviço Azure para funções azure

Utilize o acionador do Service Bus para responder às mensagens a partir de uma fila do Service Bus ou tópico.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que lê metadados de [mensagens](#message-metadata) e regista uma mensagem de fila de ônibus de serviço:

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

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra um gatilho de ônibus de serviço ligando num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que utiliza a ligação. A função lê [metadados de mensagens](#message-metadata) e regista uma mensagem de fila de ônibus de serviço.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra um gatilho de ônibus de serviço ligando num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função lê [metadados de mensagens](#message-metadata) e regista uma mensagem de fila de ônibus de serviço. 

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

# <a name="python"></a>[python](#tab/python)

O exemplo que se segue demonstra como ler uma mensagem de fila de ônibus de serviço através de um gatilho.

Uma ligação de ônibus de serviço é definida em *função.json* onde *o tipo* está definido para `serviceBusTrigger`.

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

O código no  *_\_inite_\_.py* declara um parâmetro como `func.ServiceBusMessage`, o que lhe permite ler a mensagem de fila na sua função.

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

# <a name="java"></a>[Java](#tab/java)

A função Java seguinte utiliza a `@ServiceBusQueueTrigger` anotação da [biblioteca de tempo de funcionamento](/java/api/overview/azure/functions/runtime) das funções Java para descrever a configuração para um gatilho de fila de ônibus de serviço. A função agarra a mensagem colocada na fila e adiciona-a aos registos.

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

As funções java também podem ser desencadeadas quando uma mensagem é adicionada a um tópico de ônibus de serviço. O exemplo que se segue utiliza a anotação `@ServiceBusTopicTrigger` para descrever a configuração do gatilho.

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

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar um gatilho de autocarro de serviço:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Construtor do atributo obtém o nome da fila ou tópico e subscrição. Na versão das funções do Azure 1.x, também pode especificar direitos de acesso da ligação. Se não especificar os direitos de acesso, o predefinido é `Manage`. Para mais informações, consulte a secção [de configuração Do Gatilho.](#configuration)

  Eis um exemplo que mostra o atributo utilizado com um parâmetro de cadeia de caracteres:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Pode definir a propriedade `Connection` para especificar o nome de uma definição de aplicação que contém a cadeia de ligação service Bus a utilizar, como mostra o seguinte exemplo:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Para um exemplo completo, consulte [O Gatilho - exemplo](#example).

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

* A propriedade `Connection` do `ServiceBusTrigger` atributo.
* O atributo `ServiceBusAccount` aplicado ao mesmo parâmetro que o atributo `ServiceBusTrigger`.
* O atributo `ServiceBusAccount` aplicado à função.
* O atributo `ServiceBusAccount` aplicado à classe.
* A definição de aplicação "AzureWebJobsServiceBus".

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A anotação `ServiceBusQueueTrigger` permite criar uma função que funciona quando uma mensagem de fila de ônibus de serviço é criada. As opções de configuração disponíveis incluem o nome da fila e o nome da corda de ligação.

A anotação `ServiceBusTopicTrigger` permite-lhe designar um tópico e subscrição para direcionar o que os dados desencadeiam a função.

Consulte o [exemplo](#example) do gatilho para mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `ServiceBusTrigger`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido para "serviceBusTrigger". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido para "in". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**nome** | n/d | O nome da variável que representa a mensagem de fila ou tópico no código de função. |
|**queueName**|**Nome da fila**|Nome da fila para monitorizar.  Defina apenas se a monitorização de uma fila, não para um tópico.
|**tópicoNome**|**Nome tópico**|Nome do tópico para monitorizar. Defina apenas se a monitorização de um tópico, não para uma fila.|
|**nome de subscrição**|**Nome de subscrição**|Nome da subscrição a monitorizar. Defina apenas se a monitorização de um tópico, não para uma fila.|
|**conexão**|**Conexão**|O nome de uma definição de aplicação que contém a cadeia de ligação do Service Bus para utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se definir `connection` para "MyServiceBus", o tempo de funcionamento das Funções procura uma definição de aplicação que se chama "AzureWebJobsMyServiceBus". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de ônibus de serviço predefinido na definição da aplicação que se chama "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos mostrados no [Get the management credentials](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico. |
|**acessoDireitos**|**Acesso**|Direitos de acesso da cadeia de ligação. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, o que indica que o `connection` tem a permissão **De gestão.** Se utilizar uma cadeia de ligação que não tenha a permissão **'Gerir',** desempente `accessRights` para "ouvir". Caso contrário, as funções de tempo de execução poderá falhar tentando fazer operações que exigem gerir direitos. Na versão 2.x e superior da Azure Functions, esta propriedade não está disponível porque a versão mais recente do Service Bus SDK não suporta operações de gestão.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` se ligar a uma fila ou subscrição [conscientes da sessão.](../service-bus-messaging/message-sessions.md) `false` o contrário, que é o valor padrão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Os seguintes tipos de parâmetros estão disponíveis para a fila ou mensagem tópico:

* `string` - Se a mensagem for texto.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - se a mensagem contém JSON, as funções do Azure tenta desserializar os dados JSON.
* `BrokeredMessage` - Dá-lhe a mensagem desserializada com o método [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Estes tipos de parâmetros destinam-se à versão 1.x das Funções Azure; para 2.x e mais alto, use [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage`.

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Os seguintes tipos de parâmetros estão disponíveis para a fila ou mensagem tópico:

* `string` - Se a mensagem for texto.
* `byte[]` - Útil para dados binários.
* Um tipo personalizado - se a mensagem contém JSON, as funções do Azure tenta desserializar os dados JSON.
* `BrokeredMessage` - Dá-lhe a mensagem desserializada com o método [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Estes parâmetros são para a versão 1.x das Funções Azure; para 2.x e mais alto, use [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) em vez de `BrokeredMessage`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda à fila ou à mensagem de tópico utilizando `context.bindings.<name from function.json>`. A mensagem do Service Bus é passada para a função como uma cadeia ou um objeto JSON.

# <a name="python"></a>[python](#tab/python)

A mensagem de fila está disponível para a função através de um parâmetro dado como `func.ServiceBusMessage`. A mensagem do Service Bus é passada para a função como uma cadeia ou um objeto JSON.

# <a name="java"></a>[Java](#tab/java)

A mensagem de ônibus de serviço de entrada está disponível através de um parâmetro de `ServiceBusQueueMessage` ou `ServiceBusTopicMessage`.

[Veja o exemplo para mais detalhes.](#example)

---

## <a name="poison-messages"></a>Mensagens venenosas

Processamento de mensagens não processáveis não pode ser controlado ou configurado nas funções do Azure. Do Service Bus processa mensagens suspeitas em si.

## <a name="peeklock-behavior"></a>Comportamento peekLock

O tempo de execução das funções recebe uma mensagem no [modo PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Chama `Complete` na mensagem se a função terminar com sucesso, ou chama `Abandon` se a função falhar. Se a função for mais longa do que o intervalo de tempo `PeekLock`, o bloqueio é automaticamente renovado enquanto a função estiver em funcionamento. 

O `maxAutoRenewDuration` é configurável em *host.json*, que mapeia para [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). O máximo permitido para esta definição é de 5 minutos, de acordo com a documentação do Service Bus, ao passo que pode aumentar o limite de tempo de funções da predefinição de 5 minutos para 10 minutos. Para as funções do Service Bus não iria querer fazer isso, em seguida, porque iria exceder o limite de renovação do Service Bus.

## <a name="message-metadata"></a>Metadados de mensagens

O gatilho do Ônibus de serviço fornece várias [propriedades de metadados.](./functions-bindings-expressions-patterns.md#trigger-metadata) Essas propriedades podem ser utilizadas como parte das expressões de associação nas outras associações, ou como parâmetros no seu código. Estas propriedades são membros da classe [BrokeredMessage.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|O número de entregas.|
|`DeadLetterSource`|`string`|A origem de entregues.|
|`ExpiresAtUtc`|`DateTime`|A hora de expiração em UTC.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo de colocados em fila em UTC.|
|`MessageId`|`string`|Um valor definido pelo utilizador que o Service Bus pode utilizar para identificar mensagens duplicadas, se estiver ativada.|
|`ContentType`|`string`|Um identificador de tipo de conteúdo utilizado pelo remetente e pelo recetor para uma lógica específica da aplicação.|
|`ReplyTo`|`string`|O endereço de resposta fila.|
|`SequenceNumber`|`Int64`|O número exclusivo atribuído a uma mensagem pelo Service Bus.|
|`To`|`string`|Enviar para endereço.|
|`Label`|`string`|O rótulo específico da aplicação.|
|`CorrelationId`|`string`|O ID de correlação.|

Consulte [exemplos](#example) de código que usam estas propriedades no início deste artigo.

## <a name="next-steps"></a>Passos seguintes

- [Envie mensagens de ônibus de serviço Azure a partir de funções Azure (encadernação de saída)](./functions-bindings-service-bus-output.md)
