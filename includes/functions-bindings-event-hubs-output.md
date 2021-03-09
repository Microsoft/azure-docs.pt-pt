---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: dadd86521a7b6c20dab2ed036555b798b869344c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510938"
---
Utilize a ligação de saída do Event Hubs para escrever eventos num stream de eventos. Para escrever eventos em um hub de eventos, é preciso ter permissão de envio para ele.

Certifique-se de que as referências de embalagem necessárias estão em vigor antes de tentar implementar uma ligação de saída.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](../articles/azure-functions/functions-dotnet-class-library.md) que escreve uma mensagem para um centro de eventos, utilizando o valor de retorno do método como saída:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

O exemplo a seguir mostra como usar a `IAsyncCollector` interface para enviar um lote de mensagens. Este cenário é comum quando está a processar mensagens provenientes de um Centro de Eventos e a enviar o resultado para outro Centro de Eventos.

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação do gatilho do centro de eventos numa *function.jsno* ficheiro e numa [função de script C#](../articles/azure-functions/functions-reference-csharp.md) que utiliza a ligação. A função escreve uma mensagem para um centro de eventos.

Os exemplos a seguir mostram que os dados de ligação do Event Hubs no *function.jsarquivados.* O primeiro exemplo é para funções 2.x e superior, e o segundo é para funções 1.x. 

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

Aqui está o código de script C# que cria uma mensagem:

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

Aqui está o código de script C# que cria várias mensagens:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho do centro de eventos numa *function.jsno* ficheiro e numa [função JavaScript](../articles/azure-functions/functions-reference-node.md) que utiliza a ligação. A função escreve uma mensagem para um centro de eventos.

Os exemplos a seguir mostram que os dados de ligação do Event Hubs no *function.jsarquivados.* O primeiro exemplo é para funções 2.x e superior, e o segundo é para funções 1.x. 

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

Aqui está o código JavaScript que envia uma única mensagem:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Aqui está o código JavaScript que envia várias mensagens:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação do gatilho do centro de eventos numa *function.jsem* ficheiro e numa [função Python](../articles/azure-functions/functions-reference-python.md) que utiliza a ligação. A função escreve uma mensagem para um centro de eventos.

Os exemplos a seguir mostram que os dados de ligação do Event Hubs no *function.jsarquivados.*

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Aqui está o código Python que envia uma única mensagem:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que escreve uma mensagem contendo a hora atual para um Centro de Eventos.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a `@EventHubOutput` anotação em parâmetros cujo valor seria publicado no Event Hub.  O parâmetro deve ser do tipo `OutputBinding<T>` , onde T é um POJO ou qualquer tipo nativo de Java.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Para [bibliotecas de classe C#,](../articles/azure-functions/functions-dotnet-class-library.md)utilize o atributo [EventHubAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs)

O construtor do atributo tem o nome do centro de eventos e o nome de uma definição de aplicação que contém a cadeia de ligação. Para obter mais informações sobre estas definições, consulte [a configuração De saída -](#configuration). Aqui está um `EventHub` exemplo de atributo:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [o exemplo de Saída - C#](#example).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a anotação [EventHubOutput](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) em parâmetros cujo valor seria publicado no Event Hub. O parâmetro deve ser do tipo `OutputBinding<T>` , onde é um `T` POJO ou qualquer tipo nativo de Java.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `EventHub` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para "eventHub". |
|**direção** | n/a | Deve ser definido para "fora". Este parâmetro é definido automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/a | O nome variável usado no código de função que representa o evento. |
|**caminho** |**Nome eventHub** | Funciona apenas 1.x. O nome do centro de eventos. Quando o nome do hub do evento também está presente na cadeia de ligação, esse valor sobrepõe-se a esta propriedade em tempo de execução. |
|**eventHubName** |**Nome eventHub** | Funções 2.x e superior. O nome do centro de eventos. Quando o nome do hub do evento também está presente na cadeia de ligação, esse valor sobrepõe-se a esta propriedade em tempo de execução. |
|**conexão** |**Ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação ao espaço de nome do centro de eventos. Copie esta cadeia de ligação clicando no botão **Informação de Ligação** para o *espaço de nomes,* e não o próprio hub do evento. Esta cadeia de ligação deve ter permissões de envio da mensagem para o stream do evento.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Envie mensagens utilizando um parâmetro de método, como `out string paramName` . No script C# `paramName` é o valor especificado na propriedade defunction.js`name` *em*. Para escrever várias mensagens, pode utilizar `ICollector<string>` ou no lugar de `IAsyncCollector<string>` `out string` .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Envie mensagens utilizando um parâmetro de método, como `out string paramName` . No script C# `paramName` é o valor especificado na propriedade defunction.js`name` *em*. Para escrever várias mensagens, pode utilizar `ICollector<string>` ou no lugar de `IAsyncCollector<string>` `out string` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda ao evento de saída utilizando `context.bindings.<name>` onde está o valor especificado na propriedade defunction.js`<name>` `name` *em*.

# <a name="python"></a>[Python](#tab/python)

Existem duas opções para a saída de uma mensagem do Event Hub a partir de uma função:

- **Valor de retorno**: Desa estafunction.js`name` o *imóvel.* `$return` Com esta configuração, o valor de retorno da função é persistido como uma mensagem Event Hub.

- **Imperativo**: Passe um valor ao método [definido](/python/api/azure-functions/azure.functions.out#set-val--t-----none) do parâmetro declarado como um tipo [out.](/python/api/azure-functions/azure.functions.out) O valor passado `set` é persistido como uma mensagem do Event Hub.

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para a saída de uma mensagem Do Event Hub a partir de uma função utilizando a anotação [EventHubOutput:](/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)

- **Valor de retorno**: Aplicando a anotação à função em si, o valor de retorno da função é persistido como uma mensagem Do Centro de Eventos.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.OutputBinding) , onde se encontra um `T` POJO ou qualquer tipo de Java nativo. Com esta configuração, passar um valor para o `setValue` método persiste o valor como uma mensagem Event Hub.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| Hub de Eventos | [Guia de Operações](/rest/api/eventhub/publisher-policy-operations) |