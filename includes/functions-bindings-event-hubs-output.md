---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 78836ca4e51875be4237267b3bb9256cc4541fe2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791706"
---
Utilize a ligação de saída dos Centros de Eventos para escrever eventos num fluxo de eventos. Para escrever eventos em um hub de eventos, é preciso ter permissão de envio para ele.

Certifique-se de que as referências de pacote saem em vigor antes de tentar implementar uma ligação de saída.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte mostra uma [função C#](../articles/azure-functions/functions-dotnet-class-library.md) que escreve uma mensagem a um centro de eventos, utilizando o valor de retorno do método como saída:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

O exemplo que se `IAsyncCollector` segue mostra como usar a interface para enviar um lote de mensagens. Este cenário é comum quando está a processar mensagens provenientes de um Hub de Eventos e a enviar o resultado para outro Hub de Eventos.

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

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função de script C#](../articles/azure-functions/functions-reference-csharp.md) que utiliza a ligação. A função escreve uma mensagem para um centro de eventos.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.* O primeiro exemplo é para funções 2.x e superior, e o segundo é para funções 1.x. 

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

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que utiliza a ligação. A função escreve uma mensagem para um centro de eventos.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.* O primeiro exemplo é para funções 2.x e superior, e o segundo é para funções 1.x. 

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

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função Python](../articles/azure-functions/functions-reference-python.md) que utiliza a ligação. A função escreve uma mensagem para um centro de eventos.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.*

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

O exemplo seguinte mostra uma função Java que escreve uma mensagem que contém o tempo atual para um Hub de Eventos.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Na [biblioteca de tempo de funcionamento das funções java,](/java/api/overview/azure/functions/runtime)utilize a `@EventHubOutput` anotação em parâmetros cujo valor seria publicado no Event Hub.  O parâmetro deve ser `OutputBinding<T>` de tipo, onde T é um POJO ou qualquer tipo java nativo.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Para [bibliotecas de classe C#,](../articles/azure-functions/functions-dotnet-class-library.md)utilize o atributo [EventHubAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs)

O construtor do atributo tem o nome do centro do evento e o nome de uma definição de aplicação que contém a cadeia de ligação. Para obter mais informações sobre estas definições, consulte [Saída - configuração](#configuration). Aqui está `EventHub` um exemplo de atributo:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [a saída - C# exemplo](#example).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de tempo de funcionamento das funções java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)utilize a anotação [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) em parâmetros cujo valor seria publicado no Event Hub. O parâmetro deve ser `OutputBinding<T>` de `T` tipo, onde é um POJO ou qualquer tipo java nativo.

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `EventHub` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido para "eventHub". |
|**direção** | n/d | Deve ser definido para "fora". Este parâmetro é definido automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/d | O nome variável usado no código de função que representa o evento. |
|**caminho** |**Nome EventHub** | Funciona apenas 1.x. O nome do centro de eventos. Quando o nome do centro do evento também está presente na cadeia de ligação, esse valor sobrepõe-se a esta propriedade em tempo de execução. |
|**eventHubName** |**Nome EventHub** | Funções 2.x e superiores. O nome do centro de eventos. Quando o nome do centro do evento também está presente na cadeia de ligação, esse valor sobrepõe-se a esta propriedade em tempo de execução. |
|**conexão** |**Conexão** | O nome de uma definição de aplicação que contém a cadeia de ligação ao espaço de nome do centro de eventos. Copie esta cadeia de ligação clicando no botão Informação de **Ligação** para o espaço de *nome,* e não para o próprio centro de eventos. Esta cadeia de ligação deve ter enviado permissões para enviar a mensagem para o fluxo do evento.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C #](#tab/csharp)

Envie mensagens utilizando um parâmetro `out string paramName`de método como . No script C#, `paramName` é o `name` valor especificado na propriedade da *função.json*. Para escrever várias mensagens, pode utilizar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Envie mensagens utilizando um parâmetro `out string paramName`de método como . No script C#, `paramName` é o `name` valor especificado na propriedade da *função.json*. Para escrever várias mensagens, pode utilizar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda ao evento `context.bindings.<name>` `<name>` de saída utilizando onde `name` está o valor especificado na propriedade da *função.json*.

# <a name="python"></a>[Python](#tab/python)

Existem duas opções para distribuir uma mensagem do Event Hub a partir de uma função:

- **Valor de devolução**: Coloque a `name` propriedade em *função.json* para `$return`. Com esta configuração, o valor de retorno da função é persistido como uma mensagem Do Hub do Evento.

- **Imperativo**: Passe um valor para o método [definido](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) O valor `set` passado é persistiu como uma mensagem do Event Hub.

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para eliminar uma mensagem do Event Hub a partir de uma função, utilizando a anotação [EventHubOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)

- **Valor de devolução**: Aplicando a anotação à própria função, o valor de retorno da função é persinedo como mensagem Do Centro de Eventos.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)a `T` anotação a um parâmetro específico do tipo, onde se trata de um POJO ou de qualquer tipo de Java nativo. Com esta configuração, passar `setValue` um valor para o método persiste o valor como mensagem Do Hub do Evento.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| Hub de Eventos | [Guia de Operações](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |
