---
title: Ligações de saída de ônibus da Azure Service para funções Azure
description: Aprenda a enviar mensagens de autocarro da Azure Service da Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: ae2be8dbcb4839c7d16b864c484c3360fdcfe324
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425588"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Ligação de saída de ônibus Azure Service para funções Azure

Utilize a ligação de saída do Azure Service Bus para enviar mensagens de fila ou tópicos.

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](functions-bindings-service-bus.md).

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que envia uma mensagem de fila do Service Bus:

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

O exemplo a seguir mostra uma ligação de saída do Service Bus numa *function.jsno* ficheiro e uma [função de script C#](functions-reference-csharp.md) que utiliza a ligação. A função utiliza um gatilho temporizador para enviar uma mensagem de fila a cada 15 segundos.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que envia uma mensagem para uma fila de ônibus de serviço `myqueue` quando desencadeada por um pedido HTTP.

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

 Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a `@QueueOutput` anotação em parâmetros de função cujo valor seria escrito para uma fila de Autocarros de Serviço.  O tipo de parâmetro deve ser `OutputBinding<T>` , onde T é qualquer tipo nativo java de um POJO.

As funções de Java também podem escrever para um tópico de Service Bus. O exemplo a seguir utiliza a `@ServiceBusTopicOutput` anotação para descrever a configuração para a ligação de saída. 

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação de saída de ônibus de serviço numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função utiliza um gatilho temporizador para enviar uma mensagem de fila a cada 15 segundos.

Aqui estão os dados vinculativos do *function.jsarquivado:*

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir mostra uma ligação de saída do Service Bus numa *function.jsno* ficheiro e numa [função PowerShell](functions-reference-powershell.md) que utiliza a ligação. 

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
  "bindings": [
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "outputSbMsg",
      "queueName": "outqueue",
      "topicName": "outtopic"
    }
  ]
}
```

Aqui está o PowerShell que cria uma mensagem como saída da função.

```powershell
param($QueueItem, $TriggerMetadata) 

Push-OutputBinding -Name outputSbMsg -Value @{ 
    name = $QueueItem.name 
    employeeId = $QueueItem.employeeId 
    address = $QueueItem.address 
} 
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como escrever para uma fila de autocarros de serviço em Python.

Uma definição de ligação de serviço bus é definida em *function.jsno* local onde *o tipo* está definido para `serviceBus` .

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

No *_\_ seu .py,_ \_* pode escrever uma mensagem para a fila, passando um valor para o `set` método.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

