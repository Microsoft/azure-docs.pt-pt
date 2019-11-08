---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 0f94c89a52de138b261796cbef25c0acb57622c4
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800043"
---
## <a name="trigger"></a>Acionador

Use o gatilho de função para responder a um evento enviado a um fluxo de eventos do hub de eventos. Você deve ter acesso de leitura ao Hub de eventos subjacente para configurar o gatilho. Quando a função é disparada, a mensagem passada para a função é digitada como uma cadeia de caracteres.

## <a name="trigger---scaling"></a>Gatilho-dimensionamento

Cada instância de uma função disparada por evento é apoiada por uma única instância de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) . O gatilho (da plataforma de hubs de eventos) garante que apenas uma instância [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) possa obter uma concessão em uma determinada partição.

Por exemplo, considere um hub de eventos da seguinte maneira:

* 10 partições
* 1\.000 eventos distribuídos uniformemente em todas as partições, com 100 mensagens em cada partição

Quando sua função é habilitada pela primeira vez, há apenas uma instância da função. Vamos chamar a primeira instância de função `Function_0`. A função `Function_0` tem uma única instância de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) que contém uma concessão em todas as dez partições. Esta instância está lendo eventos das partições 0-9. Deste ponto em diante, um dos seguintes acontece:

* **Novas instâncias de função não são necessárias**: `Function_0` é capaz de processar todos os eventos 1.000 antes que a lógica de dimensionamento de funções entre em vigor. Nesse caso, todas as mensagens 1.000 são processadas pelo `Function_0`.

* **Uma instância de função adicional é adicionada**: se a lógica de dimensionamento de funções determinar que `Function_0` tem mais mensagens do que pode processar, uma nova instância de aplicativo de função (`Function_1`) será criada. Essa nova função também tem uma instância associada de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Como os hubs de eventos subjacentes detectam que uma nova instância do host está tentando ler mensagens, ele balanceia a carga das partições em suas instâncias de host. Por exemplo, as partições 0-4 podem ser atribuídas a `Function_0` e partições 5-9 a `Function_1`.

* **N mais instâncias de função são adicionadas**: se a lógica de dimensionamento de funções determinar que tanto `Function_0` quanto `Function_1` têm mais mensagens do que podem ser processadas, novas instâncias de aplicativo de função `Functions_N` são criadas.  Os aplicativos são criados para o ponto em que `N` é maior que o número de partições do hub de eventos. Em nosso exemplo, os hubs de eventos equilibram novamente as partições, nesse caso, nas instâncias `Function_0`...`Functions_9`.

Quando o Functions é dimensionado, `N` instâncias é um número maior que o número de partições do hub de eventos. Isso é feito para garantir que as instâncias de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) estejam disponíveis para obter bloqueios em partições à medida que se tornam disponíveis de outras instâncias. Você é cobrado apenas pelos recursos usados quando a instância de função é executada. Em outras palavras, você não será cobrado por esse excesso de provisionamento.

Quando todas as execuções de função são concluídas (com ou sem erros), os pontos de verificação são adicionados à conta de armazenamento associada. Quando o ponto de verificação for executado com sucesso, todas as mensagens 1.000 nunca serão recuperadas novamente.

## <a name="trigger---example"></a>Gatilho-exemplo

Consulte o exemplo específico de linguagem:

