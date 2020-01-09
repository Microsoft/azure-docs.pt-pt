---
title: Azure Cosmos DB API de Tabela usando o SDK do .NET Standard
description: Saiba como armazenar e consultar os dados estruturados na conta Azure Cosmos DB API de Tabela
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: 7341d86e68986a2e502f714a4dcf1f882200d384
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441071"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Introdução ao Azure Cosmos DB API de Tabela e ao armazenamento de tabelas do Azure usando o SDK do .NET

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Você pode usar o Azure Cosmos DB API de Tabela ou o armazenamento de tabelas do Azure para armazenar dados NoSQL estruturados na nuvem, fornecendo um repositório de chaves/atributos com um esquema com menos design. Como Azure Cosmos DB API de Tabela e o armazenamento de tabelas são menos esquemas, é fácil adaptar seus dados à medida que as necessidades de seu aplicativo evoluem. Você pode usar Azure Cosmos DB API de Tabela ou o armazenamento de tabelas para armazenar conjuntos de dados flexíveis, como os de usuário para aplicativos Web, catálogos de endereços, informações de dispositivo ou outros tipos de metadados que seu serviço requer. 

Este tutorial descreve um exemplo que mostra como usar o [Microsoft Azure Cosmos DB biblioteca de tabelas para .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) com Azure Cosmos DB API de tabela e cenários de armazenamento de tabelas do Azure. Você deve usar a conexão específica para o serviço do Azure. Esses cenários são explorados usando C# exemplos que ilustram como criar tabelas, inserir/atualizar dados, consultar dados e excluir as tabelas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este exemplo com êxito, precisa do seguinte:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure biblioteca de tabelas CosmosDB para .net](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) -esta biblioteca está disponível no momento para .net Standard e .NET Framework. 

* [Azure Cosmos DB API de tabela conta](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Criar um projeto de console .NET

No Visual Studio, crie um novo aplicativo de console .NET. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2019. Você pode usar a biblioteca de tabelas Azure Cosmos DB em qualquer tipo de aplicativo .NET, incluindo um serviço de nuvem do Azure ou aplicativo Web, e aplicativos móveis e de desktop. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

1. Selecione **Ficheiro** > **Novo** > **Projeto**.

1. Escolha **aplicativo de console (.NET Core)** e, em seguida, selecione **Avançar**.

1. No campo **nome do projeto** , insira um nome para seu aplicativo, como **CosmosTableSamples**. (Você pode fornecer um nome diferente, conforme necessário.)

1. Selecione **Criar**.

Todos os exemplos de código neste exemplo podem ser adicionados ao método Main () do arquivo **Program.cs** do seu aplicativo de console.

## <a name="install-the-required-nuget-package"></a>Instalar o pacote NuGet necessário

Para obter o pacote NuGet, siga estas etapas:

1. Clique com o botão direito do rato no projeto no **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**.

1. Pesquise online `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json``Microsoft.Extensions.Configuration.Binder` e selecione **instalar** para instalar a biblioteca de tabelas do Microsoft Azure Cosmos DB.

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

1. No [portal do Azure](https://portal.azure.com/), navegue até sua conta do cosmos do Azure ou a conta de armazenamento de tabela. 

1. Abra a **cadeia de conexão** ou o painel **chaves de acesso** . Utilize o botão de cópia à direita da janela, para copiar a **CADEIA DE LIGAÇÃO PRIMÁRIA**.

   ![Ver e copiar a CADEIA DE LIGAÇÃO PRIMÁRIA no painel Cadeia de Ligação](./media/create-table-dotnet/connection-string.png)
   
1. Para configurar a cadeia de conexão, no Visual Studio, clique com o botão direito do mouse no seu projeto **CosmosTableSamples**.

1. Selecione **Adicionar** e **novo item**. Crie um novo arquivo **Settings. JSON** com o tipo de arquivo como arquivo de **configuração JSON do TypeScript** . 

1. Substitua o código no arquivo Settings. JSON pelo código a seguir e atribua sua cadeia de conexão primária:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Clique com o botão direito do mouse no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **novo item** e adicione uma classe chamada **appSettings.cs**.

1. Adicione o código a seguir ao arquivo AppSettings.cs. Esse arquivo lê a cadeia de conexão do arquivo Settings. JSON e o atribui ao parâmetro de configuração:

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

## <a name="parse-and-validate-the-connection-details"></a>Analisar e validar os detalhes da conexão 

1. Clique com o botão direito do mouse no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **novo item** e adicione uma classe chamada **Common.cs**. Você escreverá o código para validar os detalhes da conexão e criar uma tabela dentro dessa classe.

1. Defina um método `CreateStorageAccountFromConnectionString` conforme mostrado abaixo. Esse método analisará os detalhes da cadeia de conexão e validará que o nome da conta e os detalhes da chave de conta fornecidos no arquivo "Settings. JSON" são válidos. 

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


## <a name="create-a-table"></a>Criar uma tabela 

A classe [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) permite-lhe obter tabelas e entidades armazenadas no Table Storage. Como não temos nenhuma tabela na conta Cosmos DB API de Tabela, vamos adicionar o método `CreateTableAsync` à classe **Common.cs** para criar uma tabela:

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

## <a name="define-the-entity"></a>Definir a entidade 

As entidades são C# mapeadas para objetos usando uma classe personalizada derivada de [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade.

Clique com o botão direito do mouse no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **nova pasta** e nomeie-o como **modelo**. Na pasta modelo, adicione uma classe chamada **CustomerEntity.cs** e adicione o código a seguir a ela.

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

Esse código define uma classe de entidade que usa o primeiro nome do cliente como a chave de linha e o sobrenome como a chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam-na de forma exclusiva na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que as entidades com chaves de partição diferentes, mas o uso de chaves de partição diversas permite uma maior escalabilidade de operações paralelas. As entidades a serem armazenadas em tabelas devem ser de um tipo com suporte, por exemplo, derivadas da classe [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity) . As propriedades de entidade que pretende armazenar numa tabela têm de ser propriedades públicas do tipo e suportar a introdução e a definição de valores. Além disso, o tipo de entidade deve expor um construtor sem parâmetros.

## <a name="insert-or-merge-an-entity"></a>Inserir ou mesclar uma entidade

O exemplo de código a seguir cria um objeto de entidade e o adiciona à tabela. O método InsertOrMerge dentro da classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) é usado para inserir ou mesclar uma entidade. O método [cloudtable. ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) é chamado para executar a operação. 

Clique com o botão direito do mouse no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **novo item** e adicione uma classe chamada **SamplesUtils.cs**. Essa classe armazena todo o código necessário para executar operações CRUD nas entidades. 

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

### <a name="get-an-entity-from-a-partition"></a>Obter uma entidade de uma partição

Você pode obter a entidade de uma partição usando o método retrieve na classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) . O exemplo de código a seguir obtém a chave de linha da chave de partição, o email e o número de telefone de uma entidade de cliente. Este exemplo também imprime as unidades de solicitação consumidas para consultar a entidade. Para consultar uma entidade, acrescente o seguinte código ao arquivo **SamplesUtils.cs** : 

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

