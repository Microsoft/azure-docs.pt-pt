---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 145db7693db126d4e114e8c8a885ea7fd7809e69
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608920"
---
Utilize o gatilho de função para responder a um evento enviado para um fluxo de eventos do centro de eventos. Deve ter lido o acesso ao centro de eventos subjacente para configurar o gatilho. Quando a função é desencadeada, a mensagem transmitida para a função é dactilografada como uma corda.

## <a name="scaling"></a>Dimensionamento

Cada instância de uma função desencadeada por um evento é apoiada por uma única instância [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor) O gatilho (alimentado por Event Hubs) garante que apenas uma instância [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) pode obter um arrendamento numa determinada partição.

Por exemplo, considere um Centro de Eventos da seguinte forma:

* 10 divisórias
* 1.000 eventos distribuídos uniformemente em todas as divisórias, com 100 mensagens em cada partição

Quando a sua função está ativada pela primeira vez, há apenas uma instância da função. Vamos chamar a primeira instância `Function_0` de função. A `Function_0` função tem uma única instância de [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) que detém um arrendamento em todas as dez divisórias. Este caso está a ler eventos de divisórias 0-9. A partir de agora, um dos seguintes acontece:

* **Não são necessários novos casos de função:** `Function_0` é capaz de processar todos os 1.000 eventos antes que a lógica de escalagem de funções produza efeito. Neste caso, todas as 1.000 mensagens são processadas por `Function_0` .

* **Uma instância de função adicional é adicionada**: Se a lógica de escala de Funções determinar que `Function_0` tem mais mensagens do que pode processar, é criada uma nova instância de aplicação de função `Function_1` ( ) Esta nova função também tem uma instância associada do [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor). À medida que os Centros de Eventos subjacentes detetam que uma nova instância de anfitrião está a tentar ler mensagens, ele carrega equilibrar as divisórias entre as instâncias do anfitrião. Por exemplo, as divisórias 0-4 podem ser `Function_0` atribuídas e as divisórias 5-9 a `Function_1` .

* **N mais instâncias de função são adicionadas**: Se a lógica de escala de Funções determinar que ambos `Function_0` e ter mais `Function_1` mensagens do que podem processar, `Functions_N` novas instâncias de aplicações de função são criadas.  As aplicações são criadas ao ponto de serem `N` maiores do que o número de divisórias do centro de eventos. No nosso exemplo, o Event Hubs volta a carregar equilibra as divisórias, neste caso através dos `Function_0` casos... `Functions_9` .

À medida que o escalonamento ocorre, `N` os casos são um número maior do que o número de partições do centro de eventos. Este padrão é usado para garantir que as instâncias [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor) estão disponíveis para obter bloqueios em divisórias à medida que ficam disponíveis a partir de outras instâncias. Só é cobrado pelos recursos utilizados quando a instância de função é executada. Por outras palavras, não é cobrado por este excesso de provisão.