* [C#](#trigger---c-example)
* [C#script (. CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Gatilho- C# exemplo

O exemplo a seguir mostra uma [ C# função](../articles/azure-functions/functions-dotnet-class-library.md) que registra o corpo da mensagem do gatilho do hub de eventos.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para obter acesso aos [metadados de evento](#trigger---event-metadata) no código de função, associe a um objeto [EVENTDATA](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer uma instrução using para `Microsoft.Azure.EventHubs`). Você também pode acessar as mesmas propriedades usando expressões de associação na assinatura do método.  O exemplo a seguir mostra as duas maneiras de obter os mesmos dados:

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

Para receber eventos em um lote, faça `string` ou `EventData` uma matriz.  

> [!NOTE]
> Ao receber em um lote, você não pode associar a parâmetros de método como no exemplo acima com `DateTime enqueuedTimeUtc` e deve recebê-los de cada objeto `EventData`  

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

### <a name="trigger---c-script-example"></a>Gatilho- C# exemplo de script

O exemplo a seguir mostra uma associação de gatilho de Hub de eventos em um arquivo *Function. JSON* e uma [ C# função de script](../articles/azure-functions/functions-reference-csharp.md) que usa a associação. A função registra o corpo da mensagem do gatilho do hub de eventos.

Os exemplos a seguir mostram dados de vinculação de hubs de eventos no arquivo *Function. JSON* .

#### <a name="version-2x"></a>Versão 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versão 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este é o C# código de script:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para obter acesso aos [metadados de evento](#trigger---event-metadata) no código de função, associe a um objeto [EVENTDATA](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer uma instrução using para `Microsoft.Azure.EventHubs`). Você também pode acessar as mesmas propriedades usando expressões de associação na assinatura do método.  O exemplo a seguir mostra as duas maneiras de obter os mesmos dados:

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

Para receber eventos em um lote, faça `string` ou `EventData` uma matriz:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Gatilho- F# exemplo

O exemplo a seguir mostra uma associação de gatilho de Hub de eventos em um arquivo *Function. JSON* e uma [ F# função](../articles/azure-functions/functions-reference-fsharp.md) que usa a associação. A função registra o corpo da mensagem do gatilho do hub de eventos.

Os exemplos a seguir mostram dados de vinculação de hubs de eventos no arquivo *Function. JSON* . 

#### <a name="version-2x"></a>Versão 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versão 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este é o F# código:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Gatilho-exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de Hub de eventos em um arquivo *Function. JSON* e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que usa a associação. A função lê [metadados de eventos](#trigger---event-metadata) e registra a mensagem.

Os exemplos a seguir mostram dados de vinculação de hubs de eventos no arquivo *Function. JSON* .

#### <a name="version-2x"></a>Versão 2. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versão 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este é o código JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Para receber eventos em um lote, defina `cardinality` como `many` no arquivo *Function. JSON* , conforme mostrado nos exemplos a seguir.

#### <a name="version-2x"></a>Versão 2. x

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

#### <a name="version-1x"></a>Versão 1. x

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

Este é o código JavaScript:

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

### <a name="trigger---python-example"></a>Gatilho-exemplo de Python

O exemplo a seguir mostra uma associação de gatilho de Hub de eventos em um arquivo *Function. JSON* e uma [função Python](../articles/azure-functions/functions-reference-python.md) que usa a associação. A função lê [metadados de eventos](#trigger---event-metadata) e registra a mensagem.

Os exemplos a seguir mostram dados de vinculação de hubs de eventos no arquivo *Function. JSON* .

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Este é o código Python:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Gatilho-exemplo de Java

O exemplo a seguir mostra uma associação de gatilho de Hub de eventos em um arquivo *Function. JSON* e uma [função Java](../articles/azure-functions/functions-reference-java.md) que usa a associação. A função registra o corpo da mensagem do gatilho do hub de eventos.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

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

 Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `EventHubTrigger` em parâmetros cujo valor venha do hub de eventos. Parâmetros com essas anotações fazem com que a função seja executada quando um evento chega.  Esta anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando > opcional\<T.

## <a name="trigger---attributes"></a>Gatilho-atributos

Em [ C# bibliotecas de classes](../articles/azure-functions/functions-dotnet-class-library.md), use o atributo [EventHubTriggerAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) .

O construtor do atributo usa o nome do hub de eventos, o nome do grupo de consumidores e o nome de uma configuração de aplicativo que contém a cadeia de conexão. Para obter mais informações sobre essas configurações, consulte a [seção de configuração do gatilho](#trigger---configuration). Aqui está um exemplo de atributo `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [gatilho C# -exemplo](#trigger---c-example).

## <a name="trigger---configuration"></a>Gatilho-configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `EventHubTrigger`.

|Propriedade function. JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como `eventHubTrigger`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direção** | n/d | Deve ser definido como `in`. Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**nomes** | n/d | O nome da variável que representa o item de evento no código de função. |
|**Multi-Path** |**EventHubName** | Somente funções 1. x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de conexão, esse valor substitui essa propriedade em tempo de execução. |
|**eventHubName** |**EventHubName** | Somente funções 2. x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de conexão, esse valor substitui essa propriedade em tempo de execução. |
|**consumerGroup** |**ConsumerGroup** | Uma propriedade opcional que define o [grupo de consumidores](../articles/event-hubs/event-hubs-features.md#event-consumers) usado para assinar eventos no Hub. Se omitido, o grupo de consumidores `$Default` será usado. |
|**cardinalidade** | n/d | Para JavaScript. Defina como `many` para habilitar o envio em lote.  Se omitido ou definido como `one`, mensagem única passada para a função. |
|**conexão** |**Conexão** | O nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie essa cadeia de conexão clicando no botão **informações de conexão** do [namespace](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), não no próprio Hub de eventos. Essa cadeia de conexão deve ter pelo menos permissões de leitura para ativar o gatilho.|
|**Multi-Path**|**EventHubName**|O nome do hub de eventos. Pode ser referenciado por meio das configurações do aplicativo `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Gatilho-metadados de evento

O gatilho de hubs de eventos fornece várias [Propriedades de metadados](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Essas propriedades podem ser usadas como parte de expressões de associação em outras associações ou como parâmetros em seu código. Essas são propriedades da classe [EVENTDATA](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) .

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|A instância de `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo enfileirado em UTC.|
|`Offset`|`string`|O deslocamento dos dados relativos ao fluxo de partição do hub de eventos. O deslocamento é um marcador ou identificador de um evento dentro do fluxo de hubs de eventos. O identificador é exclusivo dentro de uma partição do fluxo dos hubs de eventos.|
|`PartitionKey`|`string`|A partição para a qual os dados de evento devem ser enviados.|
|`Properties`|`IDictionary<String,Object>`|As propriedades do usuário dos dados do evento.|
|`SequenceNumber`|`Int64`|O número de sequência lógica do evento.|
|`SystemProperties`|`IDictionary<String,Object>`|As propriedades do sistema, incluindo os dados do evento.|

Consulte os [exemplos de código](#trigger---example) que usam essas propriedades anteriormente neste artigo.

## <a name="trigger---hostjson-properties"></a>Trigger-Propriedades de host. JSON

O arquivo [host. JSON](../articles/azure-functions/functions-host-json.md#eventhub) contém configurações que controlam o comportamento do gatilho de hubs de eventos.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Saída

Use a associação de saída dos hubs de eventos para gravar eventos em um fluxo de eventos. Você deve ter permissão de envio para um hub de eventos para gravar eventos nele.

Verifique se as referências de pacote necessárias estão em vigor: funções 1. x ou funções 2. x

## <a name="output---example"></a>Saída-exemplo

Consulte o exemplo específico de linguagem:

* [C#](#output---c-example)
* [C#script (. CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Saída- C# exemplo

O exemplo a seguir mostra uma [ C# função](../articles/azure-functions/functions-dotnet-class-library.md) que grava uma mensagem em um hub de eventos, usando o valor de retorno do método como a saída:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

O exemplo a seguir mostra como usar a interface `IAsyncCollector` para enviar um lote de mensagens. Esse cenário é comum quando você está processando mensagens provenientes de um hub de eventos e enviando o resultado para outro hub de eventos.

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

### <a name="output---c-script-example"></a>Exemplo de C# script de saída

O exemplo a seguir mostra uma associação de gatilho de Hub de eventos em um arquivo *Function. JSON* e uma [ C# função de script](../articles/azure-functions/functions-reference-csharp.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de vinculação de hubs de eventos no arquivo *Function. JSON* . O primeiro exemplo é para o Functions 2. x e o segundo é para o Functions 1. x. 

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

Aqui está C# o código de script que cria uma mensagem:

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

Aqui está C# o código de script que cria várias mensagens:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Saída- F# exemplo

O exemplo a seguir mostra uma associação de gatilho de Hub de eventos em um arquivo *Function. JSON* e uma [ F# função](../articles/azure-functions/functions-reference-fsharp.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de vinculação de hubs de eventos no arquivo *Function. JSON* . O primeiro exemplo é para o Functions 2. x e o segundo é para o Functions 1. x. 

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

Este é o F# código:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Saída-exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de Hub de eventos em um arquivo *Function. JSON* e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de vinculação de hubs de eventos no arquivo *Function. JSON* . O primeiro exemplo é para o Functions 2. x e o segundo é para o Functions 1. x. 

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

Este é o código JavaScript que envia uma única mensagem:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Este é o código JavaScript que envia várias mensagens:

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

### <a name="output---python-example"></a>Saída-exemplo de Python

O exemplo a seguir mostra uma associação de gatilho de Hub de eventos em um arquivo *Function. JSON* e uma [função Python](../articles/azure-functions/functions-reference-python.md) que usa a associação. A função grava uma mensagem em um hub de eventos.

Os exemplos a seguir mostram dados de vinculação de hubs de eventos no arquivo *Function. JSON* .

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Aqui está o código do Python que envia uma única mensagem:

```python
import datetime
import logging
import azure.functions as func


def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp)
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Saída-exemplo de Java

O exemplo a seguir mostra uma função Java que grava uma mensagem contianing a hora atual em um hub de eventos.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@EventHubOutput` em parâmetros cujo valor seria publicado no Hub de eventos.  O parâmetro deve ser do tipo `OutputBinding<T>`, em que T é um POJO ou qualquer tipo de Java nativo.

## <a name="output---attributes"></a>Saída-atributos

Para [ C# bibliotecas de classes](../articles/azure-functions/functions-dotnet-class-library.md), use o atributo [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) .

O construtor do atributo usa o nome do hub de eventos e o nome de uma configuração de aplicativo que contém a cadeia de conexão. Para obter mais informações sobre essas configurações, consulte [saída-configuração](#output---configuration). Aqui está um exemplo de atributo `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída C# -exemplo](#output---c-example).

## <a name="output---configuration"></a>Saída-configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *Function. JSON* e o atributo `EventHub`.

|Propriedade function. JSON | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "eventHub". |
|**direção** | n/d | Deve ser definido como "out". Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**nomes** | n/d | O nome da variável usada no código de função que representa o evento. |
|**Multi-Path** |**EventHubName** | Somente funções 1. x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de conexão, esse valor substitui essa propriedade em tempo de execução. |
|**eventHubName** |**EventHubName** | Somente funções 2. x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de conexão, esse valor substitui essa propriedade em tempo de execução. |
|**conexão** |**Conexão** | O nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie essa cadeia de conexão clicando no botão **informações de conexão** do *namespace*, não no próprio Hub de eventos. Essa cadeia de conexão deve ter permissões de envio para enviar a mensagem para o fluxo de eventos.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída-uso

Em C# e C# script, envie mensagens usando um parâmetro de método, como `out string paramName`. Em C# script, `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. Para gravar várias mensagens, você pode usar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

No JavaScript, acesse o evento de saída usando `context.bindings.<name>`. `<name>` é o valor especificado na propriedade `name` de *Function. JSON*.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vinculação | Referência |
|---|---|
| Hub de Eventos | [Guia de operações](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configurações de host. JSON

Esta seção descreve as definições de configuração global disponíveis para essa associação na versão 2. x. O arquivo host. JSON de exemplo abaixo contém apenas as configurações de versão 2. x para essa associação. Para obter mais informações sobre definições de configuração global na versão 2. x, consulte [referência de host. JSON para Azure Functions versão 2. x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Para obter uma referência de host. JSON nas funções 1. x, consulte [referência de host. JSON para Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|maxBatchSize|64|A contagem máxima de eventos recebida por loop de recebimento.|
|prefetchCount|n/d|O PrefetchCount padrão que será usado pelo EventProcessorHost subjacente.|
|batchCheckpointFrequency|1|O número de lotes de eventos a serem processados antes de criar um ponto de verificação de cursor do EventHub.|