O construtor do atributo tem o nome da fila ou do tópico e subscrição. Também pode especificar os direitos de acesso da ligação. Como escolher a definição de direitos de acesso é explicado na secção [de configuração De saída](#configuration) -. Aqui está um exemplo que mostra o atributo aplicado ao valor de retorno da função:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Pode definir a `Connection` propriedade para especificar o nome de uma definição de aplicação que contém a cadeia de ligação Service Bus para usar, como mostra o seguinte exemplo:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [a Saída - exemplo](#example).

Pode utilizar o `ServiceBusAccount` atributo para especificar a conta Service Bus para utilizar no nível de classe, método ou parâmetro.  Para obter mais informações, consulte [Trigger - atributos](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

As `ServiceBusQueueOutput` `ServiceBusTopicOutput` anotações e anotações estão disponíveis para escrever uma mensagem como uma saída de função. O parâmetro decorado com estas anotações deve ser declarado como um `OutputBinding<T>` local onde é o tipo correspondente ao tipo da `T` mensagem.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `ServiceBus` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para "serviceBus". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/a | Deve ser definido para "fora". Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/a | O nome da variável que representa a fila ou mensagem de tópico no código de função. Definir para "$return" para referenciar o valor de retorno da função. |
|**nome de fila**|**Nome da fila**|O nome da fila.  Definir apenas se enviar mensagens de fila, não para um tópico.
|**temaName**|**Nome tópico**|O nome do tópico. Desacoda apenas se enviar mensagens de tópico, não para uma fila.|
|**conexão**|**Ligação**|O nome de uma definição de aplicação que contém a cadeia de ligação Service Bus para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome. Por exemplo, se definir `connection` para "MyServiceBus", o tempo de execução de Funções procura uma definição de aplicação que se chama "AzureWebJobsMyServiceBus". Se deixar `connection` vazio, o tempo de execução de Funções utiliza a cadeia de ligação padrão do Service Bus na definição da aplicação que é denominada "AzureWebJobsServiceBus".<br><br>Para obter uma cadeia de ligação, siga os passos indicados na [Get as credenciais de gestão](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). A cadeia de ligação deve ser para um espaço de nomes do Service Bus, não se limitando a uma fila ou tópico específico.|
|**acessosRights (apenas** v1)|**Acesso**|Direitos de acesso para a cadeia de ligação. Os valores disponíveis são `manage` `listen` e. O padrão é `manage` , o que indica que tem a `connection` permissão **'Gerir'.** Se utilizar uma cadeia de ligação que não tenha a permissão **'Gerir',** desemaça `accessRights` "ouvir". Caso contrário, o tempo de execução das Funções pode falhar ao tentar fazer operações que exijam direitos de gestão. Na versão 2.x e superior do Azure Functions, esta propriedade não está disponível porque a versão mais recente do Service Bus SDK não suporta operações de gestão.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

Nas Funções Azure 1.x, o tempo de execução cria a fila se não existir e tiver definido `accessRights` para `manage` . Nas Funções versão 2.x e superior, a fila ou tópico já deve existir; se especificar uma fila ou tópico que não existe, a função falhará. 

# <a name="c"></a>[C#](#tab/csharp)

Utilize os seguintes tipos de parâmetros para a ligação de saída:

* `out T paramName` - `T` pode ser qualquer tipo json-serializável. Se o valor do parâmetro for nulo quando a função sai, as funções criam a mensagem com um objeto nulo.
* `out string` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out byte[]` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out BrokeredMessage` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem (para as funções 1.x)
* `out Message` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem (para as funções 2.x e superior)
* `ICollector<T>` ou `IAsyncCollector<T>` (para métodos async) - Para criar várias mensagens. Uma mensagem é criada quando se chama o `Add` método.

Ao trabalhar com funções C#:

* As funções async precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

* Para aceder ao ID da sessão, ligue-se a um [`Message`](/dotnet/api/microsoft.azure.servicebus.message) tipo e use a `sessionId` propriedade.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Utilize os seguintes tipos de parâmetros para a ligação de saída:

* `out T paramName` - `T` pode ser qualquer tipo json-serializável. Se o valor do parâmetro for nulo quando a função sai, as funções criam a mensagem com um objeto nulo.
* `out string` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out byte[]` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem.
* `out BrokeredMessage` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem (para as funções 1.x)
* `out Message` - Se o valor do parâmetro for nulo quando a função sai, as funções não criam uma mensagem (para as funções 2.x e superior)
* `ICollector<T>` ou `IAsyncCollector<T>` - Para criar várias mensagens. Uma mensagem é criada quando se chama o `Add` método.

Ao trabalhar com funções C#:

* As funções async precisam de um valor de retorno ou `IAsyncCollector` em vez de um `out` parâmetro.

* Para aceder ao ID da sessão, ligue-se a um [`Message`](/dotnet/api/microsoft.azure.servicebus.message) tipo e use a `sessionId` propriedade.

# <a name="java"></a>[Java](#tab/java)

Utilize o [Azure Service Bus SDK](../service-bus-messaging/index.yml) em vez da ligação de saída incorporada.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda à fila ou tópico utilizando `context.bindings.<name from function.json>` . Pode atribuir uma cadeia, uma matriz de byte ou um objeto JavaScript (deserializado em JSON) para `context.binding.<name>` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A saída para o Service Bus está disponível através do `Push-OutputBinding` cmdlet onde passa argumentos que correspondem ao nome designado pelo parâmetro de nome de encadernação no *function.jsno* ficheiro.

# <a name="python"></a>[Python](#tab/python)

Utilize o [Azure Service Bus SDK](../service-bus-messaging/index.yml) em vez da ligação de saída incorporada.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| Service Bus | [Códigos de erro do autocarro de serviço](../service-bus-messaging/service-bus-messaging-exceptions.md) |
| Service Bus | [Limites de autocarro de serviço](../service-bus-messaging/service-bus-quotas.md) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsnas definições

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x ou superiores. O exemplo host.jsno ficheiro abaixo contém apenas as definições para esta ligação. Para obter mais informações sobre as configurações globais, consulte [host.jsna referência para a versão Azure Functions](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host.jsem Funções 1.x, consulte [host.jsem referência para as Funções Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
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

Se `isSessionsEnabled` tiver pronto `true` para, o será `sessionHandlerOptions` honrado.  Se `isSessionsEnabled` tiver pronto `false` para, o será `messageHandlerOptions` honrado.

|Propriedade  |Predefinição | Description |
|---------|---------|---------|
|prefetchCount|0|Recebe ou define o número de mensagens que o recetor de mensagens pode simultaneamente solicitar.|
|maxAutoRenewDuration|00:05:00|A duração máxima dentro da qual o bloqueio de mensagem será renovado automaticamente.|
|autoComplete|true|Se o gatilho deve ligar automaticamente completamente após o processamento, ou se o código de função ligar manualmente completo.<br><br>A definição `false` para é suportada apenas em C#.<br><br>Se for `true` programado, o gatilho completa a mensagem automaticamente se a execução da função terminar com sucesso e abandonar a mensagem de outra forma.<br><br>Quando definido `false` para , você é responsável por ligar para os métodos [MessageReceiver](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver) para completar, abandonar ou matar a mensagem. Se for lançada uma exceção (e nenhum dos `MessageReceiver` métodos for chamado), então o bloqueio permanece. Uma vez expirado o bloqueio, a mensagem é novamente em fila com o `DeliveryCount` incremento e o bloqueio é automaticamente renovado.<br><br>Em funções não-C#, as exceções na função resultam nas chamadas de tempo de `abandonAsync` execução em segundo plano. Se não ocorrer exceção, `completeAsync` então é chamado em segundo plano. |
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para a chamada que a bomba de mensagem deve iniciar por instância em escala. Por predefinição, o tempo de execução de Funções processa várias mensagens simultaneamente.|
|maxConcurrentSessions|2000|O número máximo de sessões que podem ser manuseadas simultaneamente por instância em escala.|

## <a name="next-steps"></a>Passos seguintes

- [Executar uma função quando uma fila de autocarro de serviço ou mensagem de tópico é criada (Trigger)](./functions-bindings-service-bus-trigger.md)
