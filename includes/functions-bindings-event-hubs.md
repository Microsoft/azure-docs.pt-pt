---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: ec3a7b6420144278df66f693d9fd9933449b3d80
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748794"
---
## <a name="trigger"></a>Acionador

Use o gatilho de função para responder a um evento enviado a um fluxo de eventos do hub de eventos. Você deve ter acesso de leitura ao Hub de eventos subjacente para configurar o gatilho. Quando a função é disparada, a mensagem passada para a função é digitada como uma cadeia de caracteres.

## <a name="trigger---scaling"></a>Gatilho-dimensionamento

Cada instância de uma função desencadeada por eventos é apoiada por uma única instância do [EventProcessorHost.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) O gatilho (alimentado por Event Hubs) garante que apenas uma instância do [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) pode obter um arrendamento numa determinada partição.

Por exemplo, considere um hub de eventos da seguinte maneira:

* 10 partições
* 1\.000 eventos distribuídos uniformemente em todas as partições, com 100 mensagens em cada partição

Quando sua função é habilitada pela primeira vez, há apenas uma instância da função. Vamos chamar a primeira função `Function_0`. A função `Function_0` tem uma única instância de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) que detém um contrato de arrendamento em todas as dez divisórias. Esta instância está lendo eventos das partições 0-9. Deste ponto em diante, um dos seguintes acontece:

* **Não são necessárias novas instâncias**de função : `Function_0` é capaz de processar todos os 1.000 eventos antes que a lógica de escalação das Funções entre em vigor. Neste caso, todas as 1.000 mensagens são processadas por `Function_0`.

* **Uma instância de função adicional é adicionada**: Se a lógica de escalagem de Funções determinar que `Function_0` tem mais mensagens do que pode processar, é criada uma nova instância de aplicação de funções (`Function_1`). Esta nova função também tem uma instância associada de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). À medida que os Centros de Eventos subjacentes detetam que uma nova instância de anfitrião está a tentar ler mensagens, carrega os equilíbrios das divisórias através das instâncias anfitriãs. Por exemplo, as divisórias 0-4 podem ser atribuídas a `Function_0` e divisórias 5-9 a `Function_1`.

* **N mais casos de função são adicionados**: Se a lógica de escalação de Funções determinar que tanto `Function_0` como `Function_1` têm mais mensagens do que podem processar, novas instâncias de aplicações de função `Functions_N` são criadas.  As aplicações são criadas ao ponto de `N` é maior do que o número de divisórias do centro de eventos. No nosso exemplo, os Event Hubs voltam a carregar equilibra as divisórias, neste caso em todos os casos `Function_0`...`Functions_9`.

À medida que a escala ocorre, `N` casos é um número maior do que o número de divisórias do centro de eventos. Este padrão é usado para garantir que os casos do [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) estão disponíveis para obter fechaduras em divisórias à medida que ficam disponíveis de outras instâncias. Você é cobrado apenas pelos recursos usados quando a instância de função é executada. Em outras palavras, você não será cobrado por esse excesso de provisionamento.

