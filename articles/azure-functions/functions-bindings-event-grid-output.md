---
title: Ligação de saída da grelha de eventos Azure para funções Azure
description: Aprenda a enviar um evento de Grade de Eventos em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 888afdc2764fed9f0b2c8b548c3e2b1c48e9a31e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97094681"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Ligação de saída da grelha de eventos Azure para funções Azure

Utilize a ligação de saída da Grelha de Eventos para escrever eventos a um tópico personalizado. Deve ter uma chave de acesso válida [para o tópico personalizado.](../event-grid/security-authenticate-publishing-clients.md)

Para obter informações sobre detalhes de configuração e configuração, consulte a [visão geral](./functions-bindings-event-grid.md).

> [!NOTE]
> A ligação de saída da Grelha de Eventos não suporta assinaturas de acesso partilhada (fichas SAS). Tem de usar a chave de acesso do tópico.

> [!IMPORTANT]
> A ligação de saída da Grelha de Eventos só está disponível para funções 2.x e superior.

## <a name="example"></a>Exemplo

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que escreve uma mensagem para um tópico personalizado da Grade de Eventos, utilizando o valor de retorno do método como saída:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

O exemplo a seguir mostra como usar a `IAsyncCollector` interface para enviar um lote de mensagens.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra os dados de ligação à saída da Grelha de Eventos no *function.jsficheiro.*

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

Aqui está o código de script C# que cria um evento:

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

Aqui está o código de script C# que cria vários eventos:

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

# <a name="java"></a>[Java](#tab/java)

A ligação de saída da Grade de Eventos não está disponível para a Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra os dados de ligação à saída da Grelha de Eventos no *function.jsficheiro.*

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como configurar uma função para a saída de uma mensagem de evento de Event Grid. A secção onde `type` está definida para `eventGrid` configurar os valores necessários para estabelecer uma ligação de saída da Grade de Eventos.

```powershell
{
  "bindings": [
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    },
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    }
  ]
}
```

Na sua função, utilize o `Push-OutputBinding` para enviar um evento para um tópico personalizado através da ligação de saída da Grade de Eventos.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message

Push-OutputBinding -Name outputEvent -Value  @{
    id = "1"
    EventType = "testEvent"
    Subject = "testapp/testPublish"
    EventTime = "2020-08-27T21:03:07+00:00"
    Data = @{
        Message = $message
    }
    DataVersion = "1.0"
}

Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação do gatilho numa *function.jsem* ficheiro e uma [função Python](functions-reference-python.md) que utiliza a ligação. Em seguida, envia num evento para o tópico personalizado, conforme especificado pelo `topicEndpointUri` .

Aqui estão os dados vinculativos do *function.jsarquivado:*

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    },
    {
      "type": "eventGrid",
      "name": "outputEvent",
      "topicEndpointUri": "MyEventGridTopicUriSetting",
      "topicKeySetting": "MyEventGridTopicKeySetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Aqui está a amostra python para enviar um evento para um tópico personalizado, definindo o `EventGridOutputEvent` :

```python
import logging
import azure.functions as func
import datetime

def main(eventGridEvent: func.EventGridEvent, 
         outputEvent: func.Out[func.EventGridOutputEvent]) -> None:

    logging.log("eventGridEvent: ", eventGridEvent)

    outputEvent.set(
        func.EventGridOutputEvent(
            id="test-id",
            data={"tag1": "value1", "tag2": "value2"},
            subject="test-subject",
            event_type="test-event-1",
            event_time=datetime.datetime.utcnow(),
            data_version="1.0"))
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Para [bibliotecas de classe C#,](functions-dotnet-class-library.md)utilize o atributo [EventGridAttribute.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs)

O construtor do atributo tem o nome de uma definição de app que contém o nome do tópico personalizado, e o nome de uma definição de aplicação que contém a chave de tópico. Para obter mais informações sobre estas definições, consulte [a configuração De saída -](#configuration). Aqui está um `EventGrid` exemplo de atributo:

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para um exemplo completo, veja [o exemplo.](#example)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

A ligação de saída da Grade de Eventos não está disponível para a Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

A ligação de saída da Grade de Eventos não está disponível para Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `EventGrid` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para "eventGrid". |
|**direção** | n/a | Deve ser definido para "fora". Este parâmetro é definido automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/a | O nome variável usado no código de função que representa o evento. |
|**tópicoEndpointUri** |**TópicoEndpointUri** | O nome de uma definição de aplicação que contém o URI para o tópico personalizado, como `MyTopicEndpointUri` . |
|**tópicoSetting** |**TópicoKeySetting** | O nome de uma definição de aplicação que contém uma chave de acesso para o tópico personalizado. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Certifique-se de que define o valor da propriedade de `TopicEndpointUri` configuração para o nome de uma definição de aplicação que contém o URI do tópico personalizado. Não especifique o URI do tópico personalizado diretamente nesta propriedade.

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Envie mensagens utilizando um parâmetro de método, como `out EventGridEvent paramName` . Para escrever várias mensagens, pode utilizar `ICollector<EventGridEvent>` ou no lugar de `IAsyncCollector<EventGridEvent>` `out EventGridEvent` .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Envie mensagens utilizando um parâmetro de método, como `out EventGridEvent paramName` . No script C# `paramName` é o valor especificado na propriedade defunction.js`name` *em*. Para escrever várias mensagens, pode utilizar `ICollector<EventGridEvent>` ou no lugar de `IAsyncCollector<EventGridEvent>` `out EventGridEvent` .

# <a name="java"></a>[Java](#tab/java)

A ligação de saída da Grade de Eventos não está disponível para a Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda ao evento de saída utilizando `context.bindings.<name>` onde está o valor especificado na propriedade defunction.js`<name>` `name` *em*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aceda ao evento de saída utilizando o `Push-OutputBinding` comando para enviar um evento para a ligação de saída da Grade de Eventos.

# <a name="python"></a>[Python](#tab/python)

A ligação de saída da Grade de Eventos não está disponível para Python.

---

## <a name="next-steps"></a>Passos seguintes

* [Despachar um evento de Grade de Eventos](./functions-bindings-event-grid-trigger.md)
