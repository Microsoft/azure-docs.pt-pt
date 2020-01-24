---
title: Associações de armazenamento de tabelas do Azure para Azure Functions
description: Entenda como usar as associações de armazenamento de tabela do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 1be6420598e7983ef9014f617da1f87f5550fa6a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705365"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Associações de armazenamento de tabelas do Azure para Azure Functions

Este artigo explica como trabalhar com associações de armazenamento de tabelas do Azure no Azure Functions. O Azure Functions dá suporte a associações de entrada e saída para o armazenamento de tabelas do Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

As associações de armazenamento de tabela são fornecidas no pacote NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versão 2. x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) repositório do GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes-funções 2. x e superior

As associações de armazenamento de tabela são fornecidas no pacote NuGet [Microsoft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) , versão 3. x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input

Use a associação de entrada de armazenamento de tabela do Azure para ler uma tabela em uma conta de armazenamento do Azure.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Uma entidade

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que lê uma única linha da tabela. 

O valor da chave de linha "{queueTrigger}" indica que a chave de linha vem da cadeia de caracteres de mensagem da fila.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="iqueryable"></a>IQueryable

O exemplo seguinte mostra uma [ C# função](functions-dotnet-class-library.md) que lê várias linhas de mesa onde a classe `MyPoco` deriva de `TableEntity`.

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

### <a name="cloudtable"></a>CloudTable

Não há suporte para `IQueryable` no [tempo de execução do Functions v2](functions-versions.md). Uma alternativa é usar um parâmetro de método `CloudTable` para ler a tabela usando o SDK do armazenamento do Azure. Veja um exemplo de uma função que consulta uma tabela de log de Azure Functions:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Para obter mais informações sobre como usar o Cloudtable, consulte Introdução [ao armazenamento de tabelas do Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se você tentar associar a `CloudTable` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

### <a name="one-entity"></a>Uma entidade

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *Function. JSON* e [ C# ](functions-reference-csharp.md) código de script que usa a associação. A função usa um gatilho de fila para ler uma única linha de tabela. 

O arquivo *Function. JSON* especifica um `partitionKey` e um `rowKey`. O valor de `rowKey` "{queueTrigger}" indica que a chave de linha é proveniente da cadeia de caracteres de mensagem da fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O [configuração](#input---configuration) seção explica essas propriedades.

Aqui está o código de script do c#:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="iqueryable"></a>IQueryable

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *Function. JSON* e [ C# ](functions-reference-csharp.md) código de script que usa a associação. A função lê entidades para uma chave de partição que é especificada em uma mensagem da fila.

Aqui está o *Function* ficheiro:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O [configuração](#input---configuration) seção explica essas propriedades.

O C# código de script adiciona uma referência ao SDK do armazenamento do Azure para que o tipo de entidade possa derivar de `TableEntity`:

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

Não há suporte para `IQueryable` no tempo de execução do Functions para [as versões 2. x e superior)](functions-versions.md). Uma alternativa é usar um parâmetro de método `CloudTable` para ler a tabela usando o SDK do armazenamento do Azure. Veja um exemplo de uma função que consulta uma tabela de log de Azure Functions:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Para obter mais informações sobre como usar o Cloudtable, consulte Introdução [ao armazenamento de tabelas do Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se você tentar associar a `CloudTable` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *Function. JSON* e [código JavaScript](functions-reference-node.md) que usa a associação. A função usa um gatilho de fila para ler uma única linha de tabela. 

O arquivo *Function. JSON* especifica um `partitionKey` e um `rowKey`. O valor de `rowKey` "{queueTrigger}" indica que a chave de linha é proveniente da cadeia de caracteres de mensagem da fila.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O [configuração](#input---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Fila de mesa única 

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função disparada por HTTP que retorna a contagem total dos itens em uma partição especificada no armazenamento de tabela.

```java
@FunctionName("getallcount")
public int run(
   @HttpTrigger(name = "req",
                 methods = {HttpMethod.GET},
                 authLevel = AuthorizationLevel.ANONYMOUS) Object dummyShouldNotBeUsed,
   @TableInput(name = "items",
                tableName = "mytablename",  partitionKey = "myparkey",
                connection = "myconnvarname") MyItem[] items
) {
    return items.length;
}
```

---

## <a name="input---attributes-and-annotations"></a>Entrada-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

 Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use os seguintes atributos para configurar uma associação de entrada de tabela:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  O construtor do atributo usa o nome da tabela, a chave de partição e a chave de linha. O atributo pode ser utilizado num parâmetro `out` ou no valor de retorno da função, como se pode ver no seguinte exemplo:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Você pode definir a propriedade `Connection` para especificar a conta de armazenamento a ser usada, conforme mostrado no exemplo a seguir:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Para obter um exemplo completo, consulte entrada C# -exemplo.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra maneira de especificar a conta de armazenamento a ser usada. O construtor usa o nome de uma configuração de aplicativo que contém uma cadeia de conexão de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo seguinte mostra o nível de classe e método:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A conta de armazenamento a ser usada é determinada na seguinte ordem:

* O `Table` do atributo `Connection` propriedade.
* O `StorageAccount` atributo aplicado para o mesmo parâmetro como o `Table` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` aplicado à classe de atributo.
* A conta de armazenamento padrão para o aplicativo de funções (configuração de aplicativo "AzureWebJobsStorage").

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@TableInput` em parâmetros cujo valor venha a ser do armazenamento de tabela.  Essa anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando `Optional<T>`.

---

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Table` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `table`. Essa propriedade é definida automaticamente quando você cria a associação no portal do Azure.|
|**direção** | n/d | Tem de ser definido como `in`. Essa propriedade é definida automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | O nome da variável que representa a tabela ou entidade no código de função. | 
|**tableName** | **TableName** | O nome da tabela.| 
|**partitionKey** | **PartitionKey** |Opcional. A chave de partição da entidade de tabela a ser lida. Consulte a seção [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | Opcional. A chave de linha da entidade de tabela a ser lida. Consulte a seção [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**take** |**Ter** | Opcional. O número máximo de entidades a serem lidas em JavaScript. Consulte a seção [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**filter** |**Sem** | Opcional. Uma expressão de filtro OData para entrada de tabela em JavaScript. Consulte a seção [uso](#input---usage) para obter orientação sobre como usar essa propriedade.| 
|**ligação** |**ligação** | O nome de uma configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada para essa associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão de armazenamento padrão na configuração do aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Introdução - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

* **Leia uma linha em**

  Defina `partitionKey` e `rowKey`. Acesse os dados da tabela usando um parâmetro de método `T <paramName>`. Em C# script, `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. `T` normalmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. As propriedades `filter` e `take` não são usadas neste cenário.

* **Leia uma ou mais linhas**

  Acesse os dados da tabela usando um parâmetro de método `IQueryable<T> <paramName>`. Em C# script, `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. `T` deve ser um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Você pode usar `IQueryable` métodos para fazer qualquer filtragem necessária. As propriedades `partitionKey`, `rowKey`, `filter`e `take` não são usadas neste cenário.  

  > [!NOTE]
  > Não há suporte para `IQueryable` no [tempo de execução do Functions v2](functions-versions.md). Uma alternativa é [usar um parâmetro de método paramName cloudtable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela usando o SDK do armazenamento do Azure. Se você tentar associar a `CloudTable` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

* **Leia uma linha em**

  Defina `partitionKey` e `rowKey`. Acesse os dados da tabela usando um parâmetro de método `T <paramName>`. Em C# script, `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. `T` normalmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. As propriedades `filter` e `take` não são usadas neste cenário.

* **Leia uma ou mais linhas**

  Acesse os dados da tabela usando um parâmetro de método `IQueryable<T> <paramName>`. Em C# script, `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. `T` deve ser um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Você pode usar `IQueryable` métodos para fazer qualquer filtragem necessária. As propriedades `partitionKey`, `rowKey`, `filter`e `take` não são usadas neste cenário.  

  > [!NOTE]
  > Não há suporte para `IQueryable` no [tempo de execução do Functions v2](functions-versions.md). Uma alternativa é [usar um parâmetro de método paramName cloudtable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela usando o SDK do armazenamento do Azure. Se você tentar associar a `CloudTable` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Defina as propriedades `filter` e `take`. Não defina `partitionKey` ou `rowKey`. Aceda à entidade da tabela de entrada (ou entidades) utilizando `context.bindings.<BINDING_NAME>`. Os objetos desserializados têm `RowKey` e `PartitionKey` Propriedades.

# <a name="pythontabpython"></a>[Python](#tab/python)

Os dados da tabela são transmitidos para a função como uma cadeia JSON. Desserialize a mensagem chamando `json.loads` como mostrado no [exemplo](#input)da entrada .

# <a name="javatabjava"></a>[Java](#tab/java)

O atributo [tableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) dá-lhe acesso à linha da mesa que desencadeou a função.

---

## <a name="output"></a>Saída

Use uma associação de saída de armazenamento de tabela do Azure para gravar entidades em uma tabela em uma conta de armazenamento do Azure.

> [!NOTE]
> Esta associação de saída não dá suporte à atualização de entidades existentes. Utilize a operação `TableOperation.Replace` [a partir do Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) para atualizar uma entidade existente.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que usa um gatilho http para gravar uma única linha de tabela. 

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *Function. JSON* e [ C# ](functions-reference-csharp.md) código de script que usa a associação. A função grava várias entidades de tabela.

Aqui está o *Function* ficheiro:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O [configuração](#output---configuration) seção explica essas propriedades.

Aqui está o código de script do c#:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *Function. JSON* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava várias entidades de tabela.

Aqui está o *Function* ficheiro:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

O [configuração](#output---configuration) seção explica essas propriedades.

Eis o código JavaScript:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }
    
    context.done();
};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo que se segue demonstra como utilizar a ligação de saída de armazenamento da tabela. A ligação `table` é configurada na *função.json* atribuindo valores a `name`, `tableName`, `partitionKey`e `connection`:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
    }
  ]
}
```

A função seguinte gera um UUI único para o valor `rowKey` e persiste a mensagem no armazenamento da mesa.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo seguinte mostra uma função Java que usa um gatilho HTTP para escrever uma única linha de mesa.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}
    public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

O exemplo seguinte mostra uma função Java que usa um gatilho HTTP para escrever várias linhas de mesa.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="output---attributes-and-annotations"></a>Saída-atributos e anotações

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o [TableName](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

O construtor do atributo usa o nome da tabela. O atributo pode ser utilizado num parâmetro `out` ou no valor de retorno da função, como se pode ver no seguinte exemplo:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Você pode definir a propriedade `Connection` para especificar a conta de armazenamento a ser usada, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Para obter um exemplo completo, consulte [saída - exemplo do c#](#output).

Você pode usar o atributo `StorageAccount` para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Input-Attributes](#input---attributes-and-annotations).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

O script não dá suporte C# a atributos.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Não há suporte para atributos pelo JavaScript.

# <a name="pythontabpython"></a>[Python](#tab/python)

Não há suporte para atributos no Python.

# <a name="javatabjava"></a>[Java](#tab/java)

Na [biblioteca de tempo de funcionamento das funções Java,](/java/api/overview/azure/functions/runtime)utilize a anotação [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) em parâmetros para escrever valores em armazenamento de mesa.

Veja o [exemplo para mais detalhes](#output).

---

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Table` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `table`. Essa propriedade é definida automaticamente quando você cria a associação no portal do Azure.|
|**direção** | n/d | Tem de ser definido como `out`. Essa propriedade é definida automaticamente quando você cria a associação no portal do Azure. |
|**name** | n/d | O nome da variável usada no código de função que representa a tabela ou entidade. Defina como `$return` para referenciar o valor de retorno da função.| 
|**tableName** |**TableName** | O nome da tabela.| 
|**partitionKey** |**PartitionKey** | A chave de partição da entidade de tabela a ser gravada. Consulte a [seção uso](#output---usage) para obter orientação sobre como usar essa propriedade.| 
|**rowKey** |**RowKey** | A chave de linha da entidade de tabela a ser gravada. Consulte a [seção uso](#output---usage) para obter orientação sobre como usar essa propriedade.| 
|**ligação** |**ligação** | O nome de uma configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada para essa associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão de armazenamento padrão na configuração do aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Aceda à entidade da tabela de saída utilizando um metómetro `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclui as propriedades `PartitionKey` e `RowKey`. Estas propriedades são frequentemente acompanhadas pela implementação de `ITableEntity` ou herdando `TableEntity`.

Em alternativa, pode utilizar um parâmetro de método `CloudTable` para escrever à mesa utilizando o SDK de Armazenamento Azure. Se você tentar associar a `CloudTable` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-scripttabcsharp-script"></a>[C#Prescritiva](#tab/csharp-script)

Aceda à entidade da tabela de saída utilizando um metómetro `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclui as propriedades `PartitionKey` e `RowKey`. Estas propriedades são frequentemente acompanhadas pela implementação de `ITableEntity` ou herdando `TableEntity`. O valor `paramName` é especificado na propriedade `name` *função.json*.

Em alternativa, pode utilizar um parâmetro de método `CloudTable` para escrever à mesa utilizando o SDK de Armazenamento Azure. Se você tentar associar a `CloudTable` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Aceda ao evento de saída utilizando `context.bindings.<name>` onde `<name>` é o valor especificado na propriedade `name` *função.json*.

# <a name="pythontabpython"></a>[Python](#tab/python)

Existem duas opções para colocar uma mensagem de linha de armazenamento de tabela a partir de uma função:

- **Valor de retorno**: defina a propriedade `name` em *Function. JSON* como `$return`. Com esta configuração, o valor de retorno da função é persistido como uma linha de armazenamento de mesa.

- **Imperativo**: passe um valor para o método [set](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) . O valor passado para `set` é perinendo como uma mensagem do Event Hub.

# <a name="javatabjava"></a>[Java](#tab/java)

Existem duas opções para eliminar uma linha de armazenamento de tabela sacar de uma função utilizando a anotação [TableStorageOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet)

- **Valor de devolução**: Aplicando a anotação à própria função, o valor de retorno da função é persinedo como linha de armazenamento de mesa.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do tipo [`OutputBinding<T>`, ](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)onde `T` inclui as propriedades `PartitionKey` e `RowKey`. Estas propriedades são frequentemente acompanhadas pela implementação de `ITableEntity` ou herdando `TableEntity`.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Tabelas | [Códigos de erro de tabela](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabela, fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabela, fila | [Resolução de Problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
