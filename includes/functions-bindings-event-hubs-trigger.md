---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: e055f2d7b98df9357ecdee5e044305e35935682e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81791701"
---
Utilize o gatilho de função para responder a um evento enviado para um fluxo de eventos do centro de eventos. Deve ter lido o acesso ao centro de eventos subjacente para configurar o gatilho. Quando a função é ativada, a mensagem transmitida para a função é dactilografada como uma corda.

## <a name="scaling"></a>Dimensionamento

Cada instância de uma função desencadeada por eventos é apoiada por uma única instância do [EventProcessorHost.](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) O gatilho (alimentado por Event Hubs) garante que apenas uma instância do [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) pode obter um arrendamento numa determinada partição.

Por exemplo, considere um Hub de Eventos da seguinte forma:

* 10 divisórias
* 1.000 eventos distribuídos uniformemente por todas as divisórias, com 100 mensagens em cada partição

Quando a sua função está ativada pela primeira vez, há apenas um exemplo da função. Vamos chamar a primeira `Function_0`função de exemplo. A `Function_0` função tem uma única instância de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) que detém um contrato de arrendamento em todas as dez divisórias. Este caso é ler eventos de divisórias 0-9. A partir de agora, um dos seguintes acontece:

* **Não são necessárias novas instâncias**de função : `Function_0` é capaz de processar todos os 1.000 eventos antes que a lógica de escalação das Funções entre em vigor. Neste caso, todas as 1.000 `Function_0`mensagens são processadas por .

* **Uma instância de função adicional é adicionada:** Se `Function_0` a lógica de escalagem de Funções`Function_1`determinar que tem mais mensagens do que pode processar, é criada uma nova instância de aplicação de função ( ). Esta nova função também tem uma instância associada de [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). À medida que os Centros de Eventos subjacentes detetam que uma nova instância de anfitrião está a tentar ler mensagens, carrega os equilíbrios das divisórias através das instâncias anfitriãs. Por exemplo, as divisórias 0-4 podem ser atribuídas `Function_0` e divisórias 5-9 a `Function_1`.

* **N mais instâncias de função são adicionadas**: Se `Function_0` `Function_1` a lógica de escalagem `Functions_N` de Funções determinar que ambos e têm mais mensagens do que podem processar, novas instâncias de aplicações de função são criadas.  As aplicações são `N` criadas ao ponto de ser maior do que o número de divisórias do centro de eventos. No nosso exemplo, o Event Hubs volta a carregar equilibra `Function_0`as divisórias, neste caso em todos os casos... `Functions_9`.

À medida que a `N` escala ocorre, os casos são um número maior do que o número de divisórias do centro de eventos. Este padrão é usado para garantir que os casos do [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) estão disponíveis para obter fechaduras em divisórias à medida que ficam disponíveis de outras instâncias. Só é cobrado pelos recursos utilizados quando a instância de função executa. Por outras palavras, não lhe é cobrado este excesso de provisionamento.

Quando toda a execução da função completa (com ou sem erros), os pontos de verificação são adicionados à conta de armazenamento associada. Quando a verificação de verificação for bem sucedida, todas as 1.000 mensagens nunca mais são recuperadas.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte mostra uma [função C#](../articles/azure-functions/functions-dotnet-class-library.md) que regista o corpo da mensagem do gatilho do centro do evento.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    log.LogInformation($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para obter acesso aos metadados de [eventos](#event-metadata) no código de função, ligue-se a um objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer uma declaração de utilização `Microsoft.Azure.EventHubs`para ). Também pode aceder às mesmas propriedades utilizando expressões vinculativas na assinatura do método.  O exemplo que se segue mostra ambas as formas de obter os mesmos dados:

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

Para receber eventos em `string` `EventData` lote, faça ou uma matriz.  

> [!NOTE]
> Ao receber um lote não pode ligar-se a parâmetros métodos como no exemplo acima e `DateTime enqueuedTimeUtc` deve recebê-los de cada `EventData` objeto  

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

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função de script C#](../articles/azure-functions/functions-reference-csharp.md) que utiliza a ligação. A função regista o corpo da mensagem do gatilho do centro do evento.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.*

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

Aqui está o código de script C#:

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# function triggered to process a message: {myEventHubMessage}");
}
```

Para obter acesso aos metadados de [eventos](#event-metadata) no código de função, ligue-se a um objeto [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (requer uma declaração de utilização `Microsoft.Azure.EventHubs`para ). Também pode aceder às mesmas propriedades utilizando expressões vinculativas na assinatura do método.  O exemplo que se segue mostra ambas as formas de obter os mesmos dados:

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

Para receber eventos num `string` `EventData` lote, fazer ou numa matriz:

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

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função JavaScript](../articles/azure-functions/functions-reference-node.md) que utiliza a ligação. A função lê metadados de [eventos](#event-metadata) e regista a mensagem.

Os exemplos seguintes mostram dados de ligação do Event Hubs no ficheiro *function.json.*

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

Para receber eventos num `cardinality` `many` lote, definido no ficheiro *função.json,* como mostram os seguintes exemplos.

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

O exemplo seguinte mostra uma ligação do gatilho do centro de eventos num ficheiro *function.json* e uma [função Python](../articles/azure-functions/functions-reference-python.md) que utiliza a ligação. A função lê metadados de [eventos](#event-metadata) e regista a mensagem.

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

Aqui está o código Python:

```python
import logging
import azure.functions as func


