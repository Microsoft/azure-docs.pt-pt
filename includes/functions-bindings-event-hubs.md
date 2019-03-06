---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 1957fa4310a22a162ee2a621d1e0349e253badb3
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456572"
---
## <a name="trigger"></a>Acionador

Utilize o acionador de função para responder a um evento enviado para um fluxo de eventos do hub de eventos. Tem de ter acesso de leitura para o hub de eventos subjacente para configurar o acionador. Quando a função é acionada, a mensagem passada para a função é digitada como uma cadeia de caracteres.

## <a name="trigger---scaling"></a>Acionar - dimensionamento

Cada instância de uma função acionada de eventos é apoiada por um único [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instância. O acionador (com tecnologia dos Hubs de eventos) garante que apenas [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instância pode obter uma concessão sobre uma determinada partição.

Por exemplo, considere um Hub de eventos da seguinte forma:

* 10 partições
* 1000 eventos distribuídos uniformemente por todas as partições, com 100 mensagens em cada partição

Quando a sua função é habilitada pela primeira vez, isso significa que existe apenas uma instância da função. Vamos chamar a primeira instância de função `Function_0`. O `Function_0` função tem uma única instância do [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) que contém uma concessão em todas as partições de dez. Esta instância é ler eventos de 0 a 9 de partições. A partir deste ponto, um dos seguintes acontece:

* **Novas instâncias de função não são necessários**: `Function_0` é capaz de processar todos os 1000 eventos antes das funções de dimensionamento lógica entrem em vigor. Neste caso, todos os 1.000 mensagens são processadas pelo `Function_0`.

* **É adicionada uma instância de função adicionais**: Se as funções de dimensionamento lógica determina que `Function_0` tem mais mensagens do que consegue processar, uma nova instância de aplicação de função (`Function_1`) é criado. Esta nova função também tem uma instância associada [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Como os Hubs de eventos subjacente detectar que uma nova instância de anfitrião está a tentar ler as mensagens, ele balanceamentos de carga as partições entre as suas instâncias de anfitrião. Por exemplo, as partições de 0 a 4 podem ser atribuídas a `Function_0` e cria partições 5 a 9 para `Function_1`.

* **N mais instâncias de função são adicionadas**: Se as funções de dimensionamento lógica determina que ambos `Function_0` e `Function_1` têm mais mensagens do que o que podem ser processadas, novo `Functions_N` são criadas instâncias da aplicação de função.  Aplicações são criadas para o ponto onde `N` é maior do que o número de partições do hub de eventos. No nosso exemplo, os Hubs de eventos novamente carga equilibra as partições, neste caso pelas instâncias `Function_0`... `Functions_9`.

Quando as funções de escalas, `N` instâncias é um número maior do que o número de partições do hub de eventos. Isso é feito para garantir [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) instâncias estão disponíveis para obter bloqueios em partições à medida que ficam disponíveis a partir de outras instâncias. É cobrado apenas os recursos utilizados quando a instância de função for executada. Em outras palavras, não são cobradas deste aprovisionamento excessivo.

Quando todos os de função de execução estar concluída (com ou sem erros), pontos de verificação são adicionados à conta de armazenamento associados. Quando a verificação for bem sucedida, todos os 1.000 mensagens são nunca obtidas novamente.

## <a name="trigger---example"></a>Acionador - exemplo

Veja o exemplo de idioma específico:

* [C#](#trigger---c-example)
* [Script do c# (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Acionador - exemplo do c#

A exemplo a seguir mostra um [função c#](../articles/azure-functions/functions-dotnet-class-library.md) que regista o corpo da mensagem do acionador do hub de eventos.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para obter acesso ao [metadados de eventos](#trigger---event-metadata) no código de função, vincular a um [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objeto (requer a uma utilização de instrução para `Microsoft.Azure.EventHubs`). Também pode acessar as mesmas propriedades utilizando expressões de associação na assinatura do método.  O exemplo seguinte mostra as duas formas de obter os mesmos dados:

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

Para receber eventos num lote, tornar `string` ou `EventData` uma matriz.  

> [!NOTE]
> Ao receber num batch não é possível vincular aos parâmetros de método, como no exemplo acima com `DateTime enqueuedTimeUtc` e tem recebê-las de cada `EventData` objeto  

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

### <a name="trigger---c-script-example"></a>Acionador - exemplo de script do c#

O exemplo seguinte mostra um acionador do hub de eventos de enlace num *Function* ficheiro e uma [função de script do c#](../articles/azure-functions/functions-reference-csharp.md) que utiliza o enlace. A função regista o corpo da mensagem do acionador do hub de eventos.

Os exemplos seguintes mostram a vinculação de dados de Hubs de eventos no *Function* ficheiro.

#### <a name="version-2x"></a>Versão 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versão 1.x

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

Para obter acesso ao [metadados de eventos](#trigger---event-metadata) no código de função, vincular a um [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) objeto (requer a uma utilização de instrução para `Microsoft.Azure.EventHubs`). Também pode acessar as mesmas propriedades utilizando expressões de associação na assinatura do método.  O exemplo seguinte mostra as duas formas de obter os mesmos dados:

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

Para receber eventos num lote, tornar `string` ou `EventData` uma matriz:

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# function triggered to process a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Acionador - F# exemplo

O exemplo seguinte mostra um acionador do hub de eventos de enlace num *Function* ficheiro e uma [ F# função](../articles/azure-functions/functions-reference-fsharp.md) que utiliza o enlace. A função regista o corpo da mensagem do acionador do hub de eventos.

Os exemplos seguintes mostram a vinculação de dados de Hubs de eventos no *Function* ficheiro. 

#### <a name="version-2x"></a>Versão 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versão 1.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o F# código:

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Log(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Acionador - exemplo de JavaScript

O exemplo seguinte mostra um acionador do hub de eventos de enlace num *Function* ficheiro e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que utiliza o enlace. A função lê [metadados de eventos](#trigger---event-metadata) e regista a mensagem.

Os exemplos seguintes mostram a vinculação de dados de Hubs de eventos no *Function* ficheiro.

#### <a name="version-2x"></a>Versão 2.x

```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

#### <a name="version-1x"></a>Versão 1.x

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
module.exports = function (context, eventHubMessage) {
    context.log('Function triggered to process a message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);

    context.done();
};
```

Para receber eventos num lote, defina `cardinality` para `many` no *Function* ficheiro, conforme mostrado nos exemplos a seguir.

#### <a name="version-2x"></a>Versão 2.x

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

#### <a name="version-1x"></a>Versão 1.x

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

### <a name="trigger---python-example"></a>Acionador - exemplo de Python

O exemplo seguinte mostra um acionador do hub de eventos de enlace num *Function* ficheiro e uma [função Python](../articles/azure-functions/functions-reference-python.md) que utiliza o enlace. A função lê [metadados de eventos](#trigger---event-metadata) e regista a mensagem.

Os exemplos seguintes mostram a vinculação de dados de Hubs de eventos no *Function* ficheiro.

```json
{
  "type": "eventHubTrigger",
  "name": "event",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Aqui está o código de Python:

```python
import logging
import azure.functions as func

def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

### <a name="trigger---java-example"></a>Acionador - exemplo de Java

O exemplo seguinte mostra um acionador do Hub de eventos de enlace num *Function* ficheiro e uma [função Java](../articles/azure-functions/functions-reference-java.md) que utiliza o enlace. A função regista o corpo da mensagem do acionador do Hub de eventos.

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

 Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `EventHubTrigger` anotação em parâmetros cujo valor virá do Hub de eventos. Parâmetros com essas anotações fazer com que a função ser executada quando um evento é recebido.  Esta anotação pode ser utilizada com tipos nativos de Java, POJOs ou valores anuláveis usando opcional<T>.

## <a name="trigger---attributes"></a>Acionador - atributos

Na [bibliotecas de classes do c#](../articles/azure-functions/functions-dotnet-class-library.md), utilize o [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs) atributo.

Construtor o atributo usa o nome do hub de eventos, o nome do grupo de consumidores e o nome de uma definição de aplicação que contém a cadeia de ligação. Para obter mais informações sobre estas definições, consulte a [acionar a seção de configuração](#trigger---configuration). Aqui está um `EventHubTriggerAttribute` exemplo de atributo:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [acionador - exemplo do c#](#trigger---c-example).

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `EventHubTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `eventHubTrigger`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido como `in`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável que representa o item de evento no código de função. |
|**path** |**EventHubName** | Funciona apenas 1.x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de ligação, esse valor substitui essa propriedade em tempo de execução. |
|**eventHubName** |**EventHubName** | Funciona apenas 2.x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de ligação, esse valor substitui essa propriedade em tempo de execução. |
|**consumerGroup** |**ConsumerGroup** | Uma propriedade opcional que define a [grupo de consumidores](../articles/event-hubs/event-hubs-features.md)consumidores de eventos de #) utilizado para subscrever a eventos no hub. Se for omitido, o `$Default` for utilizado. |
|**Cardinalidade** | n/d | Para Javascript. Definido como `many` para ativar a criação de batches.  Se for omitido ou definido como `one`, única mensagem transmitido à função. |
|**ligação** |**ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação ao espaço de nomes do hub de eventos. Copiar esta cadeia de ligação ao clicar no **informações da ligação** botão para o [espaço de nomes](../articles/event-hubs/event-hubs-create.md)#create-um-event-hubs-espaço de nomes), não o hub de eventos em si. Esta cadeia de ligação tem de ter, pelo menos, permissões de leitura para ativar o acionador.|
|**path**|**EventHubName**|O nome do hub de eventos. Pode ser referenciada através das definições de aplicação `%eventHubName%`|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Acionador - metadados de eventos

O acionador de Hubs de eventos fornece vários [propriedades de metadados](../articles/azure-functions/./functions-bindings-expressions-patterns.md). Essas propriedades podem ser utilizadas como parte das expressões de associação nas outras associações, ou como parâmetros no seu código. Estas são as propriedades do [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata) classe.

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|O `PartitionContext` instância.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo de colocados em fila em UTC.|
|`Offset`|`string`|O deslocamento de dados em relação ao fluxo de partição do Hub de eventos. O desvio é um marcador ou um identificador para um evento dentro do fluxo de Hubs de eventos. O identificador é exclusivo dentro de uma partição da transmissão em fluxo dos Hubs de eventos.|
|`PartitionKey`|`string`|A partição para o evento que dados devem ser enviados.|
|`Properties`|`IDictionary<String,Object>`|As propriedades de utilizador dos dados do evento.|
|`SequenceNumber`|`Int64`|O número de sequência lógica do evento.|
|`SystemProperties`|`IDictionary<String,Object>`|As propriedades do sistema, incluindo os dados de eventos.|

Ver [exemplos de código](#trigger---example) que utilizam estas propriedades no início deste artigo.

## <a name="trigger---hostjson-properties"></a>Acionador - propriedades de Host. JSON

O [Host. JSON](../articles/azure-functions/functions-host-json.md#eventhub) ficheiro contém as definições que controlam o comportamento de Acionador de Hubs de eventos.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Saída

Utilize a enlace para gravar eventos para um fluxo de eventos de saída de Hubs de eventos. Tem de ter permissão de envio para um hub de eventos para escrever eventos no mesmo.

Certifique-se de que as referências do pacote necessários são cumpridos: As funções 1.x ou 2.x de funções

## <a name="output---example"></a>Saída - exemplo

Veja o exemplo de idioma específico:

* [C#](#output---c-example)
* [Script do c# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Saída - exemplo do c#

A exemplo a seguir mostra um [função c#](../articles/azure-functions/functions-dotnet-class-library.md) que escreve uma mensagem para um hub de eventos, com o valor de retorno do método como a saída:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Saída - exemplo de script do c#

O exemplo seguinte mostra um acionador do hub de eventos de enlace num *Function* ficheiro e uma [função de script do c#](../articles/azure-functions/functions-reference-csharp.md) que utiliza o enlace. A função escreve uma mensagem para um hub de eventos.

Os exemplos seguintes mostram a vinculação de dados de Hubs de eventos no *Function* ficheiro. O primeiro exemplo é para as funções 2.x, e a segunda é para as funções 1.x. 

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

Aqui está o script código c# que cria uma mensagem:

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

### <a name="output---f-example"></a>Saída - F# exemplo

O exemplo seguinte mostra um acionador do hub de eventos de enlace num *Function* ficheiro e uma [ F# função](../articles/azure-functions/functions-reference-fsharp.md) que utiliza o enlace. A função escreve uma mensagem para um hub de eventos.

Os exemplos seguintes mostram a vinculação de dados de Hubs de eventos no *Function* ficheiro. O primeiro exemplo é para as funções 2.x, e a segunda é para as funções 1.x. 

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

Aqui está o F# código:

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: ILogger) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.LogInformation(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Saída - exemplo de JavaScript

O exemplo seguinte mostra um acionador do hub de eventos de enlace num *Function* ficheiro e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que utiliza o enlace. A função escreve uma mensagem para um hub de eventos.

Os exemplos seguintes mostram a vinculação de dados de Hubs de eventos no *Function* ficheiro. O primeiro exemplo é para as funções 2.x, e a segunda é para as funções 1.x. 

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

Código de JavaScript a seguir que envia uma única mensagem:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

Código de JavaScript a seguir que envia várias mensagens:

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

### <a name="output---python-example"></a>Saída - exemplo de Python

O exemplo seguinte mostra um acionador do hub de eventos de enlace num *Function* ficheiro e uma [função Python](../articles/azure-functions/functions-reference-python.md) que utiliza o enlace. A função escreve uma mensagem para um hub de eventos.

Os exemplos seguintes mostram a vinculação de dados de Hubs de eventos no *Function* ficheiro.

```json
{
    "type": "eventHub",
    "name": "$return",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Código de Python a seguir que envia uma única mensagem:

```python
import datetime
import logging
import azure.functions as func

def main(timer: func.TimerRequest) -> str:
    timestamp = datetime.datetime.utcnow()
    logging.info('Message created at: %s', timestamp);   
    return 'Message created at: {}'.format(timestamp)
```

### <a name="output---java-example"></a>Saída - exemplo de Java

O exemplo seguinte mostra uma função de Java que escreve uma mensagem contianing a hora atual para um Hub de eventos.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `@EventHubOutput` anotação em parâmetros cujo valor seria publicado para o Hub de eventos.  O parâmetro deve ser do tipo `OutputBinding<T>` , em que T é um POJO ou qualquer tipo nativo do Java.

## <a name="output---attributes"></a>Saída - atributos

Para [bibliotecas de classes do c#](../articles/azure-functions/functions-dotnet-class-library.md), utilize o [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs) atributo.

Construtor do atributo utiliza o nome do hub de eventos e o nome de uma definição de aplicação que contém a cadeia de ligação. Para obter mais informações sobre estas definições, consulte [de saída - configuração](#output---configuration). Aqui está um `EventHub` exemplo de atributo:

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída - exemplo do c#](#output---c-example).

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `EventHub` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido para "eventHub". |
|**direção** | n/d | Tem de ser definido para "Sair". Este parâmetro é definido automaticamente quando criar o enlace no portal do Azure. |
|**name** | n/d | O nome da variável no código de função que representa o evento. |
|**path** |**EventHubName** | Funciona apenas 1.x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de ligação, esse valor substitui essa propriedade em tempo de execução. |
|**eventHubName** |**EventHubName** | Funciona apenas 2.x. O nome do hub de eventos. Quando o nome do hub de eventos também está presente na cadeia de ligação, esse valor substitui essa propriedade em tempo de execução. |
|**ligação** |**ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação ao espaço de nomes do hub de eventos. Copiar esta cadeia de ligação ao clicar no **informações da ligação** botão para o *espaço de nomes*, não o hub de eventos em si. Esta cadeia de ligação tem de ter permissões de envio para enviar a mensagem para o fluxo de eventos.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

No c# e de script c#, enviar mensagens com um parâmetro de método como `out string paramName`. No script c# `paramName` é o valor especificado no `name` propriedade da *Function*. Para escrever várias mensagens, pode usar `ICollector<string>` ou `IAsyncCollector<string>` em vez de `out string`.

No JavaScript, acessar o evento de saída usando `context.bindings.<name>`. `<name>` é o valor especificado no `name` propriedade do *Function*.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Hub de Eventos | [Guia de operações](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta secção descreve as definições de configuração global disponíveis para essa ligação na versão 2.x. O ficheiro de Host. JSON de exemplo abaixo contém apenas as versão 2.x as definições para este enlace. Para obter mais informações sobre as definições de configuração global na versão 2.x, consulte [referência de Host. JSON para a versão das funções do Azure 2.x](../articles/azure-functions/functions-host-json.md).

> [!NOTE]
> Para obter uma referência de Host. JSON nas funções 1.x, consulte [referência de Host. JSON para as funções do Azure 1.x](../articles/azure-functions/functions-host-json-v1.md).

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
|maxBatchSize|64|A contagem máxima do evento recebida por loop de recebimento.|
|prefetchCount|n/d|A predefinição PrefetchCount que será utilizada pelo EventProcessorHost subjacente.|
|batchCheckpointFrequency|1|O número de lotes de eventos para processar antes de criar um ponto de verificação de cursor de EventHub.|