---
title: Azure Cosmos DB Table API usando .NET Standard SDK
description: Saiba como armazenar e consultar os dados estruturados na conta API da Tabela DB da Azure Cosmos
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: 6e1dc5f0016c27d987361aa52e59682806a31c95
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118921"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Introdução ao armazenamento de API de Tabela do Azure Cosmos DB e Tabelas do Azure com o SDK .NET

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Pode utilizar o armazenamento API da Tabela AZure Cosmos ou Azure Table para armazenar dados NoSQL estruturados na nuvem, fornecendo uma loja chave/atributo com um design de esquemas a menos. Como o Azure Cosmos DB Table API e o armazenamento de mesa são menos esquemas, é fácil adaptar os seus dados à medida que as necessidades da sua aplicação evoluem. Pode utilizar a Azure Cosmos DB Table API ou o armazenamento da Tabela para armazenar conjuntos de dados flexíveis, tais como dados do utilizador para aplicações web, livros de endereços, informações de dispositivos ou outros tipos de metadados que o seu serviço necessita. 

Este tutorial descreve uma amostra que mostra como usar a [Microsoft Azure Cosmos DB Table Library para .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) com Azure Cosmos DB Table API e Azure Table storage scenarios. Deve utilizar a ligação específica ao serviço Azure. Estes cenários são explorados usando exemplos C# que ilustram como criar tabelas, inserir/atualizar dados, consultar dados e eliminar as tabelas.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este exemplo com êxito, precisa do seguinte:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure CosmosDB Table Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) - Esta biblioteca está atualmente disponível para o quadro .NET Standard e .NET. 