def main(event: func.EventHubEvent):
    logging.info('Function triggered to process a message: ', event.get_body())
    logging.info('  EnqueuedTimeUtc =', event.enqueued_time)
    logging.info('  SequenceNumber =', event.sequence_number)
    logging.info('  Offset =', event.offset)
```

# <a name="java"></a>[Java](#tab/java)

O exemplo seguinte mostra uma ligação do gatilho do Event Hub que regista o corpo de mensagem do gatilho do Centro de Eventos.

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

 Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)utilize a `EventHubTrigger` anotação em parâmetros cujo valor viria do Event Hub. Os parâmetros com estas anotações fazem com que a função funcione quando um evento chega.  Esta anotação pode ser usada com tipos nativos de `Optional<T>`Java, POJOs ou valores nuníveis usando .

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](../articles/azure-functions/functions-dotnet-class-library.md)utilize o atributo [EventHubTriggerAttribute.](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubTriggerAttribute.cs)

O construtor do atributo tem o nome do centro do evento, o nome do grupo de consumidores e o nome de uma definição de aplicação que contém a cadeia de ligação. Para obter mais informações sobre estas definições, consulte a secção de [configuração](#configuration)do gatilho . Aqui está `EventHubTriggerAttribute` um exemplo de atributo:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [o exemplo Trigger - C#](#example).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

A partir da biblioteca de [tempo de funcionamento](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)das funções java, use a anotação [EventHubTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhubtrigger) em parâmetros cujo valor viria do Event Hub. Os parâmetros com estas anotações fazem com que a função funcione quando um evento chega. Esta anotação pode ser usada com tipos nativos de `Optional<T>`Java, POJOs ou valores nuníveis usando .

---

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `EventHubTrigger` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de `eventHubTrigger`ser definido para. Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção** | n/d | Tem de `in`ser definido para. Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/d | O nome da variável que representa o item do evento no código de função. |
|**caminho** |**Nome EventHub** | Funciona apenas 1.x. O nome do centro de eventos. Quando o nome do centro do evento também está presente na cadeia de ligação, esse valor sobrepõe-se a esta propriedade em tempo de execução. |
|**eventHubName** |**Nome EventHub** | Funções 2.x e superiores. O nome do centro de eventos. Quando o nome do centro do evento também está presente na cadeia de ligação, esse valor sobrepõe-se a esta propriedade em tempo de execução. Pode ser referenciado através de configurações de aplicativo %eventHubName% |
|**consumerGroup** |**Grupo de Consumidores** | Uma propriedade opcional que define o grupo de [consumidores](../articles/event-hubs/event-hubs-features.md#event-consumers) usado para subscrever eventos no centro. Se omitir, o grupo de `$Default` consumidores é utilizado. |
|**cardinalidade** | n/d | Para JavaScript. `many` Demodo a permitir o loteamento.  Se omitida ou definida `one`para, uma única mensagem é passada para a função. |
|**conexão** |**Conexão** | O nome de uma definição de aplicação que contém a cadeia de ligação ao espaço de nome do centro de eventos. Copie esta cadeia de ligação clicando no botão Informação de **Ligação** para o espaço de [nome,](../articles/event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)e não para o próprio centro de eventos. Esta cadeia de ligação deve ter pelo menos permissões para ativar o gatilho.|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="event-metadata"></a>Metadados de eventos

O gatilho do Event Hubs fornece várias [propriedades de metadados.](../articles/azure-functions/./functions-bindings-expressions-patterns.md) As propriedades dos metadados podem ser usadas como parte de expressões de ligação noutras encadernações ou como parâmetros no seu código. As propriedades vêm da classe [EventData.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata)

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`PartitionContext`|[Contexto de partição](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|A `PartitionContext` instância.|
|`EnqueuedTimeUtc`|`DateTime`|O tempo enqueuado na UTC.|
|`Offset`|`string`|A compensação dos dados relativos ao fluxo de partição do Event Hub. O offset é um marcador ou identificador para um evento dentro da corrente de Hubs de Eventos. O identificador é único dentro de uma partição do fluxo de Hubs de Eventos.|
|`PartitionKey`|`string`|A partição para que os dados do evento devem ser enviados.|
|`Properties`|`IDictionary<String,Object>`|As propriedades dos utilizadores dos dados do evento.|
|`SequenceNumber`|`Int64`|O número de sequência lógica do evento.|
|`SystemProperties`|`IDictionary<String,Object>`|As propriedades do sistema, incluindo os dados do evento.|

Consulte [exemplos](#example) de código que usam estas propriedades no início deste artigo.

## <a name="hostjson-properties"></a>propriedades host.json
<a name="host-json"></a>

O ficheiro [host.json](../articles/azure-functions/functions-host-json.md#eventhub) contém configurações que controlam o comportamento do gatilho do Event Hubs. A configuração é diferente dependendo da versão Funções Azure.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]