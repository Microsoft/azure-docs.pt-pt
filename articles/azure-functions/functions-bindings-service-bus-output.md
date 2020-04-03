---
title: Ligações de ônibus de serviço Azure para funções azure
description: Aprenda a enviar mensagens de ônibus de serviço Azure a partir de Funções Azure.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 02d9ce87d45c5f1c9a123aae18f7d710b268f03e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582259"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Ligação de saída de ônibus de serviço Azure para funções Azure

Utilize a ligação de saída do ônibus de serviço Azure para enviar mensagens de fila ou tópicos.

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](functions-bindings-service-bus-output.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que envia uma mensagem de fila de ônibus de serviço:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo seguinte mostra uma ligação de saída do Ônibus de serviço num ficheiro *function.json* e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função utiliza um gatilho do temporizador para enviar uma mensagem de fila a cada 15 segundos.

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

Aqui está o código de script C# que cria uma única mensagem:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Aqui está o código de script C# que cria várias mensagens:

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

O exemplo seguinte mostra uma ligação de saída do Ônibus de serviço num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função utiliza um gatilho do temporizador para enviar uma mensagem de fila a cada 15 segundos.

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

# <a name="python"></a>[Python](#tab/python)

O exemplo que se segue demonstra como escrever para uma fila de ônibus de serviço em Python.

Uma definição de ligação de ônibus *type* de serviço `serviceBus`é definida na *função.json* onde o tipo está definido para .

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

No `set` init *\_.py, pode escrever uma mensagem para a fila, passando um valor ao método. _ \__*

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

O exemplo seguinte mostra uma função Java que `myqueue` envia uma mensagem para uma fila de ônibus de serviço quando desencadeada por um pedido HTTP.

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

 Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)utilize a `@QueueOutput` anotação em parâmetros de função cujo valor seria escrito para uma fila de ônibus de serviço.  O tipo de `OutputBinding<T>`parâmetro deve ser, onde T é qualquer tipo de Java nativo de um POJO.

As funções java também podem escrever para um tópico de ônibus de serviço. O exemplo seguinte `@ServiceBusTopicOutput` utiliza a anotação para descrever a configuração para a ligação de saída. 

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

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

