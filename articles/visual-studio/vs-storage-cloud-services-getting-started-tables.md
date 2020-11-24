---
title: Começa com o armazenamento de mesas usando o Visual Studio (serviços em nuvem)
description: Como começar a usar o armazenamento da Azure Table num projeto de serviço em nuvem em Visual Studio depois de se ligar a uma conta de armazenamento usando serviços conectados visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e5e687b172b49fec5f77615e332d0a2204162c43
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545796"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução aos serviços ligados (projetos de serviços cloud) Armazenamento de Tabelas do Azure e o Visual Studio
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a usar o armazenamento de mesa Azure no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento Azure num projeto de serviços em nuvem utilizando o diálogo visual Studio **Add Connected Services.** A operação **Add Connected Services** instala os pacotes NuGet apropriados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento aos ficheiros de configuração do projeto.

O serviço de armazenamento Azure Table permite armazenar grandes quantidades de dados estruturados. O serviço é uma loja de dados NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Para começar, primeiro precisa de criar uma tabela na sua conta de armazenamento. Vamos mostrar-lhe como criar uma tabela Azure em código, e também como realizar operações básicas de tabela e entidade, tais como adicionar, modificar, ler e ler entidades de tabela. As amostras estão escritas em código C \# e utilizam a biblioteca de [clientes microsoft Azure Storage para .NET](/previous-versions/azure/dn261237(v=azure.100)).

**NOTA:** Algumas das APIs que realizam chamadas para o armazenamento do Azure são assíncronas. Consulte [a programação Asynchronous com Async e aguarde](/previous-versions/hh191443(v=vs.140)) mais informações. O código abaixo pressupõe que estão a ser utilizados métodos de programação async.

* Consulte [Começar com o armazenamento da tabela Azure usando .NET](../cosmos-db/tutorial-develop-table-dotnet.md) para obter mais informações sobre tabelas de manipulação programática.
* Consulte [a documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o Armazenamento Azure.
* Consulte [a documentação dos Serviços cloud](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços na nuvem Azure.
* Consulte [ASP.NET](https://www.asp.net) para obter mais informações sobre a programação ASP.NET aplicações.

## <a name="access-tables-in-code"></a>Tabelas de acesso em código
Para aceder a tabelas em projetos de serviços em nuvem, é necessário incluir os seguintes itens em quaisquer ficheiros de origem C# que acedam ao armazenamento da mesa Azure.

1. Certifique-se de que as declarações de espaço de nome no topo do ficheiro C# incluem estas **declarações.**
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. Obtenha um objeto **CloudStorageAccount** que represente as informações da sua conta de armazenamento. Utilize o seguinte código para obter as informações da conta de ligação de armazenamento e armazenamento da configuração do serviço Azure.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage account name>
    _AzureStorageConnectionString"));
    ```
   > [!NOTE]
   > Utilize todo o código acima em frente ao código nas seguintes amostras.
   > 
   > 
3. Obtenha um objeto **CloudTableClient** para fazer referência aos objetos de mesa na sua conta de armazenamento.
   
    ```csharp
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```
4. Obtenha um objeto de referência **CloudTable** para referência a uma tabela e entidades específicas.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    CloudTable peopleTable = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Criar uma tabela em código
Para criar a tabela Azure, basta adicionar uma chamada ao **CreateIfNotExistsAsync** para a seguinte a obter um objeto **CloudTable,** conforme descrito na secção "Tabelas de acesso em código".

```csharp
// Create the CloudTable if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. O código que se segue define uma classe de entidade chamada **CustomerEntity** que utiliza o primeiro nome do cliente como chave de linha e o último nome como chave de partição.

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

As operações de mesa envolvendo entidades são feitas usando o objeto **CloudTable** que criou anteriormente em "Tabelas de acesso em código". O objeto **TableOperation** representa a operação a ser feita. O exemplo de código que se segue mostra como criar um objeto **CloudTable** e um objeto **Da Entidade do Cliente.** Para preparar a operação, é criada uma **TableOperation** para inserir a entidade do cliente na tabela. Finalmente, a operação é executada chamando **CloudTable.ExebonitoAsync**.

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
Pode inserir várias entidades numa tabela numa única operação de escrita. O exemplo de código a seguir cria dois objetos de entidade ("Jeff Smith" e "Ben Smith"), adiciona-os a um objeto **TableBatchOperation** utilizando o método Insert, e inicia a operação chamando **CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades em uma divisória
Para consultar uma tabela para todas as entidades numa divisória, utilize um objeto **TableQuery.** O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

```csharp
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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

return View();
```


## <a name="get-a-single-entity"></a>Obtenha uma única entidade
Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código utiliza um objeto **TableOperation** para especificar um cliente chamado 'Ben Smith'. Este método devolve apenas uma entidade, em vez de uma coleção, e o valor devolvido em **TableResult.Result** é um objeto **da Entidade do Cliente.** Especificar as teclas de divisória e de linha numa consulta é a forma mais rápida de recuperar uma única entidade do serviço **Table.**

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
Pode apagar uma entidade depois de a encontrar. O seguinte código procura uma entidade cliente chamada "Ben Smith", e se o encontrar, elimina-o.

```csharp
// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

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