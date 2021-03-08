---
title: Gatilho de armazenamento da fila Azure para funções Azure
description: Aprenda a executar uma Função Azure à medida que os dados de armazenamento da Fila Azure mudam.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: f4477a09f151695b826d0becf28e92ceaf3f9e85
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453211"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Gatilho de armazenamento da fila Azure para funções Azure

O gatilho de armazenamento da fila executa uma função à medida que as mensagens são adicionadas ao armazenamento da fila Azure.

## <a name="encoding"></a>Encoding

As funções esperam uma cadeia codificada *base64.* Quaisquer ajustes ao tipo de codificação (para preparar os dados como uma cadeia codificada *base64)* devem ser implementados no serviço de chamada.

## <a name="example"></a>Exemplo

Utilize o gatilho da fila para iniciar uma função quando um novo item for recebido numa fila. A mensagem da fila é apresentada como uma entrada da função.

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que regista a `myqueue-items` fila e escreve um registo cada vez que um item de fila é processado.

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação do gatilho de fila numa *function.jsno* ficheiro e o código de [script C# (.csx)](functions-reference-csharp.md) que utiliza a ligação. A função sonda a `myqueue-items` fila e escreve um registo cada vez que um item de fila é processado.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código do guião C:

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

A secção [de utilização](#usage) `myQueueItem` explica, que é nomeada pela `name` propriedade em function.js.  A [secção de metadados](#message-metadata) de mensagem explica todas as outras variáveis mostradas.

# <a name="java"></a>[Java](#tab/java)

O exemplo de Java que se segue mostra uma função de gatilho da fila de armazenamento, que regista a mensagem ativada colocada na fila `myqueuename` .

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação do gatilho de fila numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função sonda a `myqueue-items` fila e escreve um registo cada vez que um item de fila é processado.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#configuration) explica estas propriedades.

> [!NOTE]
> O parâmetro do nome reflete como `context.bindings.<name>` no código JavaScript que contém a carga útil do item da fila. Esta carga útil também é passada como o segundo parâmetro para a função.

Aqui está o código JavaScript:

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

A secção [de utilização](#usage) `myQueueItem` explica, que é nomeada pela `name` propriedade em function.js.  A [secção de metadados](#message-metadata) de mensagem explica todas as outras variáveis mostradas.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como ler uma mensagem de fila passada para uma função através de um gatilho.

Um gatilho de fila de armazenamento é definido em *function.jsno* ficheiro onde está definido `type` para `queueTrigger` .

```json
{
  "bindings": [
    {
      "name": "QueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

O código no ficheiro *Run.ps1* declara um parâmetro como `$QueueItem` , o que lhe permite ler a mensagem de fila na sua função.

```powershell
# Input bindings are passed in via param block.
param([string] $QueueItem, $TriggerMetadata)

# Write out the queue message and metadata to the information log.
Write-Host "PowerShell queue trigger function processed work item: $QueueItem"
Write-Host "Queue item expiration time: $($TriggerMetadata.ExpirationTime)"
Write-Host "Queue item insertion time: $($TriggerMetadata.InsertionTime)"
Write-Host "Queue item next visible time: $($TriggerMetadata.NextVisibleTime)"
Write-Host "ID: $($TriggerMetadata.Id)"
Write-Host "Pop receipt: $($TriggerMetadata.PopReceipt)"
Write-Host "Dequeue count: $($TriggerMetadata.DequeueCount)"
```

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como ler uma mensagem de fila passada para uma função através de um gatilho.

Um gatilho de fila de armazenamento é definido em *function.jsno* local onde *o tipo* está definido para `queueTrigger` .

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

O código *_\_ .py_ \_* declara um parâmetro como `func.QueueMessage` , que lhe permite ler a mensagem de fila na sua função.

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

 ---

## <a name="attributes-and-annotations"></a>Atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar um gatilho de fila:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  O construtor do atributo tem o nome da fila para monitorizar, como mostra o seguinte exemplo:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Pode definir a `Connection` propriedade para especificar a definição da aplicação que contém o fio de ligação da conta de armazenamento a utilizar, como mostra o exemplo seguinte:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Para um exemplo completo, veja [o exemplo.](#example)

* [ArmazenamentoCotaçãotribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra forma de especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado ao nível do parâmetro, método ou classe. O exemplo a seguir mostra o nível de classe e o nível do método:

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

* Propriedade `QueueTrigger` do `Connection` atributo.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro que o `QueueTrigger` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A definição da aplicação "AzureWebJobsStorage".

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

A `QueueTrigger` anotação dá-lhe acesso à fila que desencadeia a função. O exemplo a seguir torna a mensagem de fila disponível para a função através do `message` parâmetro.

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
|`name`       | Declara o nome do parâmetro na assinatura da função. Quando a função é desencadeada, o valor deste parâmetro tem o conteúdo da mensagem de fila. |
|`queueName`  | Declara o nome da fila na conta de armazenamento. |
|`connection` | Aponta para o fio de ligação da conta de armazenamento. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `QueueTrigger` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a| Deve ser definido para `queueTrigger` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure.|
|**direção**| n/a | Nofunction.jsapenas *arquivado.* Deve ser definido para `in` . Esta propriedade é definida automaticamente quando cria o gatilho no portal Azure. |
|**nome** | n/a |O nome da variável que contém a carga útil do item da fila no código de função.  |
|**nome de fila** | **Nome da fila**| O nome da fila para a sondagem. |
|**conexão** | **Ligação** |O nome de uma definição de aplicação que contém o fio de ligação de armazenamento para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui.<br><br>Por exemplo, se definir `connection` "MyStorage", o tempo de execução de Funções procura uma definição de app chamada "MyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .<br><br>Se estiver a utilizar [a versão 5.x ou superior da extensão](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher), em vez de uma cadeia de ligação, pode fornecer uma referência a uma secção de configuração que defina a ligação. Ver [Ligações](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Predefinição

Aceda aos dados da mensagem utilizando um parâmetro de método como `string paramName` . Pode ligar-se a qualquer um dos seguintes tipos:

* Objeto - O tempo de execução das funções deserializa uma carga útil JSON num caso de uma classe arbitrária definida no seu código.
* `string`
* `byte[]`
* [CloudQueueMessage]

Se tentar ligar-se `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

### <a name="additional-types"></a>Tipos adicionais

As aplicações que utilizam a [versão 5.0.0 ou superior da extensão de Armazenamento](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) também podem utilizar tipos do [Azure SDK para .NET](/dotnet/api/overview/azure/storage.queues-readme). Esta versão deixa cair o suporte para o tipo legado `CloudQueueMessage` a favor dos seguintes tipos:

- [FilaMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
 
Por exemplo, utilizando estes tipos, consulte [o repositório GitHub para a extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="default"></a>Predefinição

Aceda aos dados da mensagem utilizando um parâmetro de método como `string paramName` . O `paramName` valor especificado na `name` propriedade de *function.jsem*. Pode ligar-se a qualquer um dos seguintes tipos:

* Objeto - O tempo de execução das funções deserializa uma carga útil JSON num caso de uma classe arbitrária definida no seu código. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Se tentar ligar-se `CloudQueueMessage` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

### <a name="additional-types"></a>Tipos adicionais

As aplicações que utilizam a [versão 5.0.0 ou superior da extensão de Armazenamento](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) também podem utilizar tipos do [Azure SDK para .NET](/dotnet/api/overview/azure/storage.queues-readme). Esta versão deixa cair o suporte para o tipo legado `CloudQueueMessage` a favor dos seguintes tipos:

- [FilaMessage](/dotnet/api/azure.storage.queues.models.queuemessage)

Por exemplo, utilizando estes tipos, consulte [o repositório GitHub para a extensão](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="java"></a>[Java](#tab/java)

A anotação [QueueTrigger](/java/api/com.microsoft.azure.functions.annotation.queuetrigger) dá-lhe acesso à mensagem de fila que desencadeou a função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A carga útil do item da fila está disponível através `context.bindings.<NAME>` do qual corresponde ao nome definido emfunction.js`<NAME>` *em*. Se a carga útil for JSON, o valor é deseralizado num objeto.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Aceda à mensagem de fila através do parâmetro de cadeia que corresponda ao nome designado pelo parâmetro de ligação `name` no *function.jsno* ficheiro.

# <a name="python"></a>[Python](#tab/python)

Aceda à mensagem de fila através do parâmetro dactilografado como [QueueMessage](/python/api/azure-functions/azure.functions.queuemessage).

---

## <a name="message-metadata"></a>Metadados de mensagens

O gatilho da fila fornece várias [propriedades de metadados.](./functions-bindings-expressions-patterns.md#trigger-metadata) Estas propriedades podem ser usadas como parte de expressões de ligação em outras encadernações ou como parâmetros no seu código. As propriedades são membros da classe [CloudQueueMessage.](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage)

|Propriedade|Tipo|Descrição|
|--------|----|-----------|
|`QueueTrigger`|`string`|Carga útil da fila (se uma corda válida). Se a carga útil da mensagem de fila for uma cadeia, `QueueTrigger` tem o mesmo valor que a variável nomeada pela propriedade emfunction.js`name` *em*.|
|`DequeueCount`|`int`|O número de vezes que esta mensagem foi descoduada.|
|`ExpirationTime`|`DateTimeOffset`|O tempo que a mensagem expirar.|
|`Id`|`string`|Identificação de mensagem de fila.|
|`InsertionTime`|`DateTimeOffset`|O tempo que a mensagem foi adicionada à fila.|
|`NextVisibleTime`|`DateTimeOffset`|O tempo que a mensagem será visível a seguir.|
|`PopReceipt`|`string`|O recibo da mensagem é.|

## <a name="poison-messages"></a>Mensagens venenosas

Quando uma função de gatilho de fila falha, o Azure Functions retrimba a função até cinco vezes para uma determinada mensagem de fila, incluindo a primeira tentativa. Se todas as cinco tentativas falharem, o tempo de execução das funções adiciona uma mensagem a uma fila chamada *&lt; originalqueuename>-veneno*. Pode escrever uma função para processar mensagens da fila do veneno registando-as ou enviando uma notificação de que é necessária atenção manual.

Para lidar manualmente com mensagens venenosas, verifique o [dequendundo a](#message-metadata) mensagem da fila.

## <a name="polling-algorithm"></a>Algoritmo de sondagem

O gatilho da fila implementa um algoritmo de back-off exponencial aleatório para reduzir o efeito da votação em fila ociosa nos custos de transação de armazenamento.

O algoritmo utiliza a seguinte lógica:

- Quando uma mensagem é encontrada, o tempo de execução espera dois segundos e, em seguida, verifica se há outra mensagem
- Quando nenhuma mensagem é encontrada, espera cerca de quatro segundos antes de tentar novamente.
- Após tentativas falhadas subsequentes de obter uma mensagem de fila, o tempo de espera continua a aumentar até atingir o tempo máximo de espera, o que não chega a um minuto.
- O tempo máximo de espera é configurável através da `maxPollingInterval` propriedade nahost.jsem [ arquivo](functions-host-json-v1.md#queues).

Para o desenvolvimento local, o intervalo máximo de votação é de dois segundos.

No que diz respeito à faturação, o tempo gasto nas sondagens pelo tempo de funcionamento é "gratuito" e não é contabilizado contra a sua conta.

## <a name="concurrency"></a>Simultaneidade

Quando há várias mensagens de fila à espera, o gatilho da fila recupera um lote de mensagens e invoca instâncias de função simultaneamente para as processar. Por predefinição, o tamanho do lote é de 16. Quando o número que está a ser processado chega a 8, o tempo de funcionamento recebe outro lote e começa a processar essas mensagens. Assim, o número máximo de mensagens simultâneas a serem processadas por função numa máquina virtual (VM) é de 24. Este limite aplica-se separadamente a cada função acionada por fila em cada VM. Se a sua aplicação de função se dimensionar para vários VMs, cada VM aguardará os gatilhos e tentará executar funções. Por exemplo, se uma aplicação de função se eleva a 3 VMs, o número máximo predefinido de instâncias simultâneas de uma função desencadeada pela fila é de 72.

O tamanho do lote e o limiar para obter um novo lote são configuráveis no [host.jsem arquivo](functions-host-json.md#queues). Se pretender minimizar a execução paralela para funções desencadeadas por filas numa aplicação de função, pode definir o tamanho do lote para 1. Esta definição elimina a conusncy apenas desde que a sua aplicação de função seja executado numa única máquina virtual (VM). 

O gatilho da fila impede automaticamente que uma função processe uma mensagem de fila várias vezes simultaneamente.

## <a name="hostjson-properties"></a>host.jssobre propriedades

A [host.jsno](functions-host-json.md#queues) ficheiro contém definições que controlam o comportamento do gatilho da fila. Consulte a secçãohost.jsna secção [ de definições](functions-bindings-storage-queue.md#hostjson-settings) para obter informações sobre as definições disponíveis.

## <a name="next-steps"></a>Passos seguintes

- [Escreva mensagens de armazenamento de fila (ligação de saída)](./functions-bindings-storage-queue-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