O construtor do atributo tem o nome da fila ou do tópico e subscrição. Também pode especificar os direitos de acesso da ligação. Como escolher a definição de direitos de acesso é explicada na secção [De saída - configuração.](#configuration) Aqui está um exemplo que mostra o atributo aplicado ao valor de retorno da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Pode definir `Connection` a propriedade para especificar o nome de uma definição de aplicação que contém a cadeia de ligação service Bus a utilizar, como mostra o seguinte exemplo:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [A Saída - exemplo](#example).

Pode utilizar `ServiceBusAccount` o atributo para especificar a conta Service Bus para utilizar na classe, método ou nível de parâmetro.  Para mais informações, consulte [Trigger - atributos](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

As `ServiceBusQueueOutput` `ServiceBusTopicOutput` anotações e anotações estão disponíveis para escrever uma mensagem como uma saída de função. O parâmetro decorado com estas anotações deve `OutputBinding<T>` ser `T` declarado como um local onde se corresponde ao tipo correspondente ao tipo da mensagem.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `ServiceBus` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido para "serviceBus". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/d | Deve ser definido para "fora". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/d | O nome da variável que representa a fila ou a mensagem de tópico no código de função. Definir para "$return" para referenciar o valor de devolução da função. |
|**queueName**|**Nome da fila**|Nome da fila.  Definir apenas se enviar mensagens de fila, não para um tópico.
|**tópicoNome**|**Nome tópico**|Nome do tema. Definir apenas se enviar mensagens de tópicos, não para uma fila.|
|**conexão**|**Conexão**|O nome de uma definição de aplicação que contém a cadeia de ligação service Bus para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se `connection` definir para "MyServiceBus", o tempo de funcionamento das Funções procura uma definição de aplicação que se chama "AzureWebJobsMyServiceBus". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de ônibus de serviço predefinido na definição da aplicação que se chama "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos mostrados no [Get the management credentials](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de ligação deve ser para um espaço de nome do Ônibus de serviço, não se limitando a uma fila ou tópico específico.|
|**acessoDireitos**|**Acesso**|Direitos de acesso para a cadeia de ligação. Os valores disponíveis são `manage` e `listen`. O padrão `manage`é , `connection` o que indica que tem a permissão **Gerir.** Se utilizar uma cadeia de ligação que `accessRights` não tenha a permissão **'Gerir',** definida para "ouvir". Caso contrário, o tempo de funcionamento das Funções pode falhar ao tentar realizar operações que requerem direitos de gestão. Na versão 2.x e superior da Azure Functions, esta propriedade não está disponível porque a versão mais recente do Service Bus SDK não suporta operações de gestão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

Nas Funções Azure 1.x, o tempo de funcionamento cria a `accessRights` `manage`fila se não existir e tem definido para . Nas Funções versão 2.x e superior, a fila ou tópico já deve existir; se especificar uma fila ou tópico que não existe, a função falhará. 

# <a name="c"></a>[C#](#tab/csharp)

Utilize os seguintes tipos de parâmetros para a ligação de saída:

* `out T paramName` - `T`pode ser qualquer tipo jSON-serializável. Se o valor do parâmetro for nulo quando a função sair, as funções criam a mensagem com um objeto nulo.
* `out string`- Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out byte[]`- Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out BrokeredMessage`- Se o valor do parâmetro for nulo quando a função sair, as funções não criam uma mensagem (para funções 1.x)
* `out Message`- Se o valor do parâmetro for nulo quando a função sair, as funções não criam uma mensagem (para funções 2.x e superiores)
* `ICollector<T>`ou `IAsyncCollector<T>` - Para criar várias mensagens. Uma mensagem é criada `Add` quando se chama o método.

Ao trabalhar com funções C#:

* As funções de asincronização precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

* Para aceder ao ID da [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sessão, `sessionId` ligue-se a um tipo e use a propriedade.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Utilize os seguintes tipos de parâmetros para a ligação de saída:

* `out T paramName` - `T`pode ser qualquer tipo jSON-serializável. Se o valor do parâmetro for nulo quando a função sair, as funções criam a mensagem com um objeto nulo.
* `out string`- Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out byte[]`- Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out BrokeredMessage`- Se o valor do parâmetro for nulo quando a função sair, as funções não criam uma mensagem (para funções 1.x)
* `out Message`- Se o valor do parâmetro for nulo quando a função sair, as funções não criam uma mensagem (para funções 2.x e superiores)
* `ICollector<T>`ou `IAsyncCollector<T>` - Para criar várias mensagens. Uma mensagem é criada `Add` quando se chama o método.

Ao trabalhar com funções C#:

* As funções de asincronização precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

* Para aceder ao ID da [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sessão, `sessionId` ligue-se a um tipo e use a propriedade.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda à fila `context.bindings.<name from function.json>`ou tópico utilizando . Pode atribuir uma corda, uma matriz de byte ou um objeto JavaScript `context.binding.<name>`(desserializado em JSON) a .

# <a name="python"></a>[Python](#tab/python)

Utilize o [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) em vez da encadernação de saída incorporada.

# <a name="java"></a>[Java](#tab/java)

Utilize o [Azure Service Bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) em vez da encadernação de saída incorporada.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| Service Bus | [Códigos de erro de ônibus de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limites de ônibus de serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações host.json

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
|pré-fetchCount|0|Obtém ou define o número de mensagens que o recetor de mensagens pode simultaneamente solicitar.|
|maxAutoRenovar Duração|00:05:00|A duração máxima dentro da qual o bloqueio da mensagem será renovado automaticamente.|
|autoComplete|true|Se o gatilho deve marcar imediatamente a mensagem como completa (autocompleta) ou esperar que a função saia com sucesso para ligar completa.|
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o backback que a bomba de mensagem deve iniciar. Por predefinição, o tempo de execução das Funções processa várias mensagens simultaneamente. Para direcionar o tempo de execução para processar apenas `maxConcurrentCalls` uma única fila ou mensagem de tópico de cada vez, definida para 1. |

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando uma fila de ônibus de serviço ou mensagem tópico é criada (Gatilho)](./functions-bindings-service-bus-trigger.md)
