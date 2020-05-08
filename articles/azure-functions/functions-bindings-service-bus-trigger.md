---
title: Ligações de ônibus de serviço Azure para funções azure
description: Aprenda a executar uma Função Azure quando forem criadas mensagens de ônibus de serviço Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: b5e7f1b70aca50b4e42d056beb0b17795430091c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690710"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Gatilho de ônibus de serviço Azure para funções azure

Utilize o gatilho do Ônibus de serviço para responder a mensagens de uma fila de ônibus de serviço ou tópico. Começando pela versão de extensão 3.1.0, pode acionar uma fila ou tópico ativado por sessão.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que lê metadados de [mensagens](#message-metadata) e regista uma mensagem de fila de ônibus de serviço:

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

O exemplo seguinte mostra uma ligação do gatilho do ônibus de serviço num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função lê [metadados de mensagens](#message-metadata) e regista uma mensagem de fila de ônibus de serviço.

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

Aqui está o código de script C#:

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

# <a name="python"></a>[Python](#tab/python)

O exemplo que se segue demonstra como ler uma mensagem de fila de ônibus de serviço através de um gatilho.

Uma ligação de ônibus de serviço é definida `serviceBusTrigger`em *função.json* onde o *tipo* está definido para .

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

O código * _ \_inite_\_.py* declara `func.ServiceBusMessage`um parâmetro como , que lhe permite ler a mensagem de fila na sua função.

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

A função Java `@ServiceBusQueueTrigger` seguinte utiliza a anotação da biblioteca de [tempo de funcionamento](/java/api/overview/azure/functions/runtime) das funções Java para descrever a configuração para um gatilho de fila de ônibus de serviço. A função agarra a mensagem colocada na fila e adiciona-a aos registos.

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

As funções java também podem ser desencadeadas quando uma mensagem é adicionada a um tópico de ônibus de serviço. O exemplo seguinte `@ServiceBusTopicTrigger` utiliza a anotação para descrever a configuração do gatilho.

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

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar um gatilho de autocarro de serviço:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  O construtor do atributo tem o nome da fila ou do tópico e subscrição. Na versão 1.x das Funções Azure, também pode especificar os direitos de acesso da ligação. Se não especificar os direitos de `Manage`acesso, o predefinido é . Para mais informações, consulte a secção [de configuração Do Gatilho.](#configuration)

  Aqui está um exemplo que mostra o atributo usado com um parâmetro de corda:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Uma `Connection` vez que a propriedade não está definida, `AzureWebJobsServiceBus`as Funções procuram uma definição de app chamada , que é o nome padrão para a cadeia de ligação service Bus. Também pode definir `Connection` a propriedade para especificar o nome de uma definição de aplicação que contém a cadeia de ligação do Ônibus de serviço a utilizar, como mostra o seguinte exemplo:

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

  Fornece outra forma de especificar a conta De Serviço Bus a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de ônibus de serviço. O atributo pode ser aplicado no parâmetro, método ou nível de classe. O exemplo que se segue mostra o nível de classe e o nível de método:

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

A conta de ônibus de serviço a utilizar é determinada na seguinte ordem:

* A `ServiceBusTrigger` propriedade do `Connection` atributo.
* O `ServiceBusAccount` atributo aplicado ao mesmo `ServiceBusTrigger` parâmetro que o atributo.
* O `ServiceBusAccount` atributo aplicado à função.
* O `ServiceBusAccount` atributo aplicado à classe.
* A definição da aplicação "AzureWebJobsServiceBus".

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A `ServiceBusQueueTrigger` anotação permite criar uma função que funciona quando uma mensagem de fila de ônibus de serviço é criada. As opções de configuração disponíveis incluem o nome da fila e o nome da corda de ligação.

A `ServiceBusTopicTrigger` anotação permite-lhe designar um tópico e subscrição para direcionar o que os dados desencadeiam a função.

Consulte o [exemplo](#example) do gatilho para mais detalhes.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `ServiceBusTrigger` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido para "serviceBusTrigger". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/d | Deve ser definido para "in". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/d | O nome da variável que representa a fila ou a mensagem de tópico no código de função. |
|**queueName**|**Nome da fila**|Nome da fila para monitorizar.  Definir apenas se monitorizar uma fila, não para um tópico.
|**tópicoNome**|**Nome tópico**|Nome do tópico a monitorizar. Definir apenas se monitorizar um tópico, não para uma fila.|
|**nome de subscrição**|**Nome de subscrição**|Nome da subscrição para monitorizar. Definir apenas se monitorizar um tópico, não para uma fila.|
|**conexão**|**Conexão**|O nome de uma definição de aplicação que contém a cadeia de ligação service Bus para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se `connection` definir para "MyServiceBus", o tempo de funcionamento das Funções procura uma definição de aplicação que se chama "AzureWebJobsMyServiceBus". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de ônibus de serviço predefinido na definição da aplicação que se chama "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos mostrados no [Get the management credentials](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de ligação deve ser para um espaço de nome do Ônibus de serviço, não se limitando a uma fila ou tópico específico. |
|**acessoDireitos**|**Acesso**|Direitos de acesso para a cadeia de ligação. Os valores disponíveis são `manage` e `listen`. O padrão `manage`é , `connection` o que indica que tem a permissão **Gerir.** Se utilizar uma cadeia de ligação que `accessRights` não tenha a permissão **'Gerir',** definida para "ouvir". Caso contrário, o tempo de funcionamento das Funções pode falhar ao tentar realizar operações que requerem direitos de gestão. Na versão 2.x e superior da Azure Functions, esta propriedade não está disponível porque a versão mais recente do Service Bus SDK não suporta operações de gestão.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`se ligar a uma fila ou subscrição [conscientes da sessão.](../service-bus-messaging/message-sessions.md) `false`caso contrário, que é o valor padrão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Os seguintes tipos de parâmetros estão disponíveis para a fila ou mensagem tópico:

* `string`- Se a mensagem for sms.
* `byte[]`- Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, as Funções Azure tentam desserializar os dados da JSON.
* `BrokeredMessage`- Dá-lhe a mensagem desserializada com o método [BrokeredMessage.GetBody\<T>().](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Estes tipos de parâmetros destinam-se à versão 1.x das Funções Azure; para 2.x e [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) mais `BrokeredMessage`alto, use em vez de .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os seguintes tipos de parâmetros estão disponíveis para a fila ou mensagem tópico:

* `string`- Se a mensagem for sms.
* `byte[]`- Útil para dados binários.
* Um tipo personalizado - Se a mensagem contiver JSON, as Funções Azure tentam desserializar os dados da JSON.
* `BrokeredMessage`- Dá-lhe a mensagem desserializada com o método [BrokeredMessage.GetBody\<T>().](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Estes parâmetros são para a versão 1.x das Funções Azure; para 2.x e [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) mais `BrokeredMessage`alto, use em vez de .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda à fila ou `context.bindings.<name from function.json>`à mensagem de tópico utilizando . A mensagem do Ônibus de serviço é transmitida para a função como um objeto de corda ou JSON.

# <a name="python"></a>[Python](#tab/python)

A mensagem de fila está disponível para `func.ServiceBusMessage`a função através de um parâmetro dado como . A mensagem do Ônibus de serviço é transmitida para a função como um objeto de corda ou JSON.

# <a name="java"></a>[Java](#tab/java)

A mensagem de ônibus de `ServiceBusQueueMessage` `ServiceBusTopicMessage` serviço de entrada está disponível através de um ou parâmetro.

[Veja o exemplo para mais detalhes.](#example)

---

## <a name="poison-messages"></a>Mensagens venenosas

O manuseamento de mensagens venenosas não pode ser controlado ou configurado nas Funções Azure. O autocarro de serviço lida com mensagens venenosas em si.

## <a name="peeklock-behavior"></a>Comportamento peekLock

O tempo de execução das funções recebe uma mensagem no [modo PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). A `Complete` mensagem chama a mensagem se a função terminar com sucesso, ou se `Abandon` a função falhar. Se a função `PeekLock` for mais longa do que o tempo, o bloqueio é automaticamente renovado enquanto a função estiver em funcionamento. 

O `maxAutoRenewDuration` é configurável em *host.json*, que mapeia para [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). O máximo permitido para esta definição é de 5 minutos de acordo com a documentação do Ônibus de serviço, enquanto que pode aumentar o limite de tempo de Funções do padrão de 5 minutos para 10 minutos. Para as funções de Ônibus de serviço, você não gostaria de fazer isso então, porque você excederia o limite de renovação do Ônibus de serviço.

## <a name="message-metadata"></a>Metadados de mensagens

O gatilho do Ônibus de serviço fornece várias [propriedades de metadados.](./functions-bindings-expressions-patterns.md#trigger-metadata) Estas propriedades podem ser usadas como parte de expressões de ligação noutras encadernações ou como parâmetros no seu código. Estas propriedades são membros da classe [Mensagem.](/dotnet/api/microsoft.azure.servicebus.message?view=azure-dotnet)

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`ContentType`|`string`|Um identificador de tipo de conteúdo utilizado pelo remetente e pelo recetor para uma lógica específica da aplicação.|
|`CorrelationId`|`string`|A identificação da correlação.|
|`DeadLetterSource`|`string`|A fonte da carta morta.|
|`DeliveryCount`|`Int32`|O número de entregas.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo enqueuado na UTC.|
|`ExpiresAtUtc`|`DateTime`|O tempo de validade na UTC.|
|`Label`|`string`|O rótulo específico da aplicação.|
|`MessageId`|`string`|Um valor definido pelo utilizador que o Service Bus pode usar para identificar mensagens duplicadas, se ativado.|
|`MessageReceiver`|`MessageReceiver`|Recetor de mensagem de ônibus de serviço. Pode ser usado para abandonar, completar ou escrever a mensagem.|
|`MessageSession`|`MessageSession`|Um recetor de mensagens especificamente para filas e tópicos ativados por sessão.|
|`ReplyTo`|`string`|A resposta ao endereço da fila.|
|`SequenceNumber`|`long`|O número único atribuído a uma mensagem pelo Ônibus de Serviço.|
|`To`|`string`|O envio para o endereço.|
|`UserProperties`|`IDictionary<string, object>`|Propriedades definidas pelo remetente.|

Consulte [exemplos](#example) de código que usam estas propriedades no início deste artigo.

## <a name="next-steps"></a>Passos seguintes

- [Envie mensagens de ônibus de serviço Azure a partir de funções Azure (encadernação de saída)](./functions-bindings-service-bus-output.md)
