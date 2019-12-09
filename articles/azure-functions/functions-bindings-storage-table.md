---
title: Associações de armazenamento de tabelas do Azure para Azure Functions
description: Entenda como usar as associações de armazenamento de tabela do Azure no Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 1308463694754231aa6d770bf716fd3def219981
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74925314"
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

## <a name="input---example"></a>Entrada-exemplo

Veja o exemplo de idioma específico:

* [C#ler uma entidade](#input---c-example---one-entity)
* [C#associar a IQueryable](#input---c-example---iqueryable)
* [C#associar à Cloudtable](#input---c-example---cloudtable)
* [C#script de leitura de uma entidade](#input---c-script-example---one-entity)
* [C#script de associação a IQueryable](#input---c-script-example---iqueryable)
* [C#associar script a Cloudtable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Entrada- C# exemplo-uma entidade

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

### <a name="input---c-example---iqueryable"></a>Entrada- C# exemplo-IQueryable

O exemplo a seguir mostra uma [ C# função](functions-dotnet-class-library.md) que lê várias linhas da tabela. Observe que a classe `MyPoco` deriva de `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>Entrada- C# exemplo-cloudtable

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

### <a name="input---c-script-example---one-entity"></a>Exemplo de C# script de entrada – uma entidade

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

### <a name="input---c-script-example---iqueryable"></a>Exemplo de C# script de entrada-IQueryable

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

### <a name="input---c-script-example---cloudtable"></a>Entrada- C# exemplo de script-cloudtable

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

### <a name="input---f-example"></a>Entrada- F# exemplo

O exemplo a seguir mostra uma associação de entrada de tabela em um arquivo *Function. JSON* e [ F# ](functions-reference-fsharp.md) código de script que usa a associação. A função usa um gatilho de fila para ler uma única linha de tabela. 

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

Aqui está o F# código:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.LogInformation(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.LogInformation(sprintf "Name in Person entity: %s" personEntity.Name)
```

### <a name="input---javascript-example"></a>Entrada-exemplo de JavaScript

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

### <a name="input---java-example"></a>Entrada – exemplo de Java

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


## <a name="input---attributes"></a>Introdução - atributos
 
Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use os seguintes atributos para configurar uma associação de entrada de tabela:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  O construtor do atributo usa o nome da tabela, a chave de partição e a chave de linha. Ele pode ser usado em um parâmetro out ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

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

## <a name="input---java-annotations"></a>Entrada – anotações de Java

Na [biblioteca de tempo de execução de funções Java](/java/api/overview/azure/functions/runtime), use a anotação `@TableInput` em parâmetros cujo valor venha a ser do armazenamento de tabela.  Esta anotação pode ser usada com tipos Java nativos, POJOs ou valores anuláveis usando > opcional\<T. 

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
|**ligação** |**ligação** | O nome de uma configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada para essa associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome aqui. Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyStorage". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão de armazenamento padrão na configuração do aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Introdução - utilização

A associação de entrada de armazenamento de tabela dá suporte aos seguintes cenários:

* **Ler uma linha C# ou C# script**

  Defina `partitionKey` e `rowKey`. Acesse os dados da tabela usando um parâmetro de método `T <paramName>`. Em C# script, `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. `T` normalmente é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. As propriedades `filter` e `take` não são usadas neste cenário. 

* **Ler uma ou mais linhas no C# script C# ou**

  Acesse os dados da tabela usando um parâmetro de método `IQueryable<T> <paramName>`. Em C# script, `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. `T` deve ser um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Você pode usar `IQueryable` métodos para fazer qualquer filtragem necessária. As propriedades `partitionKey`, `rowKey`, `filter`e `take` não são usadas neste cenário.  

  > [!NOTE]
  > Não há suporte para `IQueryable` no [tempo de execução do Functions v2](functions-versions.md). Uma alternativa é [usar um parâmetro de método paramName cloudtable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela usando o SDK do armazenamento do Azure. Se você tentar associar a `CloudTable` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

* **Ler uma ou mais linhas em JavaScript**

  Defina as propriedades `filter` e `take`. Não defina `partitionKey` ou `rowKey`. Acesse a entidade (ou entidades) da tabela de entrada usando `context.bindings.<BINDING_NAME>`. Os objetos desserializados têm `RowKey` e `PartitionKey` Propriedades.

## <a name="output"></a>Saída

Use uma associação de saída de armazenamento de tabela do Azure para gravar entidades em uma tabela em uma conta de armazenamento do Azure.

> [!NOTE]
> Esta associação de saída não dá suporte à atualização de entidades existentes. Use a operação `TableOperation.Replace` [do SDK do armazenamento do Azure](https://docs.microsoft.com/azure/cosmos-db/tutorial-develop-table-dotnet#delete-an-entity) para atualizar uma entidade existente.   

## <a name="output---example"></a>Saída - exemplo

Veja o exemplo de idioma específico:

* [C#](#output---c-example)
* [Script do c# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo do c#

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

### <a name="output---c-script-example"></a>Saída - exemplo de script do c#

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

### <a name="output---f-example"></a>Saída - F# exemplo

O exemplo a seguir mostra uma associação de saída de tabela em um arquivo *Function. JSON* e [ F# ](functions-reference-fsharp.md) código de script que usa a associação. A função grava várias entidades de tabela.

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

Aqui está o F# código:

```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: ILogger) =
    for i = 1 to 10 do
        log.LogInformation(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

### <a name="output---javascript-example"></a>Saída - exemplo de JavaScript

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

## <a name="output---attributes"></a>Saída - atributos

Em [ C# bibliotecas de classes](functions-dotnet-class-library.md), use o [TableName](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

O construtor do atributo usa o nome da tabela. Ele pode ser usado em um parâmetro `out` ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

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

Para obter um exemplo completo, consulte [saída - exemplo do c#](#output---c-example).

Você pode usar o atributo `StorageAccount` para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Input-Attributes](#input---attributes).

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
|**ligação** |**ligação** | O nome de uma configuração de aplicativo que contém a cadeia de conexão de armazenamento a ser usada para essa associação. Se o nome da configuração do aplicativo começar com "AzureWebJobs", você poderá especificar apenas o restante do nome aqui. Por exemplo, se você definir `connection` como "mystorage", o tempo de execução do Functions procurará uma configuração de aplicativo chamada "AzureWebJobsMyStorage". Se você deixar `connection` vazio, o tempo de execução do Functions usará a cadeia de conexão de armazenamento padrão na configuração do aplicativo chamada `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

A associação de saída de armazenamento de tabela dá suporte aos seguintes cenários:

* **Escrever uma linha em qualquer idioma**

  Em C# e C# script, acesse a entidade da tabela de saída usando um parâmetro de método como `out T paramName` ou o valor de retorno da função. Em C# script, `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. `T` pode ser qualquer tipo serializável se a chave de partição e a chave de linha forem fornecidas pelo arquivo *Function. JSON* ou pelo atributo `Table`. Caso contrário, `T` deve ser um tipo que inclui as propriedades `PartitionKey` e `RowKey`. Nesse cenário, `T` normalmente implementa `ITableEntity` ou deriva de `TableEntity`, mas não é necessário.

* **Gravar uma ou mais linhas no C# script C# ou**

  Em C# e C# script, acesse a entidade da tabela de saída usando um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName`. Em C# script, `paramName` é o valor especificado na propriedade `name` de *Function. JSON*. `T` especifica o esquema das entidades que você deseja adicionar. Normalmente, `T` deriva de `TableEntity` ou implementa `ITableEntity`, mas não é necessário. Os valores da chave de partição e de chave de linha em *Function. JSON* ou no construtor de atributo `Table` não são usados neste cenário.

  Uma alternativa é usar um parâmetro de método `CloudTable` para gravar na tabela usando o SDK do armazenamento do Azure. Se você tentar associar a `CloudTable` e receber uma mensagem de erro, verifique se você tem uma referência para [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x). Para obter um exemplo de código que se associa a `CloudTable`, consulte os exemplos de associação [C#](#input---c-example---cloudtable) de entrada para o ou [ C# script](#input---c-script-example---cloudtable) anteriormente neste artigo.

* **Gravar uma ou mais linhas em JavaScript**

  Em funções do JavaScript, acesse a saída da tabela usando `context.bindings.<BINDING_NAME>`.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Tabelas | [Códigos de erro de tabela](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabela, fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabela, fila | [Resolução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
