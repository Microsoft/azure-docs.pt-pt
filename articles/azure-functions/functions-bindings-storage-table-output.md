---
title: Encadernações de saída de armazenamento de mesa Azure para funções Azure
description: Compreenda como utilizar as ligações de saída de armazenamento da tabela Azure em Funções Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 825a336b5ad07f50d96b19036d70c759b907bd07
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209684"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Encadernações de saída de armazenamento de mesa Azure para funções Azure

Utilize uma ligação de saída de armazenamento da tabela Azure para escrever entidades a uma tabela numa conta de Armazenamento Azure.

> [!NOTE]
> Esta vinculação de saída não suporta a atualização das entidades existentes. Utilize a `TableOperation.Replace` operação [a partir da Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) para atualizar uma entidade existente.

## <a name="example"></a>Exemplo

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

A secção [de configuração](#configuration) explica estas propriedades.

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

A secção [de configuração](#configuration) explica estas propriedades.

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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O exemplo a seguir demonstra como escrever várias entidades para uma tabela a partir de uma função.

Configuração de encadernação em _function.jsem_:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Código PowerShell em _run.ps1:_

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
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

---

## <a name="attributes-and-annotations"></a>Atributos e anotações

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

Para um exemplo completo, consulte o [exemplo C#](#example).

Pode utilizar o `StorageAccount` atributo para especificar a conta de armazenamento no nível de classe, método ou parâmetro. Para obter mais informações, consulte [Input - atributos](./functions-bindings-storage-table-input.md#attributes-and-annotations).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Os atributos não são suportados pelo Script C#.

# <a name="java"></a>[Java](#tab/java)

Na biblioteca de [funções Java,](/java/api/overview/azure/functions/runtime)utilize a anotação [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) nos parâmetros para escrever valores no armazenamento de mesa.

Veja o [exemplo para mais detalhes.](#example)

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
|**direção** | n/a | Deve ser definido para `out` . Esta propriedade é definida automaticamente quando cria a ligação no portal Azure. |
|**nome** | n/a | O nome variável usado no código de função que representa a tabela ou entidade. Definir `$return` para referenciar o valor de retorno da função.| 
|**nome de mesa** |**TableName** | O nome da mesa.| 
|**partitionKey** |**PartitionKey** | A chave de partição da entidade de mesa para escrever. Consulte a [secção de utilização](#usage) para obter orientações sobre como utilizar esta propriedade.| 
|**rowKey** |**RowKey** | A chave de linha da entidade de mesa para escrever. Consulte a [secção de utilização](#usage) para obter orientações sobre como utilizar esta propriedade.| 
|**conexão** |**Ligação** | O nome de uma definição de aplicação que contém o fio de ligação de armazenamento para usar para esta ligação. Se o nome de definição da aplicação começar com "AzureWebJobs", pode especificar apenas o restante do nome aqui. Por exemplo, se definir `connection` "MyStorage", o tempo de execução de Funções procura uma configuração de aplicação que se chama "MyStorage". Se deixar `connection` vazio, o tempo de execução das funções utiliza a cadeia de ligação de armazenamento predefinido na definição da aplicação que está denominada `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Utilização

# <a name="c"></a>[C#](#tab/csharp)

Aceda à entidade de tabela de saída utilizando um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclua o e `PartitionKey` `RowKey` propriedades. Estas propriedades são frequentemente acompanhadas pela implementação `ITableEntity` ou `TableEntity` herdação.

Em alternativa, pode utilizar um `CloudTable` parâmetro de método para escrever à mesa utilizando o Azure Storage SDK. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Aceda à entidade de tabela de saída utilizando um parâmetro de método `ICollector<T> paramName` ou `IAsyncCollector<T> paramName` onde `T` inclua o e `PartitionKey` `RowKey` propriedades. Estas propriedades são frequentemente acompanhadas pela implementação `ITableEntity` ou `TableEntity` herdação. O `paramName` valor é especificado na propriedade defunction.js`name` *em*.

Em alternativa, pode utilizar um `CloudTable` parâmetro de método para escrever à mesa utilizando o Azure Storage SDK. Se tentar ligar-se `CloudTable` e obter uma mensagem de erro, certifique-se de que tem uma referência à [versão SDK de armazenamento correta](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x).

# <a name="java"></a>[Java](#tab/java)

Existem duas opções para a saída de uma linha de armazenamento de mesa a partir de uma função, utilizando a anotação [TableStorageOutput:](/java/api/com.microsoft.azure.functions.annotation.tableoutput)

- **Valor de retorno**: Aplicando a anotação à função em si, o valor de retorno da função é persistido como uma linha de armazenamento de mesa.

- **Imperativo**: Para definir explicitamente o valor da mensagem, aplique a anotação a um parâmetro específico do [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) tipo, onde `T` inclui o e as `PartitionKey` `RowKey` propriedades. Estas propriedades são frequentemente acompanhadas pela implementação `ITableEntity` ou `TableEntity` herdação.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Aceda ao evento de saída utilizando `context.bindings.<name>` onde está o valor especificado na propriedade defunction.js`<name>` `name` *em*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para escrever para os dados de tabela, utilize o `Push-OutputBinding` cmdlet, descreva o `-Name TableBinding` parâmetro e o parâmetro iguais aos `-Value` dados da linha. Consulte o [exemplo PowerShell](#example) para obter mais detalhes.

# <a name="python"></a>[Python](#tab/python)

Existem duas opções para a saída de uma mensagem de linha de armazenamento de mesa a partir de uma função:

- **Valor de retorno**: Desa estafunction.js`name` o *imóvel.* `$return` Com esta configuração, o valor de retorno da função é persistido como uma linha de armazenamento de mesa.

- **Imperativo**: Passe um valor ao método [definido](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none) do parâmetro declarado como um tipo [out.](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) O valor passado `set` é persistido como uma mensagem do Event Hub.

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