Quando toda a execução da função estiver concluída (com ou sem erros), os pontos de verificação são adicionados à conta de armazenamento associada. Quando o check-pointing tem sucesso, todas as 1.000 mensagens nunca mais são recuperadas.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](../articles/azure-functions/functions-dotnet-class-library.md) que regista o corpo de mensagem do gatilho do centro de eventos.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para ter acesso aos [metadados de eventos](#event-metadata) no código de função, ligue-se a um objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer uma declaração de utilização para `Microsoft.Azure.EventHubs` ). Também pode aceder às mesmas propriedades usando expressões de encadernação na assinatura do método.  O exemplo a seguir mostra ambas as formas de obter os mesmos dados:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    ILogger log)
{
    log.LogInformation($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    // Metadata accessed by binding to EventData
    log.LogInformation($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.LogInformation($"Offset={myEventHubMessage.SystemProperties.Offset}");
    // Metadata accessed by using binding expressions in method parameters
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"SequenceNumber={sequenceNumber}");
    log.LogInformation($"Offset={offset}");
}
```

Para receber eventos num lote, faça `string` ou `EventData` array.  

> [!NOTE]
> Ao receber num lote não pode ligar-se a parâmetros de método como no exemplo acima com `DateTime enqueuedTimeUtc` e deve recebê-los de cada `EventData` objeto  

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData[] eventHubMessages, ILogger log)
{
    foreach (var message in eventHubMessages)
    {
        log.LogInformation($"C# function triggered to process a message: {Encoding.UTF8.GetString(message.Body)}");
        log.LogInformation($"EnqueuedTimeUtc={message.SystemProperties.EnqueuedTimeUtc}");
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação do gatilho do centro de eventos numa *function.jsno* ficheiro e numa [função de script C#](../articles/azure-functions/functions-reference-csharp.md) que utiliza a ligação. A função regista o corpo de mensagem do gatilho do centro do evento.

Os exemplos a seguir mostram que os dados de ligação do Event Hubs no *function.jsarquivados.*

### <a name="version-2x-and-higher"></a>Versão 2.x e superior

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código do guião C:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para ter acesso aos [metadados de eventos](#event-metadata) no código de função, ligue-se a um objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer uma declaração de utilização para `Microsoft.Azure.EventHubs` ). Também pode aceder às mesmas propriedades usando expressões de encadernação na assinatura do método.  O exemplo a seguir mostra ambas as formas de obter os mesmos dados:

```cs
#r "Microsoft.Azure.EventHubs"

using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;
using Microsoft.Azure.EventHubs;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc,
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.Body)}");
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.SystemProperties.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SystemProperties.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.SystemProperties.Offset}");

    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Para receber eventos num lote, faça `string` ou `EventData` array:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho do centro de eventos numa *function.jsno* ficheiro e numa [função JavaScript](../articles/azure-functions/functions-reference-node.md) que utiliza a ligação. A função lê [metadados de evento](#event-metadata) e regista a mensagem.

Os exemplos a seguir mostram que os dados de ligação do Event Hubs no *function.jsarquivados.*

### <a name="version-2x-and-higher"></a>Versão 2.x e superior

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Para receber eventos num lote, definido `cardinality` `many` nofunction.js *em* arquivo, como mostrado nos seguintes exemplos.

### <a name="version-2x-and-higher"></a>Versão 2.x e superior

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código JavaScript:

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);

    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir mostra uma ligação do gatilho do centro de eventos numa *function.jsem* ficheiro e numa [função Python](../articles/azure-functions/functions-reference-python.md) que utiliza a ligação. A função lê [metadados de evento](#event-metadata) e regista a mensagem.

Os exemplos a seguir mostram que os dados de ligação do Event Hubs no *function.jsarquivados.*

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)

    # Metadata
    for key in event.metadata:
        logging.info(f'Metadata: {key} = ', event.metadata[key])
```

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma ligação do gatilho do Event Hub que regista o corpo de mensagem do gatilho do Event Hub.

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context )
       {
          context.getLogger().info(message);
 }
```

 Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a `EventHubTrigger` anotação em parâmetros cujo valor viria do Event Hub. Parâmetros com estas anotações fazem com que a função funcione quando um evento chega.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores anulados usando `Optional<T>` .

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C#,](../articles/azure-functions/functions-dotnet-class-library.md)utilize o atributo [EventHubTriggerAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs)

