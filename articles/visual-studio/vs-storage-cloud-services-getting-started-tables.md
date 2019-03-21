---
title: Introdução ao armazenamento de tabelas e o Visual Studio (serviços cloud) de serviços ligados | Documentos da Microsoft
description: Serviços ligados de como começar a utilizar o armazenamento de tabelas do Azure num projeto de serviço em nuvem no Visual Studio depois de ligar a uma conta de armazenamento com o Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: cec8ab9d678ff559176580fa8eccc261f449f4c5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012893"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introdução ao armazenamento de tabelas do Azure e o Visual Studio ligados (projetos de serviços cloud) de serviços
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral
Este artigo descreve como começar a utilizar o armazenamento de tabelas do Azure no Visual Studio depois de ter criado ou referenciados uma conta de armazenamento do Azure num projeto de serviços em nuvem utilizando o Visual Studio **adicionar serviços ligados** caixa de diálogo. O **adicionar serviços ligados** operação instala os pacotes de NuGet adequados para aceder ao armazenamento do Azure no seu projeto e adiciona a cadeia de ligação para a conta de armazenamento para ficheiros de configuração do projeto.

O serviço de armazenamento de tabelas do Azure permite-lhe armazenar grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e fora da cloud do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Para começar, tem primeiro de criar uma tabela na sua conta de armazenamento. Vamos mostrar-lhe como criar uma tabela do Azure no código bem como realizar operações de entidade, como adicionar, modificar, ler e entidades da tabela de leitura e de tabela básica. Os exemplos são escritos em C\# de código e utilizar o [biblioteca de clientes de armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**NOTA:** Algumas das APIs que realizam chamadas de saída para o armazenamento do Azure são assíncronas. Ver [programação assíncrona com Async e Await](https://msdn.microsoft.com/library/hh191443.aspx) para obter mais informações. O código abaixo parte do princípio de métodos de programação assíncrona estão a ser utilizados.

* Ver [introdução ao armazenamento de tabelas do Azure com o .NET](../storage/storage-dotnet-how-to-use-tables.md) para obter mais informações sobre a manipulação programática de tabelas.
* Ver [documentação do armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.
* Ver [documentação de serviços Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços cloud do Azure.
* Ver [ASP.NET](https://www.asp.net) para obter mais informações sobre a programação de aplicativos do ASP.NET.

## <a name="access-tables-in-code"></a>Tabelas de acesso no código
Para aceder a tabelas em projetos de serviço cloud, terá de incluir os seguintes itens para c# ficheiros de origem que acede ao armazenamento de tabelas do Azure.

1. Certifique-se de que as declarações de namespace na parte superior do arquivo c# incluem-las **usando** instruções.
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Obter um **CloudStorageAccount** objeto que representa as suas informações de conta de armazenamento. Utilize o seguinte código para obter a cadeia de ligação de armazenamento e as informações de conta de armazenamento da configuração do serviço do Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Utilize todo o código acima na frente o código nos exemplos a seguir.
   > 
   > 
3. Obter um **CloudTableClient** objeto para referenciar os objetos de tabela na sua conta de armazenamento.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Obter um **CloudTable** objeto de referência para fazer referência a uma tabela específica e entidades.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Criar uma tabela no código
Para criar a tabela do Azure, basta adicionar uma chamada para **CreateIfNotExistsAsync** para o depois de obter um **CloudTable** objeto conforme descrito na secção "Tabelas no código de acesso".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade. O código a seguir define uma classe de entidade chamada **CustomerEntity** que utiliza o nome próprio do cliente como a chave de linha e o apelido como a chave de partição.

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

As operações de tabela que envolvem entidades são realizadas com o **CloudTable** objeto que criou anteriormente no "Tabelas no Access no código." O **TableOperation** objeto representa a operação a ser feito. O exemplo de código seguinte mostra como criar uma **CloudTable** objeto e um **CustomerEntity** objeto. Para preparar a operação, uma **TableOperation** é criado para inserir a entidade customer na tabela. Por fim, a operação é executada chamando **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Pode inserir várias entidades numa tabela numa operação de escrita única. O exemplo de código seguinte cria dois objetos de entidade ("Jeff Smith" e "Bernardo Santos"), adiciona-os para uma **TableBatchOperation** usando o método de inserção de objeto e, em seguida, inicia a operação com a chamada  **CloudTable.ExecuteBatchAsync**.

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

## <a name="get-all-of-the-entities-in-a-partition"></a>Obter todas as entidades numa partição
Para consultar uma tabela para todas as entidades numa partição, utilize um **TableQuery** objeto. O exemplo de código seguinte especifica um filtro para as entidades em que “Santos” é a chave de partição. Este exemplo imprime os campos de cada entidade nos resultados da consulta para a consola.

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
Pode escrever uma consulta para obter uma entidade única e específica. O seguinte código utiliza um **TableOperation** objeto para especificar um cliente com o nome "Bernardo Santos". Este método devolve apenas uma entidade, em vez de uma coleção e o valor devolvido em **Tableresult** é um **CustomerEntity** objeto. Especificar chaves de partição e de linha numa consulta é a forma mais rápida de obter uma única entidade a partir da **tabela** serviço.

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
Pode eliminar uma entidade depois de encontrá-lo. O código a seguir procura por uma entidade de cliente "Bernardo Santos" com o nome e, se considerá-lo, elimina-lo.

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

## <a name="next-steps"></a>Passos Seguintes
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