Quando todas as execuções de função são concluídas (com ou sem erros), os pontos de verificação são adicionados à conta de armazenamento associada. Quando o ponto de verificação for executado com sucesso, todas as mensagens 1.000 nunca serão recuperadas novamente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](../articles/azure-functions/functions-dotnet-class-library.md) que regista o corpo da mensagem do gatilho do centro do evento.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para obter acesso aos metadados de [eventos](#trigger---event-metadata) no código de função, ligue-se a um objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer uma declaração de utilização para `Microsoft.Azure.EventHubs`). Você também pode acessar as mesmas propriedades usando expressões de associação na assinatura do método.  O exemplo a seguir mostra as duas maneiras de obter os mesmos dados:

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

Para receber eventos num lote, faça `string` ou `EventData` uma matriz.  

> [!NOTE]
> Ao receber um lote não é possível ligar-se a parâmetros métodos como no exemplo acima com `DateTime enqueuedTimeUtc` e deve recebê-los de cada `EventData` objeto  

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

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [ C# função de script](../articles/azure-functions/functions-reference-csharp.md) que utiliza a ligação. A função registra o corpo da mensagem do gatilho do hub de eventos.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.*

#### <a name="version-2x-and-higher"></a>Versão 2. x e superior

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versão 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código de script do c#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para obter acesso aos metadados de [eventos](#trigger---event-metadata) no código de função, ligue-se a um objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer uma declaração de utilização para `Microsoft.Azure.EventHubs`). Você também pode acessar as mesmas propriedades usando expressões de associação na assinatura do método.  O exemplo a seguir mostra as duas maneiras de obter os mesmos dados:

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

Para receber eventos num lote, faça `string` ou `EventData` uma matriz:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que utiliza a ligação. A função lê metadados de [eventos](#trigger---event-metadata) e regista a mensagem.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.*

#### <a name="version-2x-and-higher"></a>Versão 2. x e superior

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

### <a name="version-1x"></a>Versão 1. x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Eis o código JavaScript:

```javascript
module.exports = function (context, myEventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Para receber eventos num lote, detete `cardinality` `many` no ficheiro *função.json,* como mostram os seguintes exemplos.

#### <a name="version-2x-and-higher"></a>Versão 2. x e superior

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

### <a name="version-1x"></a>Versão 1. x

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

Eis o código JavaScript:

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

# <a name="pythontabpython"></a>[python](#tab/python)

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função Python](../articles/azure-functions/functions-reference-python.md) que utiliza a ligação. A função lê metadados de [eventos](#trigger---event-metadata) e regista a mensagem.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.*

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

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo seguinte mostra uma ligação do gatilho do Event Hub num ficheiro *function.json* e uma [função Java](../articles/azure-functions/functions-reference-java.md) que utiliza a ligação. A função registra o corpo da mensagem do gatilho do hub de eventos.

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

 Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)utilize a anotação `EventHubTrigger` em parâmetros cujo valor viria do Event Hub. Parâmetros com essas anotações fazem com que a função seja executada quando um evento chega.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores nuníveis usando `Optional<T>`.

 ---

## <a name="trigger---attributes-and-annotations"></a>Gatilho-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](../articles/azure-functions/functions-dotnet-class-library.md)utilize o atributo [EventHubTriggerAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs)

O construtor do atributo usa o nome do hub de eventos, o nome do grupo de consumidores e o nome de uma configuração de aplicativo que contém a cadeia de conexão. Para obter mais informações sobre estas definições, consulte a secção de [configuração](#trigger---configuration)do gatilho . Aqui está um exemplo de atributo `EventHubTriggerAttribute`:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [O Gatilho - C# exemplo](#trigger).

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A partir da biblioteca de [tempo de funcionamento](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)das funções java, use a anotação [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) em parâmetros cujo valor viria do Event Hub. Parâmetros com essas anotações fazem com que a função seja executada quando um evento chega. Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores nuníveis usando `Optional<T>`.

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `EventHubTrigger`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | deve ser definido para `eventHubTrigger`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | deve ser definido para `in`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**nome** | n/d | O nome da variável que representa o item de evento no código de função. |
|**caminho** |**Nome EventHub** | Somente funções 1. x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de conexão, esse valor substitui essa propriedade em tempo de execução. |
|**eventoHubName** |**Nome EventHub** | Functions 2. x e superior. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de conexão, esse valor substitui essa propriedade em tempo de execução. Pode ser referenciado através de configurações de aplicativo %eventHubName% |
|**consumerGroup** |**Grupo de Consumidores** | Uma propriedade opcional que define o grupo de [consumidores](../articles/event-hubs/event-hubs-features.md#event-consumers) usado para subscrever eventos no centro. Se omitir, o grupo de consumidores `$Default` é utilizado. |
|**cardinalidade** | n/d | Para JavaScript. Desloque-se para `many` de modo a ativar o loteamento.  Se omitida ou definida para `one`, uma única mensagem é passada para a função. |
|**conexão** |**Conexão** | O nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie esta cadeia de ligação clicando no botão Informação de **Ligação** para o espaço de [nome,](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)e não para o próprio centro de eventos. Essa cadeia de conexão deve ter pelo menos permissões de leitura para ativar o gatilho.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Gatilho-metadados de evento

O gatilho do Event Hubs fornece várias [propriedades de metadados.](../articles/azure-functions/./functions-bindings-expressions-patterns.md) As propriedades dos metadados podem ser usadas como parte de expressões de ligação noutras encadernações ou como parâmetros no seu código. As propriedades vêm da classe [EventData.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata)

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`PartitionContext`|[Contexto de partição](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|O `PartitionContext` caso.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo de colocados em fila em UTC.|
|`Offset`|`string`|O deslocamento dos dados relativos ao fluxo de partição do hub de eventos. O deslocamento é um marcador ou identificador de um evento dentro do fluxo de hubs de eventos. O identificador é exclusivo dentro de uma partição do fluxo dos hubs de eventos.|
|`PartitionKey`|`string`|A partição para a qual os dados de evento devem ser enviados.|
|`Properties`|`IDictionary<String,Object>`|As propriedades do usuário dos dados do evento.|
|`SequenceNumber`|`Int64`|O número de sequência lógica do evento.|
|`SystemProperties`|`IDictionary<String,Object>`|As propriedades do sistema, incluindo os dados do evento.|

Consulte [exemplos](#trigger) de código que usam estas propriedades no início deste artigo.

## <a name="trigger---hostjson-properties"></a>Acionador - propriedades de Host. JSON

O ficheiro [host.json](../articles/azure-functions/functions-host-json.md#eventhub) contém configurações que controlam o comportamento do gatilho do Event Hubs.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Saída

Use a associação de saída dos hubs de eventos para gravar eventos em um fluxo de eventos. Deve ter enviado permissão a um centro de eventos para escrever eventos.

Certifique-se de que as referências de pacote saem em vigor antes de tentar implementar uma ligação de saída.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](../articles/azure-functions/functions-dotnet-class-library.md) que escreve uma mensagem a um centro de eventos, utilizando o valor de retorno do método como saída:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

O exemplo que se segue mostra como usar a interface `IAsyncCollector` para enviar um lote de mensagens. Esse cenário é comum quando você está processando mensagens provenientes de um hub de eventos e enviando o resultado para outro hub de eventos.

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

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [ C# função de script](../articles/azure-functions/functions-reference-csharp.md) que utiliza a ligação. A função grava uma mensagem em um hub de eventos.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.* O primeiro exemplo é para o Functions 2. x e superior, e o segundo é para o Functions 1. x. 

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

O script código c# aqui está que cria várias mensagens:

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que utiliza a ligação. A função grava uma mensagem em um hub de eventos.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.* O primeiro exemplo é para o Functions 2. x e superior, e o segundo é para o Functions 1. x. 

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

# <a name="pythontabpython"></a>[python](#tab/python)

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função Python](../articles/azure-functions/functions-reference-python.md) que utiliza a ligação. A função grava uma mensagem em um hub de eventos.

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

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que grava uma mensagem que contém a hora atual em um hub de eventos.

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Na [biblioteca de tempo de funcionamento das funções java,](/java/api/overview/azure/functions/runtime)utilize a anotação `@EventHubOutput` em parâmetros cujo valor seria publicado no Event Hub.  O parâmetro deve ser de tipo `OutputBinding<T>`, onde T é um POJO ou qualquer tipo java nativo.

---

## <a name="output---attributes-and-annotations"></a>Saída-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Para [ C# bibliotecas de classes,](../articles/azure-functions/functions-dotnet-class-library.md)utilize o atributo [EventHubAttribute.](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs)

O construtor do atributo usa o nome do hub de eventos e o nome de uma configuração de aplicativo que contém a cadeia de conexão. Para obter mais informações sobre estas definições, consulte [Saída - configuração](#output---configuration). Aqui está um exemplo de atributo `EventHub`:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [A Saída - C# exemplo](#output).

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Na [biblioteca de tempo de funcionamento das funções java,](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)utilize a anotação [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) em parâmetros cujo valor seria publicado no Event Hub. O parâmetro deve ser de tipo `OutputBinding<T>`, onde `T` é um POJO ou qualquer tipo java nativo.

---

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `EventHub`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "eventHub". |
|**direção** | n/d | Tem de ser definido para "Sair". Esse parâmetro é definido automaticamente quando você cria a associação no portal do Azure. |
|**nome** | n/d | O nome da variável usada no código de função que representa o evento. |
|**caminho** |**Nome EventHub** | Somente funções 1. x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de conexão, esse valor substitui essa propriedade em tempo de execução. |
|**eventoHubName** |**Nome EventHub** | Functions 2. x e superior. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de conexão, esse valor substitui essa propriedade em tempo de execução. |
|**conexão** |**Conexão** | O nome de uma configuração de aplicativo que contém a cadeia de conexão para o namespace do hub de eventos. Copie esta cadeia de ligação clicando no botão Informação de **Ligação** para o espaço de *nome,* e não para o próprio centro de eventos. Essa cadeia de conexão deve ter permissões de envio para enviar a mensagem para o fluxo de eventos.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Envie mensagens utilizando um parâmetro de método como `out string paramName`. No C# script, `paramName` é o valor especificado na propriedade `name` da *função.json*. Para escrever várias mensagens, pode utilizar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Envie mensagens utilizando um parâmetro de método como `out string paramName`. No C# script, `paramName` é o valor especificado na propriedade `name` da *função.json*. Para escrever várias mensagens, pode utilizar `ICollector<string>` ou `IAsyncCollector<string>` no lugar de `out string`.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aceda ao evento de saída utilizando `context.bindings.<name>` onde `<name>` é o valor especificado na propriedade `name` *função.json*.

# <a name="pythontabpython"></a>[python](#tab/python)

Há duas opções para a saída de uma mensagem do hub de eventos de uma função:

- **Valor de devolução**: Coloque a propriedade `name` em *função.json* para `$return`. Com esta configuração, o valor de retorno da função é persistido como uma mensagem Do Hub do Evento.

- **Imperativo**: Passe um valor para o método [definido](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) O valor passado para `set` é perinendo como uma mensagem do Event Hub.

# <a name="javatabjava"></a>[Java](#tab/java)

Existem duas opções para eliminar uma mensagem do Event Hub a partir de uma função, utilizando a anotação [EventHubOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput)

- **Valor de devolução**: Aplicando a anotação à própria função, o valor de retorno da função é persinedo como mensagem Do Centro de Eventos.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`, ](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)onde `T` seja um POJO ou qualquer tipo de Java nativo. Com esta configuração, passar um valor para o método `setValue` persiste o valor como mensagem Do Hub do Evento.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Hub de Eventos | [Guia de Operações](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O arquivo host. JSON de exemplo abaixo contém apenas as configurações da versão 2. x + para essa associação. Para obter mais informações sobre as configurações globais de configuração nas versões 2.x e além, consulte a [referência host.json para funções azure](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Para uma referência do host.json nas funções 1.x, consulte a [referência host.json para as funções Azure 1.x](../articles/azure-functions/functions-host-json-v1.md).

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
|`maxBatchSize`|10|A contagem máxima de eventos recebida por loop de recebimento.|
|`prefetchCount`|300|A contagem pre-fetch predefinida utilizada pela `EventProcessorHost`subjacente .|
|`batchCheckpointFrequency`|1|O número de lotes de eventos a serem processados antes de criar um ponto de verificação de cursor do EventHub.|
