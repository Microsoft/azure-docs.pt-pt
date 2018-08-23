---
title: Como começar a utilizar com o Visual Studio e o armazenamento de tabelas ligadas serviços (ASP.NET Core) | Documentos da Microsoft
description: Serviços ligados de como começar com o armazenamento de tabelas do Azure num projeto ASP.NET Core no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ms.openlocfilehash: 9a9fe0a92f8a8eadbd72ae56303348413a9d10c3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057376"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Serviços ligados de como começar com o armazenamento de tabelas do Azure e o Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Este artigo descreve como começar a utilizar o armazenamento de tabelas do Azure no Visual Studio depois de ter criado ou referenciados uma conta de armazenamento do Azure num projeto do ASP.NET Core, utilizando o Visual Studio **serviços ligados** funcionalidade. O **serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto. (Consulte [documentação do armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.)

O serviço de armazenamento de tabelas do Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e fora da cloud do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Para obter mais informações sobre como utilizar o Table storage do Azure, consulte [introdução ao armazenamento de tabelas do Azure com o .NET](../storage/storage-dotnet-how-to-use-tables.md).

Para começar a utilizar, crie primeiro uma tabela na sua conta de armazenamento. Este artigo, em seguida, mostra como criar uma tabela no c# e como realizar operações de tabela básico, como adicionar, modificar, ler e remover as entradas da tabela.  O código utiliza a biblioteca de cliente de armazenamento do Azure para .NET. Para obter mais informações sobre o ASP.NET, consulte [ASP.NET](http://www.asp.net).

Algumas das APIs de armazenamento do Azure são assíncronas e o código neste artigo supõe que métodos async que estão a ser utilizados. Ver [programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-tables-in-code"></a>Tabelas de acesso no código

Para aceder a tabelas em projetos do ASP.NET Core, precisa incluir os seguintes itens para c# ficheiros de origem que acede ao armazenamento de tabelas do Azure.

1. Adicione as informações necessárias `using` instruções:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Obter um `CloudStorageAccount` objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código, usando o nome da sua conta de armazenamento e a chave da conta, que pode ser encontrado na cadeia de ligação de armazenamento no appSettings:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Obter um `CloudTableClient` objeto para referenciar os objetos de tabela na sua conta de armazenamento:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obter um `CloudTable` objeto de referência para fazer referência a uma tabela específica e entidades:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Criar uma tabela no código

Para criar a tabela do Azure, crie um método async e dentro da mesma, chame `CreateIfNotExistsAsync()`:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. O código a seguir define uma classe de entidade chamada `CustomerEntity` que utiliza o nome próprio do cliente como a chave de linha e o apelido como a chave de partição.

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

Tabela operações que envolvem entidades utilização a `CloudTable` objeto criado anteriormente na [aceder a tabelas no código](#access-tables-in-code). O `TableOperation` objeto representa a operação a ser feito. O exemplo de código seguinte mostra como criar uma `CloudTable` objeto e um `CustomerEntity` objeto. Para preparar a operação, um `TableOperation` é criado para inserir a entidade customer na tabela. Por fim, a operação é executada chamando `CloudTable.ExecuteAsync`.

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

Pode inserir várias entidades numa tabela numa operação de escrita única. O exemplo de código seguinte cria dois objetos de entidade ("Jeff Smith" e "Bernardo Santos"), adiciona-os para uma `TableBatchOperation` objeto usando o `Insert` método, e, em seguida, inicia a operação com a chamada `CloudTable.ExecuteBatchAsync`.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades numa partição

Para consultar uma tabela para todas as entidades numa partição, utilize um `TableQuery` objeto. O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

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

Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código utiliza um `TableOperation` objeto para especificar um cliente com o nome "Bernardo Santos". O método devolve apenas uma entidade, em vez de uma coleção e o valor devolvido em `TableResult.Result` é um `CustomerEntity` objeto. Especificar chaves de partição e de linha numa consulta é a forma mais rápida de obter uma única entidade do `Table` serviço.

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

Pode eliminar uma entidade depois de encontrá-lo. O código a seguir procura e elimina uma entidade de cliente "Bernardo Santos" com o nome:

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
