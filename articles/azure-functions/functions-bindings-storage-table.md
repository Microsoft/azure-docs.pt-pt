---
title: Encadernações de armazenamento de mesa Azure para funções Azure
description: Compreenda como utilizar as ligações de armazenamento da tabela Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 50706e1b525a3e3a39701ef2135d44c02c35077e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89181141"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Encadernações de armazenamento de mesa Azure para funções Azure

Este artigo explica como trabalhar com as ligações de armazenamento da tabela Azure em Funções Azure. As Funções Azure suportam encadernações de entrada e saída para o armazenamento da tabela Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As ligações de armazenamento da tabela são fornecidas no pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x. O código-fonte para o pacote está no [repositório azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superior

As ligações de armazenamento da tabela são fornecidas no pacote [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet, versão 3.x. O código-fonte para o pacote está no [repositório azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input

Utilize a ligação de entrada de armazenamento da tabela Azure para ler uma tabela numa conta de Armazenamento Azure.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Uma entidade

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê uma única fila de tabela. Para cada mensagem enviada para a fila, a função será ativada.

O valor da chave da linha "{queueTrigger}" indica que a tecla de linha provém da linha de mensagem de fila.

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

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que lê várias filas de tabelas de onde a `MyPoco` classe provém `TableEntity` .

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

`IQueryable` só é suportado no [tempo de execução funções v1](functions-versions.md). Uma alternativa é utilizar um `CloudTable` parâmetro de método para ler a tabela utilizando o Azure Storage SDK. Aqui está um exemplo de uma função que consulta uma tabela de registo de funções Azure:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
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

Para obter mais informações sobre como utilizar o CloudTable, consulte [Começar com o armazenamento da Tabela Azure.](../cosmos-db/tutorial-develop-table-dotnet.md)

Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="one-entity"></a>Uma entidade

O exemplo a seguir mostra uma ligação de entrada de tabela numa *function.jsno* ficheiro e código de [script C#](functions-reference-csharp.md) que utiliza a ligação. A função utiliza um gatilho de fila para ler uma única linha de tabela. 

O *function.jsno* ficheiro especifica a e a `partitionKey` `rowKey` . O `rowKey` valor "{queueTrigger}" indica que a tecla de linha provém da linha de mensagem de fila.

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

A secção [de configuração](#input---configuration) explica estas propriedades.

Aqui está o código do guião C:

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

O exemplo a seguir mostra uma ligação de entrada de tabela numa *function.jsno* ficheiro e código de [script C#](functions-reference-csharp.md) que utiliza a ligação. A função lê entidades para uma chave de partição que é especificada numa mensagem de fila.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#input---configuration) explica estas propriedades.

O código de script C# adiciona uma referência ao Azure Storage SDK para que o tipo de entidade possa derivar `TableEntity` de:

```csharp
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
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

`IQueryable` não é suportado no tempo de execução de Funções para [versões 2.x e superior)](functions-versions.md). Uma alternativa é utilizar um `CloudTable` parâmetro de método para ler a tabela utilizando o Azure Storage SDK. Aqui está um exemplo de uma função que consulta uma tabela de registo de funções Azure:

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
#r "Microsoft.Azure.Cosmos"
using Microsoft.Azure.Cosmos.Table;
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

Para obter mais informações sobre como utilizar o CloudTable, consulte [Começar com o armazenamento da Tabela Azure.](../cosmos-db/tutorial-develop-table-dotnet.md)

Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação de entrada de tabela numa *function.jsno* ficheiro e [código JavaScript](functions-reference-node.md) que utiliza a ligação. A função utiliza um gatilho de fila para ler uma única linha de tabela. 

O *function.jsno* ficheiro especifica a e a `partitionKey` `rowKey` . O `rowKey` valor "{queueTrigger}" indica que a tecla de linha provém da linha de mensagem de fila.

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

A secção [de configuração](#input---configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

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

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função desencadeada HTTP que devolve uma lista de objetos pessoais que estão numa partição especificada no armazenamento da tabela. No exemplo, a chave de partição é extraída da rota http, e o nome de mesa e a ligação são das definições de função. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

A anotação TableInput também pode extrair as encadernações do corpo json do pedido, como mostra o exemplo a seguir.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Os exemplos a seguir utilizam o Filtro para consultar pessoas com um nome específico numa Tabela Azure e limita o número de correspondências possíveis a 10 resultados.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

---

## <a name="input---attributes-and-annotations"></a>Entrada - atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

 Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar uma ligação de entrada de tabela:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  O construtor do atributo tem o nome da mesa, a chave de partição e a chave da linha. O atributo pode ser utilizado num `out` parâmetro ou no valor de retorno da função, como mostra o seguinte exemplo:

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

  Pode definir a `Connection` propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

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

  Para um exemplo completo, consulte o exemplo de Entrada - C#.

* [ArmazenamentoCotaçãotribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra forma de especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado ao nível do parâmetro, método ou classe. O exemplo a seguir mostra o nível de classe e o nível do método:

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

A conta de armazenamento a utilizar é determinada na seguinte ordem:

* Propriedade `Table` do `Connection` atributo.
* O `StorageAccount` atributo aplicado ao mesmo parâmetro que o `Table` atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento predefinida para a aplicação de função (definição de aplicação "AzureWebJobsStorage").

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a `@TableInput` anotação em parâmetros cujo valor viria do armazenamento da Mesa.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores anulados usando `Optional<T>` .

---

## <a name="input---configuration"></a>Entrada - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Table` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `table` . Esta propriedade é definida automaticamente quando cria a ligação no portal Azure.|
|**direção** | n/a | Deve ser definido para `in` . Esta propriedade é definida automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/a | O nome da variável que representa a tabela ou entidade no código de função. | 
|**tableName** | **Nome de mesa** | O nome da mesa.| 
|**partitionKey** | **PartitionKey** |Opcional. A chave de partição da entidade de mesa para ler. Consulte a secção [de utilização](#input---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | Opcional. A chave de linha da entidade de mesa para ler. Consulte a secção [de utilização](#input---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**take** |**Tomar** | Opcional. O número máximo de entidades a ler no JavaScript. Consulte a secção [de utilização](#input---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**filtro** |**Filtro** | Opcional. Uma expressão de filtro OData para entrada de tabela em JavaScript. Consulte a secção [de utilização](#input---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**conexão** |**Ligação** | O nome de uma definição de aplicação que contém o fio de ligação de armazenamento para usar para esta ligação. A definição pode ser o nome de uma definição de aplicação pré-fixa "AzureWebJobs" ou nome de cadeia de ligação. Por exemplo, se o seu nome de definição for "AzureWebJobsMyStorage", pode especificar "MyStorage" aqui. O tempo de execução das Funções procurará automaticamente uma definição de aplicação com o nome de "AzureWebJobsMyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - utilização

# <a name="c"></a>[C#](#tab/csharp)

* **Leia uma linha em**

  Conjunto `partitionKey` e `rowKey` . Aceda aos dados da tabela utilizando um parâmetro de `T <paramName>` método. No script C# `paramName` é o valor especificado na propriedade defunction.js`name` * em*. `T` é tipicamente um tipo que implementa `ITableEntity` ou deriva de `TableEntity` . As `filter` propriedades e propriedades não são `take` utilizadas neste cenário.

* **Leia uma ou mais filas**

  Aceda aos dados da tabela utilizando um parâmetro de `IQueryable<T> <paramName>` método. No script C# `paramName` é o valor especificado na propriedade defunction.js`name` * em*. `T` deve ser um tipo que implemente `ITableEntity` ou deriva de `TableEntity` . Pode utilizar `IQueryable` métodos para fazer qualquer filtragem necessária. Os `partitionKey` , e propriedades não são `rowKey` `filter` `take` usados neste cenário.  

  > [!NOTE]
  > `IQueryable` não é suportado no [tempo de execução funções v2](functions-versions.md). Uma alternativa é [usar um parâmetro do método cloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela utilizando o Azure Storage SDK. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

* **Leia uma linha em**

  Conjunto `partitionKey` e `rowKey` . Aceda aos dados da tabela utilizando um parâmetro de `T <paramName>` método. No script C# `paramName` é o valor especificado na propriedade defunction.js`name` * em*. `T` é tipicamente um tipo que implementa `ITableEntity` ou deriva de `TableEntity` . As `filter` propriedades e propriedades não são `take` utilizadas neste cenário.

* **Leia uma ou mais filas**

  Aceda aos dados da tabela utilizando um parâmetro de `IQueryable<T> <paramName>` método. No script C# `paramName` é o valor especificado na propriedade defunction.js`name` * em*. `T` deve ser um tipo que implemente `ITableEntity` ou deriva de `TableEntity` . Pode utilizar `IQueryable` métodos para fazer qualquer filtragem necessária. Os `partitionKey` , e propriedades não são `rowKey` `filter` `take` usados neste cenário.  

  > [!NOTE]
  > `IQueryable` não é suportado no [tempo de execução funções v2](functions-versions.md). Uma alternativa é [usar um parâmetro do método cloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela utilizando o Azure Storage SDK. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Desa estação `filter` e `take` propriedades. Não se `partitionKey` desemalte ou `rowKey` . . Aceda à entidade de tabela de entrada (ou entidades) que `context.bindings.<BINDING_NAME>` utiliza. Os objetos deserizados têm `RowKey` e `PartitionKey` propriedades.

# <a name="python"></a>[Python](#tab/python)

Os dados da tabela são transmitidos para a função como uma cadeia JSON. Des-serialize a mensagem ligando `json.loads` como mostrado no [exemplo](#input)de entrada .

# <a name="java"></a>[Java](#tab/java)

O atributo [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) dá-lhe acesso à linha de mesa que desencadeou a função.

---

## <a name="output"></a>Saída

Utilize uma ligação de saída de armazenamento da tabela Azure para escrever entidades a uma tabela numa conta de Armazenamento Azure.

> [!NOTE]
> Esta vinculação de saída não suporta a atualização das entidades existentes. Utilize a `TableOperation.Replace` operação [a partir da Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) para atualizar uma entidade existente.

# <a name="c"></a>[C#](#tab/csharp)

O exemplo a seguir mostra uma [função C#](functions-dotnet-class-library.md) que utiliza um gatilho HTTP para escrever uma única linha de tabela. 

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

O exemplo a seguir mostra uma ligação de saída de tabela numa *function.jsno* ficheiro e código de [script C#](functions-reference-csharp.md) que utiliza a ligação. A função escreve várias entidades de tabela.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#output---configuration) explica estas propriedades.

Aqui está o código do guião C:

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo a seguir mostra uma ligação de saída de tabela numa *function.jsno* ficheiro e numa [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve várias entidades de tabela.

Aqui está a *function.jsarquivada:*

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

A secção [de configuração](#output---configuration) explica estas propriedades.

Aqui está o código JavaScript:

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

# <a name="python"></a>[Python](#tab/python)

O exemplo a seguir demonstra como utilizar a ligação de saída de armazenamento da tabela. A `table` ligação é configurada no *function.js,* atribuindo valores `name` `tableName` `partitionKey` a, e `connection` :

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

A seguinte função gera um UUI único pelo `rowKey` valor e persiste a mensagem no armazenamento de mesa.

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

# <a name="java"></a>[Java](#tab/java)

O exemplo a seguir mostra uma função Java que usa um gatilho HTTP para escrever uma única linha de tabela.

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

O exemplo a seguir mostra uma função Java que usa um gatilho HTTP para escrever várias linhas de tabela.

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

## <a name="output---attributes-and-annotations"></a>Saída - atributos e anotações

# <a name="c"></a>[C#](#tab/csharp)

Nas [bibliotecas de classe C,](functions-dotnet-class-library.md)utilize o [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

O construtor do atributo tem o nome da mesa. O atributo pode ser utilizado num `out` parâmetro ou no valor de retorno da função, como mostra o seguinte exemplo:

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

Pode definir a `Connection` propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

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

Para um exemplo completo, consulte [o exemplo de Saída - C#](#output).

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Input - atributos](#input---attributes-and-annotations).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a anotação [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) nos parâmetros para escrever valores no armazenamento de mesa.

Veja o [exemplo para mais detalhes.](#output)

---

## <a name="output---configuration"></a>Saída - configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Table` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `table` . Esta propriedade é definida automaticamente quando cria a ligação no portal Azure.|
|**direção** | n/a | Deve ser definido para `out` . Esta propriedade é definida automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/a | O nome variável usado no código de função que representa a tabela ou entidade. Definir `$return` para referenciar o valor de retorno da função.| 
|**tableName** |**Nome de mesa** | O nome da mesa.| 
|**partitionKey** |**PartitionKey** | A chave de partição da entidade de mesa para escrever. Consulte a [secção de utilização](#output---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | A chave de linha da entidade de mesa para escrever. Consulte a [secção de utilização](#output---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**conexão** |**Ligação** | O nome de uma definição de aplicação que contém o fio de ligação de armazenamento para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` "MyStorage", o tempo de execução de Funções procura uma configuração de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

# <a name="c"></a>[C#](#tab/csharp)

Aceda à entidade de tabela de saída utilizando um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclua o e `PartitionKey` `RowKey` propriedades. Estas propriedades são frequentemente acompanhadas pela implementação `ITableEntity` ou `TableEntity` herdação.

Em alternativa, pode utilizar um `CloudTable` parâmetro de método para escrever à mesa utilizando o Azure Storage SDK. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Aceda à entidade de tabela de saída utilizando um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclua o e `PartitionKey` `RowKey` propriedades. Estas propriedades são frequentemente acompanhadas pela implementação `ITableEntity` ou `TableEntity` herdação. O `paramName` valor é especificado na propriedade defunction.js`name` * em*.

Em alternativa, pode utilizar um `CloudTable` parâmetro de método para escrever à mesa utilizando o Azure Storage SDK. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda ao evento de saída utilizando `context.bindings.<name>` onde está o valor especificado na propriedade defunction.js`<name>` `name` * em*.

# <a name="python"></a>[Python](#tab/python)

Existem duas opções para a saída de uma mensagem de linha de armazenamento de mesa a partir de uma função:

- **Valor de retorno**: Desa estafunction.js`name` o *imóvel.* `$return` Com esta configuração, o valor de retorno da função é persistido como uma linha de armazenamento de mesa.

- **Imperativo**: Passe um valor ao método [definido](/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out.](/python/api/azure-functions/azure.functions.out?view=azure-python) O valor passado `set` é persistido como uma mensagem do Event Hub.

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para a saída de uma linha de armazenamento de mesa a partir de uma função, utilizando a anotação [TableStorageOutput:](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet)

- **Valor de retorno**: Aplicando a anotação à função em si, o valor de retorno da função é persistido como uma linha de armazenamento de mesa.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) tipo, onde `T` inclui o e as `PartitionKey` `RowKey` propriedades. Estas propriedades são frequentemente acompanhadas pela implementação `ITableEntity` ou `TableEntity` herdação.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| Tabela | [Códigos de erro de tabela](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Mesa, Fila | [Códigos de erro de armazenamento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Mesa, Fila | [Resolução de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções Azure desencadeia e encaderna](functions-triggers-bindings.md)
