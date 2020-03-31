---
title: Começar com o armazenamento de mesa usando o Visual Studio (ASP.NET Core)
description: Como começar com o armazenamento da Mesa Azure num projeto ASP.NET Core no Estúdio Visual depois de se ligar a uma conta de armazenamento usando serviços conectados do Estúdio Visual
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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d209f8117b1e061877daf2f8d316bd01ed4f84cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72298824"
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Como começar com o armazenamento de mesa Azure e serviços conectados do Estúdio Visual

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Este artigo descreve como começar a usar o armazenamento de Mesa Azure no Estúdio Visual depois de ter criado ou referenciado uma conta de armazenamento Azure num projeto ASP.NET Core utilizando a funcionalidade Visual Studio **Connected Services.** A operação **Connected Services** instala os pacotes NuGet apropriados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento aos seus ficheiros de configuração do projeto. (Consulte a [documentação](https://azure.microsoft.com/documentation/services/storage/) de armazenamento para obter informações gerais sobre o Armazenamento de Azure.)

O serviço de armazenamento de mesa azure permite armazenar grandes quantidades de dados estruturados. O serviço é uma loja de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Para obter informações mais gerais sobre a utilização do armazenamento da mesa Azure, consulte Iniciar com [o armazenamento da Mesa Azure utilizando .NET](../storage/storage-dotnet-how-to-use-tables.md).

Para começar, crie primeiro uma mesa na sua conta de armazenamento. Este artigo mostra então como criar uma tabela em C# e como realizar operações básicas de tabela, tais como adicionar, modificar, ler e remover as entradas de mesa.  O código utiliza a Biblioteca de Clientes de Armazenamento Azure para .NET. Para mais informações sobre ASP.NET, consulte [ASP.NET](https://www.asp.net).

Algumas das APIs de Armazenamento Azure são assíncronas, e o código neste artigo pressupõe que os métodos de asincronização estão a ser usados. Consulte [a programação assíncrona](https://docs.microsoft.com/dotnet/csharp/async) para obter mais informações.

## <a name="access-tables-in-code"></a>Tabelas de acesso em código

Para aceder a tabelas em projetos ASP.NET Core, você precisa incluir os seguintes itens para quaisquer ficheiros de origem C# que acedam ao armazenamento de mesa Azure.

1. Adicione as `using` declarações necessárias:

    ```csharp
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    ```

1. Obtenha `CloudStorageAccount` um objeto que represente a informação da sua conta de armazenamento. Utilize o seguinte código, utilizando o nome da sua conta de armazenamento e a chave da conta, que pode encontrar na cadeia de ligação de armazenamento em appSettings.json:

    ```csharp
        CloudStorageAccount storageAccount = new CloudStorageAccount(
            new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
                "<name>", "<account-key>"), true);
    ```

1. Obtenha `CloudTableClient` um objeto para fazer referência aos objetos de mesa na sua conta de armazenamento:

    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Obtenha `CloudTable` um objeto de referência para fazer referência a uma tabela e entidades específicas:

    ```csharp
    // Get a reference to a table named "peopleTable"
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Criar uma tabela em código

Para criar a tabela Azure, crie um `CreateIfNotExistsAsync()`método de asincronização e dentro dela, ligue:

```csharp
async void CreatePeopleTableAsync()
{
    // Create the CloudTable if it does not exist
    await peopleTable.CreateIfNotExistsAsync();
}
```
    
## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, cria-se uma classe que define as propriedades da sua entidade. O código seguinte define uma `CustomerEntity` classe de entidade chamada que usa o primeiro nome do cliente como chave de linha e apelido como chave de partição.

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

As operações de `CloudTable` mesa envolvendo entidades utilizam o objeto que criou anteriormente nas tabelas de [Acesso em código.](#access-tables-in-code) O `TableOperation` objeto representa a operação a ser feita. O exemplo de código que `CloudTable` se `CustomerEntity` segue mostra como criar um objeto e um objeto. Para preparar a `TableOperation` operação, é criado um para inserir a entidade cliente na tabela. Finalmente, a operação é `CloudTable.ExecuteAsync`executada através da chamada.

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

Pode inserir várias entidades numa tabela numa única operação de escrita. O exemplo de código que se segue cria dois objetos de `TableBatchOperation` entidade `Insert` ("Jeff Smith" e `CloudTable.ExecuteBatchAsync`"Ben Smith"), adiciona-os a um objeto usando o método, e depois inicia a operação através da chamada .

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

Para consultar uma mesa para todas as entidades `TableQuery` numa partição, use um objeto. O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

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

Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código `TableOperation` utiliza um objeto para especificar um cliente chamado 'Ben Smith'. O método devolve apenas uma entidade, em vez `TableResult.Result` de `CustomerEntity` uma coleção, e o valor devolvido é um objeto. Especificar as teclas de partição e de remo numa consulta `Table` é a forma mais rápida de recuperar uma única entidade do serviço.

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

Pode apagar uma entidade depois de a encontrar. O seguinte código procura e elimina uma entidade cliente chamada "Ben Smith":

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

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