* [Conta Azure Cosmos DB Table API](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta da API de Tabelas do Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Criar um projeto de consola .NET

No Visual Studio, crie uma nova aplicação de consola .NET. Os passos seguintes mostram-lhe como criar uma aplicação de consola no Visual Studio 2019. Pode utilizar a Biblioteca de MesaS Azure Cosmos DB em qualquer tipo de aplicação .NET, incluindo um serviço de nuvem Azure ou aplicação web, e aplicações de desktop e mobile. Neste guia, utilizamos uma aplicação de consola pela simplicidade.

1. Selecione **o**  >  **novo**  >  **projeto de arquivo**.

1. Escolha **a App consola (.NET Core)** e, em seguida, selecione **Seguinte**.

1. No campo nome do **Projeto,** insira um nome para a sua aplicação, como **CosmosTableSamples**. (Pode fornecer um nome diferente quando necessário.)

1. Selecione **Criar**.

Todos os exemplos de código desta amostra podem ser adicionados ao método principal do ficheiro **Program.cs** da sua aplicação de consola.

## <a name="install-the-required-nuget-package"></a>Instale o pacote NuGet necessário

Para obter o pacote NuGet, siga estes passos:

1. Clique com o botão direito do rato no projeto no **Explorador de Soluções** e escolha **Gerir Pacotes NuGet**.

1. Procure online por [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) , e [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) selecione **Instalar** para instalar a Microsoft Azure Cosmos DB Table Library.

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

1. A partir do [portal Azure,](https://portal.azure.com/)navegue para a sua conta Azure Cosmos ou para a conta de Armazenamento de Mesa. 

1. Abra o painel **de chaves de ligação** ou **de acesso.** Utilize o botão de cópia à direita da janela, para copiar a **CADEIA DE LIGAÇÃO PRIMÁRIA**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Ver e copiar a CADEIA DE LIGAÇÃO PRIMÁRIA no painel Cadeia de Ligação":::
   
1. Para configurar a sua cadeia de conexão, a partir do estúdio visual clique no seu projeto **CosmosTableSamples**.

1. **Selecione Adicionar** e, em seguida, **Novo Item**. Crie um novo ficheiro **Settings.js** com o tipo de ficheiro como **Ficheiro de Configuração JSON do TypeScript.** 

1. Substitua o código no Settings.jsficheiro com o seguinte código e atribua a sua cadeia de ligação primária:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Clique à direita no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe chamada **AppSettings.cs**.

1. Adicione o seguinte código ao ficheiro AppSettings.cs. Este ficheiro lê a cadeia de ligação a partir de Settings.jsno ficheiro e atribui-a ao parâmetro de configuração:

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

1. Clique à direita no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe chamada **Common.cs**. Irá escrever código para validar os detalhes da ligação e criar uma tabela dentro desta classe.

1. Defina um método `CreateStorageAccountFromConnectionString` como mostrado abaixo. Este método analisará os detalhes da cadeia de ligação e validará que o nome da conta e os dados chave da conta fornecidos no ficheiro "Settings.json" são válidos. 

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

A classe [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) permite-lhe obter tabelas e entidades armazenadas no Table Storage. Como não temos tabelas na conta API da Tabela Cosmos, vamos adicionar o `CreateTableAsync` método à classe **Common.cs** para criar uma tabela:

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

Se obtém um erro de "exceção indisponíveis de serviço 503", é possível que as portas necessárias para o modo de conectividade sejam bloqueadas por uma firewall. Para corrigir este problema, abra as portas necessárias ou utilize a conectividade do modo gateway, como indicado no seguinte código:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definir a entidade 

As entidades mapeiam para objetos C# utilizando uma classe personalizada derivada da [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Para adicionar uma entidade a uma tabela, crie uma classe que define as propriedades de entidade.

Clique à direita no seu projeto **CosmosTableSamples**. **Selecione Adicionar**, **Nova Pasta** e nomeie-a como **Modelo**. Dentro da pasta Modelo adicione uma classe chamada **CustomerEntity.cs** e adicione-lhe o seguinte código.

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

Este código define uma classe de entidade que usa o primeiro nome do cliente como chave de linha e apelido como chave de partição. Em conjunto, a chave da fila e a partição da entidade identificam-na de forma exclusiva na tabela. As entidades com a mesma chave de partição podem ser questionadas mais rapidamente do que as entidades com diferentes chaves de partição, mas a utilização de chaves de partição diversificadas permite uma maior escalabilidade de operações paralelas. As entidades a serem armazenadas em tabelas têm de ter um tipo suportado, por exemplo derivadas da classe [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). As propriedades de entidade que pretende armazenar numa tabela têm de ser propriedades públicas do tipo e suportar a introdução e a definição de valores. Além disso, o tipo de entidade tem de expor um construtor sem parâmetros.

## <a name="insert-or-merge-an-entity"></a>Inserir ou fundir uma entidade

O exemplo de código a seguir cria um objeto de entidade e adiciona-o à tabela. O método InsertOrMerge dentro da classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) é utilizado para inserir ou fundir uma entidade. O [métodoCloudTable.Exebonito Async](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) é chamado para executar a operação. 

Clique à direita no seu projeto **CosmosTableSamples**. Selecione **Adicionar**, **Novo Item** e adicione uma classe chamada **SamplesUtils.cs**. Esta classe armazena todo o código necessário para a realização de operações CRUD nas entidades. 

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

Pode obter a entidade a partir de uma partição utilizando o método Recuperar sob a classe [TableOperation.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) O exemplo de código a seguir obtém a chave de partição, o e-mail e o número de telefone de uma entidade cliente. Este exemplo também imprime as unidades de pedido consumidas para consulta da entidade. Para consultar uma entidade, apencha o seguinte código para **SamplesUtils.cs** ficheiro: 

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

Pode facilmente eliminar uma entidade depois de a ter obtido através do mesmo padrão mostrado para atualizar uma entidade. O código seguinte obtém e elimina uma entidade de cliente. Para eliminar uma entidade, apencha o seguinte código para **SamplesUtils.cs** ficheiro: 

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

## <a name="execute-the-crud-operations-on-sample-data"></a>Executar as operações CRUD em dados de amostra

Depois de definir os métodos para criar entidades de mesa, inserção ou fusão, execute estes métodos nos dados da amostra. Para tal, clique em clique no seu projeto **CosmosTableSamples**. **Selecione Adicionar**, **Novo Item** e adicione uma classe nomeada **BasicSamples.cs** e adicione-lhe o seguinte código. Este código cria uma tabela, acrescenta-lhe entidades. Se desejar eliminar a entidade e a mesa no final do projeto, remova os comentários `table.DeleteIfExistsAsync()` e `SamplesUtils.DeleteEntityAsync(table, customer)` métodos do seguinte código:

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

O código anterior cria uma tabela que começa com "demo" e o GUID gerado é anexado ao nome da mesa. Em seguida, adiciona uma entidade cliente com o primeiro e último nome como "Harp Walter" e posteriormente atualiza o número de telefone deste utilizador. 

Neste tutorial, criou um código para realizar operações básicas da CRUD nos dados armazenados na conta API da tabela. Também pode efetuar operações avançadas, tais como – dados de inserção de lotes, consulta de todos os dados dentro de uma partição, consultar uma série de dados dentro de uma partição, Listar tabelas na conta cujos nomes começam com o prefixo especificado. You can download the complete sample form [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) GitHub repository. A [classe AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) tem mais operações que pode realizar nos dados.  

## <a name="run-the-project"></a>Executar o projeto

Do seu projeto **CosmosTableSamples.** Abra a classe denominada **Program.cs** e adicione-lhe o seguinte código para ligar para os BasicSamples quando o projeto for executado.

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

Agora construa a solução e pressione F5 para executar o projeto. Quando o projeto for executado, verá a seguinte saída na solicitação de comando:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Saída do comando":::

Se receber um erro que diga Settings.jsno ficheiro não pode ser encontrado ao executar o projeto, pode resolvê-lo adicionando a seguinte entrada de XML nas definições do projeto. Clique no CosmosTableSamples, selecione CosmosTableSamples.csproj e adicione o seguinte itemGrupo: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Agora pode inscrever-se no portal Azure e verificar se os dados existem na tabela. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Resultados no portal":::

## <a name="next-steps"></a>Passos seguintes

Agora pode proceder ao próximo tutorial e aprender a migrar dados para a conta AZure Cosmos DB Table API. 

> [!div class="nextstepaction"]
>[Como consultar dados](../cosmos-db/table-import.md)
