---
title: Encadernações de armazenamento de mesa seletiva para funções azure
description: Entenda como utilizar encadernações de armazenamento de mesa seletiva seletiva em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.openlocfilehash: edeafb5730f06dac22fd9919ca42ea388d5fd0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277183"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Encadernações de armazenamento de mesa seletiva para funções azure

Este artigo explica como trabalhar com encadernações de armazenamento de mesa azure em funções Azure. A Azure Functions suporta encadernações de entrada e saída para armazenamento de mesa seletiva.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pacotes - Funções 1.x

As encadernações de armazenamento de mesa são fornecidas no pacote [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet, versão 2.x. O código fonte para o pacote está no repositório [Azur-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x-and-higher"></a>Pacotes - Funções 2.x e superiores

As encadernações de armazenamento de mesa são fornecidas no [microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet package, versão 3.x. O código fonte para o pacote está no repositório [Azur-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input

Utilize o encadernação de entrada de armazenamento da mesa Azure para ler uma tabela numa conta de Armazenamento Azure.

# <a name="c"></a>[C #](#tab/csharp)

### <a name="one-entity"></a>Uma entidade

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que lê uma única linha de mesa. Para cada registo inserido na tabela, a função será ativada.

O valor da tecla da linha "{queueTrigger}" indica que a tecla da linha vem da cadeia de mensagens de fila.

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

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) `MyPoco` que lê `TableEntity`várias linhas de mesa de onde a classe deriva de .

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

`IQueryable`não é suportado nas [funções v2 tempo](functions-versions.md)de funcionamento . Uma alternativa é `CloudTable` usar um parâmetro de método para ler a tabela utilizando o SDK de Armazenamento Azure. Aqui está um exemplo de uma função que consulta uma tabela de registo sinuosa de Funções Azure:

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

Para mais informações sobre como utilizar o CloudTable, consulte [Iniciar-se com o armazenamento da Mesa Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à versão [SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="one-entity"></a>Uma entidade

O exemplo seguinte mostra uma ligação de entrada de mesa num ficheiro *function.json* e código [de script C#](functions-reference-csharp.md) que utiliza a encadernação. A função utiliza um gatilho de fila para ler uma única linha de mesa. 

O ficheiro *função.json* especifica a `partitionKey` e a `rowKey`. O `rowKey` valor "{queueTrigger}" indica que a tecla da linha vem da cadeia de mensagens de fila.

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

A secção de [configuração](#input---configuration) explica estas propriedades.

Aqui está o código de script C#:

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

O exemplo seguinte mostra uma ligação de entrada de mesa num ficheiro *function.json* e código [de script C#](functions-reference-csharp.md) que utiliza a encadernação. A função lê as entidades para uma chave de partição especificada numa mensagem de fila.

Aqui está o ficheiro *função.json:*

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

A secção de [configuração](#input---configuration) explica estas propriedades.

O código de script C# adiciona uma referência ao SDK de `TableEntity`Armazenamento Azure para que o tipo de entidade possa derivar de:

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

`IQueryable`não é suportado nas Funções tempo de execução para [versões 2.x e superiores)](functions-versions.md). Uma alternativa é `CloudTable` usar um parâmetro de método para ler a tabela utilizando o SDK de Armazenamento Azure. Aqui está um exemplo de uma função que consulta uma tabela de registo sinuosa de Funções Azure:

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

Para mais informações sobre como utilizar o CloudTable, consulte [Iniciar-se com o armazenamento da Mesa Azure](../cosmos-db/table-storage-how-to-use-dotnet.md).

Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à versão [SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).


# <a name="javascript"></a>[JavaScript](#tab/javascript)

O exemplo seguinte mostra uma ligação de entrada de mesa num ficheiro *function.json* e [código JavaScript](functions-reference-node.md) que utiliza a ligação. A função utiliza um gatilho de fila para ler uma única linha de mesa. 

O ficheiro *função.json* especifica a `partitionKey` e a `rowKey`. O `rowKey` valor "{queueTrigger}" indica que a tecla da linha vem da cadeia de mensagens de fila.

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

A secção de [configuração](#input---configuration) explica estas propriedades.

Aqui está o código JavaScript:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="python"></a>[Pitão](#tab/python)

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

O exemplo seguinte mostra uma função ativada http que devolve uma lista de objetos pessoais que estão numa divisória especificada no armazenamento da mesa. No exemplo, a chave de partição é extraída da rota http, e o nome do quadro e a ligação são das definições de função. 

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

A anotação tableInput também pode extrair as encadernações do corpo json do pedido, como mostra o exemplo seguinte.

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

Os seguintes exemplos utilizam o Filtro para consultar pessoas com um nome específico numa Tabela Azure, e limita o número de possíveis correspondências a 10 resultados.

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

# <a name="c"></a>[C #](#tab/csharp)

 Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize os seguintes atributos para configurar uma ligação de entrada de tabela:

* [Atribuição de Tabela](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  O construtor do atributo pega no nome da mesa, na chave da divisória e na chave da linha. O atributo pode ser `out` utilizado num parâmetro ou no valor de retorno da função, como se pode ver no seguinte exemplo:

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

  Pode definir `Connection` a propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

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

  Para um exemplo completo, consulte o exemplo de Entrada - C# .

* [ArmazenamentoAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Fornece outra forma de especificar a conta de armazenamento a utilizar. O construtor tem o nome de uma definição de aplicação que contém uma cadeia de ligação de armazenamento. O atributo pode ser aplicado no parâmetro, método ou nível de classe. O exemplo que se segue mostra o nível de classe e o nível de método:

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

* A `Table` propriedade do `Connection` atributo.
* O `StorageAccount` atributo aplicado ao mesmo `Table` parâmetro que o atributo.
* O `StorageAccount` atributo aplicado à função.
* O `StorageAccount` atributo aplicado à classe.
* A conta de armazenamento padrão para a aplicação de função (definição de aplicação "AzureWebJobsStorage").

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Pitão](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de tempo de funcionamento das funções java,](/java/api/overview/azure/functions/runtime)utilize a `@TableInput` anotação em parâmetros cujo valor viria do armazenamento da mesa.  Esta anotação pode ser usada com tipos nativos de `Optional<T>`Java, POJOs ou valores nuníveis usando .

---

## <a name="input---configuration"></a>Entrada - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `Table` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de `table`ser definido para. Esta propriedade é definida automaticamente quando cria a ligação no portal Azure.|
|**direção** | n/d | Tem de `in`ser definido para. Esta propriedade é definida automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/d | O nome da variável que representa a tabela ou entidade no código de função. | 
|**tableName** | **Nome de mesa** | O nome da mesa.| 
|**partiçãoChave** | **PartitionKey** |Opcional. A chave de partição da entidade de mesa para ler. Consulte a secção de [utilização](#input---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | Opcional. A chave da fila da entidade da mesa para ler. Consulte a secção de [utilização](#input---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**tomar** |**Tomar** | Opcional. O número máximo de entidades a ler no JavaScript. Consulte a secção de [utilização](#input---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**filtro** |**Filtro** | Opcional. Uma expressão de filtro OData para entrada de mesa no JavaScript. Consulte a secção de [utilização](#input---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**conexão** |**Conexão** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se `connection` definir para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza `AzureWebJobsStorage`a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>Entrada - utilização

# <a name="c"></a>[C #](#tab/csharp)

* **Leia uma linha em**

  Conjunto `partitionKey` `rowKey`e . Aceda aos dados da tabela `T <paramName>`utilizando um parâmetro de método . No script C#, `paramName` é o `name` valor especificado na propriedade da *função.json*. `T`é tipicamente um tipo `ITableEntity` que implementa ou deriva de `TableEntity`. As `filter` `take` propriedades e propriedades não são usadas neste cenário.

* **Leia uma ou mais linhas**

  Aceda aos dados da tabela `IQueryable<T> <paramName>`utilizando um parâmetro de método . No script C#, `paramName` é o `name` valor especificado na propriedade da *função.json*. `T`deve ser um tipo `ITableEntity` que implemente ou deriva de `TableEntity`. Pode utilizar `IQueryable` métodos para fazer qualquer filtragem necessária. `partitionKey`Os, `rowKey` `filter`e `take` as propriedades não são usados neste cenário.  

  > [!NOTE]
  > `IQueryable`não é suportado nas [funções v2 tempo](functions-versions.md)de funcionamento . Uma alternativa é usar um parâmetro de parâmetro [CloudTable para](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) ler a tabela utilizando o SDK de Armazenamento Azure. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à versão [SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

* **Leia uma linha em**

  Conjunto `partitionKey` `rowKey`e . Aceda aos dados da tabela `T <paramName>`utilizando um parâmetro de método . No script C#, `paramName` é o `name` valor especificado na propriedade da *função.json*. `T`é tipicamente um tipo `ITableEntity` que implementa ou deriva de `TableEntity`. As `filter` `take` propriedades e propriedades não são usadas neste cenário.

* **Leia uma ou mais linhas**

  Aceda aos dados da tabela `IQueryable<T> <paramName>`utilizando um parâmetro de método . No script C#, `paramName` é o `name` valor especificado na propriedade da *função.json*. `T`deve ser um tipo `ITableEntity` que implemente ou deriva de `TableEntity`. Pode utilizar `IQueryable` métodos para fazer qualquer filtragem necessária. `partitionKey`Os, `rowKey` `filter`e `take` as propriedades não são usados neste cenário.  

  > [!NOTE]
  > `IQueryable`não é suportado nas [funções v2 tempo](functions-versions.md)de funcionamento . Uma alternativa é usar um parâmetro de parâmetro [CloudTable para](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) ler a tabela utilizando o SDK de Armazenamento Azure. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à versão [SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Desloque as propriedades e propriedades. `filter` `take` Não se `partitionKey` acomode ou. `rowKey` Aceda à entidade da tabela de `context.bindings.<BINDING_NAME>`entrada (ou entidades) utilizando . Os objetos desserializados têm `RowKey` e `PartitionKey` propriedades.

# <a name="python"></a>[Pitão](#tab/python)

Os dados da tabela são transmitidos para a função como uma cadeia JSON. Desserialize a mensagem `json.loads` ligando como mostrado no [exemplo](#input)da entrada .

# <a name="java"></a>[Java](#tab/java)

O atributo [tableInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableinput) dá-lhe acesso à linha da mesa que desencadeou a função.

---

## <a name="output"></a>Saída

Utilize uma ligação de saída de armazenamento de mesa Azure para escrever entidades para uma tabela numa conta de Armazenamento Azure.

> [!NOTE]
> Esta ligação de saída não suporta a atualização das entidades existentes. Utilize `TableOperation.Replace` a operação [a partir do Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) para atualizar uma entidade existente.

# <a name="c"></a>[C #](#tab/csharp)

O exemplo seguinte mostra uma [função C#](functions-dotnet-class-library.md) que usa um gatilho HTTP para escrever uma única linha de mesa. 

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

O exemplo seguinte mostra uma ligação de saída de tabela num ficheiro *function.json* e código [de script C#](functions-reference-csharp.md) que utiliza a encadernação. A função escreve várias entidades de tabela.

Aqui está o ficheiro *função.json:*

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

A secção de [configuração](#output---configuration) explica estas propriedades.

Aqui está o código de script C#:

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

O exemplo seguinte mostra uma ligação de saída de tabela num ficheiro *function.json* e uma [função JavaScript](functions-reference-node.md) que utiliza a ligação. A função escreve várias entidades de tabela.

Aqui está o ficheiro *função.json:*

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

A secção de [configuração](#output---configuration) explica estas propriedades.

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

# <a name="python"></a>[Pitão](#tab/python)

O exemplo que se segue demonstra como utilizar a ligação de saída de armazenamento da tabela. A `table` ligação está configurada na *função.json* `partitionKey`atribuindo `connection`valores a, `name` `tableName`, , e:

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

A função seguinte gera um UUI único para o `rowKey` valor e persiste a mensagem no armazenamento de mesa.

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

## <a name="output---attributes-and-annotations"></a>Saída - atributos e anotações

# <a name="c"></a>[C #](#tab/csharp)

Nas [bibliotecas da classe C#,](functions-dotnet-class-library.md)utilize o [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

O construtor do atributo tem o nome da mesa. O atributo pode ser `out` utilizado num parâmetro ou no valor de retorno da função, como se pode ver no seguinte exemplo:

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

Pode definir `Connection` a propriedade para especificar a conta de armazenamento a utilizar, como mostra o seguinte exemplo:

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

Para um exemplo completo, consulte [a saída - C# exemplo](#output).

Pode utilizar `StorageAccount` o atributo para especificar a conta de armazenamento na classe, método ou nível de parâmetro. Para mais informações, consulte [Input - atributos](#input---attributes-and-annotations).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados por C# Script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os atributos não são suportados pelo JavaScript.

# <a name="python"></a>[Pitão](#tab/python)

Os atributos não são suportados pela Python.

# <a name="java"></a>[Java](#tab/java)

Na [biblioteca de tempo de funcionamento das funções Java,](/java/api/overview/azure/functions/runtime)utilize a anotação [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) em parâmetros para escrever valores em armazenamento de mesa.

Veja o [exemplo para mais detalhes](#output).

---

## <a name="output---configuration"></a>Saída - configuração

A tabela a seguir explica as propriedades de configuração de ligação que definiu no ficheiro *função.json* e no `Table` atributo.

|propriedade fun.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Tem de `table`ser definido para. Esta propriedade é definida automaticamente quando cria a ligação no portal Azure.|
|**direção** | n/d | Tem de `out`ser definido para. Esta propriedade é definida automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/d | O nome variável utilizado no código de função que representa a tabela ou entidade. `$return` Decidiu para referenciar o valor de devolução da função.| 
|**tableName** |**Nome de mesa** | O nome da mesa.| 
|**partiçãoChave** |**PartitionKey** | A chave de partição da entidade de mesa para escrever. Consulte a secção de [utilização](#output---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | A chave da linha da entidade da mesa para escrever. Consulte a secção de [utilização](#output---usage) para obter orientações sobre como utilizar esta propriedade.| 
|**conexão** |**Conexão** | O nome de uma definição de aplicação que contém a cadeia de ligação de armazenamento a utilizar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se `connection` definir para "MyStorage", o tempo de execução das Funções procura uma definição de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de funcionamento das funções utiliza `AzureWebJobsStorage`a cadeia de ligação de armazenamento predefinida na definição da aplicação que é denominada .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Saída - utilização

# <a name="c"></a>[C #](#tab/csharp)

Aceda à entidade da tabela `ICollector<T> paramName` de `IAsyncCollector<T> paramName` `T` saída `PartitionKey` utilizando `RowKey` um parâmetro de método ou onde inclua as propriedades e propriedades. Estas propriedades são frequentemente `ITableEntity` acompanhadas `TableEntity`pela implementação ou herdação.

Em alternativa, pode `CloudTable` utilizar um parâmetro de método para escrever à mesa utilizando o SDK de Armazenamento Azure. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à versão [SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Aceda à entidade da tabela `ICollector<T> paramName` de `IAsyncCollector<T> paramName` `T` saída `PartitionKey` utilizando `RowKey` um parâmetro de método ou onde inclua as propriedades e propriedades. Estas propriedades são frequentemente `ITableEntity` acompanhadas `TableEntity`pela implementação ou herdação. O `paramName` valor é especificado na `name` propriedade da *função.json*.

Em alternativa, pode `CloudTable` utilizar um parâmetro de método para escrever à mesa utilizando o SDK de Armazenamento Azure. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à versão [SDK de armazenamento correta](#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda ao evento `context.bindings.<name>` `<name>` de saída utilizando onde `name` está o valor especificado na propriedade da *função.json*.

# <a name="python"></a>[Pitão](#tab/python)

Existem duas opções para colocar uma mensagem de linha de armazenamento de tabela a partir de uma função:

- **Valor de devolução**: Coloque a `name` propriedade em *função.json* para `$return`. Com esta configuração, o valor de retorno da função é persistido como uma linha de armazenamento de mesa.

- **Imperativo**: Passe um valor para o método [definido](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python#set-val--t-----none) do parâmetro declarado como um tipo [out.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.out?view=azure-python) O valor `set` passado é persistiu como uma mensagem do Event Hub.

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para eliminar uma linha de armazenamento de tabela sacar de uma função utilizando a anotação [TableStorageOutput:](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet)

- **Valor de devolução**: Aplicando a anotação à própria função, o valor de retorno da função é persinedo como linha de armazenamento de mesa.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding)a `T` anotação a um parâmetro específico do tipo, onde inclua as propriedades e propriedades. `PartitionKey` `RowKey` Estas propriedades são frequentemente `ITableEntity` acompanhadas `TableEntity`pela implementação ou herdação.

---

## <a name="exceptions-and-return-codes"></a>Exceções e códigos de devolução

| Enlace | Referência |
|---|---|
| Tabela | [Códigos de erro de mesa](https://docs.microsoft.com/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, Mesa, Fila | [Códigos de erro de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, Mesa, Fila | [Resolução de problemas](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre as funções azure gatilhos e encadernações](functions-triggers-bindings.md)
