---
title: Introdução ao armazenamento de tabelas usando o Visual Studio (serviços de nuvem)
description: Como começar a usar o armazenamento de tabelas do Azure em um projeto de serviço de nuvem no Visual Studio depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c42d65b5e2c46fcdbe1b0725f2ebce881722db3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299988"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de tabelas do Azure e aos serviços conectados do Visual Studio (projetos de serviços de nuvem)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a usar o armazenamento de tabelas do Azure no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento do Azure em um projeto de serviços de nuvem usando a caixa de diálogo **Adicionar serviços conectados** do Visual Studio. A operação **Adicionar serviços conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure em seu projeto e adiciona a cadeia de conexão para a conta de armazenamento aos arquivos de configuração do projeto.

O serviço de armazenamento de tabelas do Azure permite que você armazene grandes quantidades de dados estruturados. O serviço é um repositório de armazenamento NoSQL que aceita chamadas autenticadas de dentro e fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Para começar, primeiro você precisa criar uma tabela em sua conta de armazenamento. Mostraremos como criar uma tabela do Azure no código e também como executar operações básicas de tabela e entidade, como adicionar, modificar, ler e ler entidades de tabela. Os exemplos são escritos em C\# código e usam a [biblioteca de cliente armazenamento do Microsoft Azure para .net](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**Observação:** Algumas das APIs que executam chamadas para o armazenamento do Azure são assíncronas. Consulte [programação assíncrona com Async e Await](https://msdn.microsoft.com/library/hh191443.aspx) para obter mais informações. O código a seguir pressupõe que os métodos de programação assíncrona estão sendo usados.

* Consulte Introdução [ao armazenamento de tabelas do Azure usando o .net](../storage/storage-dotnet-how-to-use-tables.md) para obter mais informações sobre como manipular tabelas programaticamente.
* Consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.
* Consulte a [documentação dos serviços de nuvem](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços de nuvem do Azure.
* Consulte [ASP.net](https://www.asp.net) para obter mais informações sobre a programação de aplicativos ASP.net.

## <a name="access-tables-in-code"></a>Acessar tabelas no código
Para acessar tabelas em projetos de serviço de nuvem, você precisa incluir os seguintes itens em C# todos os arquivos de origem que acessam o armazenamento de tabelas do Azure.

1. Verifique se as declarações de namespace na parte superior do C# arquivo incluem as instruções **using** .
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Obtenha um objeto **CloudStorageAccount** que representa as informações da sua conta de armazenamento. Use o código a seguir para obter a cadeia de conexão de armazenamento e as informações da conta de armazenamento da configuração do serviço do Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Use todo o código acima na frente do código nos exemplos a seguir.
   > 
   > 
3. Obtenha um objeto **CloudTableClient** para fazer referência aos objetos de tabela em sua conta de armazenamento.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Obtenha um objeto de referência **cloudtable** para fazer referência a uma tabela e entidades específicas.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Criar uma tabela no código
Para criar a tabela do Azure, basta adicionar uma chamada para **CreateIfNotExistsAsync** ao depois de obter um objeto **cloudtable** , conforme descrito na seção "acessar tabelas no código".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. O código a seguir define uma classe de entidade chamada **CustomerEntity** que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição.

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

As operações de tabela que envolvem entidades são feitas usando o objeto **cloudtable** que você criou anteriormente em "acessar tabelas no código". O objeto **TableOperation** representa a operação a ser feita. O exemplo de código a seguir mostra como criar um objeto **cloudtable** e um objeto **CustomerEntity** . Para preparar a operação, um **TableOperation** é criado para inserir a entidade Customer na tabela. Por fim, a operação é executada chamando **cloudtable. ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Você pode inserir várias entidades em uma tabela em uma única operação de gravação. O exemplo de código a seguir cria dois objetos de entidade ("Jeff Smith" e "Ben Smith"), adiciona-os a um objeto **TableBatchOperation** usando o método Insert e, em seguida, inicia a operação chamando **cloudtable. ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades em uma partição
Para consultar uma tabela para todas as entidades em uma partição, use um objeto **TableQuery** . O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

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


## <a name="get-a-single-entity"></a>Obter uma única entidade
Você pode escrever uma consulta para obter uma única entidade específica. O código a seguir usa um objeto **TableOperation** para especificar um cliente chamado ' Ben Smith '. Esse método retorna apenas uma entidade, em vez de uma coleção, e o valor retornado em **TableResult. Result** é um objeto **CustomerEntity** . Especificar as chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade do serviço **tabela** .

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Eliminar uma entidade
Você pode excluir uma entidade depois de encontrá-la. O código a seguir procura uma entidade de cliente chamada "Ben Smith" e, se ela encontrá-la, a exclui.

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

## <a name="next-steps"></a>Passos seguintes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

