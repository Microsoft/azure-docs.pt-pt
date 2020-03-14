---
title: Azure Cosmos DB Table API usando .NET Standard SDK
description: Saiba como armazenar e consultar os dados estruturados na conta API da Tabela API da Tabela Azure Cosmos DB
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: f4d6e1bb0d5db0dbfc30e14abc58321bce8d0baf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238455"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Inicie com o armazenamento da Mesa API e da Mesa Azure Azure azure azure com o .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Pode utilizar o armazenamento de Mesa API ou Mesa Azure Azure Azure para armazenar dados estruturados no NoSQL na nuvem, fornecendo uma loja chave/atributo com um design sem esquemas. Como o Azure Cosmos DB Table API e o armazenamento de mesa são menos esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. Pode utilizar a API de Mesa DD Do Azure Cosmos ou o armazenamento da tabela para armazenar conjuntos de dados flexíveis, tais como dados de utilizadores para aplicações web, livros de endereços, informações do dispositivo ou outros tipos de metadados que o seu serviço necessita. 

Este tutorial descreve uma amostra que mostra como usar a Biblioteca de [Mesa DB Microsoft Azure Cosmos para .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) com cenários de armazenamento de API e Mesa Azure Cosmos. Deve utilizar a ligação específica ao serviço Azure. Estes cenários são C# explorados utilizando exemplos que ilustram como criar tabelas, inserir/atualizar dados, consultar dados e apagar as tabelas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este exemplo com êxito, precisa do seguinte:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB Table Library para .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - Esta biblioteca está atualmente disponível para .NET Standard e .NET. 

* [Conta API da Tabela Azure Cosmos DB.](create-table-dotnet.md#create-a-database-account)

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Criar um projeto de consola .NET

No Visual Studio, crie uma nova aplicação de consola .NET. Os seguintes passos mostram-lhe como criar uma aplicação de consola no Visual Studio 2019. Pode utilizar a Biblioteca de Mesa SB Do Azure Cosmos em qualquer tipo de aplicação .NET, incluindo um serviço de nuvem Azure ou aplicação web, e aplicações de desktop e mobile. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

1. Selecione **Ficheiro** > **Novo** > **Projeto**.

1. Escolha **a App consola (.NET Core)** e, em seguida, selecione **Next**.

1. No campo de **nome do Projeto,** insira um nome para a sua aplicação, como **CosmosTableSamples**. (Pode fornecer um nome diferente, se necessário.)

1. Selecione **Criar**.

Todos os exemplos de código nesta amostra podem ser adicionados ao método principal do ficheiro **Program.cs** da aplicação da consola.

## <a name="install-the-required-nuget-package"></a>Instale o pacote NuGet necessário

Para obter o pacote NuGet, siga estes passos:

1. Clique com o botão direito do rato no projeto no **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**.

1. Procure online por [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), [`Microsoft.Extensions.Configuration`, ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json), [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) e selecione **Instalar** para instalar a Microsoft Azure Cosmos DB Table Library.

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

1. A partir do [portal Azure,](https://portal.azure.com/)navegue até à sua conta Azure Cosmos ou à conta de Armazenamento de Mesa. 

1. Abra o painel de teclas **de ligação** ou de **acesso.** Utilize o botão de cópia à direita da janela, para copiar a **CADEIA DE LIGAÇÃO PRIMÁRIA**.

   ![Ver e copiar a CADEIA DE LIGAÇÃO PRIMÁRIA no painel Cadeia de Ligação](./media/create-table-dotnet/connection-string.png)
   
1. Para configurar a sua cadeia de ligação, a partir do estúdio visual clique no seu projeto **CosmosTableSamples**.

1. **Selecione Adicionar** e, em seguida, **Novo Item**. Crie um novo ficheiro **Definições.json** com o tipo de ficheiro como Ficheiro de **Configuração TypeScript JSON.** 

1. Substitua o código no ficheiro Definições.json pelo seguinte código e atribua a sua cadeia de ligação primária:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Clique no seu projeto **CosmosTableSamples**. Selecione **Adicionar,** **Novo Item** e adicione uma classe chamada **AppSettings.cs**.

1. Adicione o seguinte código ao ficheiro AppSettings.cs. Este ficheiro lê a cadeia de ligação do ficheiro Definições.json e atribui-a ao parâmetro de configuração:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Parse e validar os detalhes da ligação 

1. Clique no seu projeto **CosmosTableSamples**. Selecione **Adicionar,** **Novo Item** e adicione uma classe chamada **Common.cs**. Escreverá código para validar os detalhes da ligação e criar uma tabela dentro desta classe.

1. Defina um método `CreateStorageAccountFromConnectionString` como mostrado abaixo. Este método analisará os detalhes das cordas de ligação e validará que os dados do nome da conta e da chave da conta fornecidos no ficheiro "Definições.json" são válidos. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Criar uma Tabela 

A classe [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) permite-lhe obter tabelas e entidades armazenadas no Table Storage. Como não temos tabelas na conta Cosmos DB Table API, vamos adicionar o método `CreateTableAsync` à classe **Common.cs** para criar uma tabela:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

Se obtém um erro de "exceção indisponível do serviço 503", é possível que as portas necessárias para o modo de conectividade sejam bloqueadas por uma firewall. Para corrigir este problema, abra as portas necessárias ou utilize a conectividade do modo gateway, como mostrado no seguinte código:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definir a entidade 

Entidades mapeiam objetos C# utilizando uma classe personalizada derivada da [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade.

Clique no seu projeto **CosmosTableSamples**. **Selecione Adicionar,** **Nova Pasta** e nomeá-lo como **Modelo**. Dentro da pasta Modelo adicione uma classe chamada **CustomerEntity.cs** e adicione-lhe o seguinte código.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Este código define uma classe de entidade que usa o primeiro nome do cliente como chave de linha e apelido como chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam-na de forma exclusiva na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que entidades com chaves de partição diferentes, mas a utilização de chaves de partição diversificadas permite uma maior escalabilidade de operações paralelas. As entidades a serem armazenadas em mesas devem ser de tipo suportado, por exemplo, derivados da classe [TableEntity.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) As propriedades de entidade que pretende armazenar numa tabela têm de ser propriedades públicas do tipo e suportar a introdução e a definição de valores. Além disso, o tipo de entidade deve expor um construtor sem parâmetros.

## <a name="insert-or-merge-an-entity"></a>Inserir ou fundir uma entidade

O seguinte exemplo de código cria um objeto de entidade e adiciona-o à tabela. O método InsertOrMerge dentro da classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) é utilizado para inserir ou fundir uma entidade. O método [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) é chamado para executar a operação. 

Clique no seu projeto **CosmosTableSamples**. **Selecione Adicionar,** **Novo Item** e adicione uma classe chamada **SamplesUtils.cs**. Esta classe armazena todo o código necessário para realizar operações de CRUD nas entidades. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>Obtenha uma entidade de uma partição

Pode obter entidade a partir de uma partição utilizando o método Recuperar sob a classe [TableOperation.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) O exemplo de código que se segue obtém a chave da linha da chave de partição, o e-mail e o número de telefone de uma entidade cliente. Este exemplo também imprime as unidades de pedido consumidas para consultar a entidade. Para consultar uma entidade, anexar o seguinte código a **SamplesUtils.cs** ficheiro: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Eliminar uma entidade

Pode facilmente eliminar uma entidade depois de a ter obtido através do mesmo padrão mostrado para atualizar uma entidade. O código seguinte obtém e elimina uma entidade de cliente. Para eliminar uma entidade, anexar o seguinte código a **SamplesUtils.cs** ficheiro: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Executar as operações do CRUD em dados de amostra

Depois de definir os métodos para criar tabela, inserir ou fundir entidades, executar estes métodos nos dados da amostra. Para isso, clique no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe chamada **BasicSamples.cs** e adicione-lhe o seguinte código. Este código cria uma tabela, acrescenta-lhe entidades. Se desejar eliminar a entidade e a tabela no final do projeto, remova os comentários dos métodos `table.DeleteIfExistsAsync()` e `SamplesUtils.DeleteEntityAsync(table, customer)` do seguinte código:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

O código anterior cria uma tabela que começa com "demo" e o GUID gerado é anexado ao nome da tabela. Em seguida, adiciona uma entidade cliente com o primeiro e último nome como "Harp Walter" e posteriormente atualiza o número de telefone deste utilizador. 

Neste tutorial, construiu código para realizar operações básicas de CRUD nos dados armazenados na conta API da Tabela. Também pode realizar operações avançadas como – inserir dados de lote, consultar todos os dados dentro de uma divisória, consultar uma série de dados dentro de uma divisória, listas na conta cujos nomes começam com o prefixo especificado. Você pode baixar o formulário completo de amostra [azure-cosmos-table-dotnet-core-getting-start-start-start-start](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub repositório. A [](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) AdvancedSamples.cs classe tem mais operações que pode realizar nos dados.  

## <a name="run-the-project"></a>Executar o projeto

Do seu projeto **CosmosTableSamples.** Abra a classe chamada **Program.cs** e adicione-lhe o seguinte código para chamar BasicSamples quando o projeto for executado.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Agora construa a solução e pressione f5 para executar o projeto. Quando o projeto estiver em execução, verá a seguinte saída no pedido de comando:

![Saída do pedido de comando](./media/tutorial-develop-table-standard/output-from-sample.png)

Se receber um erro que diga que o ficheiro Definições.json não pode ser encontrado ao executar o projeto, pode resolvê-lo adicionando a seguinte entrada XML nas definições do projeto. Clique à direita no CosmosTableSamples, selecione Editar CosmosTableSamples.csproj e adicione o seguinte itemGroup: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Agora pode entrar no portal Azure e verificar se os dados existem na tabela. 

![Resultados no portal](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Passos seguintes

Agora pode passar para o próximo tutorial e aprender a migrar dados para a conta API da Tabela API da Tabela Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar dados](../cosmos-db/table-import.md)