Pode facilmente eliminar uma entidade depois de a ter obtido através do mesmo padrão mostrado para atualizar uma entidade. O código seguinte obtém e elimina uma entidade de cliente. Para excluir uma entidade, acrescente o seguinte código ao arquivo **SamplesUtils.cs** : 

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

## <a name="execute-the-crud-operations-on-sample-data"></a>Executar as operações CRUD nos dados de exemplo

Depois de definir os métodos para criar tabelas, inserir ou mesclar entidades, execute esses métodos nos dados de exemplo. Para fazer isso, clique com o botão direito do mouse no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **novo item** e adicione uma classe chamada **BasicSamples.cs** e adicione o código a seguir a ela. Esse código cria uma tabela, adiciona entidades a ela. Se você quiser excluir a entidade e a tabela no final do projeto, remova os comentários de `table.DeleteIfExistsAsync()` e `SamplesUtils.DeleteEntityAsync(table, customer)` métodos do código a seguir:

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

O código anterior cria uma tabela que começa com "demo" e o GUID gerado é acrescentado ao nome da tabela. Em seguida, ele adiciona uma entidade Customer com o nome e o sobrenome como "Harp Walter" e, posteriormente, atualiza o número de telefone desse usuário. 

Neste tutorial, você criou código para executar operações CRUD básicas nos dados armazenados na conta API de Tabela. Você também pode executar operações avançadas como, por exemplo, inserir dados em lote, consultar todos os dados em uma partição, consultar um intervalo de dados dentro de uma partição, listar tabelas na conta cujos nomes começam com o prefixo especificado. Você pode baixar o exemplo de formulário completo [Azure-Cosmos-Table-dotnet-Core-Getting-Started do repositório do](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) github. A classe [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) tem mais operações que você pode executar nos dados.  

## <a name="run-the-project"></a>Executar o projeto

Do seu projeto **CosmosTableSamples**. Abra a classe chamada **Program.cs** e adicione o código a seguir a ela para chamar BasicSamples quando o projeto for executado.

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

Agora, Compile a solução e pressione F5 para executar o projeto. Quando o projeto for executado, você verá a seguinte saída no prompt de comando:

![Saída do prompt de comando](./media/tutorial-develop-table-standard/output-from-sample.png)

Se você receber um erro informando que o arquivo Settings. JSON não pode ser encontrado ao executar o projeto, você poderá resolvê-lo adicionando a seguinte entrada XML às configurações do projeto. Clique com o botão direito do mouse em CosmosTableSamples, selecione Editar CosmosTableSamples. csproj e adicione o seguinte grupo de itens: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Agora você pode entrar no portal do Azure e verificar se os dados existem na tabela. 

![Resultados no portal](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Passos seguintes

Agora você pode prosseguir para o próximo tutorial e saber como migrar dados para Azure Cosmos DB API de Tabela conta. 

> [!div class="nextstepaction"]
>[Como consultar dados](../cosmos-db/table-import.md)
