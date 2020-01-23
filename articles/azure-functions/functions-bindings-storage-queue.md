---
title: Associações de armazenamento de filas do Azure para Azure Functions
description: Entenda como usar o gatilho do armazenamento de filas do Azure e a associação de saída no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: ea213921c736bc3b6bf88c0bdd81a96656ecbe5b
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547290"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Associações de armazenamento de filas do Azure para Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de filas do Azure no Azure Functions. O Azure Functions dá suporte a associações de gatilho e saída para filas.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As associações de armazenamento de fila são fornecidas no pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) repositório do GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

As associações de armazenamento de fila são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , versão 3. x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Encoding
As funções esperam uma cadeia de caracteres codificada em *Base64* . Todos os ajustes no tipo de codificação (para preparar dados como uma cadeia de caracteres codificada em *Base64* ) precisam ser implementados no serviço de chamada.

## <a name="trigger"></a>Acionador

Use o gatilho de fila para iniciar uma função quando um novo item for recebido em uma fila. A mensagem da fila é apresentada como uma entrada da função.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que sonda a fila de `myqueue-items` e grava um log cada vez que um item de fila é processado.

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho de fila em um arquivo *Function. JSON* e [ C# um código de script (. CSX)](functions-reference-csharp.md) que usa a associação. A função sonda a fila de `myqueue-items` e grava um log cada vez que um item de fila é processado.

Aqui está o *Function* ficheiro:

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

O [configuração](#trigger---configuration) seção explica essas propriedades.

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

A seção [uso](#trigger---usage) explica `myQueueItem`, que é nomeada pela propriedade `name` em function. JSON.  A [seção de metadados de mensagem](#trigger---message-metadata) explica todas as outras variáveis mostradas.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de gatilho de fila em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função sonda a fila de `myqueue-items` e grava um log cada vez que um item de fila é processado.

Aqui está o *Function* ficheiro:

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

O [configuração](#trigger---configuration) seção explica essas propriedades.

> [!NOTE]
> O parâmetro Name reflete como `context.bindings.<name>` no código JavaScript que contém a carga do item de fila. Essa carga também é passada como o segundo parâmetro para a função.

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

A seção [uso](#trigger---usage) explica `myQueueItem`, que é nomeada pela propriedade `name` em function. JSON.  A [seção de metadados de mensagem](#trigger---message-metadata) explica todas as outras variáveis mostradas.

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem de fila passada para uma função por meio de um gatilho.

Um gatilho de fila de armazenamento é definido em *Function. JSON* , em que *Type* é definido como `queueTrigger`.

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

O código  *_\_init_\_. py* declara um parâmetro como `func.ServiceBusMessage`, o que permite que você leia a mensagem da fila em sua função.

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

O exemplo de Java a seguir mostra uma função de gatilho de fila de armazenamento, que registra a mensagem disparada colocada na fila `myqueuename`.

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

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use os seguintes atributos para configurar um gatilho de fila:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  O construtor do atributo usa o nome da fila a ser monitorada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Você pode definir a propriedade `Connection` para especificar a configuração do aplicativo que contém a cadeia de conexão da conta de armazenamento a ser usada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Para obter um exemplo completo, consulte [acionador - exemplo do c#](#trigger).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra maneira de especificar a conta de armazenamento a ser usada. O construtor usa o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo seguinte mostra o nível de classe e método:

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

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* O `QueueTrigger` do atributo `Connection` propriedade.
* O `StorageAccount` atributo aplicado para o mesmo parâmetro como o `QueueTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` aplicado à classe de atributo.
* A configuração do aplicativo "AzureWebJobsStorage".

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos no script Java.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação `QueueTrigger` fornece acesso à fila que dispara a função. O exemplo a seguir torna a mensagem da fila disponível para a função por meio do parâmetro `message`.

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

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `QueueTrigger` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d| Tem de ser definido como `queueTrigger`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção**| n/d | Somente no arquivo *Function. JSON* . Tem de ser definido como `in`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**name** | n/d |O nome da variável que contém a carga do item de fila no código da função.  |
|**queueName** | **QueueName**| O nome da fila para sondagem. |
|**ligação** | **ligação** |O nome de uma configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada para essa associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome aqui. Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "mystorage". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão de armazenamento padrão na configuração do aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Acionador - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Acesse os dados da mensagem usando um parâmetro de método, como `string paramName`. Você pode associar a qualquer um dos seguintes tipos:

* Objeto – o tempo de execução do Functions desserializa uma carga JSON em uma instância de uma classe arbitrária definida em seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se você tentar associar a `CloudQueueMessage` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Acesse os dados da mensagem usando um parâmetro de método, como `string paramName`. O `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. Você pode associar a qualquer um dos seguintes tipos:

* Objeto – o tempo de execução do Functions desserializa uma carga JSON em uma instância de uma classe arbitrária definida em seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se você tentar associar a `CloudQueueMessage` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A carga do item de fila está disponível por meio de `context.bindings.<NAME>` onde `<NAME>` corresponde ao nome definido em *Function. JSON*. Se a carga for JSON, o valor será desserializado em um objeto.

# <a name="pythontabpython"></a>[Python](#tab/python)

Acesse a mensagem da fila por meio do parâmetro digitado como [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) fornece acesso à mensagem da fila que disparou a função.

---

## <a name="trigger---message-metadata"></a>Acionador - metadados de mensagem

O gatilho de fila fornece várias [Propriedades de metadados](./functions-bindings-expressions-patterns.md#trigger-metadata). Essas propriedades podem ser utilizadas como parte das expressões de associação nas outras associações, ou como parâmetros no seu código. Essas são propriedades da classe [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`QueueTrigger`|`string`|Carga da fila (se for uma cadeia de caracteres válida). Se a carga da mensagem da fila for uma cadeia de caracteres, `QueueTrigger` terá o mesmo valor que a variável nomeada pela propriedade `name` em *Function. JSON*.|
|`DequeueCount`|`int`|O número de vezes que esta mensagem foi removida da fila.|
|`ExpirationTime`|`DateTimeOffset`|A hora em que a mensagem expira.|
|`Id`|`string`|ID da mensagem da fila.|
|`InsertionTime`|`DateTimeOffset`|A hora em que a mensagem foi adicionada à fila.|
|`NextVisibleTime`|`DateTimeOffset`|A hora em que a mensagem ficará visível.|
|`PopReceipt`|`string`|O recebimento pop da mensagem.|

## <a name="trigger---poison-messages"></a>Acionador - mensagens suspeitas

Quando uma função de gatilho de fila falha, o Azure Functions tenta novamente a função até cinco vezes para uma determinada mensagem da fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, o tempo de execução do Functions adicionará uma mensagem a uma fila chamada *&lt;originalqueuename >-suspeita*. Você pode escrever uma função para processar mensagens da fila de suspeitas, registrando-as ou enviando uma notificação de que a atenção manual é necessária.

Para manipular mensagens suspeitas manualmente, verifique o [dequeueCount](#trigger---message-metadata) da mensagem da fila.

## <a name="trigger---polling-algorithm"></a>Gatilho-algoritmo de sondagem

O gatilho de fila implementa um algoritmo de retirada exponencial aleatória para reduzir o efeito da sondagem de fila ociosa nos custos de transação de armazenamento.

O algoritmo usa a seguinte lógica:

- Quando uma mensagem é encontrada, o tempo de execução aguarda dois segundos e, em seguida, verifica se há outra mensagem
- Quando nenhuma mensagem é encontrada, ela aguarda cerca de quatro segundos antes de tentar novamente.
- Após as tentativas subsequentes falharem em obter uma mensagem da fila, o tempo de espera continuará aumentando até atingir o tempo de espera máximo, cujo padrão é um minuto.
- O tempo de espera máximo é configurável por meio da propriedade `maxPollingInterval` no [arquivo host. JSON](functions-host-json.md#queues).

Para o desenvolvimento local, o intervalo máximo de sondagem tem como padrão dois segundos.

Em relação à cobrança, o tempo gasto na sondagem pelo tempo de execução é "gratuito" e não é contado em relação à sua conta.

## <a name="trigger---concurrency"></a>Gatilho-simultaneidade

Quando há várias mensagens de fila aguardando, o gatilho de fila recupera um lote de mensagens e invoca as instâncias de função simultaneamente para processá-las. Por padrão, o tamanho do lote é 16. Quando o número que está sendo processado chega a 8, o tempo de execução Obtém outro lote e começa a processar essas mensagens. Portanto, o número máximo de mensagens simultâneas sendo processadas por função em uma VM (máquina virtual) é 24. Esse limite se aplica separadamente a cada função disparada por fila em cada VM. Se seu aplicativo de funções for dimensionado para várias VMs, cada VM aguardará gatilhos e tentará executar funções. Por exemplo, se um aplicativo de funções for dimensionado para três VMs, o número máximo padrão de instâncias simultâneas de uma função disparada por fila será 72.

O tamanho do lote e o limite para obter um novo lote são configuráveis no [arquivo host. JSON](functions-host-json.md#queues). Se você quiser minimizar a execução paralela para funções disparadas por fila em um aplicativo de funções, poderá definir o tamanho do lote como 1. Essa configuração elimina a simultaneidade somente contanto que seu aplicativo de funções seja executado em uma única máquina virtual (VM). 

O gatilho de fila impede automaticamente que uma função processe uma mensagem da fila várias vezes; as funções não precisam ser escritas para serem idempotentes.

## <a name="trigger---hostjson-properties"></a>Acionador - propriedades de Host. JSON

O arquivo [host. JSON](functions-host-json.md#queues) contém configurações que controlam o comportamento do gatilho de fila. Consulte a seção [configurações do host. JSON](#hostjson-settings) para obter detalhes sobre as configurações disponíveis.

## <a name="output"></a>Saída

Use a associação de saída do armazenamento de filas do Azure para gravar mensagens em uma fila.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que cria uma mensagem de fila para cada solicitação HTTP recebida.

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

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de gatilho http em um arquivo *Function. JSON* e [ C# um código de script (. CSX)](functions-reference-csharp.md) que usa a associação. A função cria um item de fila com uma carga de objeto **CustomQueueMessage** para cada solicitação HTTP recebida.

Aqui está o *Function* ficheiro:

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

O [configuração](#output---configuration) seção explica essas propriedades.

Aqui está C# o código de script que cria uma única mensagem de fila:

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

Você pode enviar várias mensagens ao mesmo tempo usando um parâmetro `ICollector` ou `IAsyncCollector`. Aqui está C# o código de script que envia várias mensagens, uma com os dados de solicitação HTTP e outra com valores embutidos em código:

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

O exemplo a seguir mostra uma associação de gatilho HTTP em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função cria um item de fila para cada solicitação HTTP recebida.

Aqui está o *Function* ficheiro:

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

O [configuração](#output---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Você pode enviar várias mensagens de uma só vez definindo uma matriz de mensagens para a associação de saída `myQueueItem`. O código JavaScript a seguir envia duas mensagens de fila com valores embutidos em código para cada solicitação HTTP recebida.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo a seguir demonstra como gerar valores únicos e múltiplos para filas de armazenamento. A configuração necessária para *Function. JSON* é a mesma de qualquer forma.

Uma associação de fila de armazenamento é definida em *Function. JSON* , em que *Type* é definido como `queue`.

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

Para definir uma mensagem individual na fila, você passa um único valor para o método `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Para criar várias mensagens na fila, declare um parâmetro como o tipo de lista apropriado e passe uma matriz de valores (que correspondam ao tipo de lista) para o método `set`.

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

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@QueueOutput` em parâmetros cujo valor seria gravado no armazenamento de fila.  O tipo de parâmetro deve ser `OutputBinding<T>`, em que `T` é qualquer tipo de Java nativo de um POJO.

---

## <a name="output---attributes-and-annotations"></a>Saída-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o [queueattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

O atributo se aplica a um parâmetro `out` ou ao valor de retorno da função. O construtor do atributo usa o nome da fila, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Você pode definir a propriedade `Connection` para especificar a conta de armazenamento a ser usada, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída - exemplo do c#](#output).

Você pode usar o atributo `StorageAccount` para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte Trigger-Attributes.

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos no script Java.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

A anotação `QueueOutput` permite que você acesse a gravação de uma mensagem de uma saída de uma função. O exemplo a seguir mostra uma função disparada por HTTP que cria uma mensagem de fila.

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

O parâmetro associado à anotação `QueueOutput` é digitado como uma instância de [\>de saída\<t](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java) .

---

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Queue` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `queue`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure.|
|**direção** | n/d | Tem de ser definido como `out`. Esta propriedade é definida automaticamente ao criar o acionador no portal do Azure. |
|**name** | n/d | O nome da variável que representa a fila no código de função. Defina como `$return` para referenciar o valor de retorno da função.|
|**queueName** |**QueueName** | O nome da fila. |
|**ligação** | **ligação** |O nome de uma configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada para essa associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome aqui. Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "mystorage". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão de armazenamento padrão na configuração do aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Grave uma mensagem de fila única usando um parâmetro de método, como `out T paramName`. Você pode usar o tipo de retorno do método em vez de um parâmetro `out` e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se você tentar associar a `CloudQueueMessage` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

Em C# e C# script, grave várias mensagens de fila usando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Grave uma mensagem de fila única usando um parâmetro de método, como `out T paramName`. O `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. Você pode usar o tipo de retorno do método em vez de um parâmetro `out` e `T` pode ser qualquer um dos seguintes tipos:

* Um objeto serializável como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Se você tentar associar a `CloudQueueMessage` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

Em C# e C# script, grave várias mensagens de fila usando um dos seguintes tipos: 

* `ICollector<T>` ou `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O item fila de saída está disponível por meio de `context.bindings.<NAME>` em que `<NAME>` corresponde ao nome definido em *Function. JSON*. Você pode usar uma cadeia de caracteres ou um objeto serializável em JSON para a carga do item de fila.

# <a name="pythontabpython"></a>[Python](#tab/python)

Há duas opções para a saída de uma mensagem do hub de eventos de uma função:

- **Valor de retorno**: defina a propriedade `name` em *Function. JSON* como `$return`. Com essa configuração, o valor de retorno da função é persistido como uma mensagem de armazenamento de fila.

- **Imperativo**: passe um valor para o método [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . O valor passado para `set` é persistido como uma mensagem de armazenamento de fila.

# <a name="javatabjava"></a>[Java](#tab/java)

Há duas opções para a saída de uma mensagem do hub de eventos de uma função usando a anotação [QueueOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queueoutput) :

- **Valor de retorno**: ao aplicar a anotação à própria função, o valor de retorno da função é persistido como uma mensagem do hub de eventos.

- **Imperativo**: para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding), em que `T` é um POJO ou qualquer tipo de Java nativo. Com essa configuração, passar um valor para o método `setValue` persiste o valor como uma mensagem do hub de eventos.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo |  Referência |
|---|---|
| Fila | [Códigos de erro de fila](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| BLOB, tabela, fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabela, fila |  [Resolução de Problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>definições de Host. JSON

Esta seção descreve as definições de configuração global disponíveis para essa associação nas versões 2. x e superior. O arquivo host. JSON de exemplo abaixo contém apenas as configurações da versão 2. x + para essa associação. Para obter mais informações sobre definições de configuração global nas versões 2. x e posteriores, consulte [referência de host. JSON para Azure Functions](functions-host-json.md).

> [!NOTE]
> Para obter uma referência de Host. JSON nas funções 1.x, consulte [referência de Host. JSON para as funções do Azure 1.x](functions-host-json-v1.md).

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
|maxPollingInterval|00:00:01|O intervalo máximo entre as pesquisas de fila. O mínimo é 00:00:00.100 (100 ms) e incrementa até 00:01:00 (1 min).  Em 1. x, o tipo de dados é milissegundos e, em 2. x e superior, é um TimeSpan.|
|visibilityTimeout|00:00:00|O intervalo de tempo entre as repetições quando o processamento de uma mensagem falha. |
|batchSize|16|O número de mensagens de fila que o tempo de execução do Functions recupera simultaneamente e processa em paralelo. Quando o número que está sendo processado chega à `newBatchThreshold`, o tempo de execução Obtém outro lote e começa a processar essas mensagens. Portanto, o número máximo de mensagens simultâneas sendo processadas por função é `batchSize` mais `newBatchThreshold`. Esse limite se aplica separadamente a cada função disparada por fila. <br><br>Se você quiser evitar a execução paralela para mensagens recebidas em uma fila, poderá definir `batchSize` como 1. No entanto, essa configuração elimina a simultaneidade somente contanto que seu aplicativo de funções seja executado em uma única máquina virtual (VM). Se o aplicativo de funções for dimensionado para várias VMs, cada VM poderá executar uma instância de cada função disparada por fila.<br><br>O `batchSize` máximo é 32. |
|maxDequeueCount|5|O número de vezes para tentar processar uma mensagem antes de movê-la para a fila de suspeitas.|
|newBatchThreshold|batchSize/2|Sempre que o número de mensagens sendo processadas simultaneamente chega a esse número, o tempo de execução recupera outro lote.|

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Ir para um tutorial que usa uma associação de saída de armazenamento de filas](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
