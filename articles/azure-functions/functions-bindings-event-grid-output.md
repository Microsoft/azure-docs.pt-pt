---
title: Ligação de saída da Rede de Eventos Azure para funções Azure
description: Aprenda a enviar um evento da Grelha de Eventos em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368951"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Ligação de saída da Rede de Eventos Azure para funções Azure

Utilize a ligação de saída da Rede de Eventos para escrever eventos para um tópico personalizado. Deve ter uma chave de acesso válida [para o tópico personalizado](../event-grid/security-authentication.md#custom-topic-publishing).

Para obter informações sobre os detalhes da configuração e configuração, consulte a [visão geral](./functions-bindings-event-grid.md).

> [!NOTE]
> A ligação de saída da Rede de Eventos não suporta assinaturas de acesso partilhado (tokens SAS). Deve usar a chave de acesso do tópico.

> [!IMPORTANT]
> A ligação de saída da Rede de Eventos só está disponível para funções 2.x e superiores.

## <a name="example"></a>Exemplo

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que escreve uma mensagem para um tópico personalizado da Rede de Eventos, utilizando o valor de retorno do método como saída:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

O exemplo que se segue mostra como usar a interface `IAsyncCollector` para enviar um lote de mensagens.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra os dados de ligação de saída da Rede de Eventos no ficheiro *fun.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Aqui está C# o código script que cria um evento:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

Aqui está C# o código script que cria vários eventos:

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra os dados de ligação de saída da Rede de Eventos no ficheiro *fun.json.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Aqui está o código JavaScript que cria um único evento:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

Aqui está o código JavaScript que cria vários eventos:

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="pythontabpython"></a>[python](#tab/python)

A ligação de saída da Rede de Eventos não está disponível para python.

# <a name="javatabjava"></a>[Java](#tab/java)

A ligação de saída da Rede de Eventos não está disponível para java.

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o atributo [EventGridAttribute.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)

O construtor do atributo tem o nome de uma definição de app que contém o nome do tópico personalizado, e o nome de uma definição de app que contém a chave do tópico. Para obter mais informações sobre estas definições, consulte [Saída - configuração](#configuration). Aqui está um exemplo de atributo `EventGrid`:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte o [exemplo](#example).

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

A ligação de saída da Rede de Eventos não está disponível para python.

# <a name="javatabjava"></a>[Java](#tab/java)

A ligação de saída da Rede de Eventos não está disponível para java.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `EventGrid`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido para "eventGrid". |
|**direção** | n/d | Tem de ser definido para "Sair". Este parâmetro é definido automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/d | O nome variável usado no código de função que representa o evento. |
|**topicEndpointUri** |**TopicEndpointUri** | O nome de uma definição de aplicação que contém o URI para o tópico personalizado, como `MyTopicEndpointUri`. |
|**tópicoKeySetting** |**Definição de tópicos** | O nome de uma definição de aplicação que contém uma chave de acesso para o tópico personalizado. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Certifique-se de que define o valor da propriedade de configuração `TopicEndpointUri` para o nome de uma definição de aplicação que contém o URI do tópico personalizado. Não especifique o URI do tópico personalizado diretamente nesta propriedade.

## <a name="usage"></a>Utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Envie mensagens utilizando um parâmetro de método como `out EventGridEvent paramName`. Para escrever várias mensagens, pode utilizar `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` no lugar de `out EventGridEvent`.

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Envie mensagens utilizando um parâmetro de método como `out EventGridEvent paramName`. No C# script, `paramName` é o valor especificado na propriedade `name` da *função.json*. Para escrever várias mensagens, pode utilizar `ICollector<EventGridEvent>` ou `IAsyncCollector<EventGridEvent>` no lugar de `out EventGridEvent`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aceda ao evento de saída utilizando `context.bindings.<name>` onde `<name>` é o valor especificado na propriedade `name` *função.json*.

# <a name="pythontabpython"></a>[python](#tab/python)

A ligação de saída da Rede de Eventos não está disponível para python.

# <a name="javatabjava"></a>[Java](#tab/java)

A ligação de saída da Rede de Eventos não está disponível para java.

---

## <a name="next-steps"></a>Passos seguintes

* [Despachar um evento da Grelha de Eventos](./functions-bindings-event-grid-trigger.md)
