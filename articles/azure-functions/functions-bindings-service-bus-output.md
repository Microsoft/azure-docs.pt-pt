---
title: Enlaces do Service Bus do Azure para as funções do Azure
description: Aprenda a enviar mensagens de ônibus de serviço Azure a partir de Funções Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357015"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Ligação de saída de ônibus de serviço Azure para funções Azure

Utilize o enlace de saída de Azure Service Bus para enviar mensagens de fila ou tópico.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que envia uma mensagem de fila de ônibus de serviço:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação de saída do Ônibus de serviço num ficheiro *function.json* e uma [ C# função de script](functions-reference-csharp.md) que utiliza a ligação. A função utiliza um acionador de temporizador para enviar uma mensagem de fila a cada 15 segundos.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação de saída do Ônibus de serviço num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função utiliza um acionador de temporizador para enviar uma mensagem de fila a cada 15 segundos.

Aqui estão os dados vinculativos no ficheiro *função.json:*

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

# <a name="python"></a>[python](#tab/python)

O exemplo que se segue demonstra como escrever para uma fila de ônibus de serviço em Python.

Uma definição de ligação de ônibus de serviço é definida em *função.json* onde *o tipo* está definido para `serviceBus`.

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

No  *_\_init_ a\_.py,* pode escrever uma mensagem à fila passando um valor para o método `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

O exemplo seguinte mostra uma função Java que envia uma mensagem para uma fila de ônibus de serviço `myqueue` quando desencadeada por um pedido HTTP.

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

 Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)utilize a anotação `@QueueOutput` em parâmetros de função cujo valor seria escrito para uma fila de ônibus de serviço.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde T é qualquer tipo de Java nativo de um POJO.

As funções java também podem escrever para um tópico de ônibus de serviço. O exemplo que se segue utiliza a anotação `@ServiceBusTopicOutput` para descrever a configuração para a ligação de saída. 

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

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o [ServiceBusAttribute.](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs)

Construtor do atributo obtém o nome da fila ou tópico e subscrição. Também pode especificar direitos de acesso da ligação. Como escolher a definição de direitos de acesso é explicada na secção [De saída - configuração.](#configuration) Eis um exemplo que mostra o atributo aplicado para o valor de retorno da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Pode definir a propriedade `Connection` para especificar o nome de uma definição de aplicação que contém a cadeia de ligação service Bus a utilizar, como mostra o seguinte exemplo:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [A Saída - exemplo](#example).

Pode utilizar o atributo `ServiceBusAccount` para especificar a conta Service Bus para utilizar na classe, método ou nível de parâmetro.  Para mais informações, consulte [Trigger - atributos](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

As `ServiceBusQueueOutput` e `ServiceBusTopicOutput` anotações estão disponíveis para escrever uma mensagem como uma saída de função. O parâmetro decorado com estas anotações deve ser declarado como um `OutputBinding<T>` em que `T` é o tipo correspondente ao tipo da mensagem.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `ServiceBus`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido para "serviceBus". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido para "Sair". Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**nome** | n/d | O nome da variável que representa a mensagem de fila ou tópico no código de função. Definido como "$return" para referenciar o valor de retorno da função. |
|**queueName**|**Nome da fila**|Nome da fila.  Defina apenas se o envio de mensagens de fila, não para um tópico.
|**tópicoNome**|**Nome tópico**|Nome do tema. Defina apenas se o envio de mensagens de tópico, não para uma fila.|
|**conexão**|**Conexão**|O nome de uma definição de aplicação que contém a cadeia de ligação do Service Bus para utilizar para este enlace. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se definir `connection` para "MyServiceBus", o tempo de funcionamento das Funções procura uma definição de aplicação que se chama "AzureWebJobsMyServiceBus". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de ônibus de serviço predefinido na definição da aplicação que se chama "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos mostrados no [Get the management credentials](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de ligação tem de ser para um espaço de nomes do Service Bus, que não se limitando a uma fila específica ou um tópico.|
|**acessoDireitos**|**Acesso**|Direitos de acesso da cadeia de ligação. Os valores disponíveis são `manage` e `listen`. O padrão é `manage`, o que indica que o `connection` tem a permissão **De gestão.** Se utilizar uma cadeia de ligação que não tenha a permissão **'Gerir',** desempente `accessRights` para "ouvir". Caso contrário, as funções de tempo de execução poderá falhar tentando fazer operações que exigem gerir direitos. Na versão 2.x e superior da Azure Functions, esta propriedade não está disponível porque a versão mais recente do Service Bus SDK não suporta operações de gestão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

Nas Funções Azure 1.x, o tempo de funcionamento cria a fila se não existir e `accessRights` definir para `manage`. Nas Funções versão 2.x e superior, a fila ou tópico já deve existir; se especificar uma fila ou tópico que não existe, a função falhará. 

# <a name="c"></a>[C#](#tab/csharp)

Utilize os seguintes tipos de parâmetros para a ligação de saída:

* `out T paramName` - `T` pode ser qualquer tipo json-serializável. Se o valor do parâmetro for nulo quando a função sai, as funções cria a mensagem com um objeto nulo.
* `out string` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out byte[]` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out BrokeredMessage` - Se o valor do parâmetro for nulo quando a função sair, as funções não criam uma mensagem (para funções 1.x)
* `out Message` - Se o valor do parâmetro for nulo quando a função sair, as funções não criam uma mensagem (para funções 2.x e superiores)
* `ICollector<T>` ou `IAsyncCollector<T>` - Para criar várias mensagens. Uma mensagem é criada quando se chama o método `Add`.

Ao trabalhar C# com funções:

* As funções de asincronização precisam de um valor de retorno ou `IAsyncCollector` em vez de um parâmetro `out`.

* Para aceder ao ID da sessão, ligue-se a um tipo [de`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) e use a propriedade `sessionId`.

# <a name="c-script"></a>[C#Roteiro](#tab/csharp-script)

Utilize os seguintes tipos de parâmetros para a ligação de saída:

* `out T paramName` - `T` pode ser qualquer tipo json-serializável. Se o valor do parâmetro for nulo quando a função sai, as funções cria a mensagem com um objeto nulo.
* `out string` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out byte[]` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out BrokeredMessage` - Se o valor do parâmetro for nulo quando a função sair, as funções não criam uma mensagem (para funções 1.x)
* `out Message` - Se o valor do parâmetro for nulo quando a função sair, as funções não criam uma mensagem (para funções 2.x e superiores)
* `ICollector<T>` ou `IAsyncCollector<T>` - Para criar várias mensagens. Uma mensagem é criada quando se chama o método `Add`.

Ao trabalhar C# com funções:

* As funções de asincronização precisam de um valor de retorno ou `IAsyncCollector` em vez de um parâmetro `out`.

* Para aceder ao ID da sessão, ligue-se a um tipo [de`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) e use a propriedade `sessionId`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda à fila ou ao tópico utilizando `context.bindings.<name from function.json>`. Pode atribuir uma corda, uma matriz de byte ou um objeto JavaScript (desserializado em JSON) para `context.binding.<name>`.

# <a name="python"></a>[python](#tab/python)

Utilize o [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) em vez da encadernação de saída incorporada.

# <a name="java"></a>[Java](#tab/java)

Utilize o [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) em vez da encadernação de saída incorporada.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Service Bus | [Códigos de erro de ônibus de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limites de ônibus de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x e superiores. O ficheiro exemplo host.json abaixo contém apenas as definições para esta ligação. Para obter mais informações sobre as configurações globais de configuração, consulte a [referência host.json para a versão Funções Azure](functions-host-json.md).

> [!NOTE]
> Para uma referência do host.json nas funções 1.x, consulte a [referência host.json para as funções Azure 1.x](functions-host-json-v1.md).

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
|Preenchimento automático|true|Se o gatilho deve marcar imediatamente a mensagem como completa (autocompleta) ou esperar que a função saia com sucesso para ligar completa.|
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que deve iniciar o bombardeamento de mensagens. Por predefinição, o runtime das funções processa várias mensagens em simultâneo. Para direcionar o tempo de execução para processar apenas uma única fila ou mensagem de tópico de cada vez, detete `maxConcurrentCalls` para 1. |

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando uma fila de ônibus de serviço ou mensagem tópico é criada (Gatilho)](./functions-bindings-service-bus-trigger.md)
