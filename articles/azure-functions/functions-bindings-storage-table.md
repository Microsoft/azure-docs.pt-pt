---
title: Enlaces de armazenamento de tabelas do Azure para as funções do Azure
description: Compreenda como utilizar os enlaces de armazenamento de tabelas do Azure nas funções do Azure.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: das funções do Azure, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: 0c9cd513f4d5842d14077bb7470ebd18c7a46340
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538209"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Enlaces de armazenamento de tabelas do Azure para as funções do Azure

Este artigo explica como trabalhar com ligações de armazenamento de tabelas do Azure nas funções do Azure. Funções do Azure suporta de entrada e saída enlaces para o armazenamento de tabelas do Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - funções 1.x

Os enlaces de armazenamento de tabela são fornecidos na [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) pacote NuGet, versão 2.x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) repositório do GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pacotes - funções 2.x

Os enlaces de armazenamento de tabela são fornecidos na [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) pacote NuGet, versão 3.x. Código-fonte para o pacote está no [sdk do webjobs do azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) repositório do GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input

Utilize o enlace de entrada de armazenamento de tabelas do Azure para ler uma tabela numa conta de armazenamento do Azure.

## <a name="input---example"></a>Entrada - exemplo

Veja o exemplo de idioma específico:

* [Ler uma entidade com c#](#input---c-example---one-entity)
* [C# vincular ao IQueryable](#input---c-example---iqueryable)
* [C# vincular ao CloudTable](#input---c-example---cloudtable)
* [Script c# ler uma entidade](#input---c-script-example---one-entity)
* [Enlace de script c# para IQueryable](#input---c-script-example---iqueryable)
* [Enlace de script c# para CloudTable](#input---c-script-example---cloudtable)
* [F#](#input---f-example)
* [JavaScript](#input---javascript-example)
* [Java](#input---java-example)

### <a name="input---c-example---one-entity"></a>Uma entidade de entrada – exemplo C #-

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que lê uma única linha da tabela. 

O valor de chave de linha "{queueTrigger}" indica que a chave de linha é proveniente de cadeia de caracteres de mensagem da fila.

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

### <a name="input---c-example---iqueryable"></a>Entrada – exemplo C # - IQueryable

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que lê várias linhas de tabela. Tenha em atenção que o `MyPoco` classe deriva de `TableEntity`.

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

### <a name="input---c-example---cloudtable"></a>Entrada – exemplo C # - CloudTable

`IQueryable` Não é suportado no [tempo de execução do funções v2](functions-versions.md). Uma alternativa é usar um `CloudTable` parâmetro do método para ler a tabela com o SDK de armazenamento do Azure. Eis um exemplo de uma função de 2.x consulta uma tabela de registo de funções do Azure:

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

Para obter mais informações sobre como utilizar CloudTable, consulte [introdução ao armazenamento de tabelas do Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se tentar fazer a ligação `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência ao [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---c-script-example---one-entity"></a>Uma entidade de entrada - c# exemplo do script-

O exemplo seguinte mostra um enlace de entrada de tabela num *Function* ficheiro e [script c#](functions-reference-csharp.md) código que utiliza o enlace. A função utiliza um acionador de fila para ler uma única linha da tabela. 

O *Function* ficheiro Especifica um `partitionKey` e um `rowKey`. O `rowKey` valor "{queueTrigger}" indica que a chave de linha é proveniente de cadeia de caracteres de mensagem da fila.

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

### <a name="input---c-script-example---iqueryable"></a>Entrada - c# exemplo do script - IQueryable

O exemplo seguinte mostra um enlace de entrada de tabela num *Function* ficheiro e [script c#](functions-reference-csharp.md) código que utiliza o enlace. A função lê entidades para uma chave de partição especificado numa mensagem de fila.

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

O código de script do c# adiciona uma referência para o SDK de armazenamento do Azure, para que o tipo de entidade pode derivar de `TableEntity`:

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

### <a name="input---c-script-example---cloudtable"></a>Entrada - c# exemplo do script - CloudTable

`IQueryable` Não é suportado no [tempo de execução do funções v2](functions-versions.md). Uma alternativa é usar um `CloudTable` parâmetro do método para ler a tabela com o SDK de armazenamento do Azure. Eis um exemplo de uma função de 2.x consulta uma tabela de registo de funções do Azure:

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

Para obter mais informações sobre como utilizar CloudTable, consulte [introdução ao armazenamento de tabelas do Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se tentar fazer a ligação `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência ao [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

### <a name="input---f-example"></a>Entrada - F# exemplo

O exemplo seguinte mostra um enlace de entrada de tabela num *Function* ficheiro e [ F# script](functions-reference-fsharp.md) código que utiliza o enlace. A função utiliza um acionador de fila para ler uma única linha da tabela. 

O *Function* ficheiro Especifica um `partitionKey` e um `rowKey`. O `rowKey` valor "{queueTrigger}" indica que a chave de linha é proveniente de cadeia de caracteres de mensagem da fila.

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

### <a name="input---javascript-example"></a>Introdução - exemplo de JavaScript

O exemplo seguinte mostra um enlace de entrada de tabela num *Function* ficheiro e [código JavaScript](functions-reference-node.md) que utiliza o enlace. A função utiliza um acionador de fila para ler uma única linha da tabela. 

O *Function* ficheiro Especifica um `partitionKey` e um `rowKey`. O `rowKey` valor "{queueTrigger}" indica que a chave de linha é proveniente de cadeia de caracteres de mensagem da fila.

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

### <a name="input---java-example"></a>Introdução - exemplo de Java

O exemplo seguinte mostra uma função acionada por HTTP que retorna a contagem total de itens numa partição especificada no armazenamento de tabelas.

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
 
Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize os seguintes atributos para configurar um enlace de entrada de tabela:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Construtor do atributo utiliza o nome da tabela, a chave de partição e a chave de linha. Ele pode ser usado num parâmetro de saída ou no valor de retorno da função, conforme mostrado no exemplo a seguir:

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

  Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo a seguir:

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

  Para obter um exemplo completo, consulte Introdução - C# exemplo.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Proporciona outra forma de especificar a conta de armazenamento a utilizar. O construtor obtém o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no nível de classe, método ou parâmetro. O exemplo seguinte mostra o nível de classe e método:

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

A conta de armazenamento a utilizar é determinada pela seguinte ordem:

* O `Table` do atributo `Connection` propriedade.
* O `StorageAccount` atributo aplicado para o mesmo parâmetro como o `Table` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` aplicado à classe de atributo.
* A conta de armazenamento predefinida para a aplicação de função (definição de aplicação "AzureWebJobsStorage").

## <a name="input---java-annotations"></a>Introdução - anotações de Java

Na [biblioteca de tempo de execução de funções do Java](/java/api/overview/azure/functions/runtime), utilize o `@TableInput` anotação em parâmetros cujo valor virá do armazenamento de tabelas.  Esta anotação pode ser utilizada com tipos nativos de Java, POJOs ou valores anuláveis usando opcional<T>. 

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Table` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `table`. Esta propriedade é definida automaticamente quando criar o enlace no portal do Azure.|
|**direção** | n/d | Tem de ser definido como `in`. Esta propriedade é definida automaticamente quando criar o enlace no portal do Azure. |
|**name** | n/d | O nome da variável que representa a tabela ou entidade no código de função. | 
|**tableName** | **TableName** | O nome da tabela.| 
|**partitionKey** | **PartitionKey** |Opcional. A chave de partição da entidade de tabela para ler. Consulte a [utilização](#input---usage) secção para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | Opcional. A chave de linha da entidade de tabela para ler. Consulte a [utilização](#input---usage) secção para obter orientações sobre como utilizar esta propriedade.| 
|**take** |**Take** | Opcional. O número máximo de entidades para ler em JavaScript. Consulte a [utilização](#input---usage) secção para obter orientações sobre como utilizar esta propriedade.| 
|**filter** |**Filtro** | Opcional. Uma expressão de filtro de OData para a tabela de entrada em JavaScript. Consulte a [utilização](#input---usage) secção para obter orientações sobre como utilizar esta propriedade.| 
|**ligação** |**ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para essa ligação. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o runtime das funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o runtime das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação com o nome `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Introdução - utilização

O enlace de entrada de armazenamento de tabela suporta os seguintes cenários:

* **Leia uma linha em c# ou c# script**

  Definir `partitionKey` e `rowKey`. Acessar os dados de tabela com um parâmetro de método `T <paramName>`. No script c# `paramName` é o valor especificado no `name` propriedade da *Function*. `T` Normalmente, é um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. O `filter` e `take` propriedades não são utilizadas neste cenário. 

* **Ler uma ou mais linhas em c# ou c# script**

  Acessar os dados de tabela com um parâmetro de método `IQueryable<T> <paramName>`. No script c# `paramName` é o valor especificado no `name` propriedade da *Function*. `T` tem de ser um tipo que implementa `ITableEntity` ou deriva de `TableEntity`. Pode usar `IQueryable` métodos para fazer qualquer filtragem necessário. O `partitionKey`, `rowKey`, `filter`, e `take` propriedades não são utilizadas neste cenário.  

  > [!NOTE]
  > `IQueryable` Não é suportado no [tempo de execução do funções v2](functions-versions.md). Uma alternativa é [utilizar um parâmetro de método paramName CloudTable](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela com o SDK de armazenamento do Azure. Se tentar fazer a ligação `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência ao [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x).

* **Ler uma ou mais linhas em JavaScript**

  Definir o `filter` e `take` propriedades. Não defina `partitionKey` ou `rowKey`. Aceder a entrada de tabela entidade (ou entidades) usando `context.bindings.<name>`. Os objetos de serialização anulados têm `RowKey` e `PartitionKey` propriedades.

## <a name="output"></a>Saída

Utilize uma saída de armazenamento de tabelas do Azure de enlace para escrever entidades a uma tabela de uma conta de armazenamento do Azure.

> [!NOTE]
> Este enlace de saída não suporta a atualização de entidades existentes. Utilize o `TableOperation.Replace` operação [do SDK de armazenamento do Azure](https://docs.microsoft.com/azure/cosmos-db/table-storage-how-to-use-dotnet#replace-an-entity) para atualizar uma entidade existente.   

## <a name="output---example"></a>Saída - exemplo

Veja o exemplo de idioma específico:

* [C#](#output---c-example)
* [Script do c# (.csx)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Saída - exemplo do c#

A exemplo a seguir mostra um [função c#](functions-dotnet-class-library.md) que utiliza um acionador HTTP para escrever uma única linha da tabela. 

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

O exemplo seguinte mostra uma saída de tabela de enlace num *Function* ficheiro e [script c#](functions-reference-csharp.md) código que utiliza o enlace. A função escreve várias entidades da tabela.

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

O exemplo seguinte mostra uma saída de tabela de enlace num *Function* ficheiro e [ F# script](functions-reference-fsharp.md) código que utiliza o enlace. A função escreve várias entidades da tabela.

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

O exemplo seguinte mostra uma saída de tabela de enlace num *Function* ficheiro e uma [função JavaScript](functions-reference-node.md) que utiliza o enlace. A função escreve várias entidades da tabela.

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

Na [bibliotecas de classes do c#](functions-dotnet-class-library.md), utilize o [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Construtor do atributo leva o nome da tabela. Pode ser utilizado num `out` parâmetro ou o valor de retorno da função, conforme mostrado no exemplo a seguir:

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

Pode definir o `Connection` propriedade para especificar a conta de armazenamento a utilizar, conforme mostrado no exemplo a seguir:

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

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento ao nível de classe, método ou parâmetro. Para obter mais informações, consulte [entrada de-atributos](#input---attributes).

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de ligação definida no *Function* ficheiro e o `Table` atributo.

|propriedade de Function | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de ser definido como `table`. Esta propriedade é definida automaticamente quando criar o enlace no portal do Azure.|
|**direção** | n/d | Tem de ser definido como `out`. Esta propriedade é definida automaticamente quando criar o enlace no portal do Azure. |
|**name** | n/d | O nome da variável no código de função que representa a tabela ou entidade. Definido como `$return` para referenciar o valor de retorno da função.| 
|**tableName** |**TableName** | O nome da tabela.| 
|**partitionKey** |**PartitionKey** | A chave de partição da entidade de tabela para escrever. Consulte a [secção utilização](#output---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | A chave de linha da entidade de tabela para escrever. Consulte a [secção utilização](#output---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**ligação** |**ligação** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para essa ligação. Se o nome da definição de aplicação começa com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` para "MyStorage", o runtime das funções procura uma definição de aplicação com o nome "AzureWebJobsMyStorage." Se deixar `connection` vazio, o runtime das funções utiliza a cadeia de ligação de armazenamento predefinida na definição da aplicação com o nome `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

O armazenamento de tabelas de saída enlace suporta os seguintes cenários:

* **Escrever uma linha em qualquer linguagem**

  No c# e de script c#, aceder a entidade de tabela de saída com um parâmetro de método como `out T paramName` ou a função de valor de retorno. No script c# `paramName` é o valor especificado no `name` propriedade da *Function*. `T` pode ser qualquer tipo serializável, se a chave de partição e a chave de linha são fornecidos pela *Function* ficheiro ou o `Table` atributo. Caso contrário, `T` tem de ser um tipo que inclui `PartitionKey` e `RowKey` propriedades. Neste cenário, `T` normalmente, implementa `ITableEntity` ou deriva de `TableEntity`, mas ele não precisa.

* **Escreva uma ou mais linhas no C# ou C# script**

  No c# e de script c#, aceder a entidade de tabela de saída com um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName`. No script c# `paramName` é o valor especificado no `name` propriedade da *Function*. `T` Especifica o esquema das entidades que pretende adicionar. Normalmente, `T` deriva `TableEntity` ou implementa `ITableEntity`, mas ele não precisa. A chave de partição e a linha valores da chave *Function* ou o `Table` construtor de atributo não são utilizados neste cenário.

  Uma alternativa é usar um `CloudTable` parâmetro do método escrever para a tabela com o SDK de armazenamento do Azure. Se tentar fazer a ligação `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência ao [a versão correta do SDK de armazenamento](#azure-storage-sdk-version-in-functions-1x). Para obter um exemplo de código que liga `CloudTable`, veja os exemplos de enlace de entrada para [c#](#input---c-example---cloudtable) ou [script c#](#input---c-script-example---cloudtable) no início deste artigo.

* **Escrever uma ou mais linhas em JavaScript**

  Nas funções de JavaScript, aceder à tabela de saída usando `context.bindings.<name>`.

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de retorno

| Vínculo | Referência |
|---|---|
| Tabela | [Códigos de erro de tabela](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabela, fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabela, fila | [Resolução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções do Azure acionadores e enlaces](functions-triggers-bindings.md)