O construtor do atributo tem o nome do centro de eventos, o nome do grupo de consumidores, e o nome de uma definição de aplicação que contém a cadeia de ligação. Para obter mais informações sobre estas definições, consulte a [secção de configuração](#configuration)do gatilho . Aqui está um `EventHubTriggerAttribute` exemplo de atributo:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [o exemplo do Trigger - C#](#example).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A partir da [biblioteca de funções](/java/api/overview/azure/functions/runtime)Java, use a anotação [EventHubTrigger](/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) em parâmetros cujo valor viria do Event Hub. Parâmetros com estas anotações fazem com que a função funcione quando um evento chega. Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores anulados usando `Optional<T>` .

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `EventHubTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Description|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `eventHubTrigger` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/a | Deve ser definido para `in` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/a | O nome da variável que representa o item do evento no código de função. |
|**caminho** |**Nome eventHub** | Funciona apenas 1.x. O nome do centro de eventos. Quando o nome do hub do evento também está presente na cadeia de ligação, esse valor sobrepõe-se a esta propriedade em tempo de execução. |
|**eventHubName** |**Nome eventHub** | Funções 2.x e superior. O nome do centro de eventos. Quando o nome do hub do evento também está presente na cadeia de ligação, esse valor sobrepõe-se a esta propriedade em tempo de execução. Pode ser referenciado através de [configurações de aplicativos](../articles/azure-functions/functions-bindings-expressions-patterns.md#binding-expressions---app-settings)`%eventHubName%` |
|**consumerGroup** |**Grupo de Consumidores** | Uma propriedade opcional que define o [grupo de consumidores](../articles/event-hubs/event-hubs-features.md#event-consumers) usado para subscrever eventos no centro. Se omitido, o `$Default` grupo de consumidores é utilizado. |
|**cardinalidade** | n/a | Usado para todos os idiomas não-C#. De modo a `many` ativar o lote.  Se omitir ou definir `one` para , uma única mensagem é transmitida para a função.<br><br>Em C#, esta propriedade é automaticamente atribuída sempre que o gatilho tem uma matriz para o tipo.|
|**conexão** |**Ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação ao espaço de nome do centro de eventos. Copie esta cadeia de ligação clicando no botão **Informação de Ligação** para o [espaço de nomes,](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)e não o próprio hub do evento. Esta cadeia de ligação deve ter pelo menos permissões de leitura para ativar o gatilho.<br><br>Se estiver a utilizar [a versão 5.x ou superior da extensão](../articles/azure-functions/functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher), em vez de uma cadeia de ligação, pode fornecer uma referência a uma secção de configuração que defina a ligação. Ver [Ligações](../articles/azure-functions/functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Predefinição

Pode utilizar os seguintes tipos de parâmetros para o centro de eventos de desencadeamento:

* `string`
* `byte[]`
* `POCO`
* `EventData` - As propriedades predefinidas do EventData são fornecidas no espaço de [nomes Microsoft.Azure.EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

### <a name="additional-types"></a>Tipos adicionais 
As aplicações que utilizam a versão 5.0.0 ou superior da extensão do Event Hub utilizam o `EventData` tipo em [Azure.Messaging.EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) em vez da do espaço de [nomes Microsoft.Azure.EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Esta versão deixa cair o suporte para o tipo legado `Body` a favor dos seguintes tipos:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="default"></a>Predefinição

Pode utilizar os seguintes tipos de parâmetros para o centro de eventos de desencadeamento:

* `string`
* `byte[]`
* `POCO`
* `EventData` - As propriedades predefinidas do EventData são fornecidas no espaço de [nomes Microsoft.Azure.EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet).

### <a name="additional-types"></a>Tipos adicionais 
As aplicações que utilizam a versão 5.0.0 ou superior da extensão do Event Hub utilizam o `EventData` tipo em [Azure.Messaging.EventHubs](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata?view=azure-dotnet) em vez da do espaço de [nomes Microsoft.Azure.EventHubs](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventdata?view=azure-dotnet). Esta versão deixa cair o suporte para o tipo legado `Body` a favor dos seguintes tipos:

- [EventBody](https://docs.microsoft.com/dotnet/api/azure.messaging.eventhubs.eventdata.eventbody?view=azure-dotnet)

# <a name="java"></a>[Java](#tab/java)

Consulte o [exemplo](#example) do gatilho de Java para obter mais detalhes.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Consulte o exemplo do [gatilho](#example) Javascript para obter mais detalhes.

# <a name="python"></a>[Python](#tab/python)

Consulte o [exemplo](#example) do gatilho Python para obter mais detalhes.


---


## <a name="event-metadata"></a>Metadados de eventos

O gatilho do Event Hubs fornece várias [propriedades de metadados.](../articles/azure-functions/./functions-bindings-expressions-patterns.md) As propriedades dos metadados podem ser usadas como parte de expressões de ligação noutras ligações ou como parâmetros no seu código. As propriedades provêm da classe [EventData.](/dotnet/api/microsoft.servicebus.messaging.eventdata)

|Propriedade|Tipo|Description|
|--------|----|-----------|
|`PartitionContext`|[PartitionContexto](/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|O `PartitionContext` exemplo.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo encadeado na UTC.|
|`Offset`|`string`|A compensação dos dados relativos ao fluxo de partição do Event Hub. O offset é um marcador ou identificador para um evento dentro do fluxo de Centros de Eventos. O identificador é único dentro de uma partição do fluxo de Centros de Eventos.|
|`PartitionKey`|`string`|A partição para a qual os dados do evento devem ser enviados.|
|`Properties`|`IDictionary<String,Object>`|As propriedades do utilizador dos dados do evento.|
|`SequenceNumber`|`Int64`|O número da sequência lógica do evento.|
|`SystemProperties`|`IDictionary<String,Object>`|As propriedades do sistema, incluindo os dados do evento.|

Consulte [exemplos de código](#example) que utilizam estas propriedades anteriormente neste artigo.