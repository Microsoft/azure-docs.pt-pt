---
title: Encadernações de entrada de armazenamento de mesa Azure para funções Azure
description: Compreenda como utilizar as ligações de entrada de armazenamento da tabela Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4fc2426189384856d2d2e95887cdabd2f9e9ebea
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033783"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Encadernações de entrada de armazenamento de mesa Azure para funções Azure

Utilize a ligação de entrada de armazenamento da tabela Azure para ler uma tabela numa conta de Armazenamento Azure.

## <a name="example"></a>Exemplo

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

### <a name="cloudtable"></a>CloudTable

`CloudTable` é suportado apenas nas [Funções v2 e e tempos de execução mais elevados](functions-versions.md).

Utilize um `CloudTable` parâmetro de método para ler a tabela utilizando o Azure Storage SDK. Aqui está um exemplo de uma função que consulta uma tabela de registo de funções Azure:

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

Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

### <a name="iqueryable"></a>IQueryable

`IQueryable` só é suportado no [tempo de execução funções v1](functions-versions.md).

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

A secção [de configuração](#configuration) explica estas propriedades.

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

Para obter mais informações sobre como utilizar o CloudTable, consulte [Começar com o armazenamento da Tabela Azure.](../cosmos-db/tutorial-develop-table-dotnet.md)

Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

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

A secção [de configuração](#configuration) explica estas propriedades.

O código de script C# adiciona uma referência ao Azure Storage SDK para que o tipo de entidade possa derivar `TableEntity` de:

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

O exemplo a seguir utiliza o Filtro para consultar pessoas com um nome específico numa Tabela Azure e limita o número de correspondências possíveis a 10 resultados.

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

A secção [de configuração](#configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A seguinte função utiliza um gatilho de fila para ler uma única linha de tabela como entrada para uma função.

Neste exemplo, a configuração de encadernação especifica um valor explícito para a tabela `partitionKey` e utiliza uma expressão para passar para o `rowKey` . A `rowKey` `{queueTrigger}` expressão, indica que a tecla de linha provém da linha de mensagem da fila.

Configuração de encadernação em _function.jsem_:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "MyQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "PersonEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Código PowerShell em _run.ps1:_

```powershell
param($MyQueueItem, $PersonEntity, $TriggerMetadata)
Write-Host "PowerShell queue trigger function processed work item: $MyQueueItem"
Write-Host "Person entity name: $($PersonEntity.Name)"
```

# <a name="python"></a>[Python](#tab/python)

A seguinte função utiliza um gatilho de fila para ler uma única linha de tabela como entrada para uma função.

Neste exemplo, a configuração de encadernação especifica um valor explícito para a tabela `partitionKey` e utiliza uma expressão para passar para o `rowKey` . A `rowKey` expressão indica que a `{id}` tecla de linha vem da linha de mensagem da fila.

Configuração de ligação no _function.jsno_ ficheiro:

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

Código python no ficheiro *\_ \_ \_ \_ init .py:*

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

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

  Para um exemplo completo, consulte o exemplo C#.

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

# <a name="java"></a>[Java](#tab/java)

Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a `@TableInput` anotação em parâmetros cujo valor viria do armazenamento da Mesa.  Esta anotação pode ser usada com tipos nativos de Java, POJOs ou valores anulados usando `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os atributos não são suportados pela PowerShell.

# <a name="python"></a>[Python](#tab/python)

Os atributos não são suportados pela Python.

---

## <a name="configuration"></a>Configuração

A tabela seguinte explica as propriedades de configuração de encadernação que definiu no *function.jsno* ficheiro e no `Table` atributo.

|function.jsna propriedade | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/a | Deve ser definido para `table` . Esta propriedade é definida automaticamente quando cria a ligação no portal Azure.|
|**direção** | n/a | Deve ser definido para `in` . Esta propriedade é definida automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/a | O nome da variável que representa a tabela ou entidade no código de função. | 
|**tableName** | **Nome de mesa** | O nome da mesa.| 
|**partitionKey** | **PartitionKey** |Opcional. A chave de partição da entidade de mesa para ler. Consulte a secção [de utilização](#usage) para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | Opcional. A chave de linha da entidade de mesa para ler. Consulte a secção [de utilização](#usage) para obter orientações sobre como utilizar esta propriedade.| 
|**tomar** |**Realizar** | Opcional. O número máximo de entidades a ler no JavaScript. Consulte a secção [de utilização](#usage) para obter orientações sobre como utilizar esta propriedade.| 
|**filtro** |**Filtro** | Opcional. Uma expressão de filtro OData para entrada de tabela em JavaScript. Consulte a secção [de utilização](#usage) para obter orientações sobre como utilizar esta propriedade.| 
|**conexão** |**Ligação** | O nome de uma definição de aplicação que contém o fio de ligação de armazenamento para usar para esta ligação. A definição pode ser o nome de uma definição de aplicação pré-fixa "AzureWebJobs" ou nome de cadeia de ligação. Por exemplo, se o seu nome de definição for "AzureWebJobsMyStorage", pode especificar "MyStorage" aqui. O tempo de execução das Funções procurará automaticamente uma definição de aplicação com o nome de "AzureWebJobsMyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

* **Leia uma linha em**

  Conjunto `partitionKey` e `rowKey` . Aceda aos dados da tabela utilizando um parâmetro de `T <paramName>` método. No script C# `paramName` é o valor especificado na propriedade defunction.js`name` *em*. `T` é tipicamente um tipo que implementa `ITableEntity` ou deriva de `TableEntity` . As `filter` propriedades e propriedades não são `take` utilizadas neste cenário.

* **Leia uma ou mais filas**

  Aceda aos dados da tabela utilizando um parâmetro de `IQueryable<T> <paramName>` método. No script C# `paramName` é o valor especificado na propriedade defunction.js`name` *em*. `T` deve ser um tipo que implemente `ITableEntity` ou deriva de `TableEntity` . Pode utilizar `IQueryable` métodos para fazer qualquer filtragem necessária. Os `partitionKey` , e propriedades não são `rowKey` `filter` `take` usados neste cenário.  

  > [!NOTE]
  > `IQueryable` não é suportado no [tempo de execução funções v2](functions-versions.md). Uma alternativa é [usar um parâmetro do método cloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela utilizando o Azure Storage SDK. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

* **Leia uma linha em**

  Conjunto `partitionKey` e `rowKey` . Aceda aos dados da tabela utilizando um parâmetro de `T <paramName>` método. No script C# `paramName` é o valor especificado na propriedade defunction.js`name` *em*. `T` é tipicamente um tipo que implementa `ITableEntity` ou deriva de `TableEntity` . As `filter` propriedades e propriedades não são `take` utilizadas neste cenário.

* **Leia uma ou mais filas**

  Aceda aos dados da tabela utilizando um parâmetro de `IQueryable<T> <paramName>` método. No script C# `paramName` é o valor especificado na propriedade defunction.js`name` *em*. `T` deve ser um tipo que implemente `ITableEntity` ou deriva de `TableEntity` . Pode utilizar `IQueryable` métodos para fazer qualquer filtragem necessária. Os `partitionKey` , e propriedades não são `rowKey` `filter` `take` usados neste cenário.  

  > [!NOTE]
  > `IQueryable` não é suportado no [tempo de execução funções v2](functions-versions.md). Uma alternativa é [usar um parâmetro do método cloudTable paramName](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) para ler a tabela utilizando o Azure Storage SDK. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

O atributo [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) dá-lhe acesso à linha de mesa que desencadeou a função.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Desa estação `filter` e `take` propriedades. Não se `partitionKey` desemalte ou `rowKey` . . Aceda à entidade de tabela de entrada (ou entidades) que `context.bindings.<BINDING_NAME>` utiliza. Os objetos deserizados têm `RowKey` e `PartitionKey` propriedades.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Os dados são transmitidos para o parâmetro de entrada especificado pela `name` chave no *function.jsficheiro.* Especificar O `partitionKey` e `rowKey` permite filtrar registos específicos. Consulte o [exemplo PowerShell](#example) para obter mais detalhes.

# <a name="python"></a>[Python](#tab/python)

Os dados da tabela são transmitidos para a função como uma cadeia JSON. Des-serialize a mensagem ligando `json.loads` como mostrado no [exemplo](#example)de entrada .

---

## <a name="next-steps"></a>Passos seguintes

* [Escreva dados de armazenamento de mesa a partir de uma função](./functions-bindings-storage-table-output.md)
