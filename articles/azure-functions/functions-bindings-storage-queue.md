---
title: Encadernações de armazenamento de fila azure para funções azure
description: Compreenda como utilizar o gatilho de armazenamento de fila Azure e a ligação de saída em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 33bdf718e74011dbd7adedd766ebc90923fffb83
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189844"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Encadernações de armazenamento de fila azure para funções azure

Este artigo explica como trabalhar com encadernações de armazenamento de fila Azure em Funções Azure. As Funções Azure suportam encadernações de gatilho e saída para filas.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As encadernações de armazenamento de fila são fornecidas no pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) Nuget, versão 2.x. O código fonte para o pacote está no repositório [Azur-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

As encadernações de armazenamento de fila são fornecidas no [pacote Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) Nuget, versão 3.x. O código fonte para o pacote está no repositório [Azur-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Codificação
As funções esperam uma cadeia codificada *base64.* Quaisquer ajustes ao tipo de codificação (a fim de preparar dados como uma cadeia codificada *base64)* devem ser implementados no serviço de chamada.

## <a name="trigger"></a>Acionador

Utilize o gatilho da fila para iniciar uma função quando um novo item for recebido numa fila. A mensagem de fila é fornecida como entrada para a função.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que faz sondagens à fila `myqueue-items` e escreve um registo cada vez que um item de fila é processado.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho da fila num ficheiro *function.json* e [ C# código script (.csx)](functions-reference-csharp.md) que utiliza a ligação. A função faz a `myqueue-items` fila e escreve um registo cada vez que um item de fila é processado.

Aqui está o ficheiro *função.json:*

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A secção de [configuração](#trigger---configuration) explica estas propriedades.

Aqui está o código de script do c#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

A secção de [utilização](#trigger---usage) explica `myQueueItem`, que é nomeada pela propriedade `name` em função.json.  A secção de metadados de [mensagens](#trigger---message-metadata) explica todas as outras variáveis mostradas.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação do gatilho da fila num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função faz a `myqueue-items` fila e escreve um registo cada vez que um item de fila é processado.

Aqui está o ficheiro *função.json:*

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

A secção de [configuração](#trigger---configuration) explica estas propriedades.

> [!NOTE]
> O parâmetro de nome reflete-se como `context.bindings.<name>` no código JavaScript que contém a carga útil do item da fila. Esta carga útil também é passada como o segundo parâmetro da função.

Eis o código JavaScript:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

A secção de [utilização](#trigger---usage) explica `myQueueItem`, que é nomeada pela propriedade `name` em função.json.  A secção de metadados de [mensagens](#trigger---message-metadata) explica todas as outras variáveis mostradas.

# <a name="pythontabpython"></a>[python](#tab/python)

O exemplo seguinte demonstra como ler uma mensagem de fila passada para uma função através de um gatilho.

Um gatilho de fila de armazenamento é definido na *função.json* onde o *tipo* está definido para `queueTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

O código  *_\_inite_\_.py* declara um parâmetro como `func.ServiceBusMessage`, o que lhe permite ler a mensagem de fila na sua função.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo de Java que se segue mostra uma função de gatilho de fila de armazenamento, que regista a mensagem desencadeada colocada na fila `myqueuename`.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

 ---

## <a name="trigger---attributes-and-annotations"></a>Gatilho-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar um gatilho de fila:

* [Atributo de Gatilho de Fila](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  O construtor do atributo toma o nome da fila para monitorizar, como mostra o seguinte exemplo:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Pode definir a propriedade `Connection` para especificar a definição da aplicação que contém a cadeia de ligação à conta de armazenamento a utilizar, como mostra o seguinte exemplo:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Para um exemplo completo, consulte [O Gatilho - C# exemplo](#trigger).

* [ArmazenamentoAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra forma de especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo seguinte mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a utilizar é determinada na seguinte ordem:

* A propriedade `Connection` do `QueueTrigger` atributo.
* O atributo `StorageAccount` aplicado ao mesmo parâmetro que o atributo `QueueTrigger`.
* O atributo `StorageAccount` aplicado à função.
* O atributo `StorageAccount` aplicado à classe.
* A definição da aplicação "AzureWebJobsStorage".

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos no script Java.

# <a name="pythontabpython"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação `QueueTrigger` dá-lhe acesso à fila que desencadeia a função. O exemplo seguinte disponibiliza a mensagem de fila para a função através do parâmetro `message`.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Propriedade    | Descrição |
|-------------|-----------------------------|
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é disparada, o valor desse parâmetro tem o conteúdo da mensagem da fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para a cadeia de conexão da conta de armazenamento. |

---

## <a name="trigger---configuration"></a>Acionador - configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `QueueTrigger`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d| deve ser definido para `queueTrigger`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção**| n/d | Apenas no ficheiro *função.json.* deve ser definido para `in`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**nome** | n/d |O nome da variável que contém a carga útil do item da fila no código de função.  |
|**queueName** | **Nome da fila**| O nome da fila para a sondagem. |
|**conexão** | **Conexão** |O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aceda aos dados da mensagem utilizando um parâmetro de método como `string paramName`. Pode ligar-se a qualquer um dos seguintes tipos:

* Objeto - As funções desserializa uma carga útil JSON numa instância de uma classe arbitrária definida no seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se tentar ligar-se a `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Aceda aos dados da mensagem utilizando um parâmetro de método como `string paramName`. O `paramName` é o valor especificado na propriedade `name` *função.json*. Pode ligar-se a qualquer um dos seguintes tipos:

* Objeto - As funções desserializa uma carga útil JSON numa instância de uma classe arbitrária definida no seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se tentar ligar-se a `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A carga útil do item da fila está disponível através de `context.bindings.<NAME>` onde `<NAME>` corresponde ao nome definido na *função.json*. Se a carga for JSON, o valor será desserializado em um objeto.

# <a name="pythontabpython"></a>[python](#tab/python)

Aceda à mensagem de fila através do parâmetro digitado como [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) dá-lhe acesso à mensagem de fila que desencadeou a função.

---

## <a name="trigger---message-metadata"></a>Acionador - metadados de mensagem

O gatilho da fila fornece várias [propriedades de metadados.](./functions-bindings-expressions-patterns.md#trigger-metadata) Essas propriedades podem ser utilizadas como parte das expressões de associação nas outras associações, ou como parâmetros no seu código. Estas são propriedades da classe [CloudQueueMessage.](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`QueueTrigger`|`string`|Carga útil da fila (se for uma corda válida). Se a carga de mensagem de fila for uma corda, `QueueTrigger` tem o mesmo valor que a variável denominada pela propriedade `name` em *função.json*.|
|`DequeueCount`|`int`|O número de vezes que esta mensagem foi desmarcada.|
|`ExpirationTime`|`DateTimeOffset`|O tempo que a mensagem expirar.|
|`Id`|`string`|Id de mensagem de fila.|
|`InsertionTime`|`DateTimeOffset`|O tempo que a mensagem foi adicionada à fila.|
|`NextVisibleTime`|`DateTimeOffset`|O tempo que a mensagem será a próxima será visível.|
|`PopReceipt`|`string`|O recibo da mensagem.|

## <a name="trigger---poison-messages"></a>Acionador - mensagens suspeitas

Quando uma função de gatilho de fila falha, as Funções Azure retestam a função até cinco vezes para uma determinada mensagem de fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, as funções de tempo de funcionamento adicionam uma mensagem a uma fila chamada *&lt;originalqueuename>-poison*. Pode escrever uma função para processar mensagens da fila de veneno, registando-as ou enviando uma notificação de que é necessária atenção manual.

Para manusear mensagens venenosas manualmente, verifique o [desfilar Contagem](#trigger---message-metadata) da mensagem de fila.

## <a name="trigger---polling-algorithm"></a>Trigger - algoritmo de sondagem

O gatilho da fila implementa um algoritmo de back-off exponencial aleatório para reduzir o efeito das sondagens de fila inativa nos custos de transação de armazenamento.

O algoritmo usa a seguinte lógica:

- Quando uma mensagem é encontrada, o tempo de execução espera dois segundos e, em seguida, verifica por outra mensagem
- Quando nenhuma mensagem é encontrada, espera cerca de quatro segundos antes de tentar novamente.
- Após tentativas falhadas subsequentes de obter uma mensagem de fila, o tempo de espera continua a aumentar até atingir o tempo máximo de espera, o que se desfaz para um minuto.
- O tempo máximo de espera é configurável através da propriedade `maxPollingInterval` no [ficheiro host.json](functions-host-json.md#queues).

Para o desenvolvimento local, o intervalo máximo de sondagens não se aplica a dois segundos.

No que diz respeito à faturação, o tempo gasto nas sondagens até ao fim é "gratuito" e não contado contra a sua conta.

## <a name="trigger---concurrency"></a>Gatilho - conmoeda

Quando há várias mensagens de fila à espera, o gatilho da fila recupera um lote de mensagens e invoca casos de funções simultaneamente para processá-las. Por padrão, o tamanho do lote é de 16. Quando o número que está a ser processado chega a 8, o tempo de execução recebe outro lote e começa a processar essas mensagens. Assim, o número máximo de mensagens simultâneas que estão a ser processadas por função numa máquina virtual (VM) é de 24. Este limite aplica-se separadamente a cada função ativada pela fila em cada VM. Se a sua aplicação de função se dimensionar para vários VMs, cada VM aguardará por gatilhos e tentará executar funções. Por exemplo, se uma aplicação de função se dimensionar para 3 VMs, o número máximo padrão de instâncias simultâneas de uma função desencadeada pela fila é de 72.

O tamanho do lote e o limiar para obter um novo lote são configuráveis no [ficheiro host.json](functions-host-json.md#queues). Se pretender minimizar a execução paralela para funções ativadas pela fila numa aplicação de função, pode definir o tamanho do lote para 1. Esta definição elimina a moeda apenas enquanto a sua aplicação de função funcionar numa única máquina virtual (VM). 

O gatilho da fila impede automaticamente uma função de processar uma mensagem de fila várias vezes; funções não têm que ser escritas para ser idempotente.

## <a name="trigger---hostjson-properties"></a>Acionador - propriedades de Host. JSON

O ficheiro [host.json](functions-host-json.md#queues) contém definições que controlam o comportamento do gatilho da fila. Consulte a secção de [definições host.json](#hostjson-settings) para obter mais detalhes sobre as definições disponíveis.

## <a name="output"></a>Saída

Utilize a ligação de saída de armazenamento da fila Azure para escrever mensagens para uma fila.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que cria uma mensagem de fila para cada pedido http recebido.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

O exemplo seguinte mostra uma ligação do gatilho HTTP num ficheiro *function.json* e [ C# código script (.csx)](functions-reference-csharp.md) que utiliza a ligação. A função cria um item de fila com uma carga de objeto **CustomQueueMessage** para cada pedido http recebido.

Aqui está o ficheiro *função.json:*

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A secção de [configuração](#output---configuration) explica estas propriedades.

Aqui está C# o código do script que cria uma única mensagem de fila:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Pode enviar várias mensagens ao mesmo tempo utilizando um `ICollector` ou `IAsyncCollector` parâmetro. Aqui está C# o código script que envia várias mensagens, uma com os dados de pedido http e outra com valores codificados:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação de gatilho HTTP num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função cria um item de fila para cada pedido http recebido.

Aqui está o ficheiro *função.json:*

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

A secção de [configuração](#output---configuration) explica estas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Pode enviar várias mensagens ao mesmo tempo, definindo um conjunto de mensagens para a ligação de saída `myQueueItem`. O seguinte código JavaScript envia duas mensagens de fila com valores codificados para cada pedido http recebido.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="pythontabpython"></a>[python](#tab/python)

O exemplo seguinte demonstra como obter valores únicos e múltiplos para as filas de armazenamento. A configuração necessária para *função.json* é a mesma de qualquer forma.

Uma ligação de fila de armazenamento é definida na *função.json* onde o *tipo* está definido para `queue`.

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Para definir uma mensagem individual na fila, passa um único valor para o método `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Para criar várias mensagens na fila, declare um parâmetro como o tipo de lista apropriado e passe uma série de valores (que correspondam ao tipo de lista) ao método `set`.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

# <a name="javatabjava"></a>[Java](#tab/java)

 O exemplo a seguir mostra uma função Java que cria uma mensagem de fila para quando disparada por uma solicitação HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

Na biblioteca de tempo de funcionamento das [funções java,](/java/api/overview/azure/functions/runtime)utilize a anotação `@QueueOutput` em parâmetros cujo valor seria escrito para armazenamento de fila.  O tipo de parâmetro deve ser `OutputBinding<T>`, onde `T` é qualquer tipo de Java nativo de um POJO.

---

## <a name="output---attributes-and-annotations"></a>Saída-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Nas [ C# bibliotecas de classes,](functions-dotnet-class-library.md)utilize o [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

O atributo aplica-se a um parâmetro `out` ou ao valor de retorno da função. O construtor do atributo tem o nome da fila, como mostra o seguinte exemplo:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Pode definir a propriedade `Connection` para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Para um exemplo completo, consulte [A Saída - C# exemplo](#output).

Pode utilizar o atributo `StorageAccount` para especificar a conta de armazenamento na classe, método ou nível de parâmetro. Para mais informações, consulte Trigger - atributos.

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Os atributos C# não são suportados pelo Script.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos no script Java.

# <a name="pythontabpython"></a>[python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação `QueueOutput` permite-lhe aceder a escrever uma mensagem uma saída de uma função. O exemplo a seguir mostra uma função disparada por HTTP que cria uma mensagem de fila.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Propriedade    | Descrição |
|-------------|-----------------------------|
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é disparada, o valor desse parâmetro tem o conteúdo da mensagem da fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para a cadeia de conexão da conta de armazenamento. |

O parâmetro associado à anotação `QueueOutput` é dactilografado como um\<[T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) exemplo.

---

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no atributo `Queue`.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | deve ser definido para `queue`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | deve ser definido para `out`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**nome** | n/d | O nome da variável que representa a fila no código de função. Definir para `$return` para referenciar o valor de devolução da função.|
|**queueName** |**Nome da fila** | O nome da fila. |
|**conexão** | **Conexão** |O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Escreva uma única mensagem de fila utilizando um parâmetro de método como `out T paramName`. Pode utilizar o tipo de devolução do método em vez de um parâmetro `out`, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se tentar ligar-se a `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

Dentro C# C# e script, escreva várias mensagens de fila utilizando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-scripttabcsharp-script"></a>[C#Roteiro](#tab/csharp-script)

Escreva uma única mensagem de fila utilizando um parâmetro de método como `out T paramName`. O `paramName` é o valor especificado na propriedade `name` *função.json*. Pode utilizar o tipo de devolução do método em vez de um parâmetro `out`, e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se tentar ligar-se a `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

Dentro C# C# e script, escreva várias mensagens de fila utilizando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O item da fila de saída está disponível através `context.bindings.<NAME>` onde `<NAME>` corresponde ao nome definido na *função.json*. Pode utilizar uma corda ou um objeto json-serializável para a carga útil do item da fila.

# <a name="pythontabpython"></a>[python](#tab/python)

Há duas opções para a saída de uma mensagem do hub de eventos de uma função:

- **Valor de devolução**: Coloque a propriedade `name` em *função.json* para `$return`. Com essa configuração, o valor de retorno da função é persistido como uma mensagem de armazenamento de fila.

- **Imperativo**: Passe um valor para o método [definido](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) O valor passado para `set` é persistiu como uma mensagem de armazenamento de fila.

# <a name="javatabjava"></a>[Java](#tab/java)

Existem duas opções para eliminar uma mensagem do Event Hub a partir de uma função, utilizando a anotação [QueueOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput)

- **Valor de devolução**: Aplicando a anotação à própria função, o valor de retorno da função é persinedo como mensagem Do Centro de Eventos.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`, ](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)onde `T` seja um POJO ou qualquer tipo de Java nativo. Com esta configuração, passar um valor para o método `setValue` persiste o valor como mensagem Do Hub do Evento.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo |  Referência |
|---|---|
| Filas | [Códigos de erro de fila](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Blob, Mesa, Fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Mesa, Fila |  [Resolução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta secção descreve as definições de configuração global disponíveis para esta ligação nas versões 2.x e superiores. O ficheiro exemplo host.json abaixo contém apenas as definições da versão 2.x+ para esta ligação. Para obter mais informações sobre as configurações globais de configuração nas versões 2.x e além, consulte a [referência host.json para funções azure](functions-host-json.md).

> [!NOTE]
> Para uma referência do host.json nas funções 1.x, consulte a [referência host.json para as funções Azure 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```

|Propriedade  |Predefinição | Descrição |
|---------|---------|---------|
|maxPollingInterval|00:00:01|O intervalo máximo entre as sondagens. O mínimo é 00:00:100 (100 ms) e incrementos até 00:01:00 (1 min).  Em 1.x o tipo de dados é milissegundos, e em 2.x e superior é um TimeSpan.|
|visibilidadeTimeout|00:00:00|O intervalo de tempo entre as tentativas de repetição ao processamento de uma mensagem falha. |
|batchSize|16|O número de mensagens de fila que as Funções executam o tempo de funcionamento recupera simultaneamente e processa em paralelo. Quando o número que está a ser processado chega ao `newBatchThreshold`, o tempo de execução recebe outro lote e começa a processar essas mensagens. Assim, o número máximo de mensagens simultâneas que são processadas por função é `batchSize` mais `newBatchThreshold`. Este limite aplica-se separadamente a cada função acionada pela fila. <br><br>Se quiser evitar a execução paralela de mensagens recebidas numa fila, pode definir `batchSize` para 1. No entanto, esta definição elimina a conmoeda apenas enquanto a sua aplicação de função funcionar numa única máquina virtual (VM). Se a aplicação de função se dimensionar para vários VMs, cada VM pode executar uma instância de cada função desencadeada pela fila.<br><br>O máximo `batchSize` é 32. |
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila do veneno.|
|newBatchThreshold|loteTamanho/2|Sempre que o número de mensagens que estão a ser processadas simultaneamente chega a este número, o tempo de execução recupera outro lote.|

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Vá a um tutorial que usa uma ligação de saída de armazenamento de fila](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
