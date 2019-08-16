---
title: Como começar a usar o armazenamento de tabelas e os serviços conectados do Visual Studio (ASP.NET Core) | Microsoft Docs
description: Como começar a usar o armazenamento de tabelas do Azure em um projeto ASP.NET Core no Visual Studio depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 7ac610e96d84568b7973f288623730ea1677ceac
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510932"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Como começar a usar o armazenamento de tabelas do Azure e os serviços conectados do Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Este artigo descreve como começar a usar o armazenamento de tabelas do Azure no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento do Azure em um projeto ASP.NET Core usando o recurso **Serviços conectados** do Visual Studio. A operação de **Serviços conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure em seu projeto e adiciona a cadeia de conexão para a conta de armazenamento aos arquivos de configuração do projeto. (Consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.)

O serviço de armazenamento de tabelas do Azure permite que você armazene grandes quantidades de dados estruturados. O serviço é um armazenamento de dados NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Para obter mais informações gerais sobre como usar o armazenamento de tabelas do Azure, consulte Introdução [ao armazenamento de tabelas do Azure usando o .net](../storage/storage-dotnet-how-to-use-tables.md).

Para começar, primeiro crie uma tabela em sua conta de armazenamento. Este artigo mostra como criar uma tabela no C# e como executar operações básicas de tabela, como adicionar, modificar, ler e remover entradas de tabela.  O código usa a biblioteca de cliente de armazenamento do Azure para .NET. Para obter mais informações sobre ASP.NET, consulte [ASP.net](https://www.asp.net).

Algumas das APIs de armazenamento do Azure são assíncronas, e o código neste artigo pressupõe que os métodos assíncronos estejam sendo usados. Consulte [programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-tables-in-code"></a>Acessar tabelas no código

Para acessar tabelas em projetos ASP.NET Core, você precisa incluir os itens a seguir em todos C# os arquivos de origem que acessam o armazenamento de tabelas do Azure.

1. Adicione as instruções `using` necessárias:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Obtenha um `CloudStorageAccount` objeto que representa as informações da sua conta de armazenamento. Use o código a seguir, usando o nome da sua conta de armazenamento e a chave de conta, que você pode encontrar na cadeia de conexão de armazenamento em appSettings. JSON:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Obtenha um `CloudTableClient` objeto para fazer referência aos objetos de tabela em sua conta de armazenamento:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha um `CloudTable` objeto de referência para fazer referência a uma tabela e entidades específicas:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Criar uma tabela no código

Para criar a tabela do Azure, crie um método assíncrono e, em seguida `CreateIfNotExistsAsync()`, chame:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, você cria uma classe que define as propriedades de sua entidade. O código a seguir define uma classe de `CustomerEntity` entidade chamada que usa o primeiro nome do cliente como a chave de linha e o sobrenome como a chave de partição.

```csharp
public class CustomerEntity : TableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

As operações de tabela que envolvem entidades usam o `CloudTable` objeto que você criou anteriormente em [acessar tabelas no código](#access-tables-in-code). O `TableOperation` objeto representa a operação a ser feita. O exemplo de código a seguir mostra como criar `CloudTable` um objeto e `CustomerEntity` um objeto. Para preparar a operação, um `TableOperation` é criado para inserir a entidade Customer na tabela. Por fim, a operação é executada chamando `CloudTable.ExecuteAsync`.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades

Você pode inserir várias entidades em uma tabela em uma única operação de gravação. O exemplo de código a seguir cria dois objetos de entidade ("Jeff Smith" e "Ben Smith"), adiciona `TableBatchOperation` -os a `Insert` um objeto usando o método e, em seguida `CloudTable.ExecuteBatchAsync`, inicia a operação chamando.

```csharp
// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
await peopleTable.ExecuteBatchAsync(batchOperation);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades em uma partição

Para consultar uma tabela para todas as entidades em uma partição, use um `TableQuery` objeto. O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
TableContinuationToken token = null;
do
{
    TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
    token = resultSegment.ContinuationToken;

    foreach (CustomerEntity entity in resultSegment.Results)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
    }
} while (token != null);
```

## <a name="get-a-single-entity"></a>Obter uma única entidade

Você pode escrever uma consulta para obter uma única entidade específica. O código a seguir usa `TableOperation` um objeto para especificar um cliente chamado ' Ben Smith '. O método retorna apenas uma entidade, em vez de uma coleção, e o valor retornado `TableResult.Result` em é `CustomerEntity` um objeto. Especificar as chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade `Table` do serviço.

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Eliminar uma entidade

Você pode excluir uma entidade depois de encontrá-la. O código a seguir procura e exclui uma entidade de cliente denominada "Ben Smith":

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation and then execute it.
if (deleteEntity != null)
{
   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

   // Execute the operation.
   await peopleTable.ExecuteAsync(deleteOperation);

   Console.WriteLine("Entity deleted.");
}

else
   Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
